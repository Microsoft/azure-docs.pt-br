---
title: Usar o PowerShell para implantar VMs de ponto do Azure
description: Saiba como usar Azure PowerShell para implantar VMs pontuais para economizar em custos.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 0ca3c99aed8160161c125a89da3cb176c6e745f6
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202055"
---
# <a name="deploy-spot-vms-using-azure-powershell"></a>Implantar VMs especiais usando Azure PowerShell


O uso de [VMs pontuais](../spot-vms.md) permite que você tire proveito de nossa capacidade não utilizada a uma economia de custo significativa. A qualquer momento, quando o Azure precisar da capacidade de volta, a infraestrutura do Azure removerá as VMs pontuais. Portanto, as VMs pontuais são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento em lotes, ambientes de desenvolvimento/teste, grandes cargas de trabalho de computação e muito mais.

Os preços para VMs pontuais são variáveis, com base na região e SKU. Para obter mais informações, consulte preços de VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Para obter mais informações sobre como definir o preço máximo, consulte [VMs pontuais – preços](../spot-vms.md#pricing).

Você tem a opção de definir um preço máximo que está disposto a pagar, por hora, para a VM. O preço máximo de uma VM Spot pode ser definido em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor `0.98765`seria um preço máximo de $0,98765 USD por hora. Se você definir o preço máximo como `-1` , a VM não será removida com base no preço. O preço da VM será o preço atual para o ponto ou o preço de uma VM padrão, o que nunca é menor, desde que haja capacidade e cota disponível.


## <a name="create-the-vm"></a>Criar a VM

Crie um spotVM usando [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) para criar a configuração. Incluir `-Priority Spot` e definir `-MaxPrice` como:
- `-1` Portanto, a VM não é removida com base no preço.
- um valor de dólar, até 5 dígitos. Por exemplo, `-MaxPrice .98765` significa que a VM será desalocada quando o preço de um spotVM vai cerca de US $98765 por hora.


Este exemplo cria um spotVM que não será desalocado com base no preço (somente quando o Azure precisar da capacidade de volta). A política de remoção é definida para desalocar a VM, para que ela possa ser reiniciada posteriormente. Se você quiser excluir a VM e o disco subjacente quando a VM for removida, defina `-EvictionPolicy` como `Delete` em `New-AzVMConfig` .


```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be a Spot VM

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1 -EvictionPolicy Deallocate | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Depois que a VM for criada, você poderá consultar para ver o preço máximo de todas as VMs no grupo de recursos.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="simulate-an-eviction"></a>Simular uma remoção

Você pode [simular uma remoção](/rest/api/compute/virtualmachines/simulateeviction) de uma VM Spot, para testar o quão bem seu aplicativo responderá a uma remoção repentina. 

Substitua o seguinte pelas suas informações: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

## <a name="next-steps"></a>Próximas etapas

Você também pode criar uma VM Spot usando o [CLI do Azure](../linux/spot-cli.md), o [portal](../spot-portal.md) ou um [modelo](../linux/spot-template.md).

Consulte as informações de preços atuais usando a [API de preços de varejo do Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) para obter informações sobre preços especiais. O `meterName` e `skuName` os dois conterão `Spot` .

Se você encontrar um erro, consulte [códigos de erro](../error-codes-spot.md).