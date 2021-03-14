---
title: Criar uma VM do Windows com o construtor de imagens do Azure usando o PowerShell
description: Crie uma VM do Windows com o módulo do PowerShell do Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 90d09763f2c9e167d6a0a34adbbc444ebad14c46
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693451"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder-using-powershell"></a>Versão prévia: criar uma VM do Windows com o construtor de imagem do Azure usando o PowerShell

Este artigo demonstra como você pode criar uma imagem personalizada do Windows usando o módulo do PowerShell do construtor de imagem de VM do Azure.

> [!CAUTION]
> O Construtor de Imagens do Azure está atualmente em versão prévia pública. A versão prévia é fornecida sem um contrato de nível de serviço. Ela não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se você optar por usar o PowerShell localmente, este artigo exigirá que você instale o módulo Az PowerShell e conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Para obter mais informações sobre como instalar o módulo Az PowerShell, confira [Instalar o Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Embora os módulos do PowerShell **AZ. ImageBuilder** e **AZ. ManagedServiceIdentity** estejam em versão prévia, você deve instalá-los separadamente usando o `Install-Module` cmdlet com o `AllowPrerelease` parâmetro. Depois que esses módulos do PowerShell ficarem disponíveis para o público geral, eles se tornarão parte das versões futuras do módulo do PowerShell AZ e estarão disponíveis nativamente em Azure Cloud Shell.

```azurepowershell-interactive
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se tiver várias assinaturas do Azure, escolha a que for adequada para cobrança do recurso. Selecione uma assinatura específica usando o cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

### <a name="register-features"></a>Registrar recursos

Se esta for a primeira vez que você usa o construtor de imagens do Azure durante a versão prévia, registre o novo recurso **VirtualMachineTemplatePreview** .

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

Verifique o status do registro do recurso.

> [!NOTE]
> O **RegistrationState** pode estar no `Registering` estado por vários minutos antes da alteração para `Registered` . Aguarde até que o status seja **registrado** antes de continuar.

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

Registre os provedores de recursos a seguir para uso com sua assinatura do Azure se eles ainda não estiverem registrados.

- Microsoft.Compute
- Microsoft.KeyVault
- Microsoft.Storage
- Microsoft.Network
- Microsoft.VirtualMachineImages

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute, Microsoft.KeyVault, Microsoft.Storage, Microsoft.VirtualMachineImages, Microsoft.Network |
  Where-Object RegistrationState -ne Registered |
    Register-AzResourceProvider
```

## <a name="define-variables"></a>Definir variáveis

Você usará várias informações repetidamente. Crie variáveis para armazenar as informações.

```azurepowershell-interactive
# Destination image resource group name
$imageResourceGroup = 'myWinImgBuilderRG'

# Azure region
$location = 'WestUS2'

# Name of the image to be created
$imageTemplateName = 'myWinImage'

# Distribution properties of the managed image upon completion
$runOutputName = 'myDistResults'
```

Crie uma variável para sua ID de assinatura do Azure. Para confirmar que a `subscriptionID` variável contém sua ID de assinatura, você pode executar a segunda linha no exemplo a seguir.

```azurepowershell-interactive
# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id
Write-Output $subscriptionID
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../../azure-resource-manager/management/overview.md) usando o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados como um grupo.

O exemplo a seguir cria um grupo de recursos com base no nome na variável `$imageResourceGroup` na região especificada na variável `$location`. Esse grupo de recursos é usado para armazenar o artefato do modelo de configuração de imagem e a imagem.

```azurepowershell-interactive
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="create-user-identity-and-set-role-permissions"></a>Criar identidade do usuário e definir permissões de função

Conceda permissões do Azure Image Builder para criar imagens no grupo de recursos especificado usando o exemplo a seguir. Sem essa permissão, o processo de criação de imagem não será concluído com êxito.

Crie variáveis para a definição de função e nomes de identidade. Esses valores devem ser exclusivos.

```azurepowershell-interactive
[int]$timeInt = $(Get-Date -UFormat '%s')
$imageRoleDefName = "Azure Image Builder Image Def $timeInt"
$identityName = "myIdentity$timeInt"
```

Crie uma identidade de usuário.

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName
```

Armazene o recurso de identidade e as IDs de entidade de segurança em variáveis.

```azurepowershell-interactive
$identityNameResourceId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```

### <a name="assign-permissions-for-identity-to-distribute-images"></a>Atribuir permissões para identidade para distribuir imagens

Baixe o arquivo de configuração. JSON e modifique-o com base nas configurações definidas neste artigo.

```azurepowershell-interactive
$myRoleImageCreationUrl = 'https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json'
$myRoleImageCreationPath = "$env:TEMP\myRoleImageCreation.json"

Invoke-WebRequest -Uri $myRoleImageCreationUrl -OutFile $myRoleImageCreationPath -UseBasicParsing

$Content = Get-Content -Path $myRoleImageCreationPath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $imageResourceGroup
$Content = $Content -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName
$Content | Out-File -FilePath $myRoleImageCreationPath -Force
```

Crie a definição da função.

```azurepowershell-interactive
New-AzRoleDefinition -InputFile $myRoleImageCreationPath
```

Conceda a definição de função à entidade de serviço do construtor de imagem.

```azurepowershell-interactive
$RoleAssignParams = @{
  ObjectId = $identityNamePrincipalId
  RoleDefinitionName = $imageRoleDefName
  Scope = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
}
New-AzRoleAssignment @RoleAssignParams
```

> [!NOTE]
> Se você receber o erro: "_New-AzRoleDefinition: limite de definição de função excedido. Não é possível criar mais definições de função._", consulte [solucionar problemas do RBAC do Azure](../../role-based-access-control/troubleshooting.md).

## <a name="create-a-shared-image-gallery"></a>Criar uma Galeria de Imagens Compartilhadas

Crie a galeria.

```azurepowershell-interactive
$myGalleryName = 'myImageGallery'
$imageDefName = 'winSvrImages'

New-AzGallery -GalleryName $myGalleryName -ResourceGroupName $imageResourceGroup -Location $location
```

Criar uma definição de galeria.

```azurepowershell-interactive
$GalleryParams = @{
  GalleryName = $myGalleryName
  ResourceGroupName = $imageResourceGroup
  Location = $location
  Name = $imageDefName
  OsState = 'generalized'
  OsType = 'Windows'
  Publisher = 'myCo'
  Offer = 'Windows'
  Sku = 'Win2019'
}
New-AzGalleryImageDefinition @GalleryParams
```

## <a name="create-an-image"></a>Criar uma imagem

Crie um objeto de origem do Azure Image Builder. Consulte [Localizar imagens de VM do Windows no Azure Marketplace com Azure PowerShell](./cli-ps-findimage.md) para obter valores de parâmetro válidos.

```azurepowershell-interactive
$SrcObjParams = @{
  SourceTypePlatformImage = $true
  Publisher = 'MicrosoftWindowsServer'
  Offer = 'WindowsServer'
  Sku = '2019-Datacenter'
  Version = 'latest'
}
$srcPlatform = New-AzImageBuilderSourceObject @SrcObjParams
```

Crie um objeto distribuidor do Azure Image Builder.

```azurepowershell-interactive
$disObjParams = @{
  SharedImageDistributor = $true
  ArtifactTag = @{tag='dis-share'}
  GalleryImageId = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup/providers/Microsoft.Compute/galleries/$myGalleryName/images/$imageDefName"
  ReplicationRegion = $location
  RunOutputName = $runOutputName
  ExcludeFromLatest = $false
}
$disSharedImg = New-AzImageBuilderDistributorObject @disObjParams
```

Crie um objeto de personalização do Azure Image Builder.

```azurepowershell-interactive
$ImgCustomParams01 = @{
  PowerShellCustomizer = $true
  CustomizerName = 'settingUpMgmtAgtPath'
  RunElevated = $false
  Inline = @("mkdir c:\\buildActions", "mkdir c:\\buildArtifacts", "echo Azure-Image-Builder-Was-Here  > c:\\buildActions\\buildActionsOutput.txt")
}
$Customizer01 = New-AzImageBuilderCustomizerObject @ImgCustomParams01
```

Crie um segundo objeto de personalização do Azure Image Builder.

```azurepowershell-interactive
$ImgCustomParams02 = @{
  FileCustomizer = $true
  CustomizerName = 'downloadBuildArtifacts'
  Destination = 'c:\\buildArtifacts\\index.html'
  SourceUri = 'https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html'
}
$Customizer02 = New-AzImageBuilderCustomizerObject @ImgCustomParams02
```

Crie um modelo do Azure Image Builder.

```azurepowershell-interactive
$ImgTemplateParams = @{
  ImageTemplateName = $imageTemplateName
  ResourceGroupName = $imageResourceGroup
  Source = $srcPlatform
  Distribute = $disSharedImg
  Customize = $Customizer01, $Customizer02
  Location = $location
  UserAssignedIdentityId = $identityNameResourceId
}
New-AzImageBuilderTemplate @ImgTemplateParams
```

Quando concluído, uma mensagem é retornada e um modelo de configuração do construtor de imagem é criado no `$imageResourceGroup` .

Para determinar se o processo de criação de modelo foi bem-sucedido, você pode usar o exemplo a seguir.

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

Em segundo plano, o construtor de imagem também cria um grupo de recursos de preparo em sua assinatura. Esse grupo de recursos é usado para a compilação da imagem. Ele está no formato: `IT_<DestinationResourceGroup>_<TemplateName>` .

> [!WARNING]
> Não exclua o grupo de recursos de preparo diretamente. Excluir o artefato do modelo de imagem, isso fará com que o grupo de recursos de preparo seja excluído.

Se o serviço relatar uma falha durante o envio do modelo de configuração de imagem:

- Consulte [Solucionando problemas de falhas de compilação de imagem de VM do Azure (AIB)](../linux/image-builder-troubleshoot.md).
- Exclua o modelo usando o exemplo a seguir antes de tentar novamente.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup
```

## <a name="start-the-image-build"></a>Iniciar a compilação da imagem

Envie a configuração de imagem para o serviço do construtor de imagem de VM.

```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

Aguarde a conclusão do processo de criação de imagem. Esta etapa pode levar até uma hora.

Se você encontrar erros, examine [solução de problemas de falhas de compilação de imagem de VM do Azure (AIB)](../linux/image-builder-troubleshoot.md).

## <a name="create-a-vm"></a>Criar uma máquina virtual

Armazene as credenciais de logon da VM em uma variável. A senha deve ser complexa.

```azurepowershell-interactive
$Cred = Get-Credential
```

Crie a VM usando a imagem que você criou.

```azurepowershell-interactive
$ArtifactId = (Get-AzImageBuilderRunOutput -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup).ArtifactId

New-AzVM -ResourceGroupName $imageResourceGroup -Image $ArtifactId -Name myWinVM01 -Credential $Cred
```

## <a name="verify-the-customizations"></a>Verificar as personalizações

Crie uma Conexão de Área de Trabalho Remota com a VM usando o nome de usuário e a senha que você definiu quando criou a VM. Dentro da VM, abra o PowerShell e execute `Get-Content` conforme mostrado no exemplo a seguir:

```azurepowershell-interactive
Get-Content -Path C:\buildActions\buildActionsOutput.txt
```

Você deve ver a saída com base no conteúdo do arquivo criado durante o processo de personalização da imagem.

```Output
Azure-Image-Builder-Was-Here
```

Na mesma sessão do PowerShell, verifique se a segunda personalização foi concluída com êxito verificando a presença do arquivo `c:\buildArtifacts\index.html` , conforme mostrado no exemplo a seguir:

```azurepowershell-interactive
Get-ChildItem c:\buildArtifacts\
```

O resultado deve ser uma listagem de diretório mostrando o arquivo baixado durante o processo de personalização da imagem.

```Output
    Directory: C:\buildArtifacts

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---          29/01/2021    10:04            276 index.html
```


## <a name="clean-up-resources"></a>Limpar os recursos

Se os recursos criados neste artigo não forem necessários, você poderá excluí-los executando o exemplo a seguir.

### <a name="delete-the-image-builder-template"></a>Excluir o modelo do construtor de imagem

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

### <a name="delete-the-image-resource-group"></a>Excluir o grupo de recursos de imagem

> [!CAUTION]
> O exemplo a seguir exclui o grupo de recursos especificado e todos os recursos contidos nele.
> Se existirem recursos fora do escopo deste artigo no grupo de recursos especificado, eles também serão excluídos.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $imageResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os componentes do arquivo. JSON usado neste artigo, consulte referência de [modelo do Image Builder](../linux/image-builder-json.md).
