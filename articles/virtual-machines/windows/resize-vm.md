---
title: Use o PowerShell para redimensionar uma VM Windows no Azure | Microsoft Docs
description: Redimensione uma máquina virtual do Windows criada no modelo de implantação do Resource Manager usando o Azure Powershell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 353bc3499f93eca72dd325c2114cd364145986c6
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722906"
---
# <a name="resize-a-windows-vm"></a>Redimensionar uma VM do Windows

Este artigo mostra como mover uma máquina virtual para outro [tamanho da VM](sizes.md) usando o Powershell do Azure.

Depois de criar uma VM (máquina virtual), você pode expandir ou reduzir a VM, alterando o tamanho da VM. Em alguns casos, você deverá desalocar a VM primeiro. Isso pode acontecer se o novo tamanho não estiver disponível no cluster de hardware que hospeda atualmente a VM.

Se sua VM usa a Premium Storage - Armazenamento Premium, certifique-se de que você escolha um **s** versão de tamanho para obter suporte de armazenamento Premium. Por exemplo, escolha Standard_E4**s**_v3 em vez de Standard_E4_v3.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Redimensionar uma VM do Windows que não está em um conjunto de disponibilidade

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

Se o tamanho desejado não estiver listado, execute os seguintes comandos para desalocar a VM, redimensioná-la e reiniciar a máquina virtual. Substitua  **\<newVMsize >** com o tamanho desejado.
   
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

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Redimensionar uma VM do Windows que está em um conjunto de disponibilidade

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
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Redimensione e reinicie todas as VMs no conjunto de disponibilidade.
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>Próximas etapas

Para obter escalabilidade adicional, execute várias instâncias de VM e expanda. Para saber mais, confira [Dimensionar automaticamente computadores Windows em um Conjunto de Dimensionamento de Máquinas Virtuais](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

