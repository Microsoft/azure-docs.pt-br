---
title: Armazenamento de BLOBs do Azure como fonte de grade de eventos
description: Descreve as propriedades que são fornecidas para eventos de armazenamento de blob com a Grade de Eventos do Azure
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 893e86ecf220ceb327eed9c6f95be4c7ed1afb1c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363637"
---
# <a name="azure-blob-storage-as-an-event-grid-source"></a>Armazenamento de BLOBs do Azure como uma fonte de grade de eventos

Este artigo fornece as propriedades e o esquema para eventos de armazenamento de blob. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md). Ele também fornece uma lista de inícios rápidos e tutoriais para usar o armazenamento de BLOBs do Azure como uma fonte de eventos.


>[!NOTE]
> Somente as contas de armazenamento do tipo **StorageV2 (uso geral v2)**, **BlockBlobStorage** e **BlobStorage** oferecem suporte à integração de eventos. O **armazenamento (uso geral v1)** *não oferece* suporte à integração com a grade de eventos.

## <a name="available-event-types"></a>Tipos de evento disponíveis

### <a name="list-of-events-for-blob-rest-apis"></a>Lista de eventos para APIs REST de BLOB

Esses eventos são disparados quando um cliente cria, substitui ou exclui um blob chamando APIs REST de BLOB.

> [!NOTE]
> Os `$logs` `$blobchangefeed` contêineres e não são integrados à grade de eventos, portanto, a atividade nesses contêineres não gerará eventos. Além disso, o uso do ponto de extremidade DFS *`(abfss://URI) `* para contas habilitadas para namespace não hierárquico não gerará eventos, mas o ponto de extremidade de blob *`(wasb:// URI)`* gerará eventos.

 |Nome do evento |Descrição|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Disparado quando um blob é criado ou substituído. <br>Especificamente, esse evento é disparado quando os clientes usam as `PutBlob` `PutBlockList` operações, ou `CopyBlob` que estão disponíveis na API REST do blob.   |
 |**Microsoft.Storage.BlobDeleted** |Disparado quando um blob é excluído. <br>Especificamente, esse evento é disparado quando os clientes chamam a `DeleteBlob` operação que está disponível na API REST do blob. |

> [!NOTE]
> Para o **armazenamento de BLOBs do Azure**, se você quiser garantir que o evento **Microsoft. Storage. BlobCreated** seja disparado somente quando um blob de blocos for completamente confirmado, filtre o evento para as `CopyBlob` chamadas da `PutBlob` `PutBlockList` API REST, e. Essas chamadas de API disparam o evento **Microsoft. Storage. BlobCreated** somente depois que os dados são totalmente confirmados em um blob de blocos. Para saber como criar um filtro, consulte [filtrar eventos para a grade de eventos](./how-to-filter-events.md).

### <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Lista de eventos para as APIs REST do Azure Data Lake Storage Gen 2

Esses eventos serão disparados se você habilitar um namespace hierárquico na conta de armazenamento e os clientes usarem Azure Data Lake Storage Gen2 APIs REST. Para obter mais informações obre Azure Data Lake Storage Gen2, consulte [introdução ao Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

|Nome do evento|Descrição|
|----------|-----------|
|**Microsoft.Storage.BlobCreated** | Disparado quando um blob é criado ou substituído. <br>Especificamente, esse evento é disparado quando os clientes usam as `CreateFile` `FlushWithClose` operações e que estão disponíveis na API REST do Azure data Lake Storage Gen2. |
|**Microsoft.Storage.BlobDeleted** |Disparado quando um blob é excluído. <br>Especificamente, esse evento também é disparado quando os clientes chamam a `DeleteFile` operação que está disponível na API REST do Azure data Lake Storage Gen2. |
|**Microsoft. Storage. BlobRenamed**|Disparado quando um blob é renomeado. <br>Especificamente, esse evento é disparado quando os clientes usam a `RenameFile` operação que está disponível na API REST do Azure data Lake Storage Gen2.|
|**Microsoft. Storage. DirectoryCreated**|Disparado quando um diretório é criado. <br>Especificamente, esse evento é disparado quando os clientes usam a `CreateDirectory` operação que está disponível na API REST do Azure data Lake Storage Gen2.|
|**Microsoft. Storage. DirectoryRenamed**|Disparado quando um diretório é renomeado. <br>Especificamente, esse evento é disparado quando os clientes usam a `RenameDirectory` operação que está disponível na API REST do Azure data Lake Storage Gen2.|
|**Microsoft. Storage. DirectoryDeleted**|Disparado quando um diretório é excluído. <br>Especificamente, esse evento é disparado quando os clientes usam a `DeleteDirectory` operação que está disponível na API REST do Azure data Lake Storage Gen2.|

> [!NOTE]
> Por **Azure data Lake Storage Gen2**, se você quiser garantir que o evento **Microsoft. Storage. BlobCreated** seja disparado somente quando um blob de blocos for completamente confirmado, filtre o evento para a `FlushWithClose` chamada à API REST. Essa chamada à API dispara o evento **Microsoft. Storage. BlobCreated** somente depois que os dados são totalmente confirmados em um blob de blocos. Para saber como criar um filtro, consulte [filtrar eventos para a grade de eventos](./how-to-filter-events.md).

## <a name="example-event"></a>Exemplo de evento
Quando um evento é disparado, o serviço de Grade de Eventos envia dados sobre esse evento para o ponto de extremidade de assinatura. Esta seção contém um exemplo de como os dados seriam para cada evento de armazenamento de BLOBs.

# <a name="event-grid-event-schema"></a>[Esquema de eventos da Grade de Eventos](#tab/event-grid-event-schema)

### <a name="microsoftstorageblobcreated-event"></a>Evento Microsoft. Storage. BlobCreated

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Evento Microsoft. Storage. BlobCreated (Data Lake Storage Gen2)

Se a conta de armazenamento de BLOBs tiver um namespace hierárquico, os dados se assemelharão ao exemplo anterior com uma exceção dessas alterações:

* A `dataVersion` chave é definida com um valor de `2` .

* A `data.api` chave é definida como a cadeia de caracteres `CreateFile` ou `FlushWithClose` .

* A `contentOffset` chave está incluída no conjunto de dados.

> [!NOTE]
> Se os aplicativos usarem a `PutBlockList` operação para carregar um novo BLOB na conta, os dados não conterão essas alterações.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Evento Microsoft. Storage. BlobDeleted

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Evento Microsoft. Storage. BlobDeleted (Data Lake Storage Gen2)

Se a conta de armazenamento de BLOBs tiver um namespace hierárquico, os dados se assemelharão ao exemplo anterior com uma exceção dessas alterações:

* A `dataVersion` chave é definida com um valor de `2` .

* A `data.api` chave é definida como a cadeia de caracteres `DeleteFile` .

* A `url` chave contém o caminho `dfs.core.windows.net` .

> [!NOTE]
> Se os aplicativos usarem a `DeleteBlob` operação para excluir um blob da conta, os dados não conterão essas alterações.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Evento Microsoft. Storage. BlobRenamed

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Evento Microsoft. Storage. DirectoryCreated

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Evento Microsoft. Storage. DirectoryRenamed

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Evento Microsoft. Storage. DirectoryDeleted

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[Esquema de evento de nuvem](#tab/cloud-event-schema)

### <a name="microsoftstorageblobcreated-event"></a>Evento Microsoft. Storage. BlobCreated

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "type": "Microsoft.Storage.BlobCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Evento Microsoft. Storage. BlobCreated (Data Lake Storage Gen2)

Se a conta de armazenamento de BLOBs tiver um namespace hierárquico, os dados se assemelharão ao exemplo anterior com uma exceção dessas alterações:

* A `data.api` chave é definida como a cadeia de caracteres `CreateFile` ou `FlushWithClose` .
* A `contentOffset` chave está incluída no conjunto de dados.

> [!NOTE]
> Se os aplicativos usarem a `PutBlockList` operação para carregar um novo BLOB na conta, os dados não conterão essas alterações.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "type": "Microsoft.Storage.BlobCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Evento Microsoft. Storage. BlobDeleted

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "type": "Microsoft.Storage.BlobDeleted",
  "time": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Evento Microsoft. Storage. BlobDeleted (Data Lake Storage Gen2)

Se a conta de armazenamento de BLOBs tiver um namespace hierárquico, os dados se assemelharão ao exemplo anterior com uma exceção dessas alterações:


* A `data.api` chave é definida como a cadeia de caracteres `DeleteFile` .
* A `url` chave contém o caminho `dfs.core.windows.net` .

> [!NOTE]
> Se os aplicativos usarem a `DeleteBlob` operação para excluir um blob da conta, os dados não conterão essas alterações.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "type": "Microsoft.Storage.BlobDeleted",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Evento Microsoft. Storage. BlobRenamed

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "type": "Microsoft.Storage.BlobRenamed",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Evento Microsoft. Storage. DirectoryCreated

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "type": "Microsoft.Storage.DirectoryCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Evento Microsoft. Storage. DirectoryRenamed

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "type": "Microsoft.Storage.DirectoryRenamed",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Evento Microsoft. Storage. DirectoryDeleted

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "type": "Microsoft.Storage.DirectoryDeleted",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

---


## <a name="event-properties"></a>Propriedades do evento

# <a name="event-grid-event-schema"></a>[Esquema de eventos da Grade de Eventos](#tab/event-grid-event-schema)

Um evento tem os seguintes dados de nível superior:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| `topic` | string | Caminho de recurso completo para a origem do evento. Este campo não é gravável. A Grade de Eventos fornece esse valor. |
| `subject` | string | Caminho definido pelo publicador para o assunto do evento. |
| `eventType` | string | Um dos tipos de evento registrados para a origem do evento. |
| `eventTime` | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| `id` | string | Identificador exclusivo do evento. |
| `data` | objeto | Dados de eventos do armazenamento de blob. |
| `dataVersion` | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| `metadataVersion` | string | A versão do esquema dos metadados do evento. Grade de Eventos define o esquema de propriedades de nível superior. A Grade de Eventos fornece esse valor. |

# <a name="cloud-event-schema"></a>[Esquema de evento de nuvem](#tab/cloud-event-schema)

Um evento tem os seguintes dados de nível superior:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| `source` | string | Caminho de recurso completo para a origem do evento. Este campo não é gravável. A Grade de Eventos fornece esse valor. |
| `subject` | string | Caminho definido pelo publicador para o assunto do evento. |
| `type` | string | Um dos tipos de evento registrados para a origem do evento. |
| `time` | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| `id` | string | Identificador exclusivo do evento. |
| `data` | objeto | Dados de eventos do armazenamento de blob. |
| `specversion` | string | Versão de especificação de esquema CloudEvents. |

---

O objeto de dados tem as seguintes propriedades:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| `api` | string | A operação que disparou o evento. |
| `clientRequestId` | string | uma ID de solicitação fornecida pelo cliente para a operação da API de armazenamento. Essa ID pode ser usada para correlacionar os logs de diagnóstico do armazenamento do Azure usando o campo "Client-Request-ID" nos logs e pode ser fornecida em solicitações de cliente usando o cabeçalho "x-MS-Client-Request-ID". Consulte [Formato de Log](/rest/api/storageservices/storage-analytics-log-format). |
| `requestId` | string | ID da solicitação gerada pelo serviço para a operação da API de armazenamento. Pode ser usada para correlacionar com os logs de diagnóstico do Armazenamento do Azure usando o campo "request-id-header" nos logs, e retornada pela inicialização da chamada á API no cabeçalho 'x-ms-request-id'. Consulte [Formato de Log](/rest/api/storageservices/storage-analytics-log-format). |
| `eTag` | string | O valor que você pode usar para executar operações condicionalmente. |
| `contentType` | string | O tipo de conteúdo especificado para o blob. |
| `contentLength` | inteiro | O tamanho do blob em bytes. |
| `blobType` | string | O tipo de blob. Os valores válidos são "BlockBlob" ou "PageBlob". |
| `contentOffset` | número | O deslocamento em bytes de uma operação de gravação realizada no ponto em que o aplicativo de gatilho de evento concluiu a gravação no arquivo. <br>Aparece somente para eventos disparados em contas de armazenamento de BLOBs que têm um namespace hierárquico.|
| `destinationUrl` |string | A URL do arquivo que existirá após a conclusão da operação. Por exemplo, se um arquivo for renomeado, a `destinationUrl` Propriedade conterá a URL do novo nome de arquivo. <br>Aparece somente para eventos disparados em contas de armazenamento de BLOBs que têm um namespace hierárquico.|
| `sourceUrl` |string | A URL do arquivo que existe antes da operação ser concluída. Por exemplo, se um arquivo for renomeado, o `sourceUrl` contém a URL do nome do arquivo original antes da operação de renomeação. <br>Aparece somente para eventos disparados em contas de armazenamento de BLOBs que têm um namespace hierárquico. |
| `url` | string | O caminho para o blob. <br>Se o cliente usar uma API REST de BLOB, a URL terá essa estrutura: `<storage-account-name>.blob.core.windows.net\<container-name>\<file-name>` . <br>Se o cliente usar uma API REST Data Lake Storage, a URL terá essa estrutura: `<storage-account-name>.dfs.core.windows.net/<file-system-name>/<file-name>` . |
| `recursive` | string | `True` para executar a operação em todos os diretórios filho; caso contrário `False` . <br>Aparece somente para eventos disparados em contas de armazenamento de BLOBs que têm um namespace hierárquico. |
| `sequencer` | string | Um valor de cadeia de caracteres opaca que representa a sequência lógica de eventos para qualquer nome específico de blob.  Os usuários podem usar a comparação de cadeia de caracteres padrão para entender a sequência relativa dos dois eventos no mesmo nome de blob. |
| `storageDiagnostics` | object | Dados de diagnóstico ocasionalmente incluídos pelo serviço de Armazenamento do Azure. Quando presente, deve ser ignorado pelos consumidores de evento. |

## <a name="tutorials-and-how-tos"></a>Tutoriais e instruções
|Título  |Descrição  |
|---------|---------|
| [Início Rápido: encaminhar eventos de armazenamento de Blob para um ponto de extremidade da Web personalizado com a CLI do Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar a CLI do Azure para enviar eventos de armazenamento de blob para um WebHook. |
| [Início Rápido: encaminhar eventos de armazenamento de Blobs para um ponto de extremidade da Web personalizado com PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar o Azure PowerShell para enviar eventos de armazenamento de blobs para um WebHook. |
| [Início Rápido: criar e rotear eventos personalizados de armazenamento de Blobs com o portal do Azure](blob-event-quickstart-portal.md) | Mostra como usar o portal para enviar eventos de armazenamento de blob para um WebHook. |
| [CLI do Azure: assinar eventos para uma conta de armazenamento de Blob](./scripts/event-grid-cli-blob.md) | Script de exemplo que assina evento para uma conta de armazenamento de Blob. Ele envia o evento para um WebHook. |
| [PowerShell: assinar eventos para uma conta de armazenamento de Blob](./scripts/event-grid-powershell-blob.md) | Script de exemplo que assina evento para uma conta de armazenamento de Blob. Ele envia o evento para um WebHook. |
| [Modelo do Gerenciador de Recursos: criar o armazenamento de Blob e assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Implanta uma conta de armazenamento de blobs do Azure e assina eventos para essa conta de armazenamento. Envia eventos para um WebHook. |
| [Visão geral: reagir aos eventos de armazenamento de Blobs](../storage/blobs/storage-blob-event-overview.md) | Visão geral da integração do Armazenamento de Blobs com a Grade de Eventos. |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
* Para obter uma introdução ao trabalhar com eventos de armazenamento de blob, consulte [eventos de armazenamento de Blob de rota - CLI do Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
