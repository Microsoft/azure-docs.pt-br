---
title: Temporizadores nas Funções Duráveis – Azure
description: Saiba como implementar temporizadores duráveis na extensão de Funções Duráveis do Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: azfuncdf
ms.openlocfilehash: a05f75a7e38ee7cd4dc056629d9acaacad875e08
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730218"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Temporizadores nas Funções Duráveis (Azure Functions)

As [Funções Duráveis](durable-functions-overview.md) fornecem *temporizadores duráveis* para uso em funções de orquestrador para implementar atrasos ou para configurar tempos limite em ações assíncronas. Temporizadores duráveis devem ser usados em funções de orquestrador em vez de `Thread.Sleep` ou `Task.Delay` (C#) ou `setTimeout()` e `setInterval()` (JavaScript).

Crie um temporizador durável chamando o método [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) em [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) in .NET, ou o`createTimer` método do `DurableOrchestrationContext` no JavaScript. O método retorna uma tarefa que é retomada em uma data e hora especificadas.

## <a name="timer-limitations"></a>Limitações do temporizador

Quando você cria um temporizador que expira às 4:30 pm, o Framework de Tarefa Durável subjacente enfileira uma mensagem que se torna visível apenas às 4:30 pm. Ao executar usando um plano de consumo do Azure Functions, a mensagem do temporizador recém-visível garantirá que o aplicativo de funções seja ativado em uma VM apropriada.

> [!NOTE]
> * Temporizadores duráveis não podem durar mais de 7 dias devido a limitações no Armazenamento do Azure. Estamos trabalhando em uma [solicitação de recurso para estender os temporizadores para além de 7 dias](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Sempre use [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) em vez de `DateTime.UtcNow` in .NET e `currentUtcDateTime` em vez de `Date.now` ou `Date.UTC` no JavaScript, conforme mostrado nos exemplos a seguir, ao calcular o prazo relativo de um temporizador durável.

## <a name="usage-for-delay"></a>Uso para atrasos

O exemplo a seguir ilustra como usar temporizadores duráveis para atrasar a execução. O exemplo está emitindo uma notificação de cobrança diariamente por dez dias.

### <a name="c"></a>C#

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const dayOfMonth = context.df.currentUtcDateTime.getDate();
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

> [!WARNING]
> Evite loops infinitos em funções de orquestrador. Para obter informações sobre como implementar de forma segura e eficiente cenários de loop infinito, consulte [Orquestrações eternas](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Uso para tempo limite

Este exemplo ilustra como usar temporizadores duráveis para implementar tempos limite.

### <a name="c"></a>C#

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

> [!WARNING]
> Use um `CancellationTokenSource` para cancelar um temporizador durável (C#) ou chamar `cancel()` no `TimerTask` retornado (JavaScript) se seu código não aguardar a conclusão. O Framework de tarefa durável não alterará o status de uma orquestração para "concluído" até que todas as tarefas pendentes forem concluídas ou canceladas.

Esse mecanismo não encerra a execução de funções de atividade em andamento. Em vez disso, ele simplesmente permite que a função de orquestrador ignore o resultado e prossiga. Se seu aplicativo de funções usar o Plano de Consumo, você ainda será cobrado pelo tempo e pela memória consumida pela função de atividade abandonada. Por padrão, funções em execução pelo Plano de Consumo têm um tempo limite de cinco minutos. Se esse limite for ultrapassado, o host do Azure Functions será reciclado para interromper toda a execução e evitar uma situação de cobrança sem controle. O [tempo limite da função é configurável](../functions-host-json.md#functiontimeout).

Para obter um exemplo mais detalhado de como implementar tempos limite em funções de orquestrador, consulte o passo a passo [Interação humana e tempos limite – verificação por telefone](durable-functions-phone-verification.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como acionar e manipular eventos externos](durable-functions-external-events.md)
