---
title: CLI do Azure-localizar e excluir discos gerenciados e não gerenciados desanexados
description: Como localizar e excluir discos gerenciados e não gerenciados (VHDs/Blobs de páginas) do Azure desconectados, usando a CLI do Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0c3e8bb2ff6f3313e851a4253a95a5ad923a8f70
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016209"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks-using-the-azure-cli"></a>Localizar e excluir discos gerenciados e não gerenciados desanexados do Azure usando a CLI do Azure
Quando você exclui uma VM (máquina virtual) no Azure, por padrão, nenhum disco anexado à máquina virtual é excluído. Esse recurso ajuda a evitar a perda de dados devido à exclusão não intencional de VMs. Depois que uma VM for excluída, você continuará a pagar pelos discos desanexados. Este artigo mostra como localizar e excluir discos desanexados e reduzir custos desnecessários. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Discos gerenciados: Localizar e excluir discos desanexados 

O script a seguir procura [discos gerenciados](../managed-disks-overview.md) desanexados examinando o valor da propriedade **ManagedBy**. Quando um disco gerenciado é anexado a uma VM, a propriedade **ManagedBy** contém a ID de recurso da VM. Quando um disco gerenciado é desanexado, a propriedade **ManagedBy** é nula. O script examina todos os discos gerenciados em uma assinatura do Azure. Quando o script localiza um disco gerenciado com a propriedade **ManagedBy** definida como null, o script determina que o disco está desanexado.

>[!IMPORTANT]
>Primeiro, execute o script definindo a variável **deleteUnattachedDisks** como 0. Essa ação permite localizar e exibir todos os discos gerenciados desanexados.
>
>Depois de examinar todos os discos desanexados, execute o script novamente e defina a variável **deleteUnattachedDisks** como 1. Essa ação permite excluir todos os discos gerenciados desanexados.
>

```azurecli
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0
unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Discos não gerenciados: Localizar e excluir discos desanexados 

Discos não gerenciados são arquivos VHD armazenados como [blobs de páginas](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) nas [Contas de Armazenamento do Microsoft Azure](../../storage/common/storage-account-overview.md). O script a seguir procura discos não gerenciados desanexados (blobs de página) examinando o valor da propriedade **LeaseStatus**. Se um disco não gerenciado estiver conectado a uma máquina virtual, a propriedade **LeaseStatus** estará configurada como **Locked**. Quando um disco não gerenciado é desanexado, a propriedade **LeaseStatus** está definida como **Unlocked**. O script examina todos os discos não gerenciados em todas as contas de armazenamento do Azure em uma assinatura do Azure. Quando o script localiza um disco não gerenciado com uma propriedade **LeaseStatus** propriedade definida como **Unlocked**, o script determina que o disco está desanexado.

>[!IMPORTANT]
>Primeiro, execute o script definindo a variável **deleteUnattachedVHDs** como 0. Essa ação permite localizar e exibir todos os VHDs não gerenciados desanexados.
>
>Depois de examinar todos os discos desanexados, execute o script novamente e defina a variável **deleteUnattachedVHDs** como 1. Essa ação permite excluir todos os VHDs não gerenciados desanexados.
>

```azurecli
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=0
storageAccountIds=$(az storage account list --query [].[id] -o tsv)
for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, confira [Excluir uma conta de armazenamento](../../storage/common/storage-account-create.md#delete-a-storage-account).