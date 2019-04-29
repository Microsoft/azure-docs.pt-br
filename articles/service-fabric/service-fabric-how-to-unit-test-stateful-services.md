---
title: Desenvolver testes de unidade para serviços com estado no Azure Service Fabric | Microsoft Docs
description: Saiba como desenvolver testes de unidade para serviços com estado do Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: atsenthi
ms.openlocfilehash: b066296ca52d3067f8985245161eb4fa7b484a07
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720120"
---
# <a name="create-unit-tests-for-stateful-services"></a>Criar testes de unidade para serviços com estado
Testes de unidade Os serviços com informações de estado do Service Fabric descobrem erros comuns que não necessariamente seriam detectados pelo aplicativo convencional ou pelo teste de unidade específica do domínio. Ao desenvolver testes de unidade para serviços com estado, há algumas considerações especiais que devem ser mantidas em mente.

1. Cada réplica executa o código do aplicativo, mas sob um contexto diferente. Se o serviço usa três réplicas, o código de serviço está sendo executado em três nós em paralelo em contexto / função diferente.
2. Estado armazenado dentro do serviço com estado deve ser consistente entre todas as réplicas. O gerente de estado e as coleções confiáveis fornecerão essa consistência pronta para uso. No entanto, o estado da memória precisará ser gerenciado pelo código do aplicativo.
3. Cada réplica mudará de função em algum momento durante a execução no cluster. Uma réplica secundária se tornará uma principal, no caso de o nó que hospeda o primário ficar indisponível ou sobrecarregado. Esse é o comportamento natural do Service Fabric, portanto, os serviços devem planejar a execução final em uma função diferente.

Este artigo presume que [Unit testando serviços com estado no Service Fabric](service-fabric-concepts-unit-testing.md) foi lido.

## <a name="the-servicefabricmocks-library"></a>A biblioteca ServiceFabric.Mocks
A partir da versão 3.3.0, [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) fornece uma API para zombar tanto da orquestração das réplicas quanto do gerenciamento de estado. Isso será usado nos exemplos.

[Nuget](https://www.nuget.org/packages/ServiceFabric.Mocks/)
[GitHub](https://github.com/loekd/ServiceFabric.Mocks)

*ServiceFabric.Mocks não é de propriedade nem mantido pela Microsoft. No entanto, atualmente, é recomendado a biblioteca de serviços com monitoração de estado de teste de unidade da Microsoft.*

## <a name="set-up-the-mock-orchestration-and-state"></a>Configurar a orquestração e estado de simulação
Como parte da parte de organização de um teste, um conjunto de réplicas simuladas e um gerenciador de estado serão criados. O conjunto de réplicas será proprietário da criação de uma instância do serviço testado para cada réplica. Ele também possui eventos de ciclo de vida em execução, como `OnChangeRole` e `RunAsync`. O gerente de estado simulado assegurará que quaisquer operações executadas contra o gerente de estado sejam executadas e mantidas como o gerente de estado real faria.

1. Crie um delegado de fábrica de serviços que instancie o serviço que está sendo testado. Isso deve ser semelhante ou mesmo como o retorno de chamada de fábrica de serviço geralmente se encontram na `Program.cs` para um ator ou serviço do Service Fabric. Isso deve seguir a seguinte assinatura:
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. Criar uma instância de `MockReliableStateManager` classe. Isso vai zombar de todas as interações com o gerente de estado.
3. Crie uma instância de `MockStatefulServiceReplicaSet<TStatefulService>`, em que `TStatefulService` é o tipo do serviço que está sendo testado. Isso exigirá que o delegado criado na etapa #1 e o gerenciador de estado instanciado na etapa #2
4. Adicionar réplicas ao conjunto de réplicas. Especifique a função (como Primária, ActiveSecondary, IdleSecondary) e o ID da réplica
   > Segure nos IDs da réplica! Eles provavelmente serão usados durante o ato e declararão partes de um teste de unidade.

```csharp
//service factory to instruct how to create the service instance
var serviceFactory = (StatefulServiceContext context, IReliableStateManagerReplica2 stateManager) => new MyStatefulService(context, stateManager);
//instantiate a new mock state manager
var stateManager = new MockReliableStateManager();
//instantiate a new replica set with the service factory and state manager
var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
//add a new Primary replica with id 1
await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
//add a new ActiveSecondary replica with id 2
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
//add a second ActiveSecondary replica with id 3
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);
```

## <a name="execute-service-requests"></a>Executar solicitações de serviço
Solicitações de serviço podem ser executadas em uma réplica específica usando as propriedades e pesquisas de conveniência.
```csharp
const string stateName = "test";
var payload = new Payload(StatePayload);

//execute a request on the primary replica using
await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);

//execute a request against replica with id 2
await replicaSet[2].ServiceInstance.InsertAsync(stateName, payload);

//execute a request against one of the active secondary replicas
await replicaSet.FirstActiveSecondary.InsertAsync(stateName, payload);
```

## <a name="execute-a-service-move"></a>Executar um serviço de movimentação
O conjunto de réplicas fictício expõe vários métodos de conveniência para disparar a diferentes tipos de movimentações de serviço.
```csharp
//promote the first active secondary to primary
replicaSet.PromoteNewReplicaToPrimaryAsync();
//promote the secondary with replica id 4 to primary
replicaSet.PromoteNewReplicaToPrimaryAsync(4);

//promote the first idle secondary to an active secondary
PromoteIdleSecondaryToActiveSecondaryAsync();
//promote idle secondary with replica id 4 to active secondary
PromoteIdleSecondaryToActiveSecondaryAsync(4);

//add a new replica with randomly assigned replica id and promote it to primary
PromoteNewReplicaToPrimaryAsync()
//add a new replica with replica id 4 and promote it to primary
PromoteNewReplicaToPrimaryAsync(4)
```

## <a name="putting-it-all-together"></a>Juntando as peças
O teste a seguir demonstra como configurar um conjunto de réplicas de três nós e verificar se os dados estão disponíveis em um secundário após uma alteração de função. Um problema típico que isso pode pegar é se os dados adicionados durante `InsertAsync`foram salvos em algo na memória ou em uma coleção confiável sem executar`CommitAsync`. Em ambos os casos, o secundário estaria fora de sincronia com o primário. Isso levaria a respostas inconsistentes depois passa do serviço.

```csharp
[TestMethod]
public async Task TestServiceState_InMemoryState_PromoteActiveSecondary()
{
    var stateManager = new MockReliableStateManager();
    var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
    await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);

    const string stateName = "test";
    var payload = new Payload(StatePayload);

    //insert data
    await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);
    //promote one of the secondaries to primary
    await replicaSet.PromoteActiveSecondaryToPrimaryAsync(2);
    //get data
    var payloads = (await replicaSet.Primary.ServiceInstance.GetPayloadsAsync()).ToList();

    //data should match what was inserted against the primary
    Assert.IsTrue(payloads.Count == 1);
    Assert.IsTrue(payloads[0].Content == payload.Content);

    //verify the data was saved against the reliable dictionary
    var dictionary = await StateManager.GetOrAddAsync<IReliableDictionary<string, Payload>>(MyStatefulService.StateManagerDictionaryKey);
    using(var tx = StateManager.CreateTransaction())
    {
        var payload = await dictionary.TryGetValue(stateName);
        Assert.IsTrue(payload.HasValue);
        Assert.IsTrue(payload.Value.Content == payload.Content);
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Saiba como testar [comunicação serviço a serviço](service-fabric-testability-scenarios-service-communication.md) e [simular falhas usando caos controlado](service-fabric-controlled-chaos.md).
