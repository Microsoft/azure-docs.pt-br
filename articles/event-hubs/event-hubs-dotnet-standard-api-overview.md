---
title: Visão geral das APIs do .NET Standard dos Hubs de Eventos do Azure | Microsoft Docs
description: Visão geral da API do .NET Standard
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 08/13/2018
ms.author: shvija
ms.openlocfilehash: b09f39f45936a7c43dbc1ef109780315d62c768f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821901"
---
# <a name="event-hubs-net-standard-api-overview"></a>Visão geral de API do .NET Standard de Hubs de Eventos

Este artigo resume algumas das principais APIs de cliente [.NET Standard do Azure.](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) dos Hubs de Eventos do Azure. Atualmente, há duas bibliotecas de cliente do .NET Standard para os Hubs de Eventos do Azure:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs): fornece todas as operações básicas de tempo de execução.
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor): adiciona outra funcionalidade que permite manter o controle de eventos processados e é a maneira mais fácil de fazer a leitura a partir de um hub de eventos.

## <a name="event-hubs-client"></a>Cliente dos Hubs de Eventos

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) é o objeto principal que você usa para enviar eventos, criar receptores e obter informações de tempo de execução. Esse cliente está vinculado a um hub de eventos específico e cria uma nova conexão para o ponto de extremidade de Hubs de Eventos.

### <a name="create-an-event-hubs-client"></a>Criar um cliente de Hubs de Eventos

Um objeto [EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) é criado de uma cadeia de conexão. A maneira mais simples para instanciar um novo cliente é mostrada no exemplo a seguir:

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("Event Hubs connection string");
```

Para editar a cadeia de conexão programaticamente, você pode usar a classe [EventHubsConnectionStringBuilder](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) e passar a cadeia de conexão como um parâmetro para [EventHubClient.CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient).

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("Event Hubs connection string")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>Enviar eventos

Para enviar eventos a um hub de eventos, use a classe [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata). O corpo deve ser uma matriz `byte` ou um segmento de matriz `byte`.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>Receber eventos

A maneira recomendada de receber eventos de Hubs de Eventos é usar o [Host do Processador de Eventos](#event-processor-host-apis), que fornece a funcionalidade acompanhar automaticamente as informações de deslocamento e de partição do Hub do Eventos. No entanto, há algumas situações em que você talvez queira usar a flexibilidade da biblioteca principal do Hubs de Eventos para receber eventos.

#### <a name="create-a-receiver"></a>Criar um receptor

Os receptores são vinculados a partições específicas, então, para receber todos os eventos de um Hub de Eventos, você precisará criar várias instâncias. Uma prática recomendada é obter as informações da partição via programação, ao invés de incorporar as IDs de partição ao código. Para fazer isso, você pode usar o método [GetRuntimeInformationAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient).

```csharp
// Create a list to keep track of the receivers
var receivers = new List<PartitionReceiver>();
// Use the eventHubClient created above to get the runtime information
var runTimeInformation = await eventHubClient.GetRuntimeInformationAsync();
// Loop over the resulting partition IDs
foreach (var partitionId in runTimeInformation.PartitionIds)
{
    // Create the receiver
    var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);
    // Add the receiver to the list
    receivers.Add(receiver);
}
```

Como os eventos nunca são removidos de um Hub de Eventos (e apenas expiram), é preciso especificar o ponto inicial adequado. O exemplo a seguir mostra as combinações possíveis:

```csharp
// partitionId is assumed to come from GetRuntimeInformationAsync()

// Using the constant PartitionReceiver.EndOfStream only receives all messages from this point forward.
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);

// All messages available
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, "-1");

// From one day ago
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, DateTime.Now.AddDays(-1));
```

#### <a name="consume-an-event"></a>Consumir um evento

```csharp
// Receive a maximum of 100 messages in this call to ReceiveAsync
var ehEvents = await receiver.ReceiveAsync(100);
// ReceiveAsync can return null if there are no messages
if (ehEvents != null)
{
    // Since ReceiveAsync can return more than a single event you will need a loop to process
    foreach (var ehEvent in ehEvents)
    {
        // Decode the byte array segment
        var message = UnicodeEncoding.UTF8.GetString(ehEvent.Body.Array);
        // Load the custom property that we set in the send example
        var customType = ehEvent.Properties["Type"];
        // Implement processing logic here
    }
}       
```

## <a name="event-processor-host-apis"></a>APIs de host do processador de eventos

Essas APIs fornecem resiliência aos processos de trabalho que podem se tornar indisponíveis, distribuindo partições entre os trabalhadores disponíveis:

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.

// Read these connection strings from a secure location
var ehConnectionString = "{Event Hubs connection string}";
var ehEntityPath = "{event hub path/name}";
var storageConnectionString = "{Storage connection string}";
var storageContainerName = "{Storage account container name}";

var eventProcessorHost = new EventProcessorHost(
    ehEntityPath,
    PartitionReceiver.DefaultConsumerGroupName,
    ehConnectionString,
    storageConnectionString,
    storageContainerName);

// Start/register an EventProcessorHost
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

// Disposes the Event Processor Host
await eventProcessorHost.UnregisterEventProcessorAsync();
```

A seguir é uma implementação de exemplo da interface do [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor):

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
        return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
        Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
        return Task.CompletedTask;
    }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
        Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
        return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (var eventData in messages)
        {
            var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
            Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
        }

        return context.CheckpointAsync();
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os cenários de Hubs de Eventos, consulte estes links:

* [O que é Hub de Eventos do Azure?](event-hubs-what-is-event-hubs.md)
* [Apis de Hubs de Eventos disponíveis](event-hubs-api-overview.md)

As referências de API .NET estão aqui:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)
