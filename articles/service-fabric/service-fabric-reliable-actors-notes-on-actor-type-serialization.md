---
title: Observações de Reliable Actors sobre a serialização do tipo de ator | Microsoft Docs
description: Discute os requisitos básicos para definir as classes serializáveis que podem ser usadas para estabelecer as interfaces e o estado dos Reliable Actors do Service Fabric
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 6e50e4dc-969a-4a1c-b36c-b292d964c7e3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: c8eeeb0ade6ca002adf3211cbf49127be9b76edb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725645"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Observações sobre a serialização de tipo dos Reliable Actors do Service Fabric
Os argumentos de todos os métodos, os tipos de resultado das tarefas retornados por cada método em uma interface de ator e os objetos armazenados no gerenciador de estado de um ator devem ser [serializáveis por contrato de dados](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer). Isso também se aplica aos argumentos dos métodos definidos nas [interfaces de evento de ator](service-fabric-reliable-actors-events.md). (Os métodos de interface de eventos de ator sempre retornam nulo).

## <a name="custom-data-types"></a>Tipos de dados personalizados
Neste exemplo, a interface de ator a seguir define um método que retorna um tipo de dados personalizado chamado `VoicemailBox`:

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

A interface é implementada por um ator que usa o gerenciador de estado para armazenar um objeto `VoicemailBox`:

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

Neste exemplo, o objeto `VoicemailBox` é serializado quando:

* O objeto é transmitido entre uma instância do ator e um chamador.
* O objeto é salvo no gerenciador de estado, local em que é mantido no disco e replicado para outros nós.

A estrutura Reliable Actor usa a serialização DataContract. Portanto, os objetos de dados personalizados e seus membros devem ser anotados com os atributos **DataContract** e **DataMember**, respectivamente.

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>Próximas etapas
* [Ciclo de vida do ator e coleta de lixo](service-fabric-reliable-actors-lifecycle.md)
* [Lembretes e temporizadores de ator](service-fabric-reliable-actors-timers-reminders.md)
* [Eventos de ator](service-fabric-reliable-actors-events.md)
* [Reentrância de ator](service-fabric-reliable-actors-reentrancy.md)
* [Polimorfismo de ator e padrões de design orientado a objeto](service-fabric-reliable-actors-polymorphism.md)
* [Diagnóstico e monitoramento de desempenho do ator](service-fabric-reliable-actors-diagnostics.md)
