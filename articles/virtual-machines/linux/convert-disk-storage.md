---
title: Converter o Azure managed disks o armazenamento Standard para Premium para Standard ou Premium | Microsoft Docs
description: Como converter o Azure managed disks armazenamento Standard para Premium para Standard ou Premium usando a CLI do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 2e7eb455a53abbe2df6ff72f091a599665732429
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765731"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Converter o Azure managed disks o armazenamento Standard para Premium para Standard ou Premium

Há quatro [tipos de disco](disks-types.md) para o Azure de discos gerenciados: Standard, Premium SSD, SSD Standard e armazenamento em disco do Azure de Ultra HD. Você pode alternar facilmente entre SSD Premium, padrão de SSD e HDD padrão com base em suas necessidades de desempenho com pouco tempo de inatividade. Não há suporte para essa funcionalidade para Ultra armazenamento em disco ou discos não gerenciados. Mas você pode facilmente [convert não gerenciados em managed disks](convert-unmanaged-to-managed-disks.md) para alternar entre os tipos de disco.

Este artigo mostra como converter discos gerenciados Standard para Premium para Standard ou Premium usando a CLI do Azure. Para instalar ou atualizar a ferramenta, consulte [instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

* A conversão do disco requer uma reinicialização da máquina virtual (VM), programe então a migração de seu armazenamento em disco durante uma janela de manutenção preexistente.
* Para discos não gerenciados, primeiro [converter em discos gerenciados](convert-unmanaged-to-managed-disks.md) assim você pode alternar entre as opções de armazenamento.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Alternar todos os discos gerenciados de uma VM entre Standard e Premium

Este exemplo mostra como converter todos os discos da VM a partir de padrão para o armazenamento Premium ou Premium para o armazenamento Standard. Para usar o Managed Disks Premium, sua VM deve usar um [tamanho VM](sizes.md) que dá suporte a armazenamento Premium. Este exemplo também pode mudar para um tamanho que dá suporte a armazenamento Premium.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
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
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Alternar os discos gerenciados individuais entre Standard e Premium

Para sua carga de trabalho de desenvolvimento/teste, convém ter uma combinação de discos Standard e Premium para reduzir os custos. Você pode optar por atualizar somente os discos que precisam de um melhor desempenho. Este exemplo mostra como converter um único disco VM de padrão para o armazenamento Premium ou do Premium para o armazenamento Standard. Para usar o Managed Disks Premium, sua VM deve usar um [tamanho VM](sizes.md) que dá suporte a armazenamento Premium. Este exemplo também pode mudar para um tamanho que dá suporte a armazenamento Premium.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
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

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Alternar entre o padrão de HDD e SSD Standard os discos gerenciados

Este exemplo mostra como converter um único disco VM de padrão de HDD para SSD Standard ou de SSD Standard para o disco rígido padrão.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM ID 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Alternar discos gerenciados entre Standard e Premium no portal do Azure

Siga estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione a VM na lista de **máquinas virtuais**.
3. Se a VM não está parada, selecione **pare** na parte superior da VM **visão geral** painel e aguarde até que a VM parar.
4. No painel da VM, selecione **discos** no menu.
5. Selecione o disco que você deseja converter.
6. Selecione **configuração** no menu.
7. Alterar o **tipo de conta** de **HDD padrão** para **SSD Premium** ou de **SSD Premium** para **padrão HDD**.
8. Selecione **salvar**e feche o painel de disco.

A atualização do tipo de disco é instantânea. Você pode reiniciar a VM após a conversão.

## <a name="next-steps"></a>Próximas etapas

Faça uma cópia somente leitura de uma VM por meio [instantâneos](snapshot-copy-managed-disk.md).
