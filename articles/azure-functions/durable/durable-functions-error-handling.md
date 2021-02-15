---
title: Lidando com erros nas Funções Duráveis – Azure
description: Saiba como lidar com erros na extensão de Funções Duráveis do Azure Functions.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: 023f9dfcc421935c3f7515e847108925d5e5521e
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673640"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Lidando com erros nas Funções Duráveis (Azure Functions)

Orquestrações de função duráveis são implementadas no código e podem usar os recursos internos de tratamento de erros da linguagem de programação. Na verdade, não há nenhum novo conceito que você precise aprender para adicionar o tratamento de erros e a compensação às suas orquestrações. No entanto, há alguns comportamentos a que você deve estar atento.

## <a name="errors-in-activity-functions"></a>Erros em funções de atividade

Qualquer exceção gerada em uma função de atividade é empacotada de volta para a função de orquestrador e lançada como um `FunctionFailedException` . Você pode escrever o código de compensação e tratamento de erro que atenda às suas necessidades na função de orquestrador.

Por exemplo, considere a seguinte função de orquestrador, que transfere fundos de uma conta para outra:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = context.GetInput<TransferOperation>();

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

> [!NOTE]
> Os exemplos anteriores do C# são para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    transfer_details = context.get_input()

    yield context.call_activity('DebitAccount', {
         'account': transfer_details['sourceAccount'],
         'amount' : transfer_details['amount']
    })

    try:
        yield context.call_activity('CreditAccount', {
                'account': transfer_details['destinationAccount'],
                'amount': transfer_details['amount'],
            })
    except:
        yield context.call_activity('CreditAccount', {
            'account': transfer_details['sourceAccount'],
            'amount': transfer_details['amount']
        })

main = df.Orchestrator.create(orchestrator_function)
```

---

Se a primeira chamada de função **CreditAccount** falhar, a função de orquestrador compensa creditando os fundos de volta para a conta de origem.

## <a name="automatic-retry-on-failure"></a>Repetição automática em caso de falha

Quando chama funções de atividade ou funções de suborquestração, você pode especificar uma política de repetição automática. O exemplo a seguir tenta chamar uma função até três vezes e espera cinco segundos entre cada repetição:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await context.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> Os exemplos anteriores do C# são para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    first_retry_interval_in_milliseconds = 5000
    max_number_of_attempts = 3

    retry_options = df.RetryOptions(first_retry_interval_in_milliseconds, max_number_of_attempts)

    yield context.call_activity_with_retry('FlakyFunction', retry_options)

main = df.Orchestrator.create(orchestrator_function)
```

---

A chamada de função de atividade no exemplo anterior usa um parâmetro para configurar uma política de repetição automática. Há várias opções para personalizar a política de repetição automática:

* **Núm. máximo de tentativas**: o número máximo de tentativas de repetição.
* **Primeiro intervalo de repetição**: o tempo de espera antes de fazer a primeira tentativa.
* **Coeficiente de retirada**: o coeficiente usado para determinar a taxa de aumento de retirada. O valor padrão é 1.
* **Intervalo máx. de repetição**: o tempo máximo de espera entre tentativas de repetição.
* **Tempo limite de repetição**: o tempo máximo a ser dedicado às novas tentativas. O comportamento padrão é repetir indefinidamente.
* **Identificador**: um retorno de chamada definido pelo usuário pode ser especificado para determinar se uma função deve ser repetida. 

> [!NOTE]
> Atualmente, os retornos de chamada definidos pelo usuário não têm suporte pelo Durable Functions em JavaScript ( `context.df.RetryOptions` ).


## <a name="function-timeouts"></a>Tempos limite de função

Talvez você queira abandonar uma chamada de função dentro de uma função de orquestrador se estiver demorando muito tempo para ser concluída. A maneira correta de fazer isso hoje é criando um [temporizador durável](durable-functions-timers.md) usando `context.CreateTimer` (.net), `context.df.createTimer` (JavaScript) ou `context.create_timer` (Python) em conjunto com `Task.WhenAny` (.net), `context.df.Task.any` (JavaScript) ou `context.task_any` (Python), como no exemplo a seguir:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> Os exemplos anteriores do C# são para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    deadline = context.current_utc_datetime + timedelta(seconds = 30)
    
    activity_task = context.call_activity('FlakyFunction')
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any(activity_task, timeout_task)
    if winner == activity_task:
        timeout_task.cancel()
        return True
    else:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

---

> [!NOTE]
> Esse mecanismo não encerra a execução de funções de atividade em andamento. Em vez disso, ele simplesmente permite que a função de orquestrador ignore o resultado e prossiga. Para mais informações, confira a documentação dos [Medidores de tempo](durable-functions-timers.md#usage-for-timeout).

## <a name="unhandled-exceptions"></a>Exceções sem tratamento

Se uma função de orquestrador falhar com uma exceção sem tratamento, os detalhes da exceção serão registrados e a instância será concluída com um status `Failed`.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre orquestrações do eternas](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Saiba como diagnosticar problemas](durable-functions-diagnostics.md)
