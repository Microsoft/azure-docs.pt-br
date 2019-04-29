---
title: Gerenciar o estado do Azure Service Fabric | Microsoft Docs
description: Saiba como acessar, salvar e remover o estado dos Reliable Actors do Microsoft Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 7c10d00916ef65767c98616c7337bfa444c339a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725390"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Acessar, salvar e remover estado dos Reliable Actors
[Reliable Actors](service-fabric-reliable-actors-introduction.md) são objetos single-threaded que podem encapsular a lógica e o estado e manter o estado de forma confiável. Cada instância de ator tem seu próprio [gerenciador de estado](service-fabric-reliable-actors-state-management.md): uma estrutura de dados semelhante a um dicionário que armazena pares chave-valor de forma confiável. O gerenciador de estado é um wrapper em torno de um provedor de estado. Você pode usá-lo para armazenar dados, independentemente da [configuração de persistência](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) utilizada.

As chaves do gerenciador de estado devem ser cadeias de caracteres. Os valores são genéricos e podem ser de qualquer tipo, incluindo tipos personalizados. Os valores armazenados no gerenciador de estado devem ser serializáveis pelo contrato de dados, pois poderão ser transmitidos pela rede a outros nós durante a replicação e gravados em disco, dependendo da configuração de persistência de estado de um ator.

O gerenciador de estado expõe métodos comuns de dicionário para o gerenciamento do estado, semelhantes àqueles encontrados no Dicionário Confiável.

Para obter informações, consulte [práticas recomendadas no gerenciamento de estado de atores](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Acessar estado
O estado é acessado por chave por meio do gerenciador de estado. Os métodos do gerenciador de estado são todos assíncronos, pois podem exigir E/S de disco quando os atores têm um estado persistente. No primeiro acesso, os objetos de estado são armazenados em cache na memória. As operações de acesso repetidas acessam os objetos diretamente da memória e são retornadas de forma síncrona sem incorrer em E/S de disco ou sobrecarga de troca de contexto assíncrona. Um objeto de estado é removido do cache nos seguintes casos:

* Um método de ator gera uma exceção sem tratamento depois de recuperar um objeto do gerenciador de estado.
* Um ator é reativado depois de ser desativado ou depois de uma falha.
* O provedor de estado efetua a paginação do estado em disco. Esse comportamento depende da implementação do provedor de estado. O provedor de estado padrão para a configuração `Persisted` apresenta esse comportamento.

Você poderá recuperar o estado usando uma operação *Get* padrão, que gera `KeyNotFoundException` (C#) ou `NoSuchElementException` (Java) se não existir uma entrada para a chave:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

O estado também pode ser recuperado usando um método *TryGet* que não gera exceção, caso não exista uma entrada para uma chave:

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

## <a name="save-state"></a>Salvar estado
Os métodos de recuperação do gerenciador de estado retornam uma referência a um objeto na memória local. A modificação desse objeto na memória local por si só não faz com que ele seja salvo permanentemente. Quando um objeto é recuperado do gerenciador de estado e é modificado, ele deve ser reinserido no gerenciador de estado para ser salvo permanentemente.

O estado pode ser inserido usando um *Set* incondicional, que é o equivalente da sintaxe `dictionary["key"] = value`:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

Você pode adicionar estado usando um método *Add*. Este método lança `InvalidOperationException` (C#) ou `IllegalStateException` (Java) ao tentar adicionar uma chave que já exista.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

Você também pode adicionar estado usando um método *TryAdd*. Esse método não lançará exceção ao tentar adicionar uma chave que já exista.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

No final de um método de ator, o gerenciador de estado salva automaticamente todos os valores que foram adicionados ou modificados por uma operação de inserção ou de atualização. A operação “salvar” pode incluir a persistência em disco e a replicação, dependendo das configurações utilizadas. Valores que não foram modificados não serão persistentes nem replicados. Caso nenhum valor tenha sido modificado, a operação salvar não terá nenhum efeito. Se houver uma falha na operação salvar, o estado modificado será descartado e o estado original será recarregado.

Você também pode salvar o estado manualmente ao chamar o método `SaveStateAsync` na base de ator:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

## <a name="remove-state"></a>Remover estado
O estado pode ser removido permanentemente do gerenciador de estado de um ator com a chamada do método *Remove*. Este método lança `KeyNotFoundException` (C#) ou `NoSuchElementException` (Java) ao tentar remover uma chave que não exista.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

Você também pode remover o estado permanentemente usando o método *TryRemove*. Esse método não lançará exceção ao tentar remover uma chave que não exista.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>Próximas etapas

O estado que é armazenado no Reliable Actors deve ser serializado antes de ser gravado no disco e replicado para alta disponibilidade. Saiba mais sobre a [Serialização de tipo de ator](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Em seguida, saiba mais sobre [Diagnóstico e monitoramento de desempenho do ator](service-fabric-reliable-actors-diagnostics.md).
