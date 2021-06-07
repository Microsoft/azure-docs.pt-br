---
title: Redimensionar uma VM do Windows no Azure
description: Alterar o tamanho da VM usado para uma máquina virtual do Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 01/13/2020
ms.author: cynthn
ms.openlocfilehash: f7f41c0fb2bf949dd47491761fde8025a2d35155
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560643"
---
# <a name="resize-a-windows-vm"></a>Redimensionar uma VM do Windows

Este artigo mostra como mover uma VM para um [tamanho de VM](../sizes.md)diferente.

Depois de criar uma VM (máquina virtual), você pode expandir ou reduzir a VM, alterando o tamanho da VM. Em alguns casos, você deverá desalocar a VM primeiro. Isso pode acontecer se o novo tamanho não estiver disponível no cluster de hardware que hospeda atualmente a VM.

Se sua VM usa a Premium Storage - Armazenamento Premium, certifique-se de que você escolha um **s** versão de tamanho para obter suporte de armazenamento Premium. Por exemplo, escolha Standard_E4 **s** _v3 em vez de Standard_E4_v3.

## <a name="use-the-portal"></a>Usar o portal

1. Abra o [Portal do Azure](https://portal.azure.com).
1. Abra a página da máquina virtual.
1. No menu à esquerda, selecione **tamanho**.
1. Escolha um novo tamanho na lista de tamanhos disponíveis e, em seguida, selecione **redimensionar**.


Se a máquina virtual estiver em execução no momento, alterar seu tamanho fará com que ela seja reiniciada. Parar a máquina virtual pode revelar tamanhos adicionais.

## <a name="use-powershell-to-resize-a-vm-not-in-an-availability-set"></a>Usar o PowerShell para redimensionar uma VM que não está em um conjunto de disponibilidade

Defina algumas variáveis. Substitua os valores com suas próprias informações.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Liste os tamanhos de VM que estão disponíveis no cluster do hardware onde a VM está hospedada. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Se o tamanho desejado estiver listado, execute os seguintes comandos para redimensionar a VM. Se o tamanho desejado não estiver listado, vá para a etapa 3.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Se o tamanho desejado não estiver listado, execute os seguintes comandos para desalocar a VM, redimensioná-la e reiniciar a máquina virtual. Substitua **\<newVMsize>** pelo tamanho desejado.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Desalocar a VM libera os endereços IP dinâmicos atribuídos à VM. Os discos do sistema operacional e de dados não são afetados. 
> 
> 

## <a name="use-powershell-to-resize-a-vm-in-an-availability-set"></a>Usar o PowerShell para redimensionar uma VM em um conjunto de disponibilidade

Se o novo tamanho de uma VM em um conjunto de disponibilidade não estiver disponível no cluster de hardware que está hospedando atualmente a VM, todas as VMs no conjunto de disponibilidade precisarão ser desalocadas para redimensionar a VM. Talvez também seja necessário atualizar o tamanho de outras VMs no conjunto de disponibilidade depois que uma máquina virtual for redimensionada. Para redimensionar uma VM em um conjunto de disponibilidade, execute as seguintes etapas.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Liste os tamanhos de VM que estão disponíveis no cluster do hardware onde a VM está hospedada. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Se o tamanho desejado estiver listado, execute o comando a seguir para redimensionar a VM. Se não estiver listado, vá para a próxima seção.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Se o tamanho desejado não estiver listado, continue com as seguintes etapas para desalocar todas as VMs no conjunto de disponibilidade, redimensionar VMs e reiniciá-los.

Pare todas as VMs no conjunto de disponibilidade.
   
```powershell
$availabilitySetName = "<availabilitySetName>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines |  Stop-AzVM -Force -NoWait  
```

Redimensione e reinicie todas as VMs no conjunto de disponibilidade.
   
```powershell
$availabilitySetName = "<availabilitySetName>"
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines | Foreach-Object { $_.HardwareProfile.VmSize = $newSize }
$virtualMachines | Update-AzVM
$virtualMachines | Start-AzVM
```

## <a name="next-steps"></a>Próximas etapas

Para obter escalabilidade adicional, execute várias instâncias de VM e expanda horizontalmente. Para obter mais informações, consulte [dimensionar automaticamente máquinas do Windows em um conjunto de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md).