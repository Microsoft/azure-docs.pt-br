---
title: Orquestrações eternas nas Funções Duráveis – Azure
description: Saiba como implementar orquestrações eternas usando a extensão de Funções Duráveis do Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 82108ae0b2cabaab6dfa47c8bb5e893a44df38af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182053"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Orquestrações eternas nas Funções Duráveis (Azure Functions)

*Orquestrações eternas* são funções de orquestração que nunca chegam ao fim. Elas são úteis quando você deseja usar [Funções Duráveis](durable-functions-overview.md) para agregadores e para qualquer cenário que exige um loop infinito.

## <a name="orchestration-history"></a>Histórico de orquestração

Conforme explicado no tópico do [histórico de orquestração](durable-functions-orchestrations.md#orchestration-history) , o Framework de tarefa durável controla o histórico de cada orquestração de função. Esse histórico cresce continuamente, desde que a função de orquestrador continue agendando novos trabalhos. Se a função de orquestrador entrar em um loop infinito e agendar trabalho continuamente, esse histórico poderá ficar muito grande e causar problemas de desempenho significativos. O conceito de *orquestração eterna* foi criado para mitigar esse tipo de problema para aplicativos que precisam de loops infinitos.

## <a name="resetting-and-restarting"></a>Redefinir e reiniciar

Em vez de usar loops infinitos, as funções de orquestrador redefinem seu estado chamando o `ContinueAsNew` método (.net), `continueAsNew` (JavaScript) ou `continue_as_new` (Python) da [Associação de gatilho de orquestração](durable-functions-bindings.md#orchestration-trigger). Esse método usa um único parâmetro serializável em JSON, que se torna a nova entrada para geração da próxima função de orquestrador.

Quando `ContinueAsNew` é chamado, a instância enfileira uma mensagem para si mesma antes de sair. A mensagem reinicia a instância com o novo valor de entrada. A mesma ID de instância é mantida, mas o histórico da função de orquestrador efetivamente é truncado.

> [!NOTE]
> O Framework de Tarefa Durável mantém a mesma ID de instância, mas internamente cria uma nova *ID de execução* para a função de orquestrador que é redefinida por `ContinueAsNew`. A ID dessa execução geralmente não é exposta externamente, mas pode ser útil conhecê-la ao depurar a execução da orquestração.

## <a name="periodic-work-example"></a>Exemplo de trabalho periódico

Um caso de uso das orquestrações eternas é o código que precisa realizar trabalho periódico indefinidamente.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

> [!NOTE]
> O exemplo anterior de C# é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup.toDate());

    yield context.df.continueAsNew(undefined);
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    yield context.call_activity("DoCleanup")

    # sleep for one hour between cleanups
    next_cleanup = context.current_utc_datetime + timedelta(hours = 1)
    yield context.create_timer(next_cleanup)

    context.continue_as_new(None)

main = df.Orchestrator.create(orchestrator_function)
```

---

A diferença entre esse exemplo e uma função disparada por temporizador é que os tempos de gatilho de limpeza aqui não se baseiam em uma agenda. Por exemplo, uma agenda CRON que executa uma função a cada hora a executará às 1:00, 2:00, 3:00 etc. e, potencialmente, poderia se deparar com problemas de sobreposição. Neste exemplo, no entanto, se a limpeza levar 30 minutos, ela será agendada às 1:00, 2:30, 4:00 etc. e não haverá chance de sobreposição.

## <a name="starting-an-eternal-orchestration"></a>Iniciando uma orquestração de eternas

Use o `StartNewAsync` método (.net), o `startNew` (JavaScript), `start_new` (Python) para iniciar uma orquestração eternas, assim como faria com qualquer outra função de orquestração.  

> [!NOTE]
> Se você precisar garantir que uma orquestração singleton eternas esteja em execução, é importante manter a mesma instância `id` ao iniciar a orquestração. Para obter mais informações, consulte [Gerenciamento de Instâncias](durable-functions-instance-management.md).

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";

    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> O código anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve usar o `DurableOrchestrationClient` tipo de parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = "StaticId";
    
    // null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.startNew("Periodic_Cleanup_Loop", instanceId, null);

    context.log(`Started orchestration with ID = '${instanceId}'.`);
    return client.createCheckStatusResponse(context.bindingData.req, instanceId);
};
```
# <a name="python"></a>[Python](#tab/python)

```python
async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    instance_id = 'StaticId'

    await client.start_new('Periodic_Cleanup_Loop', instance_id, None)

    logging.info(f"Started orchestration with ID = '{instance_id}'.")
    return client.create_check_status_response(req, instance_id)

```

---

## <a name="exit-from-an-eternal-orchestration"></a>Sair de uma orquestração eterna

Se uma função de orquestrador precisar ser concluída, tudo que você precisa fazer é *não* chamar `ContinueAsNew` e permitir que a função saia.

Se uma função de orquestrador estiver em um loop infinito e precisar ser interrompida, use o `TerminateAsync` método (.net), `terminate` (JavaScript) ou `terminate` (Python) da [Associação de cliente de orquestração](durable-functions-bindings.md#orchestration-client) para interrompê-la. Para obter mais informações, consulte [Gerenciamento de Instâncias](durable-functions-instance-management.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como implementar orquestrações singleton](durable-functions-singletons.md)
