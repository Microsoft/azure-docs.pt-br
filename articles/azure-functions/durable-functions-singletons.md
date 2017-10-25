---
title: "Singletons para Funções Duráveis – Azure"
description: "Como usar singletons para a extensão de Funções Duráveis do Azure Functions."
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
ms.openlocfilehash: e82cc53d53a6d0296aaab2c3a76ad4e2f6c12c54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orquestradores singleton em Funções Duráveis (Azure Functions)

Para trabalhos em segundo plano ou orquestrações com estilo de ator, normalmente você precisa garantir que apenas uma instância de um determinado orquestrador seja executada por vez. Isso pode ser feito nas [Funções Duráveis](durable-functions-overview.md) atribuindo uma ID de instância específica a um orquestrador ao criá-lo.

## <a name="singleton-example"></a>Exemplo de singleton

O exemplo em C# a seguir mostra uma função de gatilho HTTP que cria uma orquestração singleton de trabalho em segundo plano. Ela usa uma ID de instância conhecida para garantir que haja apenas uma instância.

```cs
[FunctionName("EnsureSingletonTrigger")]
public static async Task<HttpResponseMessage> Ensure(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    // Ensure only one instance is ever running at a time
    const string OrchestratorName = "MySingletonOrchestrator";
    const string InstanceId = "MySingletonInstanceId";

    var existingInstance = await starter.GetStatusAsync(InstanceId);
    if (existingInstance == null)
    {
        log.Info($"Creating singleton instance with ID = {InstanceId}...");
        await starter.StartNewAsync(OrchestratorName, InstanceId, input: null);
    }

    return starter.CreateCheckStatusResponse(req, InstanceId);
}
```

Por padrão, IDs de instância são GUIDs gerados aleatoriamente. No entanto, observe que nesse caso a função de gatilho usa uma variável `InstanceId` predefinida com valor de `MySingletonInstanceId` para pré-atribuir uma ID de instância à função de orquestrador. Isso permite que o gatilho verifique se a instância conhecida já está em execução chamando [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_).

Os detalhes da implementação da função de orquestrador, na verdade, não importam. Poderia ser uma função de orquestrador regular que é iniciada e concluída ou poderia ser uma que é executada eternamente (ou seja, um [Orquestração Eterna](durable-functions-eternal-orchestrations.md)). O aspecto importante é que há somente uma instância em execução por vez.

> [!NOTE]
> Se a instância de orquestração singleton terminar, falhar ou for concluída, não será possível recriá-la usando a mesma ID. Nesses casos, você deve estar preparado para recriá-la usando uma nova ID de instância.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como chamar suborquestrações](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Executar um singleton de exemplo](durable-functions-counter.md)
