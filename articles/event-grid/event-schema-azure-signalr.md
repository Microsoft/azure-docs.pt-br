---
title: Sinalizador do Azure como origem da grade de eventos
description: Descreve as propriedades que são fornecidas para eventos do Signalr do Azure com a grade de eventos do Azure
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 321dc4d21485af23a9cca0d42d74da0a3e121b7c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363677"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Esquema de evento da grade de eventos do Azure para o serviço Signalr

Este artigo fornece as propriedades e o esquema para eventos do serviço Signalr. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md). Ele também fornece uma lista de inícios rápidos e tutoriais para usar o Azure Signalr como uma fonte de eventos.


## <a name="available-event-types"></a>Tipos de evento disponíveis

O serviço signalr emite os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft. SignalRService. ClientConnectionConnected | Gerado quando uma conexão de cliente é conectada. |
| Microsoft. SignalRService. ClientConnectionDisconnected | Gerado quando uma conexão de cliente é desconectada. |

## <a name="example-event"></a>Exemplo de evento

# <a name="event-grid-event-schema"></a>[Esquema de eventos da Grade de Eventos](#tab/event-grid-event-schema)
O exemplo a seguir mostra o esquema de um evento conectado de conexão de cliente: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

O esquema para um evento de conexão de cliente desconectado é semelhante: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[Esquema de evento de nuvem](#tab/cloud-event-schema)

O exemplo a seguir mostra o esquema de um evento conectado de conexão de cliente: 

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "type": "Microsoft.SignalRService.ClientConnectionConnected",
  "time": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "specversion": "1.0"
}]
```

O esquema para um evento de conexão de cliente desconectado é semelhante: 

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "type": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "time": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "specversion": "1.0"
}]
```

---


### <a name="event-properties"></a>Propriedades do evento


# <a name="event-grid-event-schema"></a>[Esquema de eventos da Grade de Eventos](#tab/event-grid-event-schema)
Um evento tem os seguintes dados de nível superior:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| `topic` | string | Caminho de recurso completo para a origem do evento. Este campo não é gravável. A Grade de Eventos fornece esse valor. |
| `subject` | string | Caminho definido pelo publicador para o assunto do evento. |
| `eventType` | string | Um dos tipos de evento registrados para a origem do evento. |
| `eventTime` | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| `id` | string | Identificador exclusivo do evento. |
| `data` | object | Dados de evento do serviço signalr. |
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
| `data` | object | Dados de evento do serviço signalr. |
| `specversion` | string | Versão de especificação de esquema CloudEvents. |

---

O objeto de dados tem as seguintes propriedades:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| `timestamp` | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| `hubName` | string | O Hub ao qual a conexão do cliente pertence. |
| `connectionId` | string | O identificador exclusivo para a conexão do cliente. |
| `userId` | string | O identificador de usuário definido na declaração. |
| `errorMessage` | string | O erro que faz com que a conexão seja desconectada. |

## <a name="tutorials-and-how-tos"></a>Tutoriais e instruções
|Título | Descrição |
|---------|---------|
| [Reagir aos eventos do serviço de Signaler do Azure usando a grade de eventos](../azure-signalr/signalr-concept-event-grid-integration.md) | Visão geral da integração do serviço de Signaler do Azure com a grade de eventos. |
| [Como enviar eventos do serviço de Signaler do Azure para a grade de eventos](../azure-signalr/signalr-howto-event-grid-integration.md) | Mostra como enviar eventos do serviço de Signaler do Azure para um aplicativo por meio da grade de eventos. |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
