---
title: Reliable Actors observações sobre a serialização do tipo de ator
description: Discute os requisitos básicos para definir as classes serializáveis que podem ser usadas para estabelecer as interfaces e o estado dos Reliable Actors do Service Fabric
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a1118e394046b217a288663659a2c910098e992
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576002"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Observações sobre a serialização de tipo dos Reliable Actors do Service Fabric
Os argumentos de todos os métodos, os tipos de resultado das tarefas retornadas por cada método em uma interface de ator e os objetos armazenados no Gerenciador de estado de um ator devem ser [serializáveis no contrato de dados](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer). Isso também se aplica aos argumentos dos métodos definidos nas [interfaces de evento de ator](service-fabric-reliable-actors-events.md). (Os métodos de interface de eventos de ator sempre retornam nulo).

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

A estrutura Reliable Actor usa a serialização DataContract. Portanto, os objetos de dados personalizados e seus membros devem ser anotados com os atributos **DataContract** e **DataMember** , respectivamente.

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
* [Reentrância do ator](service-fabric-reliable-actors-reentrancy.md)
* [Polimorfismo de ator e padrões de design orientado a objeto](service-fabric-reliable-actors-polymorphism.md)
* [Diagnóstico e monitoramento de desempenho do ator](service-fabric-reliable-actors-diagnostics.md)
