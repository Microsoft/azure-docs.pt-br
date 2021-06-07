---
title: Especificar informações do plano de compra do Marketplace usando Azure PowerShell
description: Saiba como especificar detalhes do plano de compra do Azure Marketplace ao criar imagens em uma galeria de imagens compartilhada.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/07/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 9df1f6b02e6572c8f2153016c0142912e24fcfe8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562530"
---
# <a name="supply-azure-marketplace-purchase-plan-information-when-creating-images"></a>Fornecer informações do plano de compra do Azure Marketplace ao criar imagens

Se você estiver criando uma imagem em uma galeria compartilhada, usando uma fonte que foi criada originalmente a partir de uma imagem do Azure Marketplace, talvez seja necessário acompanhar as informações do plano de compra. Este artigo mostra como localizar informações do plano de compra para uma VM e, em seguida, usar essas informações ao criar uma definição de imagem. Também abordamos o uso das informações da definição de imagem para simplificar o fornecimento das informações do plano de compra ao criar uma VM para uma imagem.

Para obter mais informações sobre como localizar e usar imagens do Marketplace, consulte [Localizar e usar imagens do Azure Marketplace](./windows/cli-ps-findimage.md).


## <a name="get-the-source-vm-information"></a>Obter as informações da VM de origem
Se você ainda tiver a VM original, poderá obter o nome do plano, o editor e as informações do produto a partir dele usando Get-AzVM. Este exemplo obtém uma VM chamada *myVM* no grupo de recursos *MyResource* Group e, em seguida, exibe as informações do plano de compra para a VM.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

## <a name="create-the-image-definition"></a>Criar a definição de imagem

Obtenha a Galeria de imagens que você deseja usar para armazenar a imagem. Você pode listar todas as galerias primeiro.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Em seguida, crie variáveis para a galeria que você deseja usar. Neste exemplo, estamos criando uma variável chamada `$gallery` para *myGallery* no grupo de recursos *myGalleryRG* .

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myGalleryRG
```

Crie a definição de imagem, usando  `-PurchasePlanPublisher` os `-PurchasePlanProduct` parâmetros, e `-PurchasePlanName` .

```azurepowershell-interactive
 $imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Linux `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU' `
   -PurchasePlanPublisher $vm.Plan.Publisher `
   -PurchasePlanProduct $vm.Plan.Product `
   -PurchasePlanName  $vm.Plan.Name
```

Em seguida, crie a versão da imagem usando [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). Você pode criar uma versão de imagem de uma [VM](image-version-vm-powershell.md#create-an-image-version), [imagem gerenciada](image-version-managed-image-powershell.md#create-an-image-version), [VHD\snapshot](image-version-snapshot-powershell.md#create-an-image-version)ou [outra versão de imagem](image-version-another-gallery-powershell.md#create-the-image-version). 


## <a name="create-the-vm"></a>Criar a VM

Ao ir para criar uma VM a partir da imagem, você pode usar as informações da definição de imagem para passar as informações do Publicador usando [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan).


```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "mySIGPubVM"
$location = "West Central US"
$vmName = "mySIGPubVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version. Set-AZVMPlan is used to pass the plan information in for the VM.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2   | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Set-AzVMPlan `
     -Publisher $imageDefinition.PurchasePlan.Publisher `
     -Product $imageDefinition.PurchasePlan.Product `
     -Name $imageDefinition.PurchasePlan.Name | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create the virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como localizar e usar imagens do Marketplace, consulte [Localizar e usar imagens do Azure Marketplace](./windows/cli-ps-findimage.md).
