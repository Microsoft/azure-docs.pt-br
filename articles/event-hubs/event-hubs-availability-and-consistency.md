---
title: Disponibilidade e consistência – Hubs de Eventos do Azure | Microsoft Docs
description: Como fornecer o máximo de disponibilidade e consistência com os Hubs de Eventos do Azure usando partições.
ms.topic: article
ms.date: 03/15/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: e014a33e94fe7f90569dd2ef1e9b620eef274842
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952857"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Disponibilidade e consistência nos Hubs de Eventos
Este artigo fornece informações sobre a disponibilidade e a consistência com suporte dos hubs de eventos do Azure. 

## <a name="availability"></a>Disponibilidade
Os hubs de eventos do Azure espalham o risco de falhas catastróficas de máquinas individuais ou até mesmo de racks completos em clusters que abrangem vários domínios de falha em um datacenter. Ele implementa mecanismos de failover e detecção de falha transparente, de modo que o serviço continuará a operar dentro dos níveis de serviço garantidos e, normalmente, sem interrupções perceptíveis quando essas falhas ocorrerem. 

Se um namespace de hubs de eventos tiver sido criado com [zonas de disponibilidade](../availability-zones/az-overview.md) habilitadas, o risco de interrupção será distribuído em três instalações fisicamente separadas e o serviço terá reserva de capacidade suficiente para lidar instantaneamente com a perda catastrófica completa de todo o recurso. Para obter mais informações, consulte [hubs de eventos do Azure-recuperação de desastres geograficamente](event-hubs-geo-dr.md).

Quando um aplicativo cliente envia eventos para um hub de eventos sem especificar uma partição, os eventos são distribuídos automaticamente entre partições no Hub de eventos. Se uma partição não estiver disponível por algum motivo, os eventos serão distribuídos entre as partições restantes. Esse comportamento permite a maior quantidade possível de tempo de atividade. Para casos de uso que exigem o máximo de tempo de backup, esse modelo é preferencial em vez de enviar eventos para uma partição específica. 

## <a name="consistency"></a>Consistência
Em alguns cenários, a ordenação de eventos pode ser importante. Por exemplo, você pode desejar que seu sistema de back-end processe um comando update antes de processar um comando delete. Nesse cenário, um aplicativo cliente envia eventos para uma partição específica para que a ordem seja preservada. Quando um aplicativo de consumidor consome esses eventos da partição, eles são lidos em ordem. 

Com essa configuração, tenha em mente que, se a partição específica para a qual você está enviando não estiver disponível, você receberá uma resposta de erro. Como um ponto de comparação, se você não tiver uma afinidade com uma única partição, o serviço de hubs de eventos enviará seu evento para a próxima partição disponível.

Portanto, se a alta disponibilidade for mais importante, não direcione uma partição específica (usando a ID de partição/chave). Usar a ID/chave de partição faz downgrade da disponibilidade de um hub de eventos para o nível de partição. Nesse cenário, você está fazendo uma escolha explícita entre a disponibilidade (sem ID de partição/chave) e a consistência (fixação de eventos em uma partição específica). Para obter informações detalhadas sobre partições em hubs de eventos, consulte [partições](event-hubs-features.md#partitions).

## <a name="appendix"></a>Apêndice

### <a name="send-events-without-specifying-a-partition"></a>Enviar eventos sem especificar uma partição
É recomendável enviar eventos para um hub de eventos sem definir informações de partição para permitir que o serviço de hubs de eventos Equilibre a carga entre partições. Consulte o início rápido a seguir para saber como fazer isso em diferentes linguagens de programação. 

- [Enviar eventos usando o .NET](event-hubs-dotnet-standard-getstarted-send.md)
- [Enviar eventos usando Java](event-hubs-java-get-started-send.md)
- [Enviar eventos usando JavaScript](event-hubs-python-get-started-send.md)
- [Enviar eventos usando o Python](event-hubs-python-get-started-send.md)


### <a name="send-events-to-a-specific-partition"></a>Enviar eventos para uma partição específica
Nesta seção, você aprenderá a enviar eventos para uma partição específica usando linguagens de programação diferentes. 

### <a name="net"></a>[.NET](#tab/dotnet)
Para enviar eventos para uma partição específica, crie o lote usando o método [EventHubProducerClient. CreateBatchAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.createbatchasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_CreateBatchAsync_Azure_Messaging_EventHubs_Producer_CreateBatchOptions_System_Threading_CancellationToken_) especificando o `PartitionId` ou o `PartitionKey` em [createbatchoptions](/dotnet/api/azure.messaging.eventhubs.producer.createbatchoptions). O código a seguir envia um lote de eventos para uma partição específica, especificando uma chave de partição. Os hubs de eventos garantem que todos os eventos que compartilham um valor de chave de partição sejam armazenados juntos e entregues em ordem de chegada.

```csharp
var batchOptions = new CreateBatchOptions { PartitionKey = "cities" };
using var eventBatch = await producer.CreateBatchAsync(batchOptions);
```

Você também pode usar o método [EventHubProducerClient. SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_SendAsync_System_Collections_Generic_IEnumerable_Azure_Messaging_EventHubs_EventData__Azure_Messaging_EventHubs_Producer_SendEventOptions_System_Threading_CancellationToken_) especificando **PartitionID** ou **PartitionKey** em [SendEventOptions](/dotnet/api/azure.messaging.eventhubs.producer.sendeventoptions).

```csharp
var sendEventOptions  = new SendEventOptions { PartitionKey = "cities" };
// create the events array
producer.SendAsync(events, sendOptions)
```


### <a name="java"></a>[Java](#tab/java)
Para enviar eventos para uma partição específica, crie o lote usando o método [CreateBatch](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.createbatch) ESPECIFICANDO a **ID da partição** ou a chave de **partição** em [createbatchoptions](/java/api/com.azure.messaging.eventhubs.models.createbatchoptions). O código a seguir envia um lote de eventos para uma partição específica, especificando uma chave de partição. 

```java
CreateBatchOptions batchOptions = new CreateBatchOptions();
batchOptions.setPartitionKey("cities");
```

Você também pode usar o método [EventHubProducerClient. Send](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.send#com_azure_messaging_eventhubs_EventHubProducerClient_send_java_lang_Iterable_com_azure_messaging_eventhubs_EventData__com_azure_messaging_eventhubs_models_SendOptions_) ESPECIFICANDO a **ID da partição** ou a chave de **partição** em [sendoptions](/java/api/com.azure.messaging.eventhubs.models.sendoptions).

```java
List<EventData> events = Arrays.asList(new EventData("Melbourne"), new EventData("London"), new EventData("New York"));
SendOptions sendOptions = new SendOptions();
sendOptions.setPartitionKey("cities");
producer.send(events, sendOptions);
```


### <a name="python"></a>[Python](#tab/python) 
Para enviar eventos para uma partição específica, ao criar um lote usando o [`EventHubProducerClient.create_batch`](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#create-batch---kwargs-) método, especifique o `partition_id` ou o `partition_key` . Em seguida, use o [`EventHubProducerClient.send_batch`](/python/api/azure-eventhub/azure.eventhub.aio.eventhubproducerclient#send-batch-event-data-batch--typing-union-azure-eventhub--common-eventdatabatch--typing-list-azure-eventhub-) método para enviar o lote para a partição do hub de eventos. 

```python
event_data_batch = await producer.create_batch(partition_key='cities')
```

Você também pode usar o método [EventHubProducerClient.send_batch](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#send-batch-event-data-batch----kwargs-) especificando valores para `partition_id` parâmetros ou `partition_key` .

```python
producer.send_batch(event_data_batch, partition_key="cities")
```

### <a name="javascript"></a>[JavaScript](#tab/javascript)
Para enviar eventos para uma partição específica, [crie um lote](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) usando o objeto [EventHubProducerClient. createbatchoptions](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) especificando o `partitionId` ou o `partitionKey` . Em seguida, envie o lote para o Hub de eventos usando o método [EventHubProducerClient. SendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventDataBatch__OperationOptions_) . 

Veja o exemplo a seguir.

```javascript
const batchOptions = { partitionKey = "cities"; };
const batch = await producer.createBatch(batchOptions);
```

Você também pode usar o método [EventHubProducerClient. sendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventData____SendBatchOptions_) ESPECIFICANDO a **ID da partição** ou a chave de **partição** em [SendBatchOptions](/javascript/api/@azure/event-hubs/sendbatchoptions).

```javascript
const sendBatchOptions = { partitionKey = "cities"; };
// prepare events
producer.sendBatch(events, sendBatchOptions);
```

---



## <a name="next-steps"></a>Próximas etapas
Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

- [Visão geral do serviço dos Hubs de Eventos](./event-hubs-about.md)
- [Terminologia dos Hubs de Eventos](event-hubs-features.md)
