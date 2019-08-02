---
title: Durable Functions recursos de visualização – Azure Functions
description: Saiba mais sobre os recursos de visualização para Durable Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: article
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 7356541ed6288603a66d5caa43138284d8d4d918
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320480"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2,0 Preview (Azure Functions)

As *Funções Duráveis* são uma extensão do [Azure Functions](../functions-overview.md) e do [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) que permitem que você escreva funções com estado em um ambiente sem servidor. A extensão gerencia estado, pontos de verificação e reinicializações para você. Se você ainda não estiver familiarizado com Durable Functions, consulte a [documentação de visão geral](durable-functions-overview.md).

O Durable Functions 1. x é um recurso GA (disponível para o público) de Azure Functions, mas também contém vários sub-recursos que estão atualmente em visualização pública. Este artigo descreve os recursos de visualização lançados recentemente e entra em detalhes sobre como eles funcionam e como você pode começar a usá-los.

> [!NOTE]
> Esses recursos de visualização fazem parte de uma versão Durable Functions 2,0, que é atualmente uma **versão de qualidade de visualização** com várias alterações significativas. As compilações do pacote de extensão durável Azure Functions podem ser encontradas em nuget.org com versões na forma de **2.0.0-betaX**. Essas compilações não são destinadas a cargas de trabalho de produção, e as versões subsequentes podem conter alterações significativas adicionais.

## <a name="breaking-changes"></a>Alterações de última hora

Várias alterações significativas são introduzidas no Durable Functions 2,0. Os aplicativos existentes não devem ser compatíveis com o Durable Functions 2,0 sem alterações de código. Esta seção lista algumas das alterações:

### <a name="hostjson-schema"></a>Esquema host. JSON

O trecho a seguir mostra o novo esquema para host. JSON. As principais alterações a serem consideradas são as novas subseções:

* `"storageProvider"`(e a `"azureStorage"` subseção) para configuração específica de armazenamento
* `"tracking"`para rastreamento e configuração de log
* `"notifications"`(e a `"eventGrid"` subseção) para configuração de notificação da grade de eventos

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

Como Durable Functions 2,0 continua a estabilizar, mais alterações serão introduzidas na `durableTask` seção host. JSON. Para obter mais informações sobre essas alterações, consulte [este problema do GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Alterações de interface pública

Os vários objetos "Context" com suporte pelo Durable Functions tinham classes base abstratas destinadas a uso em testes de unidade. Como parte do Durable Functions 2,0, essas classes base abstratas foram substituídas por interfaces. O código de função que usa os tipos concretos diretamente não é afetado.

A tabela a seguir representa as principais alterações:

| Tipo antigo | Novo tipo |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

No caso em que uma classe base abstrata continha métodos virtuais, esses métodos virtuais foram substituídos por métodos de extensão `DurableContextExtensions`definidos em.

## <a name="entity-functions"></a>Funções de entidade

As funções de entidade definem operações para ler e atualizar pequenas partes de estado, conhecidas como *entidades duráveis*. Como as funções de orquestrador, as funções de entidade são funções com um tipo de gatilho especial, *gatilho de entidade*. Ao contrário das funções de orquestrador, as funções de entidade não têm nenhuma restrição de código específica. As funções de entidade também gerenciam o estado explicitamente, em vez de representar implicitamente o estado por meio do fluxo de controle.

### <a name="net-programing-models"></a>Modelos de programação .NET

Há dois modelos de programação opcionais para a criação de entidades duráveis. O código a seguir é um exemplo de uma entidade de *contador* simples implementada como uma função padrão. Essa função define três *operações*, `add` `reset`, e `get`, cada uma operando em um valor de estado inteiro, `currentValue`.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

Esse modelo funciona melhor para implementações de entidade simples ou implementações que têm um conjunto dinâmico de operações. No entanto, há também um modelo de programação baseado em classe que é útil para entidades que são estáticas, mas têm implementações mais complexas. O exemplo a seguir é uma implementação equivalente da `Counter` entidade usando classes e métodos .net.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;
    
    public void Reset() => this.CurrentValue = 0;
    
    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

O modelo baseado em classe é semelhante ao modelo de programação popular pelo [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). Nesse modelo, um tipo de entidade é definido como uma classe .NET. Cada método da classe é uma operação que pode ser invocada por um cliente externo. Ao contrário de Orleans, no entanto, as interfaces .NET são opcionais. O exemplo de *contador* anterior não usou uma interface, mas ela ainda pode ser invocada por meio de outras funções ou por meio de chamadas de API http.

As *instâncias* de entidade são acessadas por meio de um identificador exclusivo, a *ID da entidade*. Uma ID de entidade é simplesmente um par de cadeias de caracteres que identifica exclusivamente uma instância de entidade. Ele consiste em:

* Um **nome de entidade**: um nome que identifica o tipo da entidade (por exemplo, "Counter").
* Uma **chave de entidade**: uma cadeia de caracteres que identifica exclusivamente a entidade entre todas as outras entidades com o mesmo nome (por exemplo, um GUID).

Por exemplo, uma função de entidade de *contador* pode ser usada para manter a pontuação em um jogo online. Cada instância do jogo terá uma ID de entidade exclusiva, `@Counter@Game1`como, `@Counter@Game2`e assim por diante.

### <a name="comparison-with-virtual-actors"></a>Comparação com atores virtuais

O design de entidades duráveis é muito influenciado pelo [modelo de ator](https://en.wikipedia.org/wiki/Actor_model). Se você já estiver familiarizado com os atores, os conceitos por trás das entidades duráveis devem ser familiares para você. Em particular, as entidades duráveis são semelhantes aos [atores virtuais](https://research.microsoft.com/projects/orleans/) de várias maneiras:

* As entidades duráveis são endereçáveis por meio de uma *ID de entidade*.
* As operações de entidade durável são executadas em série, uma de cada vez, para evitar condições de corrida.
* As entidades duráveis são criadas automaticamente quando são chamadas ou sinalizadas.
* Quando não estiver executando operações, as entidades duráveis serão descarregadas silenciosamente da memória.

No entanto, há algumas diferenças importantes que valem a pena observar:

* As entidades duráveis priorizam a *durabilidade* em relação à *latência*e, portanto, podem não ser apropriadas para aplicativos com requisitos estritos de latência.
* As mensagens enviadas entre as entidades são entregues de forma confiável e em ordem.
* As entidades duráveis podem ser usadas em conjunto com orquestrações duráveis e podem servir como bloqueios distribuídos, que são descritos posteriormente neste artigo.
* Os padrões de solicitação/resposta em entidades são limitados a orquestrações. Para a comunicação entidade a entidade, somente mensagens unidirecionais (também conhecidas como "sinalização") são permitidas, como no modelo de ator original. Esse comportamento impede deadlocks distribuídos.

### <a name="durable-entity-net-apis"></a>APIs do .NET para entidades duráveis

O suporte a entidades envolve várias APIs. Para um, há uma nova API para definir funções de entidade, como mostrado acima, que especifica o que deve acontecer quando uma operação é invocada em uma entidade. Além disso, as APIs existentes para clientes e orquestrações foram atualizadas com novas funcionalidades para interação com entidades.

#### <a name="implementing-entity-operations"></a>Implementando operações de entidade

A execução de uma operação em uma entidade pode chamar esses membros no objeto de contexto (`IDurableEntityContext` no .net):

* **OperationName**: Obtém o nome da operação.
* **> TInput\<getinput**: Obtém a entrada para a operação.
* **> TState\<GetState**: Obtém o estado atual da entidade.
* **SetState**: atualiza o estado da entidade.
* **SignalEntity**: envia uma mensagem unidirecional para uma entidade.
* **Self**: Obtém a ID da entidade.
* **Retornar**: retorna um valor para o cliente ou orquestração que chamou a operação.
* **IsNewlyConstructed**: retorna `true` se a entidade não existia antes da operação.
* **DestructOnExit**: exclui a entidade depois de concluir a operação.

As operações são menos restritas do que as orquestrações:

* As operações podem chamar e/s externa, usando APIs síncronas ou assíncronas (Recomendamos apenas o uso de itens assíncronos).
* As operações podem ser não determinísticas. Por exemplo, é seguro chamar `DateTime.UtcNow` `Guid.NewGuid()` ou `new Random()`.

#### <a name="accessing-entities-from-clients"></a>Acessando entidades de clientes

Entidades duráveis podem ser invocadas a partir de funções `orchestrationClient` comuns por`IDurableOrchestrationClient` meio da Associação (em .net). Os métodos a seguir têm suporte:

* **ReadEntityStateAsync\<T >** : lê o estado de uma entidade.
* **SignalEntityAsync**: envia uma mensagem unidirecional para uma entidade e aguarda que ela seja enfileirada.
* **SignalEntityAsync\<T >** :igual`T`a ,masusaumobjetoproxy`SignalEntityAsync` gerado do tipo.

A chamada `SignalEntityAsync` anterior requer a especificação do nome da operação de entidade como `string` um e a carga da operação como um `object`. O código de exemplo a seguir é um exemplo desse padrão:

```csharp
EntityId id = // ...
object amount = 5;
context.SignalEntityAsync(id, "Add", amount);
```

Também é possível gerar um objeto proxy para acesso de tipo seguro. Para gerar um proxy de tipo seguro, o tipo de entidade deve implementar uma interface. Por exemplo, suponha que `Counter` a entidade mencionada anteriormente `ICounter` implementou uma interface, definida da seguinte maneira:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    int Get();
}

public class Counter : ICounter
{
    // ...
}
```

O código do cliente poderia `SignalEntityAsync<T>` , então, `ICounter` usar e especificar a interface como o parâmetro de tipo para gerar um proxy de tipo seguro. Esse uso de proxies de tipo seguro é demonstrado no exemplo de código a seguir:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [OrchestrationClient] IDurableOrchestrationClient client)
{
    int amount = int.Parse(message);
    var target = new EntityId(nameof(Counter), "MyCounter");
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

No exemplo anterior, `proxy` o parâmetro é uma instância gerada dinamicamente do `ICounter`, que converte internamente a chamada para `Add` na chamada equivalente (não tipada) para `SignalEntityAsync`.

> [!NOTE]
> É importante observar que os métodos e `ReadEntityStateAsync` `SignalEntityAsync` de priorização `IDurableOrchestrationClient` do desempenho em relação à consistência. `ReadEntityStateAsync`pode retornar um valor obsoleto e `SignalEntityAsync` pode retornar antes que a operação seja concluída.

#### <a name="accessing-entities-from-orchestrations"></a>Acessando entidades de orquestrações

As orquestrações podem acessar entidades usando `IDurableOrchestrationContext` o objeto. Eles podem escolher entre a comunicação unidirecional (incêndio e esquecido) e a comunicação bidirecional (solicitação e resposta). Os respectivos métodos são:

* **SignalEntity**: envia uma mensagem unidirecional para uma entidade.
* **CallEntityAsync**: envia uma mensagem a uma entidade e aguarda uma resposta indicando que a operação foi concluída.
* **CallEntityAsync\<T >** : envia uma mensagem para uma entidade e aguarda uma resposta que contém um resultado do tipo T.

Ao usar a comunicação bidirecional, todas as exceções geradas durante a execução da operação também são transmitidas de volta para a orquestração de chamada e relançadas. Por outro lado, ao usar acionar e esquecer, as exceções não são observadas.

Para acesso de tipo seguro, as funções de orquestração podem gerar proxies com base em uma interface. O `CreateEntityProxy` método de extensão pode ser usado para essa finalidade:

```csharp
public interface IAsyncCounter
{
    Task AddAsync(int amount);
    Task ResetAsync();
    Task<int> GetAsync();
}

[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...
    IAsyncCounter proxy = context.CreateEntityProxy<IAsyncCounter>("MyCounter");
    await proxy.AddAsync(5);
    int newValue = await proxy.GetAsync();
    // ...
}
```

No exemplo anterior, supõe-se que uma entidade "Counter" existia, que `IAsyncCounter` implementa a interface. A orquestração foi então capaz de usar a `IAsyncCounter` definição de tipo para gerar um tipo de proxy para interagir de forma síncrona com a entidade.

### <a name="locking-entities-from-orchestrations"></a>Bloqueando entidades de orquestrações

As orquestrações podem bloquear entidades. Esse recurso fornece uma maneira simples de evitar corridas indesejadas usando *seções críticas*.

O objeto Context fornece os seguintes métodos:

* **LockAsync**: adquire bloqueios em uma ou mais entidades.
* IsLocked: retorna true se estiver atualmente em uma seção crítica; caso contrário, retornará false.

A seção crítica termina e todos os bloqueios são liberados quando a orquestração termina. No .net, `LockAsync` retorna um `IDisposable` que termina a seção crítica quando descartado, que pode ser usado junto com uma `using` cláusula para obter uma representação sintática da seção crítica.

Por exemplo, considere uma orquestração que precisa testar se dois players estão disponíveis e, em seguida, atribuí-los a um jogo. Essa tarefa pode ser implementada usando uma seção crítica da seguinte maneira:

```csharp
[FunctionName("Orchestrator")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext ctx)
{
    EntityId player1 = /* ... */;
    EntityId player2 = /* ... */;

    using (await ctx.LockAsync(player1, player2))
    {
        bool available1 = await ctx.CallEntityAsync<bool>(player1, "is-available");
        bool available2 = await ctx.CallEntityAsync<bool>(player2, "is-available");

        if (available1 && available2)
        {
            Guid gameId = ctx.NewGuid();

            await ctx.CallEntityAsync(player1, "assign-game", gameId);
            await ctx.CallEntityAsync(player2, "assign-game", gameId);
        }
    }
}
```

Na seção crítica, ambas as entidades do jogador estão bloqueadas, o que significa que elas não estão executando nenhuma operação diferente daquelas que são chamadas de dentro da seção crítica). Esse comportamento impede corridas com operações conflitantes, como os jogadores sendo atribuídos a um jogo diferente ou a assinatura.

Impõem várias restrições sobre como seções críticas podem ser usadas. Essas restrições servem para evitar deadlocks e reentrância.

* Não é possível aninhar seções críticas.
* As seções críticas não podem criar suborquestrações.
* As seções críticas podem chamar somente as entidades que foram bloqueadas.
* As seções críticas não podem chamar a mesma entidade usando várias chamadas paralelas.
* As seções críticas só podem sinalizar entidades que não tenham sido bloqueadas.

## <a name="alternate-storage-providers"></a>Provedores de armazenamento alternativos

O Framework de tarefa durável dá suporte a vários provedores de armazenamento hoje, incluindo o [armazenamento do Azure](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), o [barramento de serviço do Azure](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), um [emulador na memória](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)e um provedor de [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) experimental. No entanto, até agora, a extensão de tarefa durável para Azure Functions só tem suporte no provedor de armazenamento do Azure. A partir do Durable Functions 2,0, o suporte para provedores de armazenamento alternativo está sendo adicionado, começando com o provedor Redis.

> [!NOTE]
> Durable Functions 2,0 dá suporte apenas a provedores compatíveis com .NET Standard 2,0. No momento da gravação, o provedor do barramento de serviço do Azure não dá suporte ao .NET Standard 2,0 e, portanto, não está disponível como um provedor de armazenamento alternativo.

### <a name="emulator"></a>Emulator

O provedor [DurableTask. Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) é uma memória local, um provedor de armazenamento não durável adequado para cenários de teste local. Ele pode ser configurado usando o seguinte esquema de **host. JSON** mínimo:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (experimental)

O provedor [DurableTask. Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) persiste todo o estado de orquestração para um cluster Redis configurado.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

O `connectionStringName` deve referenciar o nome de uma configuração de aplicativo ou variável de ambiente. Essa configuração de aplicativo ou a variável de ambiente deve conter um valor de cadeia de conexão Redis na forma de *Server: Port*. Por exemplo, `localhost:6379` para se conectar a um cluster Redis local.

> [!NOTE]
> O provedor Redis é experimental no momento e só dá suporte a aplicativos de funções em execução em um único nó. Não há garantia de que o provedor Redis será disponibilizado para o público geral e poderá ser removido em uma versão futura.
