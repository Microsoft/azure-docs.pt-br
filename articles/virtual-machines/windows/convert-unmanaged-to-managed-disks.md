---
title: Converter uma máquina virtual do Windows de discos não gerenciados para Managed Disks – Azure Managed Disks | Microsoft Docs
description: Como converter uma VM do Windows de discos não gerenciados em Managed Disks usando o PowerShell no modelo de implantação do Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: 21505da414b29f2ae9eeea7f9fcad9db2e57c4fe
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766137"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Converter uma máquina virtual do Windows de discos não gerenciados em Managed Disks

Se você tiver VMs (máquinas virtuais) do Windows existentes que usam discos não gerenciados, será possível converter as VMs para usar Managed Disks por meio do serviço [Azure Managed Disks](managed-disks-overview.md). Esse processo converte o disco do sistema operacional e os discos de dados anexados.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar


* Revisão [Planejar a migração para os Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Examine [as perguntas frequentes sobre migração para Managed Disks](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Converter VMs de instância única
Esta seção aborda como converter suas VMs de instância única do Azure de discos não gerenciados em discos gerenciados. (Se suas VMs estão em uma conjunto de disponibilidade, consulte a próxima seção.) 

1. Desaloque a VM usando o cmdlet [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm). O seguinte exemplo desaloca a VM `myVM` no grupo de recursos chamado `myResourceGroup`: 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. Converter a VM em Managed Disks usando o cmdlet [ConvertTo-AzVMManagedDisk](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk). O seguinte processo converte a VM anterior, incluindo o disco do sistema operacional e discos de dados, e inicia a Máquina Virtual:

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>Converter VMs em um conjunto de disponibilidade

Se as VMs que você deseja converter em discos gerenciados estão em um conjunto de disponibilidade, converta primeiro o conjunto de disponibilidade em um conjunto de disponibilidade gerenciado.

1. Converter o conjunto de disponibilidade usando o cmdlet [Update-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/update-azavailabilityset). O exemplo a seguir atualiza o conjunto de disponibilidade nomeado `myAvailabilitySet` no grupo de recursos nomeado`myResourceGroup`:

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

2. Desaloque e converta as VMs no conjunto de disponibilidade. O seguinte script desaloca cada VM usando o cmdlet [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm), converte-a usando [ConvertTo-AzVMManagedDisk](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk) e reinicia-a automaticamente como parte do processo de conversão:

   ```azurepowershell-interactive
   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

   foreach($vmInfo in $avSet.VirtualMachinesReferences)
   {
     $vm = Get-AzVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   }
   ```


## <a name="troubleshooting"></a>solução de problemas

Se houver um erro durante a conversão ou se uma VM estiver em um estado de falha devido a problemas em uma conversão anterior, execute o cmdlet `ConvertTo-AzVMManagedDisk` novamente. Normalmente, uma repetição simples desbloqueia a situação.
Antes de converter, verifique se todas as extensões de VM estão no estado 'Provisionamento bem-sucedido' ou a conversão falhará com o código de erro 409.


## <a name="convert-using-the-azure-portal"></a>Converter usando o portal do Azure

Também é possível converter discos não gerenciados em discos gerenciados usando o portal do Azure.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione a VM na lista de VMs no portal.
3. Na folha da VM, selecione **Discos** no menu.
4. Na parte superior da folha **Discos**, selecione **Migrar para discos gerenciados**.
5. Se sua VM estiver em um conjunto de disponibilidade, haverá um aviso na folha **Migrar para discos gerenciados** informando que você precisa converter o conjunto de disponibilidade primeiro. O aviso deve ter um link em que você pode clicar para converter o conjunto de disponibilidade. Quando o conjunto de disponibilidade for convertido ou se sua VM não estiver em um conjunto de disponibilidade, clique em **Migrar** para iniciar o processo de migração de seus discos para discos gerenciados. 

A VM será interrompida e reiniciada após a conclusão da migração.

## <a name="next-steps"></a>Próximas etapas

[Converter Managed Disks padrão em premium](convert-disk-storage.md)

Faça uma cópia somente leitura de uma VM usando [instantâneos](snapshot-copy-managed-disk.md).

