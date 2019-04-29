---
title: Converter o Azure managed disks o armazenamento Standard para Premium para Standard ou Premium | Microsoft Docs
description: Como converter Azure discos gerenciados Standard para Premium para Standard ou Premium usando o PowerShell do Azure.
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
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: abd893c68f2e9cac713e09dd0bdafb7f277ae889
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766097"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Atualizar o tipo de armazenamento de um disco gerenciado

Há quatro opções para o Azure managed disks: Standard, Premium SSD, SSD Standard e armazenamento em disco do Azure de Ultra HD. Você pode alternar entre esses tipos de armazenamento com base em suas necessidades de desempenho com pouco tempo de inatividade. Não há suporte para essa funcionalidade para discos não gerenciados. Mas você pode facilmente [converter um disco não gerenciado em um disco gerenciado](convert-unmanaged-to-managed-disks.md) para alternar entre os tipos de disco.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Porque a conversão requer uma reinicialização da máquina virtual (VM), você deve agendar a migração de seu armazenamento em disco durante uma janela de manutenção preexistente.
* Se o disco for gerenciado, primeiro [convertê-lo em um disco gerenciado](convert-unmanaged-to-managed-disks.md) assim você pode alternar entre as opções de armazenamento.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Alternar todos os discos gerenciados de uma VM entre Standard e Premium

Este exemplo mostra como converter todos os discos da VM a partir de padrão para o armazenamento Premium ou Premium para o armazenamento Standard. Para usar o Managed Disks Premium, sua VM deve usar um [tamanho VM](sizes.md) que dá suporte a armazenamento Premium. Este exemplo também muda para um tamanho que suporte armazenamento premium:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzDiskUpdateConfig –AccountType $storageType
        Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Alternar os discos gerenciados individuais entre Standard e Premium

Para sua carga de trabalho de desenvolvimento/teste, você talvez queira uma combinação de discos Standard e Premium para reduzir os custos. Você pode optar por atualizar somente os discos que precisam de um melhor desempenho. Este exemplo mostra como converter um único disco VM de padrão para o armazenamento Premium ou do Premium para o armazenamento Standard. Para usar o Managed Disks Premium, sua VM deve usar um [tamanho VM](sizes.md) que dá suporte a armazenamento Premium. Este exemplo também mostra como alternar para um tamanho que dá suporte a armazenamento Premium:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Converter managed disks Standard para Premium no portal do Azure

Siga estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione a VM na lista de **máquinas virtuais** no portal.
3. Se a VM não está parada, selecione **pare** na parte superior da VM **visão geral** painel e aguarde até que a VM parar.
3. No painel da VM, selecione **discos** no menu.
4. Selecione o disco que você deseja converter.
5. Selecione **configuração** no menu.
6. Alterar o **tipo de conta** de **padrão HDD** para **SSD Premium**.
7. Clique em **salvar**e feche o painel de disco.

A conversão de tipo de disco é instantânea. Você pode reiniciar a VM após a conversão.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Alternar entre o padrão de HDD e SSD Standard os discos gerenciados 

Este exemplo mostra como converter um único disco VM de padrão de HDD para SSD Standard ou de SSD Standard para o disco rígido padrão:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Próximas etapas

Faça uma cópia somente leitura de uma VM usando um [snapshot](snapshot-copy-managed-disk.md).
