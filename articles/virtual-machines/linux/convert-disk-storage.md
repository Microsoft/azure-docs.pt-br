---
title: "Converter o armazenamento de Managed Disks do Azure de padrão em premium, e vice-versa | Microsoft Docs"
description: "Como converter o armazenamento de Managed Disks do Azure de padrão em premium, e vice-versa, usando a CLI do Azure."
services: virtual-machines-linux
documentationcenter: 
author: ramankum
manager: kavithag
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: 0380b4aaa23b4aaba4c67d05e2d62f3ef41d6a32
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Converter o armazenamento de Managed Disks do Azure de padrão em premium, e vice-versa

Os Managed Disks oferecem duas opções de armazenamento: [Premium](../../storage/storage-premium-storage.md) (baseado em SSD) e [Padrão](../../storage/storage-standard-storage.md) (baseado em HDD). O recurso permite alternar facilmente entre as duas opções com mínimo tempo de inatividade de acordo com suas necessidades de desempenho. Ele não está disponível para discos não gerenciados. Porém, você pode [convertê-los em Managed Disks](convert-unmanaged-to-managed-disks.md) facilmente para alternar com facilidade entre as duas opções.

Este artigo mostra como converter Managed Disks de padrão em premium, e vice-versa, usando a CLI do Azure. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure 2.0](/cli/azure/install-azure-cli.md). 

## <a name="before-you-begin"></a>Antes de começar

* Como a conversão requer uma reinicialização da VM, programe a migração do armazenamento de discos durante uma janela de manutenção preexistente. 
* Se você estiver usando discos não gerenciados, primeiramente [converta-os em Managed Disks](convert-unmanaged-to-managed-disks.md) a fim de usar este artigo para alternar entre as duas opções de armazenamento. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Converter todos os Managed Disks de uma VM de padrão em premium, e vice-versa

No exemplo a seguir, mostramos como alternar todos os discos de uma VM de armazenamento padrão para premium. Para usar Managed Disks Premium, sua VM deve usar um [tamanho da VM](sizes.md) que dá suporte a armazenamento Premium. Este exemplo também pode mudar para um tamanho que dá suporte a armazenamento Premium.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Converter um Managed Disk de padrão em premium, e vice-versa

Para sua carga de trabalho de desenvolvimento/teste, convém ter uma combinação de discos padrão e premium para reduzir os custos. Isso é possível atualizando para o armazenamento premium, somente os discos que requerem melhor desempenho. No exemplo a seguir, mostramos como alternar um único disco de uma VM de armazenamento padrão para premium, e vice-versa. Para usar Managed Disks Premium, sua VM deve usar um [tamanho da VM](sizes.md) que dá suporte a armazenamento Premium. Este exemplo também pode mudar para um tamanho que dá suporte a armazenamento Premium.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="next-steps"></a>Próximas etapas

Faça uma cópia somente leitura de uma VM usando [instantâneos](snapshot-copy-managed-disk.md).

