---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 895a81df858e51a266cd87c96a161695a4bf2cc1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95992861"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Regiões com suporte
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

Você pode usar Azure PowerShell para criar um instantâneo incremental. Você precisará da versão mais recente do Azure PowerShell, o seguinte comando o instalará ou atualizará a instalação existente para o mais recente:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Depois de instalado, faça logon na sua sessão do PowerShell com `Connect-AzAccount` .

Para criar um instantâneo incremental com Azure PowerShell, defina a configuração com [New-AzSnapShotConfig](/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) com o `-Incremental` parâmetro e, em seguida, passe-o como uma variável para [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) por meio do `-Snapshot` parâmetro.

```PowerShell
$diskName = "yourDiskNameHere>"
$resourceGroupName = "yourResourceGroupNameHere"
$snapshotName = "yourDesiredSnapshotNameHere"

# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName $diskName -ResourceGroupName $resourceGroupName

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName -Snapshot $snapshotConfig 
```

Você pode identificar instantâneos incrementais do mesmo disco com o `SourceResourceId` e as `SourceUniqueId` Propriedades de instantâneos. `SourceResourceId` é a ID de recurso Azure Resource Manager do disco pai. `SourceUniqueId` é o valor herdado da `UniqueId` Propriedade do disco. Se você for excluir um disco e, em seguida, criar um novo disco com o mesmo nome, o valor da `UniqueId` propriedade será alterado.

Você pode usar `SourceResourceId` e `SourceUniqueId` para criar uma lista de todos os instantâneos associados a um disco específico. Substitua `<yourResourceGroupNameHere>` pelo seu valor e, em seguida, você pode usar o exemplo a seguir para listar seus instantâneos incrementais existentes:

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName $resourceGroupName

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="resource-manager-template"></a>Modelo do Resource Manager

Você também pode usar modelos de Azure Resource Manager para criar um instantâneo incremental. Você precisará certificar-se de que apiVersion está definido como **2019-03-01** e que a propriedade incremental também é definida como true. O trecho a seguir é um exemplo de como criar um instantâneo incremental com modelos do Resource Manager:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Se você quiser ver um exemplo de código que demonstra a capacidade diferencial de instantâneos incrementais, usando o .NET, consulte [copiar backups de Managed disks do Azure para outra região com capacidade diferencial de instantâneos incrementais](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).