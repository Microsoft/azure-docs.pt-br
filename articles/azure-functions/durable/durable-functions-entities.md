---
title: Entidades duráveis – Azure Functions
description: Saiba o que são entidades duráveis e como usá-las na extensão Durable Functions do Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: c898444659c2ce071163e9ab774a4534f8c51a9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632043"
---
# <a name="entity-functions"></a>Funções de entidade

As funções de entidade definem operações para leitura e atualização de pequenas partes de estado, conhecidas como *entidades duráveis*. Como as funções de orquestrador, as funções de entidade são funções com um tipo de gatilho especial, o *gatilho de entidade*. Ao contrário das funções de orquestrador, as funções de entidade gerenciam o estado de uma entidade explicitamente, em vez de representar implicitamente o estado por meio do fluxo de controle.
As entidades fornecem um meio para escalar horizontalmente os aplicativos, distribuindo o trabalho entre várias entidades, cada uma com um estado de tamanho modesto.

> [!NOTE]
> As funções de entidade e a funcionalidade relacionada estão disponíveis apenas nas [Durable Functions 2.0](durable-functions-versions.md#migrate-from-1x-to-2x) e posteriores. Atualmente, elas são compatíveis com .NET, JavaScript e Python.

## <a name="general-concepts"></a>Conceitos gerais

As entidades se comportam de modo um pouco semelhante a pequenos serviços que se comunicam por meio de mensagens. Cada entidade tem uma identidade exclusiva e um estado interno (se existir). Assim como os serviços ou objetos, as entidades executam operações quando é solicitado a elas que o façam. Quando uma operação é executada, ela pode atualizar o estado interno da entidade. Ela também pode chamar serviços externos e aguardar uma resposta. As entidades se comunicam com outras entidades, orquestrações e clientes usando mensagens que são enviadas implicitamente por meio de filas confiáveis. 

Para evitar conflitos, todas as operações em uma única entidade têm garantia de execução em série, ou seja, uma após a outra.

> [!NOTE]
> Quando uma entidade é invocada, ela processa a carga até a conclusão e, em seguida, agenda uma nova execução para ativar assim que chegarem entradas futuras. Como resultado, os logs de execução da entidade podem mostrar uma execução adicional após cada invocação de entidade. Isso é esperado.

### <a name="entity-id"></a>ID da Entidade
As entidades são acessadas por meio de um identificador exclusivo, a *ID da entidade*. Uma ID de entidade é um par de cadeias de caracteres que identifica exclusivamente uma instância de entidade. Ele consiste em um(a):

* **Nome de entidade**, que é um nome que identifica o tipo da entidade. Um exemplo é "Counter". Esse nome deve corresponder ao nome da função de entidade que implementa a entidade. Ele não diferencia maiúsculas e minúsculas.
* **Chave da entidade**, que é uma cadeia de caracteres que identifica exclusivamente a entidade entre todas as outras entidades de mesmo nome. Um exemplo é um GUID.

Por exemplo, uma função de entidade `Counter` pode ser usada para manter a pontuação em um jogo online. Cada instância do jogo terá uma ID de entidade exclusiva, como `@Counter@Game1` e `@Counter@Game2`. Todas as operações que direcionam uma entidade específica exigem a especificação de uma ID de entidade como um parâmetro.

### <a name="entity-operations"></a>Operações de entidade ###

Para invocar uma operação em uma entidade, especifique o(a):

* **ID da entidade** de destino.
* **Nome da operação**, que é uma cadeia de caracteres que especifica a operação a ser executada. Por exemplo, a entidade `Counter` poderia dar suporte às operações `add`, `get` ou `reset`.
* **Entrada da operação**, que é um parâmetro de entrada opcional para a operação. Por exemplo, a operação de adição pode usar um valor inteiro como a entrada.
* **Horário agendado**, que é um parâmetro opcional para especificar o a hora de entrega da operação. Por exemplo, uma operação pode ser agendada de maneira confiável para ser executada vários dias no futuro.

As operações podem retornar um valor de resultado ou um resultado de erro, como um erro de JavaScript ou uma exceção .NET. Esse resultado ou erro pode ser observado por orquestrações que chamaram a operação.

Uma operação de entidade também pode criar, ler, atualizar e excluir o estado da entidade. O estado da entidade é sempre persistido no armazenamento de modo durável.

## <a name="define-entities"></a>Definir entidades

Atualmente, as duas APIs distintas para definição de entidades são:

**Sintaxe baseada em função**, em que as entidades são representadas como funções e as operações são expedidas explicitamente pelo aplicativo. Essa sintaxe funciona bem para entidades com estado simples, poucas operações ou um conjunto dinâmico de operações como em estruturas do aplicativo. Manter essa sintaxe pode ser uma tarefa entediante porque ela não captura erros de tipo no tempo de compilação.

**Sintaxe baseada em classe** (somente .NET), em que as entidades e operações são representadas por classes e métodos. Essa sintaxe produz um código mais fácil de ler e permite que as operações sejam invocadas de modo fortemente tipado. A sintaxe baseada em classe é uma camada fina sobre a sintaxe baseada em função, de modo que essas duas variantes podem ser usadas de maneira intercambiável no mesmo aplicativo.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="example-function-based-syntax---c"></a>Exemplo: Sintaxe baseada em função – C#

O código a seguir é um exemplo da entidade `Counter` simples implementada como uma função durável. Essa função define três operações, `add`, `reset` e `get`, cada uma delas opera em um estado de inteiro.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

Para saber mais sobre a sintaxe baseada em função e como usá-la, confira [Sintaxe baseada em função](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="example-class-based-syntax---c"></a>Exemplo: Sintaxe baseada em classe – C#

O exemplo a seguir é uma implementação equivalente da entidade `Counter` usando métodos e classes.

```csharp
[JsonObject(MemberSerialization.OptIn)]
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

O estado dessa entidade é um objeto do tipo `Counter`, que contém um campo que armazena o valor atual do contador. Para persistir esse objeto no armazenamento, ele é serializado e desserializado pela biblioteca [Json.NET](https://www.newtonsoft.com/json). 

Para obter mais informações sobre a sintaxe baseada em classe e como usá-la, confira [Definir classes de entidade](durable-functions-dotnet-entities.md#defining-entity-classes).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="example-javascript-entity"></a>Exemplo: entidade do JavaScript

As entidades duráveis estão disponíveis em JavaScript a partir da versão **1.3.0** do pacote npm `durable-functions`. O código a seguir é uma entidade `Counter` implementada como uma função durável escrita em JavaScript.

**Counter/function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**Counter/index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```
# <a name="python"></a>[Python](#tab/python)

### <a name="example-python-entity"></a>Exemplo: entidade do Python

O código a seguir é uma entidade `Counter` implementada como uma função durável escrita em Python.

**Counter/function.json**
```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ]
}
```

**Counter/__init__.py**
```Python
import azure.functions as func
import azure.durable_functions as df


def entity_function(context: df.DurableEntityContext):
    current_value = context.get_state(lambda: 0)
    operation = context.operation_name
    if operation == "add":
        amount = context.get_input()
        current_value += amount
    elif operation == "reset":
        current_value = 0
    elif operation == "get":
        context.set_result(current_value)
    context.set_state(current_value)


main = df.Entity.create(entity_function)
```
---

## <a name="access-entities"></a>Acessar entidades

As entidades podem ser acessadas usando uma comunicação unidirecional ou bidirecional. A terminologia a seguir distingue as duas formas de comunicação: 

* **Chamar** uma entidade usa a comunicação bidirecional (viagem de ida e volta). Você envia uma mensagem de operação à entidade e aguarda a mensagem de resposta antes de continuar. A mensagem de resposta pode fornecer um valor de resultado ou um resultado de erro, como um erro de JavaScript ou uma exceção .NET. Esse resultado ou erro é então observado pelo chamador.
* **Sinalizar** uma entidade usa a comunicação unidirecional (acionar e esquecer). Você envia uma mensagem de operação, mas não aguarda uma resposta. Embora haja garantia de que a mensagem seja entregue, o remetente não sabe quando e não pode observar qualquer erro ou valor de resultado.

As entidades podem ser acessadas de dentro de funções de cliente, de dentro de funções de orquestrador ou de dentro de funções de entidade. Nem todas as formas de comunicação são compatíveis com todos os contextos:

* De dentro dos clientes, você pode sinalizar as entidades e pode ler o estado da entidade.
* De dentro das orquestrações, você pode sinalizar as entidades e pode chamar entidades.
* De dentro das entidades, você pode sinalizar entidades.

Os exemplos a seguir ilustram essas várias maneiras de acessar entidades.

### <a name="example-client-signals-an-entity"></a>Exemplo: o cliente sinaliza a uma entidade

Para acessar entidades de uma função comum do Azure, também conhecida como função de cliente, use a [associação do cliente da entidade](durable-functions-bindings.md#entity-client). O exemplo a seguir mostra uma função disparada por fila sinalizando uma entidade que usa essa associação.

# <a name="c"></a>[C#](#tab/csharp)

> [!NOTE]
> Para simplificar, os exemplos a seguir mostram a sintaxe superficialmente tipada para acessar entidades. Em geral, é recomendável [acessar entidades por meio de interfaces](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) porque isso fornece mais verificação de tipo.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

# <a name="python"></a>[Python](#tab/python)

```Python
from azure.durable_functions import DurableOrchestrationClient
import azure.functions as func


async def main(req: func.HttpRequest, starter: str, message):
    client = DurableOrchestrationClient(starter)
    entityId = df.EntityId("Counter", "myCounter")
    await client.signal_entity(entityId, "add", 1)
```

---

O termo *sinal* significa que a invocação de API de entidade é unidirecional e assíncrona. Não é possível que uma função do cliente saiba quando a entidade processou a operação. Além disso, a função do cliente não pode observar nenhum valor de resultado ou exceções. 

### <a name="example-client-reads-an-entity-state"></a>Exemplo: o cliente lê o estado de uma entidade

As funções de cliente também podem consultar o estado de uma entidade, conforme mostrado no seguinte exemplo:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    EntityStateResponse<JObject> stateResponse = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, stateResponse.EntityState);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    const stateResponse = await client.readEntityState(entityId);
    return stateResponse.entityState;
};
```

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> No momento, o Python não dá suporte à leitura dos estados de entidade de um cliente. Em vez disso, use o `callEntity` de um orquestrador.

---

As consultas de estado de entidade são enviadas ao armazenamento de acompanhamento Durável e retornam o estado persistido mais recentemente da entidade. Esse estado é sempre um estado "confirmado", ou seja, ele nunca é um estado intermediário temporário assumido no meio da execução de uma operação. No entanto, é possível que esse estado seja obsoleto se comparado com o estado na memória da entidade. Somente orquestrações podem ler o estado na memória de uma entidade, conforme descrito na seção a seguir.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Exemplo: a orquestração sinaliza e chama uma entidade

As funções de orquestrador podem acessar entidades usando APIs na [associação de gatilho de orquestração](durable-functions-bindings.md#orchestration-trigger). O código de exemplo a seguir mostra uma função de orquestrador chamando e sinalizando uma entidade `Counter`.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
});
```

> [!NOTE]
> Atualmente, o JavaScript não é compatível com a sinalização de uma entidade de um orquestrador. Use `callEntity` em vez disso.

# <a name="python"></a>[Python](#tab/python)

```Python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    entityId = df.EntityId("Counter", "myCounter")
    current_value = yield context.call_entity(entityId, "get")
    if current_value < 10:
        context.signal_entity(entityId, "add", 1)
    return state
```

---

Somente orquestrações são capazes de chamar entidades e obter uma resposta, que poderia ser um valor retornado ou uma exceção. As funções de cliente que usam a [associação de cliente](durable-functions-bindings.md#entity-client) só podem sinalizar entidades.

> [!NOTE]
> Chamar uma entidade de uma função de orquestrador é semelhante a chamar uma [função de atividade](durable-functions-types-features-overview.md#activity-functions) de uma função de orquestrador. A principal diferença é que as funções de entidade são objetos duráveis com um endereço, que é a ID da entidade. As funções de entidade permitem a especificação de um nome de operação. As funções de atividade, por outro lado, são sem estado e não têm o conceito das operações.

### <a name="example-entity-signals-an-entity"></a>Exemplo: a entidade sinaliza uma entidade

Uma função de entidade pode enviar sinais para outras entidades ou até mesmo para si própria enquanto executa uma operação.
Por exemplo, podemos modificar o exemplo de entidade `Counter` anterior para que ela envie um sinal de "marco-atingido" para alguma entidade de monitor quando o contador atingir o valor 100.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> O Python ainda não oferece suporte a sinais de entidade para entidade. Em vez disso, use um orquestrador para sinalizar entidades.

---

## <a name="entity-coordination-currently-net-only"></a><a name="entity-coordination"></a>Coordenação de entidades (no momento, somente .NET)

Pode haver ocasiões em que você precise coordenar operações em várias entidades. Por exemplo, em um aplicativo bancário, você pode ter entidades que representam contas bancárias individuais. Ao transferir fundos de uma conta para outra, é preciso garantir que a conta de origem tenha fundos suficientes. Também é preciso garantir que as atualizações para as contas de origem e de destino sejam realizadas de maneira transacionalmente consistente.

### <a name="example-transfer-funds-c"></a>Exemplo: Transferir fundos (C#)

O código de exemplo a seguir transfere fundos entre duas entidades de conta usando uma função de orquestrador. A coordenação das atualizações da entidade requer o uso do método `LockAsync` para criar uma _seção crítica_ na orquestração.

> [!NOTE]
> Para simplificar, este exemplo reutiliza a entidade `Counter` definida anteriormente. Em um aplicativo real, seria melhor definir uma entidade `BankAccount` mais detalhada.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

No .NET, `LockAsync` retorna `IDisposable`, que encerra a seção crítica quando descartado. Esse resultado `IDisposable` pode ser usado junto com um bloco `using` para obter uma representação sintática da seção crítica.

No exemplo anterior, uma função de orquestrador transferiu fundos de uma entidade de origem para uma entidade de destino. O método `LockAsync` bloqueou as entidades de conta de origem e de destino. Com esse bloqueio, nenhum outro cliente pôde consultar ou modificar o estado de qualquer conta até que a lógica da orquestração saísse da seção crítica no fim da instrução `using`. Esse comportamento evita a possibilidade de que a conta de origem efetue saque a descoberto.

> [!NOTE] 
> Quando uma orquestração é encerrada, normalmente ou com um erro, todas as seções críticas em andamento são encerradas implicitamente e todos os bloqueios são liberados.

### <a name="critical-section-behavior"></a>Comportamento da seção crítico

O método `LockAsync` cria uma seção crítica em uma orquestração. Essas seções críticas impedem que outras orquestrações façam alterações sobrepostas em um conjunto especificado de entidades. Internamente, a API `LockAsync` envia operações de "bloqueio" para as entidades e retorna quando recebe uma mensagem de resposta de "bloqueio adquirido" de cada uma dessas mesmas entidades. O bloqueio e o desbloqueio são operações internas compatíveis com todas as entidades.

Nenhuma operação de outros clientes é permitida em uma entidade enquanto ela está em um estado bloqueado. Esse comportamento verifica se apenas uma instância de orquestração pode bloquear uma entidade por vez. Se um chamador tentar invocar uma operação em uma entidade enquanto ela estiver bloqueada por uma orquestração, essa operação será colocada em uma fila de operação pendente. Nenhuma operação pendente será processada até que a orquestração em retenção libere seu bloqueio.

> [!NOTE] 
> Esse comportamento é um pouco diferente dos primitivos de sincronização usados na maioria das linguagens de programação, como a instrução `lock` em C#. Por exemplo, em C#, a instrução `lock` deve ser usada por todos os threads para garantir a sincronização apropriada em vários threads. Contudo, as entidades não exigem que todos os chamadores bloqueiem explicitamente uma entidade. Se qualquer chamador bloquear uma entidade, todas as outras operações nessa entidade serão bloqueadas e enfileiradas atrás desse bloqueio.

Os bloqueios em entidades são duráveis. Portanto, eles persistirão mesmo se o processo de execução for reciclado. Os bloqueios são persistidos internamente como parte do estado durável de uma entidade.

Ao contrário das transações, as seções críticas não revertem automaticamente as alterações em caso de erros. Em vez disso, qualquer tratamento de erro, como reversão ou repetição, precisa ser codificado explicitamente; por exemplo, capturando erros ou exceções. Essa opção de design é intencional. Em geral, a reversão automática de todos os efeitos de uma orquestração é difícil ou impossível porque as orquestrações podem executar atividades e fazer chamadas para serviços externos que não podem ser revertidos. Além disso, as tentativas de reverter podem falhar e exigir tratamento de erro adicional.

### <a name="critical-section-rules"></a>Regras de seção críticas

Ao contrário do que ocorre com os primitivos de bloqueio de nível baixo na maioria das linguagens de programação, com as seções críticas *há garantia de que não ocorrerá deadlock*. Para evitar deadlocks, impomos as seguintes restrições: 

* As seções críticas não podem ser aninhadas.
* As seções críticas não podem criar suborquestrações.
* As seções críticas podem chamar somente entidades que elas bloquearam.
* As seções críticas não podem chamar a mesma entidade usando várias chamadas paralelas.
* As seções críticas podem sinalizar apenas entidades que elas não bloquearam.

Quaisquer violações dessas regras causam um erro de runtime, como `LockingRulesViolationException` no .NET, que inclui uma mensagem explicando qual regra foi violada.

## <a name="comparison-with-virtual-actors"></a>Comparação com atores virtuais

Muitos recursos das entidades duráveis são inspirados pelo [modelo de ator](https://en.wikipedia.org/wiki/Actor_model). Se já estiver familiarizado com atores, você poderá reconhecer muitos conceitos descritos neste artigo. Entidades duráveis são particularmente semelhantes a [atores virtuais](https://research.microsoft.com/projects/orleans/), ou grãos, conforme popularizado pelo [projeto Orleans](http://dotnet.github.io/orleans/). Por exemplo:

* As entidades duráveis podem ser abordadas por meio de uma ID de entidade.
* As operações de entidade durável são executadas de maneira serial, uma por vez, para impedir condições de corrida.
* As entidades duráveis são criadas implicitamente quando são chamadas ou sinalizadas.
* Quando não executam operações, as entidades duráveis são descarregadas silenciosamente da memória.

Há algumas diferenças importantes que vale a pena serem observadas:

* As entidades duráveis priorizam a durabilidade em relação à latência e, portanto, podem não ser apropriadas para aplicativos com requisitos estritos de latência.
* Entidades duráveis não têm tempos limite internos para mensagens. No Orleans, todas as mensagens atingem o tempo limite após um tempo configurável. O padrão é 30 segundos.
* As mensagens enviadas entre entidades são entregues de maneira confiável e em ordem. No Orleans, a entrega confiável ou ordenada é compatível com conteúdo enviado por meio de fluxos, mas não é garantida para todas as mensagens entre os grãos.
* Os padrões de solicitação-resposta em entidades estão limitados a orquestrações. De dentro das entidades, apenas o sistema de mensagens unidirecional (também conhecido como sinalização) é permitido, assim como no modelo de ator original e ao contrário dos grãos no Orleans. 
* As entidades duráveis não geram deadlock. No Orleans, os deadlocks podem ocorrer e não se resolver até atingir o tempo limite das mensagens.
* As entidades duráveis podem ser usadas em conjunto com orquestrações duráveis e dão suporte a mecanismos de bloqueio distribuídos. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Leia o guia do desenvolvedor para entidades duráveis no .NET](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Saiba sobre os hubs de tarefa](durable-functions-task-hubs.md)
