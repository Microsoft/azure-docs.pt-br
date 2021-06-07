---
title: Implantar hosts dedicados do Azure usando o Azure PowerShell
description: Implante VMs em hosts dedicados usando Azure PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/12/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: ed6319d5374db56cfe85e7ef9413480e523d9a34
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102050878"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Implantar VMs em hosts dedicados usando o Azure PowerShell

Este artigo orienta como criar um [host dedicado](../dedicated-hosts.md) do Azure para hospedar suas máquinas virtuais (VMs). 

Verifique se você instalou Azure PowerShell versão 2.8.0 ou posterior e se está conectado a uma conta do Azure no com `Connect-AzAccount` . 

## <a name="limitations"></a>Limitações

- Os tamanhos e tipos de hardware disponíveis para hosts dedicados variam de acordo com a região. Consulte a [página de preços](https://aka.ms/ADHPricing) do host para saber mais.

## <a name="create-a-host-group"></a>Criar um grupo de hosts

Um **grupo de hosts** é um recurso que representa uma coleção de hosts dedicados. Você cria um grupo de hosts em uma região e uma zona de disponibilidade e adiciona hosts a ele. Ao planejar a alta disponibilidade, há opções adicionais. Você pode usar uma ou ambas as opções a seguir com hosts dedicados: 
- Alcance de várias zonas de disponibilidade. Nesse caso, é necessário ter um grupo de hosts em cada uma das zonas que você quer usar.
- Alcance de vários domínios de falha que são mapeados para racks físicos. 
 
Em ambos os casos, você precisa fornecer a contagem de domínios de falha ao seu grupo de hosts. Se você não quiser abranger domínios de falha no seu grupo, use uma contagem de domínio de falha de 1. 

Você também pode optar por usar tanto zonas de disponibilidade quanto domínios de falha. Este exemplo cria um grupo de hosts na zona 1, com 2 domínios de falha. 


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```


Adicione o `-SupportAutomaticPlacement true` parâmetro para que suas VMs e instâncias do conjunto de dimensionamento sejam colocadas automaticamente nos hosts, dentro de um grupo de hosts. Para obter mais informações, consulte [manual versus posicionamento automático ](../dedicated-hosts.md#manual-vs-automatic-placement).


## <a name="create-a-host"></a>Criar um host

Agora, vamos criar um host dedicado no grupo de hosts. Além de um nome para o host, você deve fornecer o SKU para o host. O SKU do host captura a série de VMs com suporte, bem como a geração de hardware para o host dedicado.

Para mais informações sobre preços e SKUs do host, consulte [Preços do Host Dedicado do Azure](https://aka.ms/ADHPricing).

Ao definir uma contagem de domínios de falha para seu grupo de hosts, você será solicitado a especificar o domínio de falha para o host. Neste exemplo, definimos o domínio de falha para o host como 1.


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>Criar uma máquina virtual

Crie uma máquina virtual no host dedicado. 

Se você especificou uma zona de disponibilidade ao criar o grupo de hosts, será necessário usar a mesma zona ao criar a máquina virtual. Para este exemplo, como nosso grupo de hosts está na zona 1, precisamos criar a VM na zona 1.  


```azurepowershell-interactive
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> A máquina virtual será criada em estado de falha em um host que não tenha recursos suficientes. 

## <a name="check-the-status-of-the-host"></a>Verifique o status do host

Você pode verificar o status de integridade do host e quantas máquinas virtuais você ainda pode implantar no host usando [GetAzHost](/powershell/module/az.compute/get-azhost) com o `-InstanceView` parâmetro.

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

A saída parecerá com o seguinte:

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="create-a-scale-set"></a>Criar um conjunto de escala 

Ao implantar um conjunto de dimensionamento, você especifica o grupo de hosts.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myDHScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"`
  -HostGroupId $hostGroup.Id
```

Se você quiser escolher manualmente a qual host será implantado o conjunto de dimensionamento, adicione `--host` e o nome do host.



## <a name="add-an-existing-vm"></a>Adicionar uma VM existente 

Você pode adicionar uma VM existente a um host dedicado, mas a VM deve primeiro ser Stop\Deallocated. Antes de mover uma VM para um host dedicado, verifique se a configuração da VM tem suporte:

- O tamanho da VM deve estar na mesma família de tamanho que o host dedicado. Por exemplo, se o host dedicado for DSv3, o tamanho da VM poderá ser Standard_D4s_v3, mas não poderá ser um Standard_A4_v2. 
- A VM precisa estar localizada na mesma região que o host dedicado.
- A VM não pode fazer parte de um grupo de posicionamento de proximidade. Remova a VM do grupo de posicionamento de proximidade antes de movê-la para um host dedicado. Para obter mais informações, consulte [mover uma VM para fora de um grupo de posicionamento de proximidade](./proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- A VM não pode estar em um conjunto de disponibilidade.
- Se a VM estiver em uma zona de disponibilidade, ela deverá ser a mesma zona de disponibilidade que o grupo de hosts. As configurações de zona de disponibilidade para a VM e o grupo de hosts devem corresponder.

Substitua os valores das variáveis pelas suas próprias informações.

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName
   
$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
   
$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force
   
Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug
   
Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```


## <a name="clean-up"></a>Limpar

Você é cobrado por hosts dedicados, mesmo se não houver máquinas virtuais implantadas. Você deve excluir os hosts que não está usando atualmente para economizar custos.  

Você só pode excluir um host quando não houver mais máquinas virtuais que o utilizem. Exclua as VMs usando [Remove-AzVM](/powershell/module/az.compute/remove-azvm).

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

Depois de excluir as VMs, você pode excluir o host usando [Remove-AzHost](/powershell/module/az.compute/remove-azhost).

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Depois de excluir todos os hosts, você poderá excluir o grupo de hosts usando [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup). 

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Você também pode excluir o grupo de recursos inteiro em um único comando usando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Isso excluirá todos os recursos criados no grupo, incluindo todas as VMs, hosts e grupos de hosts.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Próximas etapas

- Há um exemplo de modelo, [aqui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), que usa zonas e domínios de falha para obter resiliência máxima em uma região.

- Você também pode implantar hosts dedicados usando o [portal do Azure](../dedicated-hosts-portal.md).
