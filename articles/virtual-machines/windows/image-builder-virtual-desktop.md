---
title: Construtor de imagem – criar uma imagem de área de trabalho virtual do Windows
description: Criar uma imagem de VM do Azure da área de trabalho virtual do Windows usando o construtor de imagem do Azure no PowerShell.
author: danielsollondon
ms.author: danis
ms.reviewer: cynthn
ms.date: 01/27/2021
ms.topic: article
ms.service: virtual-machines-windows
ms.collection: windows
ms.subservice: imaging
ms.openlocfilehash: 69718b219d239ac13e5d932b05a7dd29619adaa3
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045579"
---
# <a name="create-a-windows-virtual-desktop-image-using-azure-vm-image-builder-and-powershell"></a>Criar uma imagem de área de trabalho virtual do Windows usando o Azure VM Builder e o PowerShell

Este artigo mostra como criar uma imagem de área de trabalho virtual do Windows com estas personalizações:

* Instalando o [FsLogix](https://github.com/DeanCefola/Azure-WVD/blob/master/PowerShell/FSLogixSetup.ps1).
* Executando um [script de otimização de área de trabalho virtual do Windows](https://github.com/The-Virtual-Desktop-Team/Virtual-Desktop-Optimization-Tool) do repositório da Comunidade.
* Instale [o Microsoft Teams](../../virtual-desktop/teams-on-wvd.md).
* [Reiniciar](../linux/image-builder-json.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#windows-restart-customizer)
* Executar [Windows Update](../linux/image-builder-json.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#windows-update-customizer)

Mostraremos como automatizar isso usando o construtor de imagem de VM do Azure e distribuir a imagem para uma [Galeria de imagens compartilhada](../shared-image-galleries.md), na qual é possível replicar para outras regiões, controlar a escala e compartilhar a imagem dentro e fora de suas organizações.


Para simplificar a implantação de uma configuração do Image Builder, este exemplo usa um modelo de Azure Resource Manager com o modelo do Image Builder aninhado dentro do. Isso lhe dá alguns outros benefícios, como variáveis e entradas de parâmetro. Você também pode passar parâmetros da linha de comando.

Este artigo destina-se a ser um exercício de copiar e colar.

> [!NOTE]
> Os scripts para instalar os aplicativos estão localizados no [GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/14_Building_Images_WVD). Eles são apenas para ilustração e teste, e não para cargas de trabalho de produção. 

## <a name="tips-for-building-windows-images"></a>Dicas para criar imagens do Windows 

- Tamanho da VM-o tamanho padrão da VM é um `Standard_D1_v2` , que não é adequado para o Windows. Use um `Standard_D2_v2` ou mais.
- Este exemplo usa os [scripts de personalizador do PowerShell](../linux/image-builder-json.md). Você precisa usar essas configurações ou a compilação irá parar de responder.

    ```json
      "runElevated": true,
      "runAsSystem": true,
    ```

    Por exemplo:

    ```json
      {
          "type": "PowerShell",
          "name": "installFsLogix",
          "runElevated": true,
          "runAsSystem": true,
          "scriptUri": "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/0_installConfFsLogix.ps1"
    ```
- Comente seu código-o log de compilação do AIB (customization. log) é extremamente detalhado. se você comentar seus scripts usando ' write-host ', eles serão enviados aos logs e facilitará a solução de problemas.

    ```PowerShell
     write-host 'AIB Customization: Starting OS Optimizations script'
    ```

- Códigos de saída-AIB espera que todos os scripts retornem um código de saída 0, qualquer código de saída diferente de zero resultará em AIB falha na personalização e na interrupção da compilação. Se você tiver scripts complexos, adicione os códigos de saída de instrumentação e emissão, eles serão mostrados no customization. log.

    ```PowerShell
     Write-Host "Exit code: " $LASTEXITCODE
    ```
- Teste: teste e teste seu código antes em uma VM autônoma, certifique-se de que não haja prompts do usuário, você está usando o privilégio correto, etc.

- Rede- `Set-NetAdapterAdvancedProperty` . Isso está sendo definido no script de otimização, mas falha na compilação AIB, uma vez que ele desconecta a rede, isso é comentado. Está em investigação.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter os CmdLets mais recentes do Azure PowerShell instalados, consulte [aqui](/powershell/azure/overview) para obter detalhes da instalação.

```PowerShell
# Register for Azure Image Builder Feature
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

# wait until RegistrationState is set to 'Registered'

# check you are registered for the providers, ensure RegistrationState is set to 'Registered'.
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

# If they do not saw registered, run the commented out code below.

## Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
## Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
## Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
## Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="set-up-environment-and-variables"></a>Configurar o ambiente e as variáveis

```azurepowershell-interactive
# Step 1: Import module
Import-Module Az.Accounts

# Step 2: get existing context
$currentAzContext = Get-AzContext

# destination image resource group
$imageResourceGroup="wvdImageDemoRg"

# location (see possible locations in main docs)
$location="westus2"

# your subscription, this will get your current subscription
$subscriptionID=$currentAzContext.Subscription.Id

# image template name
$imageTemplateName="wvd10ImageTemplate01"

# distribution properties object name (runOutput), i.e. this gives you the properties of the managed image on completion
$runOutputName="sigOutput"

# create resource group
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="permissions-user-identity-and-role"></a>Permissões, identidade do usuário e função 


 Crie uma identidade de usuário.

```azurepowershell-interactive
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$idenityName="aibIdentity"+$timeInt

## Add AZ PS modules to support AzUserAssignedIdentity and Az AIB
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName

$idenityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).Id
$idenityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).PrincipalId

```

Atribua permissões à identidade para distribuir imagens. Esse comando baixará e atualizará o modelo com os parâmetros especificados anteriormente.

```azurepowershell-interactive
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

> [!NOTE] 
> Se você vir este erro: ' New-AzRoleDefinition: o limite de definição de função foi excedido. Não é possível criar mais definições de função. ' consulte este artigo para resolver: https://docs.microsoft.com/azure/role-based-access-control/troubleshooting .



## <a name="create-the-shared-image-gallery"></a>Criar a Galeria de Imagens Compartilhadas 

Se você ainda não tiver uma galeria de imagens compartilhada, precisará criar uma.

```azurepowershell-interactive
$sigGalleryName= "myaibsig01"
$imageDefName ="win10wvd"

# create gallery
New-AzGallery -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup  -Location $location

# create gallery definition
New-AzGalleryImageDefinition -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup -Location $location -Name $imageDefName -OsState generalized -OsType Windows -Publisher 'myCo' -Offer 'Windows' -Sku '10wvd'

```

## <a name="configure-the-image-template"></a>Configurar o modelo de imagem

Para este exemplo, temos um modelo pronto para baixar e atualizar o modelo com os parâmetros especificados anteriormente, ele instalará FsLogix, otimizações do sistema operacional, Microsoft Teams e executar Windows Update no final.

Se você abrir o modelo, poderá ver na propriedade Source a imagem que está sendo usada, neste exemplo ele usa uma imagem de várias sessões do Win 10. 

### <a name="windows-10-images"></a>Imagens do Windows 10
Dois tipos de chave dos quais você deve estar atento: várias sessões e sessão única.

As imagens de várias sessões são destinadas ao uso em pool. Aqui está um exemplo dos detalhes da imagem no Azure:

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "20h2-evd",
"version": "latest"
```

As imagens de sessão única são pretendidas para uso individual. Aqui está um exemplo dos detalhes da imagem no Azure:

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "19h2-ent",
"version": "latest"
```

Você também pode alterar as imagens Win10 disponíveis:

```azurepowershell-interactive
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsDesktop -Offer windows-10
```

## <a name="download-template-and-configure"></a>Baixar modelo e configurar

Agora, você precisa baixar o modelo e configurá-lo para seu uso.

```azurepowershell-interactive
$templateUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json"
$templateFilePath = "armTemplateWVD.json"

Invoke-WebRequest -Uri $templateUrl -OutFile $templateFilePath -UseBasicParsing

((Get-Content -path $templateFilePath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<rgName>',$imageResourceGroup) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<runOutputName>',$runOutputName) | Set-Content -Path $templateFilePath

((Get-Content -path $templateFilePath -Raw) -replace '<imageDefName>',$imageDefName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<sharedImageGalName>',$sigGalleryName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region1>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$idenityNameResourceId) | Set-Content -Path $templateFilePath

```

Fique à vontade para exibir o [modelo](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json), todo o código é visível.


## <a name="submit-the-template"></a>Enviar o modelo

Seu modelo deve ser enviado ao serviço, isso baixará quaisquer artefatos dependentes (como scripts), validará, verificará as permissões e os armazenará no grupo de recursos de preparo, prefixado, *IT_*.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -api-version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location

# Optional - if you have any errors running the above, run:
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)
$getStatus.ProvisioningErrorCode 
$getStatus.ProvisioningErrorMessage
```
 
## <a name="build-the-image"></a>Criar a imagem
```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName -NoWait
```

> [!NOTE]
> O comando não aguardará que o serviço do construtor de imagem conclua a compilação da imagem, você poderá consultar o status abaixo.

```azurepowershell-interactive
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)

# this shows all the properties
$getStatus | Format-List -Property *

# these show the status the build
$getStatus.LastRunStatusRunState 
$getStatus.LastRunStatusMessage
$getStatus.LastRunStatusRunSubState
```
## <a name="create-a-vm"></a>Criar uma máquina virtual
Agora que a compilação foi concluída, você pode criar uma VM a partir da imagem, usar os exemplos [aqui](/powershell/module/az.compute/new-azvm#examples).

## <a name="clean-up"></a>Limpeza

Exclua o modelo de grupo de recursos primeiro, não exclua apenas o grupo de recursos inteiro, caso contrário, o grupo de recursos de preparo (*IT_*) usado pelo AIB não será limpo.

Remova o modelo de imagem.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name wvd10ImageTemplate
```

Exclua a atribuição de função.

```azurepowershell-interactive
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## remove definitions
Remove-AzRoleDefinition -Name "$idenityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## delete identity
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName -Force
```

Exclua o grupo de recursos.

```azurepowershell-interactive
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Você pode experimentar mais exemplos [no GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).