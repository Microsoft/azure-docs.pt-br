---
title: Lidando com erros nas Funções Duráveis – Azure
description: Saiba como lidar com erros na extensão de Funções Duráveis do Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: f3d7f916d31a03d7b868749026f541dd646459f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60732511"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Lidando com erros nas Funções Duráveis (Azure Functions)

Orquestrações de Funções Duráveis são implementadas no código e podem usar os recursos de tratamento de erros da linguagem de programação. Com isso em mente, não há novos conceitos que você precise aprender ao incorporar a compensação e o tratamento de erro às suas orquestrações. No entanto, há alguns comportamentos a que você deve estar atento.

## <a name="errors-in-activity-functions"></a>Erros em funções de atividade

Qualquer exceção que é lançada em uma função de atividade sofre marshaling de volta para a função de orquestrador e é lançada como um `FunctionFailedException`. Você pode escrever o código de compensação e tratamento de erro que atenda às suas necessidades na função de orquestrador.

Por exemplo, considere a seguinte função de orquestrador, que transfere fundos de uma conta para outra:

### <a name="c"></a>C#

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(DurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```

Se a chamada para a função **CreditAccount** falhar para a conta de destino, a função de orquestrador compensará isso creditando os fundos de volta na conta de origem.

## <a name="automatic-retry-on-failure"></a>Repetição automática em caso de falha

Quando chama funções de atividade ou funções de suborquestração, você pode especificar uma política de repetição automática. O exemplo a seguir tenta chamar uma função até três vezes e espera cinco segundos entre cada repetição:

### <a name="c"></a>C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const retryOptions = new df.RetryOptions(5000, 3);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

A API `CallActivityWithRetryAsync` (.NET) ou `callActivityWithRetry` (JavaScript) usa um parâmetro `RetryOptions`. As chamadas de suborquestração usando a API `CallSubOrchestratorWithRetryAsync` (.NET) ou `callSubOrchestratorWithRetry` (JavaScript) podem usar essas mesmas políticas de repetição.

Há várias opções para personalizar a política de repetição automática. São elas:

* **Número máximo de tentativas**: O número máximo de tentativas de repetição.
* **Intervalo da primeira repetição**: O tempo de espera antes de fazer a primeira tentativa.
* **Coeficiente de retirada**: O coeficiente usado para determinar a taxa de aumento de retirada. O valor padrão é 1.
* **Intervalo máximo de repetição**: O tempo máximo de espera entre tentativas de repetição.
* **Tempo limite de repetição**: O tempo máximo a ser dedicado às novas tentativas. O comportamento padrão é repetir indefinidamente.
* **Identificador**: É possível especificar um retorno de chamada definido pelo usuário, que determina se uma chamada de função deve ser repetida ou não.

## <a name="function-timeouts"></a>Tempos limite de função

Talvez você queira abandonar uma chamada de função em uma função de orquestrador se ela estiver demorando muito para ser concluída. No momento, o modo adequado de fazer isso é criar um [temporizador durável](durable-functions-timers.md) usando `context.CreateTimer` (.NET) ou `context.df.createTimer` (JavaScript) em conjunto com `Task.WhenAny` (.NET) ou `context.df.Task.any` (JavaScript), conforme mostrado no exemplo a seguir:

### <a name="c"></a>C#

```csharp
public static async Task<bool> Run(DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```

> [!NOTE]
> Esse mecanismo não encerra a execução de funções de atividade em andamento. Em vez disso, ele simplesmente permite que a função de orquestrador ignore o resultado e prossiga. Para mais informações, confira a documentação dos [Medidores de tempo](durable-functions-timers.md#usage-for-timeout).

## <a name="unhandled-exceptions"></a>Exceções sem tratamento

Se uma função de orquestrador falhar com uma exceção sem tratamento, os detalhes da exceção serão registrados e a instância será concluída com um status `Failed`.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como diagnosticar problemas](durable-functions-diagnostics.md)
