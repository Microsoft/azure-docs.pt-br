---
title: Usar o Construtor de Imagens do Azure com uma galeria de imagens para VMs do Windows (versão prévia)
description: Crie versões de imagem da Galeria Compartilhada do Azure usando o Construtor de Imagens do Azure e o Azure PowerShell.
author: cynthn
ms.author: cynthn
ms.date: 05/05/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: e3a7ea2cd696f54b032a3f17eceff466a6201436
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878673"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Visualização: Criar uma imagem do Windows e distribuí-la para uma Galeria de Imagens Compartilhadas 

Este artigo mostra como você pode usar o Construtor de Imagens do Azure e o Azure PowerShell para criar uma versão de imagem em uma [Galeria de Imagens Compartilhadas](../shared-image-galleries.md) e, em seguida, distribuir a imagem globalmente. Você também pode fazer isso usando a [CLI do Azure](../linux/image-builder-gallery.md).

Usaremos um modelo .json para configurar a imagem. O arquivo .json que estamos usando está aqui: [armTemplateWinSIG. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). Vamos baixar e editar uma versão local do modelo, assim, este artigo é escrito usando a sessão local do PowerShell.

Para distribuir a imagem a uma Galeria de Imagens Compartilhadas, o modelo usa [sharedImage](../linux/image-builder-json.md#distribute-sharedimage) como o valor da seção `distribute` do modelo.

O Construtor de Imagens do Azure executa automaticamente sysprep para generalizar a imagem. Esse é um comando sysprep genérico que você pode [substituir](../linux/image-builder-troubleshoot.md#vms-created-from-aib-images-do-not-create-successfully), se necessário. 

Esteja ciente de quantas vezes você extratifica as personalizações. Você pode executar o comando Sysprep até oito vezes em uma imagem do Windows. Depois de executar o Sysprep oito vezes, você deverá recriar a imagem do Windows. Para obter mais informações, confira [Limites de quantas vezes você pode executar o Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep). 

> [!IMPORTANT]
> O Construtor de Imagens do Azure está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Registrar os recursos
Para usar o Construtor de Imagens do Azure durante a versão prévia, você precisa registrar o novo recurso.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Verifique o status do registro do recurso.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Aguarde até que `RegistrationState` seja `Registered` para passar para a próxima etapa.

Verifique os registros do provedor. Verifique se cada um retorna `Registered`.

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
```

Se não retornarem `Registered`, use o seguinte para registrar os provedores:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="create-variables"></a>Criar variáveis

Usaremos algumas informações repetidamente, portanto, criaremos algumas variáveis para armazená-las. Substitua os valores das variáveis, como `username` e `vmpassword`, por suas informações.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"

# Create a resource group for Image Template and Shared Image Gallery
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
```


## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Criar uma identidade atribuída pelo usuário e definir permissões no grupo de recursos
O Construtor de Imagens usará a [identidade do usuário](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) fornecida para injetar a imagem na SIG (Galeria de Imagens Compartilhadas) do Azure. Neste exemplo, você criará uma definição de função do Azure que tem as ações granulares para executar a distribuição da imagem para o SIG. A definição de função será então atribuída à identidade do usuário.

```powershell
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$identityName="aibIdentity"+$timeInt

## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName

$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```


### <a name="assign-permissions-for-identity-to-distribute-images"></a>Atribuir permissões para identidade para distribuir imagens

Esse comando baixará um modelo de definição de função do Azure e atualizará o modelo com os parâmetros especificados anteriormente.

```powershell
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
New-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

### NOTE: If you see this error: 'New-AzRoleDefinition: Role definition limit exceeded. No more role definitions can be created.' See this article to resolve:
https://docs.microsoft.com/azure/role-based-access-control/troubleshooting
```


## <a name="create-the-shared-image-gallery"></a>Criar a Galeria de Imagens Compartilhadas

Para usar o Construtor de Imagens com uma galeria de imagens compartilhadas, você precisa ter uma galeria de imagens e uma definição de imagem. O Construtor de Imagens não criará a galeria de imagens e a definição de imagem para você.

Se você ainda não tiver uma definição de imagem e a definição de galeria para usar, comece criando-as. Primeiro, crie uma galeria de imagens.

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>Baixar e configurar o modelo

Baixe o modelo .json e configure-o com suas variáveis.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$identityNameResourceId) | Set-Content -Path $templateFilePath
```


## <a name="create-the-image-version"></a>Criar a versão da imagem

Seu modelo deve ser enviado ao serviço. Isso baixará artefatos dependentes (como scripts) e os armazenará no Grupo de Recursos de preparo, prefixado com *IT_* .

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -apiversion "2019-05-01-preview" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

Para criar a imagem, você precisa invocar 'Run' no modelo.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2019-05-01-preview" `
   -Action Run
```

Criar a imagem e replicá-la para ambas as regiões pode levar algum tempo. Aguarde até que essa parte seja concluída antes de passar para a criação de uma VM.

Para obter informações sobre as opções para automatizar a obtenção do status do build da imagem, confira o [Leiame](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/readme.md#get-status-of-the-image-build-and-query) para este modelo no GitHub.


## <a name="create-the-vm"></a>Criar a VM

Crie uma VM com base na versão da imagem criada pelo Construtor de Imagens do Azure.

Obtenha a versão da imagem que você criou.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Crie a VM na segunda região em que a imagem foi replicada.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>Verificar a personalização
Crie uma Conexão de Área de Trabalho Remota com a VM usando o nome de usuário e a senha que você definiu quando criou a VM. Dentro da VM, abra um prompt de cmd e digite:

```console
dir c:\
```

Você deve ver um diretório chamado `buildActions` que foi criado durante a personalização da imagem.


## <a name="clean-up-resources"></a>Limpar os recursos
Se você quiser agora tentar personalizar novamente a versão da imagem para criar uma versão da mesma imagem, **ignore esta etapa** e vá para [Usar o Construtor de Imagens do Azure para criar outra versão de imagem](image-builder-gallery-update-image-version.md).


Isso excluirá a imagem que foi criada, junto com todos os outros arquivos de recurso. Verifique se você concluiu essa implantação antes de excluir os recursos.

Exclua o modelo do grupo de recursos primeiro, caso contrário, o grupo de recursos de preparo (*IT_* ) usado pelo AIB não será limpo.

Obter ResourceID do modelo de imagem. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2019-05-01-preview"
```

Excluir modelo de imagem.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

Excluir atribuição de função

```powerShell
Remove-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

remover definições

```powerShell
Remove-AzRoleDefinition -Name "$identityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

excluir identidade

```powerShell
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName -Force
```

excluir o grupo de recursos.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Para saber como atualizar a versão de imagem que você criou, confira [Usar o Construtor de Imagens do Azure para criar outra versão de imagem](image-builder-gallery-update-image-version.md).