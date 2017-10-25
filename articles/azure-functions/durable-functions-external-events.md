---
title: "Lidando com eventos externos nas Funções Duráveis – Azure"
description: "Saiba como lidar com eventos externos na extensão de Funções Duráveis do Azure Functions."
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
ms.openlocfilehash: 23c99031ae3146a83867d10bd97d4eee8878188a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Lidando com eventos externos nas Funções Duráveis (Azure Functions)

Funções de orquestrador têm a capacidade de aguardar e escutar eventos externos. Frequentemente, esse recurso das [Funções Duráveis](durable-functions-overview.md) é útil para lidar com interação humana ou com outros gatilhos externos.

## <a name="wait-for-events"></a>Aguardar eventos

O método [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) permite que uma função de orquestrador aguarde e monitore de forma assíncrona um evento externo. O chamador declara o *nome* do evento e a *forma dos dados* que espera receber.

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

O exemplo anterior escuta um único evento e entra em ação quando ele é recebido.

Você pode escutar vários eventos ao mesmo tempo, como no exemplo a seguir, que aguarda uma de três notificações de evento possíveis.

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

O exemplo anterior escuta *qualquer* um de vários eventos. Também é possível aguardar *todos* os eventos.

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) aguarda indefinidamente por uma entrada.  O aplicativo de funções pode ser descarregado com segurança enquanto aguarda. Se um evento chegar para esta instância de orquestração, ela será ativada automaticamente e processará imediatamente o evento.

> [!NOTE]
> Não são feitas cobranças enquanto uma função de orquestrador está aguardando uma tarefa de `WaitForExternalEvent`, independentemente do tempo de espera.

Se a carga do evento não puder ser convertida no `T` do tipo esperado, uma exceção será lançada.

## <a name="send-events"></a>Enviar eventos

O método [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) da classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) envia os eventos que `WaitForExternalEvent` aguarda.  O método `RaiseEventAsync` usa *eventName* e *eventData* como parâmetros. Os dados do evento devem ser serializáveis em JSON.

Abaixo, temos um exemplo de função disparada em fila que envia um evento de "Aprovação" para uma instância de função de orquestrador. A ID da instância de orquestração vem do corpo da mensagem da fila.

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

Internamente, `RaiseEventAsync` enfileira uma mensagem que é obtida pela função de orquestrador em espera.

> [!WARNING]
> Se não houver nenhuma instância de orquestração com a *ID de instância* especificada ou se a instância não estiver aguardando o *nome do evento* especificado, a mensagem de evento será descartada. Para obter mais informações sobre esse comportamento, consulte o [Problema no GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como configurar orquestrações eternas](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Executar um exemplo que aguarda eventos externos](durable-functions-counter.md)

> [!div class="nextstepaction"]
> [Executar um exemplo que aguarda interação humana](durable-functions-phone-verification.md)

