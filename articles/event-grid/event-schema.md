---
title: Esquema de eventos da Grade de Eventos do Azure
description: "Descreve as propriedades que são fornecidas para eventos com a Grade de Eventos do Azure"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 09/18/2017
ms.author: babanisa
ms.openlocfilehash: a61357b6ba75566e0ad4d3300cc602333ece0563
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-event-grid-event-schema"></a>Esquema de eventos da Grade de Eventos do Azure

Este artigo fornece as propriedades e o esquema de eventos. Os eventos consistem em um conjunto de cinco propriedades de cadeia de caracteres obrigatórias e um objeto data obrigatório. As propriedades são comuns a todos os eventos de qualquer fornecedor. O objeto data contém propriedades que são específicas a cada fornecedor. Para tópicos do sistema, essas propriedades são específicas ao provedor de recursos, como Armazenamento do Azure ou Hub de Eventos do Azure.

Os eventos são enviados à Grade de Eventos do Azure em uma matriz, que pode conter vários objetos de evento. Se houver apenas um único evento, o comprimento da matriz será 1. A matriz pode ter um tamanho total de até 1 MB. Cada evento na matriz é limitado a 64 KB.
 
## <a name="event-properties"></a>Propriedades do evento

Todos os eventos conterão os mesmos dados de nível superior a seguir:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| topic | string | Caminho de recurso completo para a origem do evento. Esse campo não é gravável. |
| subject | string | Caminho definido pelo fornecedor para o assunto do evento. |
| eventType | string | Um dos tipos de evento registrados para a origem do evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| ID | string | Identificador exclusivo do evento. |
| data | objeto | Dados do evento específicos ao provedor de recursos. |

## <a name="available-event-sources"></a>Origens de evento disponíveis

As seguintes origens de evento publicam eventos para consumo por meio da Grade de Eventos:

* Grupos de recursos (operações de gerenciamento)
* Assinaturas do Azure (operações de gerenciamento)
* Hubs de Eventos
* Tópicos personalizados

## <a name="azure-subscriptions"></a>Assinaturas do Azure

As assinaturas do Azure agora podem emitir eventos de gerenciamento do Azure Resource Manager, como quando uma VM é criada ou uma conta de armazenamento é excluída.

### <a name="available-event-types"></a>Tipos de evento disponíveis

- **Microsoft.Resources.ResourceWriteSuccess**: gerado quando uma operação de criação ou atualização de recurso é bem-sucedida.  
- **Microsoft.Resources.ResourceWriteFailure**: gerado quando uma operação de criação ou atualização de recurso falha.  
- **Microsoft.Resources.ResourceWriteCancel**: gerado quando uma operação de criação ou atualização de recurso é cancelada.  
- **Microsoft.Resources.ResourceDeleteSuccess**: gerado quando uma operação de exclusão de recurso é bem-sucedida.  
- **Microsoft.Resources.ResourceDeleteFailure**: gerado quando uma operação de exclusão de recurso falha.  
- **Microsoft.Resources.ResourceDeleteCancel**: gerado quando uma operação de exclusão de recurso é cancelada. Isso ocorre quando a implantação de um modelo é cancelada.

### <a name="example-event-schema"></a>Exemplo do esquema de evento

```json
[
    {
    "topic":"/subscriptions/{subscription-id}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```



## <a name="resource-groups"></a>Grupos de recursos

Os Grupos de Recursos agora podem emitir eventos de gerenciamento do Azure Resource Manager, como quando uma VM é criada ou uma conta de armazenamento é excluída.

### <a name="available-event-types"></a>Tipos de evento disponíveis

- **Microsoft.Resources.ResourceWriteSuccess**: gerado quando uma operação de criação ou atualização de recurso é bem-sucedida.  
- **Microsoft.Resources.ResourceWriteFailure**: gerado quando uma operação de criação ou atualização de recurso falha.  
- **Microsoft.Resources.ResourceWriteCancel**: gerado quando uma operação de criação ou atualização de recurso é cancelada.  
- **Microsoft.Resources.ResourceDeleteSuccess**: gerado quando uma operação de exclusão de recurso é bem-sucedida.  
- **Microsoft.Resources.ResourceDeleteFailure**: gerado quando uma operação de exclusão de recurso falha.  
- **Microsoft.Resources.ResourceDeleteCancel**: gerado quando uma operação de exclusão de recurso é cancelada. Isso ocorre quando a implantação de um modelo é cancelada.

### <a name="example-event"></a>Exemplo de evento

```json
[
    {
    "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```



## <a name="event-hubs"></a>Hubs de Eventos

Os eventos dos Hubs de Eventos, atualmente, são emitidos apenas quando um arquivo é enviado automaticamente para o armazenamento por meio do recurso Capturar.

### <a name="available-event-types"></a>Tipos de evento disponíveis

- **Microsoft.EventHub.CaptureFileCreated**: gerado quando um arquivo de captura é criado.

### <a name="example-event"></a>Exemplo de evento

Este exemplo de evento mostra o esquema de um evento dos Hubs de Eventos gerado quando o recurso Capturar armazena um arquivo: 

```json
[
    {
        "topic": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{event-hubs-ns}",
        "subject": "eventhubs/eh1",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-07-11T00:55:55.0120485Z",
        "id": "bd440490-a65e-4c97-8298-ef1eb325673c",
        "data": {
            "fileUrl": "https://gridtest1.blob.core.windows.net/acontainer/eventgridtest1/eh1/1/2017/07/11/00/54/54.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 0,
            "eventCount": 0,
            "firstSequenceNumber": -1,
            "lastSequenceNumber": -1,
            "firstEnqueueTime": "0001-01-01T00:00:00",
            "lastEnqueueTime": "0001-01-01T00:00:00"
        },
    }
]

```


## <a name="azure-blob-storage"></a>Armazenamento do Blob do Azure

>[!IMPORTANT]
>Você deve estar registrado na versão prévia de eventos do Armazenamento de Blobs para usar eventos de armazenamento de blobs. Para obter mais informações sobre o programa de visualização, consulte [eventos de armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-overview#join-the-preview).  


### <a name="available-event-types"></a>Tipos de evento disponíveis

- **Microsoft.Storage.BlobCreated**: gerado quando um blob é criado.
- **Microsoft.Storage.BlobDeleted**: gerado quando um blob é excluído.

### <a name="example-event"></a>Exemplo de evento

Este exemplo de evento mostra o esquema de um evento de armazenamento gerado quando um blob é criado: 

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
  }
]
```




## <a name="custom-topics"></a>Tópicos personalizados

O conteúdo dos dados dos seus eventos personalizados é definido por você e pode ser qualquer objeto JSON formatado corretamente. Os dados de nível superior devem conter os mesmos campos do que os eventos definidos pelo recurso padrão. Ao publicar eventos em tópicos personalizados, você deve considerar a modelagem do assunto dos eventos para auxiliar no roteamento e na filtragem.

### <a name="example-event"></a>Exemplo de evento

O seguinte exemplo mostra um evento para um tópico personalizado:
````json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.EventGrid/topics/myeventgridtopic",
    "subject": "/myapp/vehicles/motorcycles",    
    "id": "b68529f3-68cd-4744-baa4-3c0498ec19e2",
    "eventType": "recordInserted",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "data":{
      "make": "Ducati",
      "model": "Monster"
    }
  }
]

````

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md).
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
