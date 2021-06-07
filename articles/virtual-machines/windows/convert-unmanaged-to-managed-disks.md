---
title: Converter uma máquina virtual do Windows de discos não gerenciados em Managed Disks
description: Como converter uma VM do Windows de discos não gerenciados em Managed Disks usando o PowerShell no modelo de implantação do Resource Manager
author: roygara
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: 3f586c8907ded618b4cc4aaaadf3c87471cda0b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550715"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Converter uma máquina virtual do Windows de discos não gerenciados em Managed Disks

Se você tiver VMs (máquinas virtuais) do Windows existentes que usam discos não gerenciados, será possível converter as VMs para usar Managed Disks por meio do serviço [Azure Managed Disks](../managed-disks-overview.md). Esse processo converte o disco do sistema operacional e os discos de dados anexados.

 

## <a name="before-you-begin"></a>Antes de começar


* Revisão [Planejar a migração para os Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Examine [as perguntas frequentes sobre migração para Managed Disks](../faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* Os VHDs originais e a conta de armazenamento usados pela VM antes da conversão não são excluídos. Eles continuam a incorrer em encargos. Para evitar ser cobrado por esses artefatos, exclua os blobs VHD originais depois de verificar que a conversão foi concluída. Se você precisar encontrar esses discos desanexados para excluí-los, consulte nosso artigo [Localizar e excluir discos gerenciados e não geridos do Azure desconectados](find-unattached-disks.md).


## <a name="convert-single-instance-vms"></a>Converter VMs de instância única
Esta seção aborda como converter suas VMs de instância única do Azure de discos não gerenciados em discos gerenciados. (Se suas VMs estão em uma conjunto de disponibilidade, consulte a próxima seção.) 

1. Desaloque a VM usando o cmdlet [Stop-AzVM](/powershell/module/az.compute/stop-azvm). O seguinte exemplo desaloca a VM `myVM` no grupo de recursos chamado `myResourceGroup`: 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. Converter a VM em Managed Disks usando o cmdlet [ConvertTo-AzVMManagedDisk](/powershell/module/az.compute/convertto-azvmmanageddisk). O seguinte processo converte a VM anterior, incluindo o disco do sistema operacional e discos de dados, e inicia a Máquina Virtual:

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>Converter VMs em um conjunto de disponibilidade

Se as VMs que você deseja converter em discos gerenciados estão em um conjunto de disponibilidade, converta primeiro o conjunto de disponibilidade em um conjunto de disponibilidade gerenciado.

1. Converter o conjunto de disponibilidade usando o cmdlet [Update-AzAvailabilitySet](/powershell/module/az.compute/update-azavailabilityset). O exemplo a seguir atualiza o conjunto de disponibilidade nomeado `myAvailabilitySet` no grupo de recursos nomeado`myResourceGroup`:

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   Se a região em que o conjunto de disponibilidade está localizado tem apenas 2 domínios de falha gerenciados, mas o número de domínios de falha não gerenciado é 3, este comando mostra um erro semelhante a "O total de domínio de falha especificado é 3 e deve estar no intervalo de 1 a 2". Para resolver o erro, atualize o domínio de falha para 2 e atualize `Sku` para `Aligned` da seguinte maneira:

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. Desaloque e converta as VMs no conjunto de disponibilidade. O seguinte script desaloca cada VM usando o cmdlet [Stop-AzVM](/powershell/module/az.compute/stop-azvm), converte-a usando [ConvertTo-AzVMManagedDisk](/powershell/module/az.compute/convertto-azvmmanageddisk) e reinicia-a automaticamente como parte do processo de conversão:

   ```azurepowershell-interactive
   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

   foreach($vmInfo in $avSet.VirtualMachinesReferences)
   {
     $vm = Get-AzVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   }
   ```


## <a name="troubleshooting"></a>Solução de problemas

Se houver um erro durante a conversão ou se uma VM estiver em um estado de falha devido a problemas em uma conversão anterior, execute o cmdlet `ConvertTo-AzVMManagedDisk` novamente. Normalmente, uma repetição simples desbloqueia a situação.
Antes de converter, verifique se todas as extensões de VM estão no estado 'Provisionamento bem-sucedido' ou a conversão falhará com o código de erro 409.

## <a name="convert-using-the-azure-portal"></a>Converter usando o portal do Azure

Também é possível converter discos não gerenciados em discos gerenciados usando o portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione a VM na lista de VMs no portal.
3. Na folha da VM, selecione **Discos** no menu.
4. Na parte superior da folha **Discos**, selecione **Migrar para discos gerenciados**.
5. Se sua VM estiver em um conjunto de disponibilidade, haverá um aviso na folha **Migrar para discos gerenciados** informando que você precisa converter o conjunto de disponibilidade primeiro. O aviso deve ter um link em que você pode clicar para converter o conjunto de disponibilidade. Quando o conjunto de disponibilidade for convertido ou se sua VM não estiver em um conjunto de disponibilidade, clique em **Migrar** para iniciar o processo de migração de seus discos para discos gerenciados.

A VM será interrompida e reiniciada após a conclusão da migração.

## <a name="next-steps"></a>Próximas etapas

[Converter Managed Disks padrão em premium](convert-disk-storage.md)

Faça uma cópia somente leitura de uma VM usando [instantâneos](snapshot-copy-managed-disk.md).