---
title: Esquema de evento armazenamento blob do Grade de Eventos do Azure
description: Descreve as propriedades que são fornecidas para eventos de armazenamento de blob com a Grade de Eventos do Azure
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: bed6c3f1efcb2d0ef34e827ddb2b521f8c038940
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445779"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Esquema de eventos da Grade de Eventos do Azure para armazenamento de Blob

Este artigo fornece as propriedades e o esquema para eventos de armazenamento de blob. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md).

Para obter uma lista de scripts e tutoriais de amostra, consulte [Storage event source](event-sources.md#storage).

## <a name="list-of-events-for-blob-rest-apis"></a>Lista de eventos para APIs REST do Blob

Esses eventos são disparados quando um cliente cria, substitui ou exclui um blob ao chamar as APIs de REST do Blob.

 |Nome do evento |DESCRIÇÃO|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Acionado quando um blob é criado ou substituído. <br>Especificamente, esse evento é acionado quando os clientes usam o `PutBlob`, `PutBlockList`, ou `CopyBlob` operações que estão disponíveis na API REST do Blob.   |
 |**Microsoft.Storage.BlobDeleted** |Acionado quando um blob é excluído. <br>Especificamente, esse evento é acionado quando os clientes chamam o `DeleteBlob` operação que está disponível na API REST do Blob. |

> [!NOTE]
> Se você quiser garantir que o **Microsoft.Storage.BlobCreated** evento é disparado apenas quando um Blob de blocos é completamente confirmado, filtrar o evento para o `CopyBlob`, `PutBlob`, e `PutBlockList` chamadas à API REST. Esses disparador de chamadas de API do **Microsoft.Storage.BlobCreated** evento somente depois que dados são totalmente confirmados ao Blob de bloco. Para saber como criar um filtro, consulte [filtrar eventos de grade de eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Lista de eventos para APIs de REST do Azure Data Lake Storage Gen 2

Esses eventos são disparados se você habilita um namespace hierárquico na conta de armazenamento e os clientes chamam APIs de REST do Azure Data Lake armazenamento Gen2.

> [!NOTE]
> Esses eventos estão em visualização pública e eles estão disponíveis apenas a **Oeste dos EUA 2** e **Centro-Oeste dos EUA** regiões.

 |Nome do evento|DESCRIÇÃO|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** | Acionado quando um blob é criado ou substituído. <br>Especificamente, esse evento é acionado quando os clientes usam o `CreateFile` e `FlushWithClose` operações que estão disponíveis na API de REST do Azure Data Lake armazenamento Gen2. |
 |**Microsoft.Storage.BlobDeleted** |Acionado quando um blob é excluído. <br>Especificamente, esse evento também é acionado quando os clientes chamam o `DeleteFile` operação que está disponível na API de REST do Azure Data Lake armazenamento Gen2. |
 |**Microsoft.Storage.BlobRenamed**|Acionado quando um blob é renomeado. <br>Especificamente, esse evento é acionado quando os clientes usam o `RenameFile` operação que está disponível na API de REST do Azure Data Lake armazenamento Gen2.|
 |**Microsoft.Storage.DirectoryCreated**|Acionado quando um diretório é criado. <br>Especificamente, esse evento é acionado quando os clientes usam o `CreateDirectory` operação que está disponível na API de REST do Azure Data Lake armazenamento Gen2.|
 |**Microsoft.Storage.DirectoryRenamed**|Acionado quando um diretório é renomeado. <br>Especificamente, esse evento é acionado quando os clientes usam o `RenameDirectory` operação que está disponível na API de REST do Azure Data Lake armazenamento Gen2.|
 |**Microsoft.Storage.DirectoryDeleted**|Acionado quando um diretório é excluído. <br>Especificamente, esse evento é acionado quando os clientes usam o `DeleteDirectory` operação que está disponível na API de REST do Azure Data Lake armazenamento Gen2.|

> [!NOTE]
> Se você quiser garantir que o **Microsoft.Storage.BlobCreated** evento é disparado apenas quando um Blob de blocos é completamente confirmado, filtrar o evento para o `FlushWithClose` chamada à API REST. Gatilhos de chamar essa API a **Microsoft.Storage.BlobCreated** evento somente depois que dados são totalmente confirmados ao Blob de bloco. Para saber como criar um filtro, consulte [filtrar eventos de grade de eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>O conteúdo de uma resposta a eventos

Quando um evento é disparado, o serviço de grade de eventos envia dados sobre esse evento para o ponto de extremidade de assinatura.

Esta seção contém um exemplo da aparência que os dados para cada evento de armazenamento de blob.

### <a name="microsoftstorageblobcreated-event"></a>Evento Microsoft.Storage.BlobCreated

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
    "eTag": "0x8D4BCC2E4835CD0",
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

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Evento Microsoft.Storage.BlobCreated (Data Lake armazenamento Gen2)

Se a conta de armazenamento de blob tem um namespace hierárquico, os dados é semelhantes ao exemplo anterior, exceto a estas alterações:

* O `dataVersion` chave é definida como um valor de `2`.

* O `data.api` chave é definida como a cadeia de caracteres `CreateFile` ou `FlushWithClose`.

* O `contentOffset` chave está incluída no conjunto de dados.

> [!NOTE]
> Se os aplicativos usarem o `PutBlockList` operação para carregar um novo blob na conta, os dados não contêm essas alterações.

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
    "eTag": "0x8D4BCC2E4835CD0",
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

### <a name="microsoftstorageblobdeleted-event"></a>Evento Microsoft.Storage.BlobDeleted

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

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Evento Microsoft.Storage.BlobDeleted (Data Lake armazenamento Gen2)

Se a conta de armazenamento de blob tem um namespace hierárquico, os dados é semelhantes ao exemplo anterior, exceto a estas alterações:

* O `dataVersion` chave é definida como um valor de `2`.

* O `data.api` chave é definida como a cadeia de caracteres `DeleteFile`.

* O `url` chave contém o caminho `dfs.core.windows.net`.

> [!NOTE]
> Se os aplicativos usarem o `DeleteBlob` operação para excluir um blob da conta, os dados não contêm essas alterações.

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

### <a name="microsoftstorageblobrenamed-event"></a>Evento Microsoft.Storage.BlobRenamed

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

### <a name="microsoftstoragedirectorycreated-event"></a>Evento Microsoft.Storage.DirectoryCreated

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

### <a name="microsoftstoragedirectoryrenamed-event"></a>Evento Microsoft.Storage.DirectoryRenamed

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

### <a name="microsoftstoragedirectorydeleted-event"></a>Evento Microsoft.Storage.DirectoryDeleted

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

## <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Type | DESCRIÇÃO |
| -------- | ---- | ----------- |
| topic | string | Caminho de recurso completo para a origem do evento. Esse campo não é gravável. Grade de Eventos fornece esse valor. |
| subject | string | Caminho definido pelo fornecedor para o assunto do evento. |
| eventType | string | Um dos tipos de evento registrados para a origem do evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| id | string | Identificador exclusivo do evento. |
| data | objeto | Dados de eventos do armazenamento de blob. |
| dataVersion | string | A versão do esquema do objeto de dados. O fornecedor define a versão do esquema. |
| metadataVersion | string | A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Type | DESCRIÇÃO |
| -------- | ---- | ----------- |
| api | string | A operação que disparou o evento. |
| clientRequestId | string | uma id de solicitação fornecida pelo cliente para a operação da API de armazenamento. Essa id pode ser usada para correlacionar com logs de diagnóstico do armazenamento do Azure usando o campo "client-request-id" nos logs e pode ser fornecida em solicitações de cliente usando o cabeçalho "x-ms-client-request-id". Consulte [Formato de Log](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | Id da solicitação gerada pelo serviço para a operação da API de armazenamento. Pode ser usada para correlacionar com os logs de diagnóstico do Armazenamento do Azure usando o campo "request-id-header" nos logs, e retornada pela inicialização da chamada á API no cabeçalho 'x-ms-request-id'. Consulte [Formato de Log](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | string | O valor que você pode usar para executar operações condicionalmente. |
| contentType | string | O tipo de conteúdo especificado para o blob. |
| contentLength | inteiro | O tamanho do blob em bytes. |
| blobType | string | O tipo de blob. Os valores válidos são "BlockBlob" ou "PageBlob". |
| contentOffset | número | O deslocamento em bytes de uma operação de gravação executadas no ponto em que o aplicativo aciona o evento completou a gravação no arquivo. <br>Aparece apenas eventos disparados em contas de armazenamento de BLOBs que têm um namespace hierárquico.|
| destinationUrl |string | A url do arquivo que existirá após a conclusão da operação. Por exemplo, se um arquivo for renomeado, o `destinationUrl` propriedade contém a url do novo nome de arquivo. <br>Aparece apenas eventos disparados em contas de armazenamento de BLOBs que têm um namespace hierárquico.|
| sourceUrl |string | A url do arquivo que existe antes da operação. Por exemplo, se um arquivo for renomeado, o `sourceUrl` contém a url do nome do arquivo original, antes da operação de renomeação. <br>Aparece apenas eventos disparados em contas de armazenamento de BLOBs que têm um namespace hierárquico. |
| url | string | O caminho para o blob. <br>Se o cliente usa uma API REST do Blob e, em seguida, a url tem esta estrutura:  *\<storage-account-name\>.blob.core.windows.net/\<nome do contêiner\>/\<nome de arquivo \>* . <br>Se o cliente usa uma API de REST de armazenamento do Data Lake e, em seguida, a url tem esta estrutura:  *\<storage-account-name\>.dfs.core.windows.net/\<nome do sistema de arquivos\> / \<nome de arquivo\>* .
|
| recursive| string| `True` para executar a operação em todos os diretórios filho; Caso contrário, `False`. <br>Aparece apenas eventos disparados em contas de armazenamento de BLOBs que têm um namespace hierárquico. |
| sequencer | string | Um valor de cadeia de caracteres opaca que representa a sequência lógica de eventos para qualquer nome específico de blob.  Os usuários podem usar a comparação de cadeia de caracteres padrão para entender a sequência relativa dos dois eventos no mesmo nome de blob. |
| storageDiagnostics | object | Dados de diagnóstico ocasionalmente incluídos pelo serviço de Armazenamento do Azure. Quando presente, deve ser ignorado pelos consumidores de evento. |

|Propriedade|Type|DESCRIÇÃO|
 |-------------------|------------------------|-----------------------------------------------------------------------|

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
* Para obter uma introdução ao trabalhar com eventos de armazenamento de blob, consulte [eventos de armazenamento de Blob de rota - CLI do Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
