---
title: Converter o armazenamento de discos gerenciados entre diferentes tipos de disco usando Azure PowerShell
description: Como converter o Azure Managed disks entre os diferentes tipos de discos usando Azure PowerShell.
author: roygara
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.openlocfilehash: 658dfbb654920c0dd90c4b4caaac7a5ca5962c81
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104607289"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Atualizar o tipo de armazenamento de um disco gerenciado

Há quatro tipos de disco de discos gerenciados do Azure: ultra discos do Azure, SSD Premium, SSD padrão e HDD padrão. Você pode alternar entre SSD Premium, SSD padrão e HDD padrão com base em suas necessidades de desempenho. Você ainda não é capaz de mudar de ou para um ultra Disk, você deve implantar um novo.

Não há suporte para essa funcionalidade em discos não gerenciados. Mas você pode [converter facilmente um disco não gerenciado em um disco gerenciado](convert-unmanaged-to-managed-disks.md) para poder alternar entre tipos de disco.



## <a name="before-you-begin"></a>Antes de começar

* Como a conversão requer uma reinicialização da VM (máquina virtual), você deve agendar a migração do armazenamento em disco durante uma janela de manutenção pré-existente.
* Se o disco não for gerenciado, primeiro [converta-o em um disco gerenciado](convert-unmanaged-to-managed-disks.md) para que você possa alternar entre as opções de armazenamento.

## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>Mudar todos os discos gerenciados de uma VM entre de uma conta para outra

Este exemplo mostra como converter todos os discos de uma VM para o armazenamento Premium. No entanto, alterando a variável $storageType neste exemplo, você pode converter o tipo de discos da VM em SSD padrão ou HDD padrão. Para usar o Premium Managed disks, sua VM deve usar um [tamanho de VM](../sizes.md) que dá suporte ao armazenamento Premium. Este exemplo também muda para um tamanho que suporte armazenamento premium:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
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
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Alternar discos gerenciados individuais entre Standard e Premium

Para sua carga de trabalho de desenvolvimento/teste, talvez você queira uma combinação de discos Standard e Premium para reduzir os custos. Você pode optar por atualizar somente os discos que precisam de melhor desempenho. Este exemplo mostra como converter um único disco de VM do armazenamento Standard para Premium. No entanto, alterando a variável $storageType neste exemplo, você pode converter o tipo de discos da VM em SSD padrão ou HDD padrão. Para usar o Premium Managed disks, sua VM deve usar um [tamanho de VM](../sizes.md) que dá suporte ao armazenamento Premium. Este exemplo também mostra como mudar para um tamanho que dá suporte ao armazenamento Premium:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
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
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>Mudar os discos gerenciados de um tipo de disco para outro

Siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione a VM na lista de **máquinas virtuais**.
3. Se a VM não estiver parada, selecione **parar** na parte superior do painel **visão geral** da VM e aguarde a interrupção da VM.
4. No painel da VM, selecione **discos** no menu.
5. Selecione o disco que você deseja converter.
6. Selecione **configuração** no menu.
7. Altere o tipo de **conta** do tipo de disco original para o tipo de disco desejado.
8. Selecione **salvar** e feche o painel de disco.

A conversão de tipo de disco é instantânea. Você pode iniciar a VM após a conversão.

## <a name="next-steps"></a>Próximas etapas

Faça uma cópia somente leitura de uma VM usando um [snapshot](snapshot-copy-managed-disk.md).
