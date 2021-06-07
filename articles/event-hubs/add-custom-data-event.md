---
title: Adicionar dados personalizados a eventos nos hubs de eventos do Azure
description: Este artigo mostra como adicionar dados personalizados a eventos nos hubs de eventos do Azure.
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 3362b6ac4b0d624969aa3ba36d2ebc83b8777cf5
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104893465"
---
# <a name="add-custom-data-to-events-in-azure-event-hubs"></a>Adicionar dados personalizados a eventos nos hubs de eventos do Azure
Como um evento consiste principalmente em um conjunto de bytes opaco, pode ser difícil para os consumidores desses eventos tomar decisões informadas sobre como processá-los. Para permitir que os editores de eventos ofereçam um contexto melhor para os consumidores, os eventos também podem conter metadados personalizados, na forma de um conjunto de pares chave-valor. Um cenário comum para a inclusão de metadados é fornecer uma dica sobre o tipo de dados contidos em um evento, para que os consumidores compreendam seu formato e possam desserializá-lo adequadamente.

> [!NOTE]
> Esses metadados não são usados pelo, ou de uma forma significativa para o, o serviço de hubs de eventos; Ele só existe para coordenação entre editores de eventos e consumidores. 

As seções a seguir mostram como adicionar dados personalizados a eventos em diferentes linguagens de programação. 

## <a name="net"></a>.NET 

```csharp
var eventBody = new BinaryData("Hello, Event Hubs!");
var eventData = new EventData(eventBody);
eventData.Properties.Add("EventType", "com.microsoft.samples.hello-event");
eventData.Properties.Add("priority", 1);
eventData.Properties.Add("score", 9.0);
```

Para obter o exemplo de código completo, consulte [publicando eventos com metadados personalizados](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md#publishing-events-with-custom-metadata).

## <a name="java"></a>Java

```java
EventData firstEvent = new EventData("EventData Sample 1".getBytes(UTF_8));
firstEvent.getProperties().put("EventType", "com.microsoft.samples.hello-event");
firstEvent.getProperties().put("priority", 1);
firstEvent.getProperties().put("score", 9.0);
```

Para obter o exemplo de código completo, consulte [Publicar eventos com metadados personalizados](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/PublishEventsWithCustomMetadata.java).


## <a name="python"></a>Python

```python
event_data = EventData('Message with properties')
event_data.properties = {'event-type': 'com.microsoft.samples.hello-event', 'priority': 1, "score": 9.0}
```

Para obter o exemplo de código completo, consulte [Enviar lote de dados de evento com propriedades](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).

## <a name="javascript"></a>JavaScript

```javascript
let eventData = { body: "First event", properties: { "event-type": "com.microsoft.samples.hello-event", "priority": 1, "score": 9.0  } };
```


## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes guias de início rápido e exemplos. 

- Início rápido: [.net](event-hubs-dotnet-standard-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [python](event-hubs-python-get-started-send.md), [JavaScript](event-hubs-node-get-started-send.md)
- Exemplos no GitHub: [.net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples), [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples), [python](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples), [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript), [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
