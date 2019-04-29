---
title: Visão geral das APIs do .NET Framework dos Hubs de Eventos do Azure | Microsoft Docs
description: Um resumo de algumas das principais APIs de cliente .NET Framework de Hubs de Eventos.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: f56882d2d73c85434c6f81972a06e5ea9ccdff99
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822039"
---
# <a name="event-hubs-net-framework-api-overview"></a>Visão geral da API do .NET Framework de Hubs de Eventos

Este artigo resume algumas das principais APIs de cliente [.NET Framework do Azure de Hubs de Eventos](https://www.nuget.org/packages/WindowsAzure.ServiceBus/). Há duas categorias: APIs de gerenciamento e de tempo de execução. As APIs de tempo de execução consistem de todas as operações necessárias para enviar e receber uma mensagem. Operações de gerenciamento permitem gerenciar um estado da entidade Hubs de Eventos, ao criar, atualizar e excluir entidades.

Os [cenários de monitoramento](event-hubs-metrics-azure-monitor.md) abrangem tanto o gerenciamento quanto o tempo de execução. Para obter a documentação de referência detalhada sobre as APIs .NET, veja as referências [.NET Framework](/dotnet/api/microsoft.servicebus.messaging.eventhubclient), [.NET Standard](/dotnet/api/microsoft.azure.eventhubs), e [EventProcessorHost API](/dotnet/api/microsoft.azure.eventhubs.processor).

## <a name="management-apis"></a>APIs de gerenciamento

Para executar as seguintes operações de gerenciamento, você deve ter permissões para **Gerenciar** no namespace de Hubs de Eventos:

### <a name="create"></a>Criar

```csharp
// Create the event hub
var ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
await namespaceManager.CreateEventHubAsync(ehd);
```

### <a name="update"></a>Atualizar

```csharp
var ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
var ruleName = "myeventhubmanagerule";
var ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
await namespaceManager.UpdateEventHubAsync(ehd);
```

### <a name="delete"></a>Excluir

```csharp
await namespaceManager.DeleteEventHubAsync("event hub name");
```

## <a name="run-time-apis"></a>APIs de tempo de execução
### <a name="create-publisher"></a>Criar publicador

```csharp
// EventHubClient model (uses implicit factory instance, so all links on same connection)
var eventHubClient = EventHubClient.Create("event hub name");
```

### <a name="publish-message"></a>Publicar mensagem

```csharp
// Create the device/temperature metric
var info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
var data = new EventData(new byte[10]); // Byte array
var data = new EventData(Stream); // Stream 
var data = new EventData(info, serializer) //Object and serializer 
{
    PartitionKey = info.DeviceId.ToString()
};

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### <a name="create-consumer"></a>Criar consumidor

```csharp
// Create the Event Hubs client
var eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
var defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index);

// From one day ago
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));

// From specific offset, -1 means oldest
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index,startingOffset:-1); 
```

### <a name="consume-message"></a>Consumir mensagem

```csharp
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)

// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## <a name="event-processor-host-apis"></a>APIs de host do processador de eventos

Essas APIs fornecem resiliência aos processos de trabalho que podem se tornar indisponíveis, distribuindo partições entre os trabalhadores disponíveis.

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.

var eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
var blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

var eventHubDescription = new EventHubDescription(EventHubName);
var host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
await host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
await host.UnregisterEventProcessorAsync();
```

A interface [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) é definida da seguinte maneira:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            // Process messages here
        }

        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }

    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os cenários de Hubs de Eventos, consulte estes links:

* [O que é Hub de Eventos do Azure?](event-hubs-what-is-event-hubs.md)
* [Guia de programação dos Hubs de Eventos](event-hubs-programming-guide.md)

As referências de API .NET estão aqui:

* [Microsoft.ServiceBus.Messaging](/dotnet/api/microsoft.servicebus.messaging)
* [Microsoft.Azure.EventHubs.EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)
