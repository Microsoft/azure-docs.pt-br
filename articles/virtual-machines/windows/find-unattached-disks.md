---
title: Localizar e excluir discos gerenciados e não gerenciados do Azure desconectados | Microsoft Docs
description: Como localizar e excluir discos gerenciados e não gerenciados (VHDs/blobs de páginas) do Azure desconectados usando o Azure PowerShell.
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
ms.openlocfilehash: f4cd419e06ba44b16d24f36e937002dce5196622
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763336"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Localizar e excluir discos gerenciados e não gerenciados do Azure desconectados

Quando você exclui uma VM (máquina virtual) no Azure, por padrão, nenhum disco anexado à máquina virtual é excluído. Esse recurso ajuda a evitar a perda de dados devido à exclusão não intencional de VMs. Depois que uma VM for excluída, você continuará a pagar pelos discos desanexados. Este artigo mostra como localizar e excluir discos desanexados e reduzir custos desnecessários.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Discos gerenciados: Localizar e excluir discos desanexados

O script a seguir procura [discos gerenciados](managed-disks-overview.md) desanexados examinando o valor da propriedade **ManagedBy**. Quando um disco gerenciado é anexado a uma VM, a propriedade **ManagedBy** contém a ID de recurso da VM. Quando um disco gerenciado é desanexado, a propriedade **ManagedBy** é nula. O script examina todos os discos gerenciados em uma assinatura do Azure. Quando o script localiza um disco gerenciado com a propriedade **ManagedBy** definida como null, o script determina que o disco está desanexado.

>[!IMPORTANT]
>Primeiro, execute o script definindo a variável **deleteUnattachedDisks** como 0. Essa ação permite localizar e exibir todos os discos gerenciados desanexados.
>
>Depois de examinar todos os discos desanexados, execute o script novamente e defina a variável **deleteUnattachedDisks** como 1. Essa ação permite excluir todos os discos gerenciados desanexados.

```azurepowershell-interactive
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0
$managedDisks = Get-AzDisk
foreach ($md in $managedDisks) {
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){
        if($deleteUnattachedDisks -eq 1){
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"
            $md | Remove-AzDisk -Force
            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "
        }else{
            $md.Id
        }
    }
 }
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Discos não gerenciados: Localizar e excluir discos desanexados

Discos não gerenciados são arquivos VHD armazenados como [blobs de páginas](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) nas [Contas de Armazenamento do Microsoft Azure](../../storage/common/storage-create-storage-account.md). O script a seguir procura discos não gerenciados desanexados (blobs de página) examinando o valor da propriedade **LeaseStatus**. Se um disco não gerenciado estiver conectado a uma máquina virtual, a propriedade**LeaseStatus** estará configurada como **Locked**. Quando um disco não gerenciado é desanexado, a propriedade **LeaseStatus** está definida como **Unlocked**. O script examina todos os discos não gerenciados em todas as contas de armazenamento do Azure em uma assinatura do Azure. Quando o script localiza um disco não gerenciado com uma propriedade **LeaseStatus** propriedade definida como **Unlocked**, o script determina que o disco está desanexado.

>[!IMPORTANT]
>Primeiro, execute o script definindo a variável **deleteUnattachedVHDs** como 0. Essa ação permite localizar e exibir todos os VHDs não gerenciados desanexados.
>
>Depois de examinar todos os discos desanexados, execute o script novamente e defina a variável **deleteUnattachedVHDs** como 1. Essa ação permite excluir todos os VHDs não gerenciados desanexados.

```azurepowershell-interactive
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=0
$storageAccounts = Get-AzStorageAccount
foreach($storageAccount in $storageAccounts){
    $storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value
    $context = New-AzStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey
    $containers = Get-AzStorageContainer -Context $context
    foreach($container in $containers){
        $blobs = Get-AzStorageBlob -Container $container.Name -Context $context
        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
                    if($deleteUnattachedVHDs -eq 1){
                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                        $_ | Remove-AzStorageBlob -Force
                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                    }
                    else{
                        $_.ICloudBlob.Uri.AbsoluteUri
                    }
            }
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [Excluir conta de armazenamento](../../storage/common/storage-create-storage-account.md) e [Identificar discos órfãos usando o PowerShell](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/)
