---
title: Criar uma VM do Windows com o construtor de imagem do Azure usando uma VNET existente (versão prévia)
description: Criar uma VM do Windows com o construtor de imagens do Azure usando uma VNET existente
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.reviewer: danis
ms.openlocfilehash: 3695732f81463efcadb3d8d8b49e367501cb6e29
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034057"
---
# <a name="use-azure-image-builder-for-windows-vms-allowing-access-to-an-existing-azure-vnet"></a>Usar o construtor de imagens do Azure para VMs do Windows permitindo o acesso a uma VNET do Azure existente

Este artigo mostra como você pode usar o construtor de imagens do Azure para criar uma imagem básica do Windows personalizada que tem acesso a recursos existentes em uma VNET. A VM de compilação que você cria é implantada em uma VNET nova ou existente que você especificar em sua assinatura. Quando você usa uma VNET do Azure existente, o serviço do construtor de imagens do Azure não requer conectividade de rede pública.

> [!IMPORTANT]
> O Construtor de Imagens do Azure está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="register-the-features"></a>Registrar os recursos

Primeiro, você deve se registrar para o serviço do construtor de imagem do Azure. O registro concede a permissão de serviço para criar, gerenciar e excluir um grupo de recursos de preparo. O serviço também tem direitos para adicionar recursos do grupo que são necessários para a compilação da imagem.

```powershell-interactive
# Register for Azure Image Builder Feature

Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

# wait until RegistrationState is set to 'Registered'
```
## <a name="set-variables-and-permissions"></a>Definir variáveis e permissões 

Você usará algumas informações repetidamente. Crie algumas variáveis para armazenar essas informações.

```powershell-interactive
# Step 1: Import module
Import-Module Az.Accounts

# Step 2: get existing context
$currentAzContext = Get-AzContext

# destination image resource group
$imageResourceGroup="aibImageRG"

# location (see possible locations in main docs)
$location="westus2"

## if you need to change your subscription: Get-AzSubscription / Select-AzSubscription -SubscriptionName 

# get subscription, this will get your current subscription
$subscriptionID=$currentAzContext.Subscription.Id

# name of the image to be created
$imageName="win2019image01"

# image distribution metadata reference name
$runOutputName="win2019ManImg02ro"

# image template name
$imageTemplateName="window2019VnetTemplate03"

# distribution properties object name (runOutput), i.e. this gives you the properties of the managed image on completion
$runOutputName="winSvrSigR01"

# VNET properties (update to match your existing VNET, or leave as-is for demo)
# VNET name
$vnetName="myexistingvnet01"
# subnet name
$subnetName="subnet01"
# VNET resource group name
$vnetRgName="existingVnetRG"
# Existing Subnet NSG Name or the demo will create it
$nsgName="aibdemoNsg"
# NOTE! The VNET must always be in the same region as the AIB service region.
```

Crie o grupo de recursos.

```powershell-interactive
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="configure-networking"></a>Configurar a rede

Se você não tiver um VNET\Subnet\NSG existente, use o script a seguir para criar um.

```powershell-interactive
New-AzResourceGroup -Name $vnetRgName -Location $location

## Create base NSG to simulate an existing NSG
New-AzNetworkSecurityGroup -Name $nsgName -ResourceGroupName $vnetRgName -location $location

$nsg = Get-AzNetworkSecurityGroup -Name $nsgName -ResourceGroupName $vnetRgName 

$subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix "10.0.1.0/24" -PrivateLinkServiceNetworkPoliciesFlag "Disabled" -NetworkSecurityGroup $nsg

New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $vnetRgName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $subnet

## NOTE! The VNET must always be in the same region as the Azure Image Builder service region.
```

### <a name="add-network-security-group-rule"></a>Adicionar regra de grupo de segurança de rede

Essa regra permite a conectividade do balanceador de carga do Azure Image Builder com a VM do proxy. A porta 60001 é para sistemas operacionais Linux e a porta 60000 é para sistemas operacionais Windows. A VM proxy conecta-se à VM de compilação usando a porta 22 para Linux OSs ou a porta 5986 para sistemas operacionais Windows.

```powershell-interactive
Get-AzNetworkSecurityGroup -Name $nsgName -ResourceGroupName $vnetRgName  | Add-AzNetworkSecurityRuleConfig -Name AzureImageBuilderAccess -Description "Allow Image Builder Private Link Access to Proxy VM" -Access Allow -Protocol Tcp -Direction Inbound -Priority 400 -SourceAddressPrefix AzureLoadBalancer -SourcePortRange * -DestinationAddressPrefix VirtualNetwork -DestinationPortRange 60000-60001 | Set-AzNetworkSecurityGroup
```

### <a name="disable-private-service-policy-on-subnet"></a>Desabilitar política de serviço particular na sub-rede

```powershell-interactive
$virtualNetwork= Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $vnetRgName 
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $subnetName} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork
```

Para obter mais informações sobre a rede do Image Builder, consulte [Opções de rede do serviço do Azure Image Builder](../linux/image-builder-networking.md).

## <a name="modify-the-example-template-and-create-role"></a>Modificar o modelo de exemplo e criar função

```powershell-interactive
$templateUrl="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1a_Creating_a_Custom_Win_Image_on_Existing_VNET/existingVNETWindows.json"
$templateFilePath = "existingVNETWindows.json"

$aibRoleNetworkingUrl="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json"
$aibRoleNetworkingPath = "aibRoleNetworking.json"

$aibRoleImageCreationUrl="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download configs
Invoke-WebRequest -Uri $templateUrl -OutFile $templateFilePath -UseBasicParsing

Invoke-WebRequest -Uri $aibRoleNetworkingUrl -OutFile $aibRoleNetworkingPath -UseBasicParsing

Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

# update AIB image config template
((Get-Content -path $templateFilePath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<rgName>',$imageResourceGroup) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<runOutputName>',$runOutputName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imageName>',$imageName) | Set-Content -Path $templateFilePath

((Get-Content -path $templateFilePath -Raw) -replace '<vnetName>',$vnetName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<subnetName>',$subnetName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<vnetRgName>',$vnetRgName) | Set-Content -Path $templateFilePath
```
## <a name="create-a-user-assigned-identity-and-set-permissions"></a>Criar uma identidade atribuída pelo usuário e definir permissões

```powershell-interactive
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$networkRoleDefName="Azure Image Builder Network Def"+$timeInt
$idenityName="aibIdentity"+$timeInt

# create user identity
## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName

$idenityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).Id
$idenityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).PrincipalId

# update template with identity
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$idenityNameResourceId) | Set-Content -Path $templateFilePath

# update the role defintion names
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role',$imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleNetworkingPath -Raw) -replace 'Azure Image Builder Service Networking Role',$networkRoleDefName) | Set-Content -Path $aibRoleNetworkingPath

# update role definitions
((Get-Content -path $aibRoleNetworkingPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleNetworkingPath
((Get-Content -path $aibRoleNetworkingPath -Raw) -replace '<vnetRgName>',$vnetRgName) | Set-Content -Path $aibRoleNetworkingPath

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath

# create role definitions from role configurations examples, this avoids granting contributor to the SPN
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json
New-AzRoleDefinition -InputFile  ./aibRoleNetworking.json

# grant role definition to image builder user identity
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $networkRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$vnetRgName"
```

Para obter mais informações sobre permissões, consulte [configurar permissões de serviço do construtor de imagem do Azure usando CLI do Azure](../linux/image-builder-permissions-cli.md) ou [configurar permissões de serviço do construtor de imagem do Azure usando o PowerShell](../linux/image-builder-permissions-powershell.md).

## <a name="create-the-image"></a>Criar a imagem

Envie a configuração de imagem para o serviço Construtor de Imagens do Azure.

```powershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -api-version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location

# note this will take minute, as validation is run (security / dependencies etc.)
```

Inicie o build da imagem.

```powershell-interactive
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Run -Force
```

## <a name="get-image-build-status-and-properties"></a>Obter o status e as propriedades da compilação da imagem

### <a name="query-the-image-template-for-current-or-last-run-status-and-image-template-settings"></a>Consultar o modelo de imagem para obter o status atual ou da última execução e as configurações do modelo de imagem
```powerShell
$managementEp = $currentAzureContext.Environment.ResourceManagerUrl

$urlBuildStatus = [System.String]::Format("{0}subscriptions/{1}/resourceGroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/{2}?api-version=2020-02-14", $managementEp, $currentAzureContext.Subscription.Id,$imageTemplateName)

$buildStatusResult = Invoke-WebRequest -Method GET  -Uri $urlBuildStatus -UseBasicParsing -Headers  @{"Authorization"= ("Bearer " + $accessToken)} -ContentType application/json 
$buildJsonStatus =$buildStatusResult.Content
$buildJsonStatus

```

A compilação da imagem para este exemplo levará aproximadamente 50 minutos (várias reinicializações, instalação/reinicialização do Windows Update), quando você consultar o status, você precisa procurar *lastRunStatus*, abaixo mostra que a compilação ainda está em execução, se ela tiver sido concluída com êxito, ela mostraria ' êxito '.

```text
  "lastRunStatus": {
   "startTime": "2019-08-21T00:39:40.61322415Z",
   "endTime": "0001-01-01T00:00:00Z",
   "runState": "Running",
   "runSubState": "Building",
   "message": ""
  },
```

### <a name="query-the-distribution-properties"></a>Consultar as propriedades de distribuição
Se você estiver distribuindo para um local de VHD, precisará de propriedades de local de imagem gerenciada ou status de replicações da Galeria de imagens compartilhadas, você precisa consultar o ' runOutput ', sempre que tiver um destino de distribuição, terá um runOutput exclusivo para descrever as propriedades do tipo de distribuição.

```powerShell
$managementEp = $currentAzureContext.Environment.ResourceManagerUrl
$urlRunOutputStatus = [System.String]::Format("{0}subscriptions/{1}/resourceGroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/{2}?api-version=2020-02-14", $managementEp, $currentAzureContext.Subscription.Id, $runOutputName)

$runOutStatusResult = Invoke-WebRequest -Method GET  -Uri $urlRunOutputStatus -UseBasicParsing -Headers  @{"Authorization"= ("Bearer " + $accessToken)} -ContentType application/json 
$runOutJsonStatus =$runOutStatusResult.Content
$runOutJsonStatus
```
## <a name="create-a-vm"></a>Criar uma máquina virtual

Agora que a compilação foi concluída, você pode criar uma VM a partir da imagem. Use os exemplos da [documentação New-AzVM do PowerShell](/powershell/module/az.compute/new-azvm#description).

## <a name="clean-up"></a>Limpar

### <a name="delete-image-template-artifact"></a>Excluir artefato de modelo de imagem
```powerShell
# Get ResourceID of the Image Template
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14"

### Delete Image Template Artifact
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force

```
### <a name="delete-role-assignment"></a>Excluir atribuição de função
```powerShell
## remove role assignments
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId  -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $networkRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$vnetRgName"

## remove definitions
Remove-AzRoleDefinition -Id $imageRoleDefObjId -Force
Remove-AzRoleDefinition -Id $networkRoleObjId -Force

## delete identity
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName -Force
```

### <a name="delete-resource-groups"></a>Excluir grupos de recursos
```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force


# delete VNET created
# BEWARE!!!!! In this example, you have either used an existing VNET or created one for this example. Do not delete your existing VNET. If you want to delete the VNET Resource group used in this example '$vnetRgName', modify the above code.
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as [Galerias de Imagens Compartilhadas do no Azure](../shared-image-galleries.md).
