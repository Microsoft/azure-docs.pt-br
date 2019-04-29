---
title: Eventos em Atores do Azure Service Fabric baseados em ator | Microsoft Docs
description: Introdução a eventos para Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: aa01b0f7-8f88-403a-bfe1-5aba00312c24
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: 9075fc8391e8afa21e3963c1eff6a630c586d647
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60726393"
---
# <a name="actor-events"></a>Eventos de ator
Os eventos de ator são uma forma de enviar notificações de melhor esforço do ator para os clientes. Os eventos de ator foram desenvolvidos para comunicação entre ator e cliente e não devem ser usados para comunicação entre ator e ator.

Os snippets de código a seguir mostram como usar os eventos de ator em seu aplicativo.

Defina uma interface que descreva os eventos publicados pelo ator. Essa interface deve ser derivada da interface `IActorEvents` . Os argumentos dos métodos devem ser [contrato de dados serializável](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Os métodos devem retornar valor nulo, pois as notificações de evento são o melhor esforço e unidirecionais.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```
```Java
public interface GameEvents implements ActorEvents
{
    void gameScoreUpdated(UUID gameId, String currentScore);
}
```
Declare os eventos publicados pelo ator na interface do ator.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```
```Java
public interface GameActor extends Actor, ActorEventPublisherE<GameEvents>
{
    CompletableFuture<?> updateGameStatus(GameStatus status);

    CompletableFuture<String> getGameScore();
}
```
No lado do cliente, implemente o manipulador de eventos.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

```Java
class GameEventsHandler implements GameEvents {
    public void gameScoreUpdated(UUID gameId, String currentScore)
    {
        System.out.println("Updates: Game: "+gameId+" ,Score: "+currentScore);
    }
}
```

No cliente, crie um proxy para o ator que publica o evento e assine para receber os eventos.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

No caso de failovers, o ator pode realizar failover para um processo ou nó diferente. O proxy de ator gerencia as assinaturas ativas e as renova automaticamente. Você pode controlar o intervalo da renovação da assinatura por meio da API `ActorProxyEventExtensions.SubscribeAsync<TEvent>` . Para cancelar a assinatura, use a API `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` .

No ator, publique os eventos à medida que eles acontecem. Se existirem assinantes no evento, o tempo de execução dos Atores enviará a eles a notificação.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Próximas etapas
* [Reentrância de ator](service-fabric-reliable-actors-reentrancy.md)
* [Diagnóstico e monitoramento de desempenho do ator](service-fabric-reliable-actors-diagnostics.md)
* [Documentação de referência da API do Ator](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de exemplo C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de exemplo em c# .NET Core](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Código de exemplo Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)
