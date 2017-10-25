---
title: Gerenciamento de estado dos Reliable Actors | Microsoft Docs
description: "Descreve como o estado dos Reliable Actors é gerenciado, persistido e replicado para alta disponibilidade."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: aca8cf2b94e8b746a5cac6af021c7221a29b7345
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="reliable-actors-state-management"></a>Gerenciamento de estado dos Reliable Actors
Reliable Actors são objetos single-threaded que podem encapsular a lógica e o estado. Como os atores são executados nos Reliable Services, eles podem manter o estado de modo confiável usando os mesmos mecanismos de persistência e replicação usados pelos Reliable Services. Dessa forma, os atores não perdem seu estado após falhas, na reativação após a coleta de lixo ou quando são movidos entre nós em um cluster devido ao balanceamento de recursos ou às atualizações.

## <a name="state-persistence-and-replication"></a>Replicação e persistência de estado
Todos os Reliable Actors são considerados *com estado* , pois cada instância de ator é mapeada para uma ID exclusiva. Isso significa que as chamadas repetidas para a mesma ID de ator são encaminhadas para a mesma instância de ator. Em um sistema sem estado, por outro lado, as chamadas de cliente não têm garantia de serem encaminhadas sempre para o mesmo servidor. Por esse motivo, os serviços de ator são sempre serviços com estado.

Mesmo que os atores sejam considerados com estado, isso não significa que eles devem armazenar o estado de modo confiável. Os atores podem escolher o nível de persistência de estado e replicação com base em seus requisitos de armazenamento de dados:

* **Estado persistente:** o estado é persistido em disco e replicado para três ou mais réplicas. Essa é a opção de armazenamento de estado mais duradoura, na qual o estado pode persistir após a interrupção completa do cluster.
* **Estado volátil:** o estado é replicado para três ou mais réplicas e mantido apenas na memória. Isso proporciona resiliência contra falhas de nó e de ator, e durante atualizações e balanceamento de recursos. No entanto, o estado não é persistido no disco. Portanto, se todas as réplicas forem perdidas ao mesmo tempo, o estado também será perdido.
* **Nenhum estado persistente:** o estado não é replicado nem é gravado em disco. Esse nível é para atores que simplesmente não precisam manter o estado de modo confiável.

Cada nível de persistência é apenas um *provedor de estado* e uma configuração de *replicação* diferentes do serviço. A decisão de gravar ou não o estado em disco depende do provedor de estado – o componente que armazena o estado em um serviço confiável. A replicação depende de quantas réplicas são implantadas com um serviço. Assim como acontece com os Reliable Services, o provedor de estado e a contagem de réplicas podem ser definidos manualmente com facilidade. A estrutura de ator fornece um atributo, que, quando usado em um ator, seleciona automaticamente um provedor de estado padrão e gera automaticamente as configurações de contagem de réplicas, a fim de alcançar uma dessas três configurações de persistência. O atributo StatePersistence não será herdado pela classe derivada. Cada tipo de Ator deve fornecer seu nível de StatePersistence.

### <a name="persisted-state"></a>Estado persistente
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
Essa configuração usa um provedor de estado que armazena dados em disco e define automaticamente a contagem de réplicas do serviço como 3.

### <a name="volatile-state"></a>Estado volátil
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Essa configuração usa um provedor de estado somente na memória e define a contagem de réplicas como 3.

### <a name="no-persisted-state"></a>Nenhum estado persistente
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Essa configuração usa um provedor de estado somente na memória e define a contagem de réplicas como 1.

### <a name="defaults-and-generated-settings"></a>Padrões e configurações geradas
Ao usar o atributo `StatePersistence`, um provedor de estado será selecionado automaticamente para você em tempo de execução quando o serviço de ator for iniciado. No entanto, a contagem de réplicas é definida no tempo de compilação pelas ferramentas de build de ator do Visual Studio. As ferramentas de build geram automaticamente um *serviço padrão* para o serviço de ator no ApplicationManifest.xml. Os parâmetros são criados para **tamanho mín. do conjunto de réplicas** e **tamanho de destino do conjunto de réplicas**.

Você pode alterar esses parâmetros manualmente. Mas, sempre que o atributo `StatePersistence` for alterado, os parâmetros serão definidos como os valores padrão de tamanho do conjunto de réplicas para o atributo `StatePersistence` selecionado, substituindo os valores anteriores. Em outras palavras, os valores definidos em ServiceManifest.xml serão substituídos *somente* no momento do build quando você alterar o valor do atributo `StatePersistence`.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>Gerenciador de estado
Cada instância de ator tem seu próprio gerenciador de estado: uma estrutura de dados semelhante a um dicionário que armazena pares chave-valor de forma confiável. O gerenciador de estado é um wrapper em torno de um provedor de estado. Você pode usá-lo para armazenar dados, independentemente da configuração de persistência utilizada. Ele não dá nenhuma garantia de que um serviço de ator em execução poderá ser alterado de uma configuração de estado volátil (somente na memória) para uma configuração de estado persistente por meio de uma atualização sem interrupção, ao mesmo tempo que preserva os dados. No entanto, é possível alterar a contagem de réplicas de um serviço em execução.

As chaves do gerenciador de estado devem ser cadeias de caracteres. Os valores são genéricos e podem ser de qualquer tipo, incluindo tipos personalizados. Os valores armazenados no gerenciador de estado devem ser serializáveis pelo contrato de dados, pois poderão ser transmitidos pela rede a outros nós durante a replicação e gravados em disco, dependendo da configuração de persistência de estado de um ator.

O gerenciador de estado expõe métodos comuns de dicionário para o gerenciamento do estado, semelhantes àqueles encontrados no Dicionário Confiável.

### <a name="accessing-state"></a>Acessando o estado
O estado pode ser acessado por chave por meio do gerenciador de estado. Os métodos do gerenciador de estado são todos assíncronos, pois podem exigir E/S de disco quando os atores têm um estado persistente. No primeiro acesso, os objetos de estado são armazenados em cache na memória. As operações de acesso repetidas acessam os objetos diretamente da memória e são retornadas de forma síncrona sem incorrer em E/S de disco ou sobrecarga de troca de contexto assíncrona. Um objeto de estado é removido do cache nos seguintes casos:

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

### <a name="saving-state"></a>Salvando o estado
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

### <a name="removing-state"></a>Removendo o estado
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
