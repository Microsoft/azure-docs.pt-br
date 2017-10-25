---
title: "Lidando com erros nas Funções Duráveis – Azure"
description: "Saiba como lidar com erros na extensão de Funções Duráveis do Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: ee5362d33bb9dadadb4194457cfd7726f4825f56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Lidando com erros nas Funções Duráveis (Azure Functions)

Orquestrações de Funções Duráveis são implementadas no código e podem usar os recursos de tratamento de erros da linguagem de programação. Com isso em mente, não há novos conceitos que você precise aprender ao incorporar a compensação e o tratamento de erro às suas orquestrações. No entanto, há alguns comportamentos a que você deve estar atento.

## <a name="errors-in-activity-functions"></a>Erros em funções de atividade

Qualquer exceção que é lançada em uma função de atividade sofre marshaling de volta para a função de orquestrador e é lançada como um `TaskFailedException`. Você pode escrever o código de compensação e tratamento de erro que atenda às suas necessidades na função de orquestrador.

Por exemplo, considere a seguinte função de orquestrador, que transfere fundos de uma conta para outra:

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

Se a chamada para a função **CreditAccount** falhar para a conta de destino, a função de orquestrador compensará isso creditando os fundos de volta na conta de origem.

## <a name="automatic-retry-on-failure"></a>Repetição automática em caso de falha

Quando chama funções de atividade ou funções de suborquestração, você pode especificar uma política de repetição automática. O exemplo a seguir tenta chamar uma função até três vezes e espera cinco segundos entre cada repetição:

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions);
    
    // ...
}
```

A API `CallActivityWithRetryAsync` utiliza um parâmetro `RetryOptions`. Chamadas de suborquestração usando a API `CallSubOrchestratorWithRetryAsync` podem usar essas mesmas políticas de repetição.

Há várias opções para personalizar a política de repetição automática. São elas:

* **Núm. máximo de tentativas**: o número máximo de tentativas de repetição.
* **Primeiro intervalo de repetição**: o tempo de espera antes de fazer a primeira tentativa.
* **Coeficiente de retirada**: o coeficiente usado para determinar a taxa de aumento de retirada. O valor padrão é 1.
* **Intervalo máx. de repetição**: o tempo máximo de espera entre tentativas de repetição.
* **Tempo limite de repetição**: o tempo máximo a ser dedicado às novas tentativas. O comportamento padrão é repetir indefinidamente.
* **Personalizado**: pode ser especificado um retorno de chamada definido pelo usuário, que determina se uma chamada de função deve ser repetida.

## <a name="function-timeouts"></a>Tempos limite de função

Talvez você queira abandonar uma chamada de função em uma função de orquestrador se ela estiver demorando muito para ser concluída. No momento, o modo adequado de fazer isso é criar um [temporizador durável](durable-functions-timers.md) usando `context.CreateTimer` em conjunto com `Task.WhenAny`, conforme mostrado no exemplo a seguir:

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

## <a name="unhandled-exceptions"></a>Exceções sem tratamento

Se uma função de orquestrador falhar com uma exceção sem tratamento, os detalhes da exceção serão registrados e a instância será concluída com um status `Failed`.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como diagnosticar problemas](durable-functions-diagnostics.md)
