---
title: Criar um conjunto de dimensionamento com base em uma imagem generalizada com Azure PowerShell
description: Crie um conjunto de dimensionamento usando uma imagem generalizada em uma galeria de imagens compartilhada usando o PowerShell.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 9edade1aa54d6f4f8160a107f84e6da2e6cf316e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878012"
---
# <a name="create-a-scale-set-from-a-generalized-image-using-powershell"></a>Criar um conjunto de dimensionamento com base em uma imagem generalizada usando o PowerShell 

Crie uma VM com base em uma versão de imagem generalizada armazenada em uma [Galeria de imagens compartilhada](../virtual-machines/shared-image-galleries.md). Se desejar criar um conjunto de dimensionamento usando uma imagem especializada, consulte [criar instâncias do conjunto de dimensionamento de uma imagem especializada](instance-specialized-image-version-powershell.md).

Depois de ter uma imagem generalizada, você pode criar um conjunto de dimensionamento de máquinas virtuais usando o cmdlet [New-AzVmss](/powershell/module/az.compute/new-azvmss) . 

Neste exemplo, estamos usando a ID de definição de imagem para garantir que sua nova VM usará a versão mais recente de uma imagem. Você também pode usar uma versão específica usando a ID de versão da imagem para `-ImageReferenceId` . Por exemplo, para usar a versão de imagem *1.0.0* Type: `-ImageReferenceId "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` . 

Lembre-se de que o uso de uma versão de imagem específica significa que a automação poderá falhar se essa versão de imagem específica não estiver disponível porque foi excluída ou removida da região. É recomendável usar a ID de definição de imagem para criar sua nova VM, a menos que uma versão de imagem específica seja necessária.


Os exemplos a seguir criam um conjunto de dimensionamento chamado *Myscalemodeset*, no grupo de recursos *myVMSSRG* , no local *SouthCentralUS* . O conjunto de dimensionamento será criado a partir da imagem *myImageDefinition* , na Galeria de imagens do *myGallery* no grupo de recursos *myGalleryRG* . Quando solicitado, defina suas próprias credenciais administrativas para as instâncias de VM no conjunto de dimensionamento.


## <a name="simplified-parameter-set"></a>Conjunto de parâmetros simplificado

Para criar rapidamente um conjunto de dimensionamento, ao mesmo tempo em que fornece informações mínimas, use o conjunto de parâmetros simplificado para criar um conjunto de dimensionamento de uma imagem da Galeria de imagens de compartilhamento.

```azurepowershell-interactive
$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myGalleryRG `
   -Name myImageDefinition

# Create user object

$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

# Create the resource group and scale set
New-AzResourceGroup -ResourceGroupName myVMSSRG -Location SouthCentralUS
New-AzVmss `
   -Credential $cred `
   -VMScaleSetName myScaleSet `
   -ImageName $imageDefinition.Id `
   -UpgradePolicyMode Automatic `
   -ResourceGroupName myVMSSRG
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento.

## <a name="extended-parameter-set"></a>Conjunto de parâmetros estendidos

Para ter controle total sobre todos os recursos, incluindo a nomenclatura, use o conjunto de parâmetros completo para criar um conjunto de dimensionamento usando uma imagem da Galeria de imagens compartilhadas. 

```azurepowershell-interactive
# Get the image definition

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myGalleryRG `
   -Name myImageDefinition

# Create user object

$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."
   
# Define variables for the scale set
$resourceGroupName = "myVMSSRG"
$scaleSetName = "myScaleSet"
$location = "South Central US"

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a networking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -ImageReferenceId $imageDefinition.Id

# Complete the configuration
Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername $cred.UserName `
  -AdminPassword $cred.Password `
  -ComputerNamePrefix "myVM"
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento.

## <a name="next-steps"></a>Próximas etapas
O [Construtor de imagens do Azure (visualização)](../virtual-machines/image-builder-overview.md) pode ajudar a automatizar a criação da versão da imagem, até mesmo usá-la para atualizar e [criar uma nova versão da imagem a partir de uma versão de imagem existente](../virtual-machines/linux/image-builder-gallery-update-image-version.md). 

Você também pode criar um recurso de Galeria de imagens compartilhadas usando modelos. Há vários Modelos de Início Rápido do Azure disponíveis: 

- [Criar uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão da Imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)

Para obter mais informações sobre galerias de imagens compartilhadas, confira a [Visão geral](../virtual-machines/shared-image-galleries.md). Se você enfrentar problemas, confira [Solução de problemas de galerias de imagens compartilhadas](../virtual-machines/troubleshooting-shared-images.md).