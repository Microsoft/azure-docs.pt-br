---
title: Esquema de evento armazenamento blob do Grade de Eventos do Azure
description: Descreve as propriedades que são fornecidas para eventos de armazenamento de blob com a Grade de Eventos do Azure
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 401eb660d7e5ddc68bc7422ef9f2e600295d2aea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614902"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Esquema de eventos da Grade de Eventos do Azure para armazenamento de Blob

Este artigo fornece as propriedades e o esquema para eventos de armazenamento de blob. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md).

Para obter uma lista de scripts e tutoriais de amostra, consulte [Storage event source](event-sources.md#storage).

## <a name="available-event-types"></a>Tipos de evento disponíveis

Armazenamento de blob emite os seguintes tipos de evento:

| Tipo de evento | DESCRIÇÃO |
| ---------- | ----------- |
| Microsoft.Storage.BlobCreated | Gerado quando um blob é criado. |
| Microsoft.Storage.BlobDeleted | Gerado quando um blob é deletado. |

## <a name="example-event"></a>Exemplo de evento

O exemplo a seguir mostra o esquema de um blob criado de evento: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
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
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

O esquema para um evento de blob excluído é semelhante: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
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
| ID | string | Identificador exclusivo do evento. |
| data | objeto | Dados de eventos do armazenamento de blob. |
| dataVersion | string | A versão do esquema do objeto de dados. O fornecedor define a versão do esquema. |
| metadataVersion | string | A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Type | DESCRIÇÃO |
| -------- | ---- | ----------- |
| api | string | A operação que disparou o evento. |
| clientRequestId | string | Um valor opaco, gerado pelo cliente, com um limite de caracteres de 1 KB. Quando você tiver habilitado o log de análise de armazenamento, ele será registrado nos logs de análise. |
| requestId | string | O identificador exclusivo da solicitação. Usá-lo para a solicitação de solução de problemas. |
| eTag | string | O valor que você pode usar para executar operações condicionalmente. |
| contentType | string | O tipo de conteúdo especificado para o blob. |
| contentLength | inteiro | O tamanho do blob em bytes. |
| BlobType | string | O tipo de blob. Os valores válidos são "BlockBlob" ou "PageBlob". |
| url | string | O caminho para o blob. |
| sequenciador | string | Um valor controlado pelo usuário que você pode usar para controlar as solicitações. |
| storageDiagnostics | objeto | Informações sobre o Diagnóstico de armazenamento. |
 
## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
* Para obter uma introdução ao trabalhar com eventos de armazenamento de blob, consulte [eventos de armazenamento de Blob de rota - CLI do Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
