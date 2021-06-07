---
title: Criar um instantâneo de um disco rígido virtual usando o portal ou o PowerShell
description: Saiba como criar uma cópia de uma VM do Azure para usar como um backup ou para solucionar problemas usando o portal ou o PowerShell.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/08/2018
ms.author: rogarana
ms.openlocfilehash: 9070b69ac4c6b85791ff3dd4662273e75a3cd22c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556053"
---
# <a name="create-a-snapshot-using-the-portal-or-powershell"></a>Criar um instantâneo usando o portal ou o PowerShell

Um instantâneo é uma cópia completa, somente leitura de um disco rígido virtual (VHD). Você pode tirar um instantâneo de um VHD para usar como backup, ou para solucionar problemas VM (máquina virtual) do disco do sistema operacional ou dados.

Se você pretende usar o instantâneo para criar uma nova VM, recomendamos desligar a VM antes de capturar um instantâneo para limpar todos os processos em andamento.

## <a name="use-the-azure-portal"></a>Use o Portal do Azure 

Para criar um instantâneo, conclua as seguintes etapas: 
1.  Na [portal do Azure](https://portal.azure.com), selecione **criar um recurso**.
2. Pesquise e selecione **instantâneo**.
3. Na janela **Instantâneo**, selecione **Criar**. A janela **Criar novo instantâneo** é exibida.
4. Insira um **Nome** para o instantâneo.
5. Selecione um [Grupo de Recursos](../../azure-resource-manager/management/overview.md#resource-groups) existente ou insira o nome de um novo. 
6. Selecione um **local** do datacenter do Azure.  
7. Para **Disco de origem**, selecione o disco gerenciado para instantâneo.
8. Escolha o **Tipo de conta** a ser usado para armazenar o instantâneo. Selecione **Standard_HDD**, a menos que você precise que o instantâneo seja armazenado em um disco de alto desempenho.
9. Selecione **Criar**.

## <a name="use-powershell"></a>Usar o PowerShell

As etapas a seguir mostram como copiar o disco VHD e criar a configuração de instantâneo. Em seguida, você pode tirar um instantâneo do disco usando o cmdlet [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) . 

 

1. Defina alguns parâmetros: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. Obtenha a VM:

   ```azurepowershell-interactive
   $vm = Get-AzVM `
       -ResourceGroupName $resourceGroupName `
       -Name $vmName
   ```

3. Crie a configuração do instantâneo. Neste exemplo, o instantâneo é do disco do sistema operacional:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig `
       -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
       -Location $location `
       -CreateOption copy
   ```
   
   > [!NOTE]
   > Se você quiser armazenar o instantâneo no armazenamento com resiliência de zona, crie-o em uma região que dá suporte a [zonas de disponibilidade](../../availability-zones/az-overview.md) e inclua o `-SkuName Standard_ZRS` parâmetro.   
   
4. Crie o instantâneo:

   ```azurepowershell-interactive
   New-AzSnapshot `
       -Snapshot $snapshot `
       -SnapshotName $snapshotName `
       -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Próximas etapas

Cria uma máquina virtual usando um instantâneo criando um disco gerenciado do instantâneo e anexando o novo disco gerenciado como disco do SO. Para obter mais informações, consulte o exemplo em [Criar uma VM de um instantâneo com o PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot).