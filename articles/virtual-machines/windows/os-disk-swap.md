---
title: Trocar o disco do SO para uma VM do Azure com o PowerShell | Microsoft Docs
description: Mude o disco do sistema operacional usado por uma máquina virtual do Azure usando o PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: ae3979f7ceae4a854df00b39d9c2b9673f65f987
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720122"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Mudar o disco do sistema operacional usado por uma VM do Azure usando o PowerShell

Se você tiver uma VM existente, mas deseja trocar o disco para um disco de backup ou outro disco do sistema operacional, você pode usar o PowerShell do Azure para trocar os discos do sistema operacional. Você não precisa excluir e recriar a VM. Você pode até usar um disco gerenciado em outro grupo de recursos, desde que ele não esteja em uso.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

A VM precisa ser interrompida\desalocada e, em seguida, a ID de recurso do disco gerenciado pode ser substituída pela ID de recurso de um disco gerenciado diferente.

Certifique-se de que o tipo de armazenamento e o tamanho da VM sejam compatíveis com o disco que você deseja anexar. Por exemplo, se o disco que você deseja usar estiver no armazenamento Premium, a VM precisa ter capacidade de armazenamento Premium (como um tamanho da série DS). 

Obter uma lista de discos em um grupo de recursos usando [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Quando você tiver o nome do disco que você deseja usar, defina esse disco como o disco do sistema operacional para a VM. Este exemplo interrompe\desaloca a VM denominada *myVM* e atribui o disco denominado *newDisk* como o novo disco de sistema operacional. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Próximas etapas**

Para criar uma cópia de um disco, consulte [Instantâneo de um disco](snapshot-copy-managed-disk.md).