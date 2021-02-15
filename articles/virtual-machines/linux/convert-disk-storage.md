---
title: Converter o armazenamento de discos gerenciados entre diferentes tipos de disco usando CLI do Azure
description: Como converter o Azure Managed disks entre os diferentes tipos de discos usando o CLI do Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.subservice: disks
ms.openlocfilehash: ec6d5b10b4c2336ba7fd221b43a7dbf43ed5cee1
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516814"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Converter o armazenamento do Azure Managed disks de Standard para Premium ou Premium para Standard

Há quatro tipos de disco de discos gerenciados do Azure: ultra discos do Azure, SSD Premium, SSD padrão e HDD padrão. Você pode alternar entre SSD Premium, SSD padrão e HDD padrão com base em suas necessidades de desempenho. Você ainda não é capaz de mudar de ou para um ultra Disk, você deve implantar um novo.

Não há suporte para essa funcionalidade em discos não gerenciados. Mas você pode [converter facilmente um disco não gerenciado em um disco gerenciado](convert-unmanaged-to-managed-disks.md) para poder alternar entre tipos de disco.

Este artigo mostra como converter discos gerenciados de um tipo de disco para outro usando o CLI do Azure. Para instalar ou atualizar a ferramenta, consulte [instalar CLI do Azure](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

* A conversão de disco requer uma reinicialização da VM (máquina virtual), portanto, Agende a migração do armazenamento em disco durante uma janela de manutenção pré-existente.
* Para discos não gerenciados, primeiro [converta em discos gerenciados](convert-unmanaged-to-managed-disks.md) para que você possa alternar entre as opções de armazenamento.


## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>Mudar todos os discos gerenciados de uma VM entre de uma conta para outra

Este exemplo mostra como converter todos os discos de uma VM para o armazenamento Premium. No entanto, alterando a variável de SKU neste exemplo, você pode converter o tipo de discos da VM em SSD padrão ou HDD padrão. Observe que, para usar o Premium Managed disks, sua VM deve usar um [tamanho de VM](../sizes.md) que ofereça suporte ao armazenamento Premium. Este exemplo também muda para um tamanho que dá suporte ao armazenamento Premium.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-from-one-disk-type-to-another"></a>Alternar discos gerenciados individuais de um tipo de disco para outro

Para sua carga de trabalho de desenvolvimento/teste, talvez você queira ter uma combinação de discos Standard e Premium para reduzir os custos. Você pode optar por atualizar somente os discos que precisam de melhor desempenho. Este exemplo mostra como converter um único disco de VM do armazenamento Standard para Premium. No entanto, alterando a variável de SKU neste exemplo, você pode converter o tipo de discos da VM em SSD padrão ou HDD padrão. Para usar o Premium Managed disks, sua VM deve usar um [tamanho de VM](../sizes.md) que dá suporte ao armazenamento Premium. Este exemplo também muda para um tamanho que dá suporte ao armazenamento Premium.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
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

A atualização do tipo de disco é instantânea. Você pode reiniciar a VM após a conversão.

## <a name="next-steps"></a>Próximas etapas

Faça uma cópia somente leitura de uma VM usando [instantâneos](snapshot-copy-managed-disk.md).