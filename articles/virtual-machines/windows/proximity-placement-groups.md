---
title: Criar um grupo de posicionamento de proximidade usando Azure PowerShell
description: Saiba como criar e usar grupos de posicionamento de proximidade usando Azure PowerShell.
services: virtual-machines
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 26921b3d102032cb36f47c3be7a79c2b596a1d0c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503667"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-powershell"></a>Implantar VMs em grupos de posicionamento de proximidade usando Azure PowerShell


Para obter as VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-las em um [grupo de posicionamento de proximidade](../co-location.md#proximity-placement-groups).

Um grupo de posicionamento por proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados próximos uns dos outros. Os grupos de posicionamento por proximidade são úteis para cargas de trabalho em que a baixa latência é um requisito.


## <a name="create-a-proximity-placement-group"></a>Criar um grupo de posicionamento de proximidade
Crie um grupo de posicionamento por proximidade usando o cmdlet [New-AzProximityPlacementGroup](/powershell/module/az.compute/new-azproximityplacementgroup). 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Lista de grupos de posicionamento por proximidade

Liste todos os grupos de posicionamento por proximidade usando o cmdlet [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>Criar uma máquina virtual

Crie uma VM no grupo de posicionamento de proximidade usando `-ProximityPlacementGroup $ppg.Id` para se referir à ID do grupo de posicionamento de proximidade ao usar [New-AzVM](/powershell/module/az.compute/new-azvm) para criar a VM.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

Você pode ver a VM no grupo de posicionamento usando [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>Mover uma VM existente para um grupo de posicionamento de proximidade

Você também pode adicionar uma VM existente a um grupo de posicionamento de proximidade. Você precisa stop\deallocate a VM primeiro e, em seguida, atualizar a VM e reiniciar.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>Mover uma VM existente para fora de um grupo de posicionamento de proximidade

Para remover uma VM de um grupo de posicionamento de proximidade, você precisa stop\deallocate a VM primeiro e, em seguida, atualizar a VM e reiniciar.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>Conjuntos de Disponibilidade
Você também pode criar um conjunto de disponibilidade em seu grupo de posicionamento de proximidade. Use o mesmo `-ProximityPlacementGroup` parâmetro com o cmdlet [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) para criar um conjunto de disponibilidade e todas as VMs criadas no conjunto de disponibilidade também serão criadas no mesmo grupo de posicionamento de proximidade.

Para adicionar ou remover um conjunto de disponibilidade existente em um grupo de posicionamento de proximidade, primeiro você precisa interromper todas as VMs no conjunto de disponibilidade. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>Mover um conjunto de disponibilidade existente para um grupo de posicionamento de proximidade

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>Mover um conjunto de disponibilidade existente para fora de um grupo de posicionamento de proximidade

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>Conjuntos de dimensionamento

Você também pode criar um conjunto de dimensionamento em seu grupo de posicionamento de proximidade. Use o mesmo `-ProximityPlacementGroup` parâmetro com [New-AzVmss](/powershell/module/az.compute/new-azvmss) para criar um conjunto de dimensionamento e todas as instâncias serão criadas no mesmo grupo de posicionamento de proximidade.


Para adicionar ou remover um conjunto de dimensionamento existente para um grupo de posicionamento de proximidade, primeiro você precisa interromper o conjunto de dimensionamento. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>Mover um conjunto de dimensionamento existente para um grupo de posicionamento de proximidade

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>Mover um conjunto de dimensionamento existente de um grupo de posicionamento de proximidade

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

Você também pode usar a [CLI do Azure](../linux/proximity-placement-groups.md) para criar grupos de posicionamento por proximidade.