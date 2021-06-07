---
title: Usar o PowerShell para implantar máquinas virtuais do Azure Spot
description: Saiba como usar Azure PowerShell para implantar máquinas virtuais do Azure spot para economizar em custos.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 9a2ad2eb197af613919efa4414da1759cd47e2e7
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802736"
---
# <a name="deploy-azure-spot-virtual-machines-using-azure-powershell"></a>Implantar máquinas virtuais do Azure Spot usando o Azure PowerShell


O uso de [máquinas virtuais do Azure Spot](../spot-vms.md) permite que você aproveite a capacidade não utilizada a uma economia de custo significativa. A qualquer momento, quando o Azure precisar da capacidade de volta, a infraestrutura do Azure removerá as máquinas virtuais do Azure Spot. Portanto, as máquinas virtuais de ponto do Azure são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento em lotes, ambientes de desenvolvimento/teste, cargas de trabalho de computação grande e muito mais.

Os preços para as máquinas virtuais do Azure Spot são variáveis, com base na região e no SKU. Para obter mais informações, consulte preços de VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Para obter mais informações sobre como definir o preço máximo, consulte [máquinas virtuais do Azure Spot – preços](../spot-vms.md#pricing).

Você tem a opção de definir um preço máximo que está disposto a pagar, por hora, para a VM. O preço máximo de uma máquina virtual de ponto do Azure pode ser definido em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor `0.98765`seria um preço máximo de $0,98765 USD por hora. Se você definir o preço máximo como `-1` , a VM não será removida com base no preço. O preço da VM será o preço atual para o ponto ou o preço de uma VM padrão, o que nunca é menor, desde que haja capacidade e cota disponível.


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

# Create a virtual machine configuration and set this to be an Azure Spot Virtual Machine

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

Você pode simular uma remoção de uma máquina virtual do Azure Spot usando REST, PowerShell ou CLI, para testar como seu aplicativo responderá a uma remoção repentina.

Na maioria dos casos, você desejará usar as máquinas virtuais da API REST [– simular a remoção](/rest/api/compute/virtualmachines/simulateeviction) para ajudar com o teste automatizado de aplicativos. Para REST, um `Response Code: 204` significa que a remoção simulada foi bem-sucedida. Você pode combinar remoções simuladas com o [serviço de evento agendado](scheduled-events.md)para automatizar como seu aplicativo responderá quando a VM for removida.

Para ver os eventos agendados em ação, Assista [à sexta-feira do Azure-usando o azure eventos agendados para se preparar para a manutenção da VM](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance).


### <a name="quick-test"></a>Teste rápido

Para um teste rápido mostrar como uma remoção simulada funcionará, vamos examinar a consulta do serviço de evento agendado para ver a aparência dele quando você simular uma remoção usando o PowerShell.

O serviço de evento agendado é habilitado para seu serviço na primeira vez que você fizer uma solicitação de eventos. 

Remoto para sua VM e, em seguida, abra um prompt de comando. 

No prompt de comando em sua VM, digite:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Essa primeira resposta pode levar até 2 minutos. De agora em diante, eles devem exibir a saída quase imediatamente.

Em um computador que tem o módulo AZ PowerShell instalado (como seu computador local), simule uma remoção usando [set-AzVM](https://docs.microsoft.com/powershell/module/az.compute/set-azvm). Substitua o nome do grupo de recursos e o nome da VM pelos seus próprios. 

```azurepowershell-interactive
Set-AzVM -ResourceGroupName "mySpotRG" -Name "mySpotVM" -SimulateEviction
```

A saída da resposta terá `Status: Succeeded` se a solicitação tiver sido feita com êxito.

Volte rapidamente para sua conexão remota para sua máquina virtual Spot e consulte o ponto de extremidade de Eventos Agendados novamente. Repita o comando a seguir até obter uma saída que contenha mais informações:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Quando o serviço de evento agendado obtiver a notificação de remoção, você receberá uma resposta semelhante a esta:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

Você pode ver isso `"EventType":"Preempt"` , e o recurso é o recurso da VM `"Resources":["myspotvm"]` . 

Você também pode ver quando a VM será removida verificando o `"NotBefore"` valor. A VM não será removida antes do tempo fornecido em `NotBefore` , portanto, essa é sua janela para que seu aplicativo seja fechado normalmente.


## <a name="next-steps"></a>Próximas etapas

Você também pode criar uma máquina virtual do Azure Spot usando o [CLI do Azure](../linux/spot-cli.md), o [portal](../spot-portal.md) ou um [modelo](../linux/spot-template.md).

Consulte as informações de preços atuais usando a [API de preços de varejo do Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) para obter informações sobre os preços de máquina virtual do Azure Spot. O `meterName` e `skuName` os dois conterão `Spot` .

Se você encontrar um erro, consulte [códigos de erro](../error-codes-spot.md).
