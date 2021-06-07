---
title: Desanexar um disco de dados de uma VM Windows – Azure
description: Desanexe um disco de dados de uma máquina virtual no Azure usando o modelo de implantação do Resource Manager.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cynthn
ms.openlocfilehash: 24c95d486ce77028a2c49917d8f98de23a3a8315
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552126"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Como desanexar um disco de dados de uma máquina virtual Windows

Quando não precisar mais de um disco de dados conectado a uma máquina virtual, você poderá desanexá-lo facilmente. Essa ação remove o disco da máquina virtual, mas não o remove do armazenamento.

> [!WARNING]
> Se você desanexar um disco, ele não será excluído automaticamente. Se você se inscreveu para o armazenamento Premium, você continuará incorrendo em encargos de armazenamento para o disco. Para obter mais informações, consulte [Preços e cobrança ao usar o Armazenamento Premium](../disks-types.md#billing).

Se desejar usar os dados existentes no disco novamente, você pode reanexá-lo à mesma máquina virtual ou anexá-lo a uma outra máquina virtual.

 

## <a name="detach-a-data-disk-using-powershell"></a>Desanexar um disco de dados usando o PowerShell

É possível remover um disco de dados *hot* usando o PowerShell, mas certifique-se de que nada esteja usando ativamente o disco antes de desanexá-lo da VM.

Neste exemplo, removemos o disco nomeado **myDisk** da VM **myVM** no grupo de recursos **myResourceGroup**. Primeiro, remova o disco usando o cmdlet [Remove-AzVMDataDisk](/powershell/module/az.compute/remove-azvmdatadisk). Em seguida, atualize o estado da máquina virtual, usando o cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm) para concluir o processo de remoção do disco de dados.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVM"
Remove-AzVMDataDisk `
   -VM $VirtualMachine `
   -Name "myDisk"
Update-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -VM $VirtualMachine
```

O disco permanecerá no armazenamento, mas não estará mais conectado a uma máquina virtual.

## <a name="detach-a-data-disk-using-the-portal"></a>Desanexar um disco de dados usando o portal

É possível remover um disco de dados *a quente*, mas verifique se nada está usando ativamente o disco antes de desanexá-lo da VM.

1. No menu à esquerda, selecione **Máquinas Virtuais**.
1. Selecione a máquina virtual que tem o disco de dados que você deseja desanexar.
1. Em **Configurações**, selecione **Discos**.
1. No painel **discos** , na extrema direita do disco de dados que você deseja desanexar, selecione o botão **X** para desanexar.
1. Selecione **Salvar** na parte superior da página para salvar as alterações.

O disco permanecerá no armazenamento, mas não estará mais conectado a uma máquina virtual. O disco não é excluído.

## <a name="next-steps"></a>Próximas etapas

Se deseja reutilizar o disco de dados, basta [anexá-lo a outra VM](attach-managed-disk-portal.md).

Se você quiser excluir o disco, para que não incorra mais nos custos de armazenamento, consulte [Localizar e excluir discos gerenciados e não-portal do Azure do Azure desconectados](../disks-find-unattached-portal.md).
