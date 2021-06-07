---
title: Lidando com eventos externos nas Funções Duráveis – Azure
description: Saiba como lidar com eventos externos na extensão de Funções Duráveis do Azure Functions.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: c08306edcea02a9207ab5a15eb62b7fffc2ecb44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576322"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Lidando com eventos externos nas Funções Duráveis (Azure Functions)

Funções de orquestrador têm a capacidade de aguardar e escutar eventos externos. Frequentemente, esse recurso das [Funções Duráveis](durable-functions-overview.md) é útil para lidar com interação humana ou com outros gatilhos externos.

> [!NOTE]
> Eventos externos são operações assíncronas unidirecionais. Não são adequados para situações em que o cliente que envia o evento precisa de uma resposta síncrona da função de orquestrador.

## <a name="wait-for-events"></a>Aguardar eventos

Os métodos [WaitForExternalEvent](/dotnet/api/microsoft.azure.webjobs.durableorchestrationcontextbase.waitforexternalevent?view=azure-dotnet-legacy) (.net), `waitForExternalEvent` (JavaScript) e `wait_for_external_event` (Python) da Associação de [gatilho de orquestração](durable-functions-bindings.md#orchestration-trigger) permitem que uma função de orquestrador Espere e escute um evento externo de forma assíncrona. A função do orquestrador que está escutando declara o *nome* do evento e a *forma dos dados* que espera receber.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    approved = context.wait_for_external_event('Approval')
    if approved:
        # approval granted - do the approved action
    else:
        # approval denied - send a notification

main = df.Orchestrator.create(orchestrator_function)
```

---

O exemplo anterior escuta um único evento específico e entra em ação quando ele é recebido.

Você pode escutar vários eventos ao mesmo tempo, como no exemplo a seguir, que aguarda uma de três notificações de evento possíveis.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    event1 = context.wait_for_external_event('Event1')
    event2 = context.wait_for_external_event('Event2')
    event3 = context.wait_for_external_event('Event3')

    winner = context.task_any([event1, event2, event3])
    if winner == event1:
        # ...
    elif winner == event2:
        # ...
    elif winner == event3:
        # ...

main = df.Orchestrator.create(orchestrator_function)
```

---

O exemplo anterior escuta *qualquer* um de vários eventos. Também é possível aguardar *todos* os eventos.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

> [!NOTE]
> O código anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

No .NET, se a carga do evento não puder ser convertida no `T` do tipo esperado, uma exceção será lançada.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    application_id = context.get_input()
    
    gate1 = context.wait_for_external_event('CityPlanningApproval')
    gate2 = context.wait_for_external_event('FireDeptApproval')
    gate3 = context.wait_for_external_event('BuildingDeptApproval')

    yield context.task_all([gate1, gate2, gate3])
    yield context.call_activity('IssueBuildingPermit', application_id)

main = df.Orchestrator.create(orchestrator_function)
```

---

`WaitForExternalEvent` aguarda indefinidamente por alguma entrada.  O aplicativo de funções pode ser descarregado com segurança enquanto aguarda. Se um evento chegar para esta instância de orquestração, ela será ativada automaticamente e processará imediatamente o evento.

> [!NOTE]
> Se o seu aplicativo de funções usar o plano de consumo, não serão cobradas tarifas de cobrança enquanto uma função de orquestrador estiver aguardando uma tarefa de `WaitForExternalEvent` (.net), `waitForExternalEvent` (JavaScript) ou `wait_for_external_event` (Python), não importa quanto tempo espera.

## <a name="send-events"></a>Enviar eventos

Você pode usar os métodos [RaiseEventAsync](/dotnet/api/microsoft.azure.webjobs.durableorchestrationclientbase.raiseeventasync?view=azure-dotnet-legacy) (.net) ou `raiseEventAsync` (JavaScript) para enviar um evento externo para uma orquestração. Esses métodos são expostos pela Associação de [cliente de orquestração](durable-functions-bindings.md#orchestration-client) . Você também pode usar a [API http de evento de elevação](durable-functions-http-api.md#raise-event) interna para enviar um evento externo para uma orquestração.

Um evento gerado inclui uma *ID de instância*, um *EventName* e um *EVENTDATA* como parâmetros. As funções de orquestrador manipulam esses eventos usando as `WaitForExternalEvent` APIs (.net) ou `waitForExternalEvent` (JavaScript). O *EventName* deve corresponder nas extremidades de envio e de recebimento para que o evento seja processado. Os dados de evento também devem ser serializáveis em JSON.

Internamente, os mecanismos de "evento de elevação" enfileiram uma mensagem que é selecionada pela função de orquestrador em espera. Se a instância não está aguardando o *nome do evento* especificado, a mensagem de evento é adicionada a uma fila na memória. Se posteriormente a instância de orquestração começa a escutar esse *nome do evento*, ela verifica a fila de mensagens de evento.

> [!NOTE]
> Se não houver nenhuma instância de orquestração com a *ID da instância* especificada, a mensagem de evento é descartada.

Abaixo, temos um exemplo de função disparada em fila que envia um evento de "Aprovação" para uma instância de função de orquestrador. A ID da instância de orquestração vem do corpo da mensagem da fila.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve usar o `DurableOrchestrationClient` tipo de parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(instance_id:str, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    await client.raise_event(instance_id, 'Approval', True)
```

---

Internamente, `RaiseEventAsync` (.net), `raiseEvent` (JavaScript) ou `raise_event` (Python) enfileira uma mensagem que é selecionada pela função de orquestrador em espera. Se a instância não está aguardando o *nome do evento* especificado, a mensagem de evento é adicionada a uma fila na memória. Se posteriormente a instância de orquestração começa a escutar esse *nome do evento*, ela verifica a fila de mensagens de evento.

> [!NOTE]
> Se não houver nenhuma instância de orquestração com a *ID da instância* especificada, a mensagem de evento é descartada.

### <a name="http"></a>HTTP

Veja a seguir um exemplo de uma solicitação HTTP que gera um evento de "aprovação" para uma instância de orquestração. 

```http
POST /runtime/webhooks/durabletask/instances/MyInstanceId/raiseEvent/Approval&code=XXX
Content-Type: application/json

"true"
```

Nesse caso, a ID da instância é codificada como *Myinstanceid*.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como implementar o tratamento de erros](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Executar um exemplo que aguarda interação humana](durable-functions-phone-verification.md)