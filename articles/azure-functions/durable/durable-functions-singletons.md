---
title: Singletons para Funções Duráveis – Azure
description: Como usar singletons na extensão Durable Functions do Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 12/07/2018
ms.date: 01/16/2019
ms.author: v-junlch
ms.openlocfilehash: aca7aa30744c79cefd3c7704a8fde1df203b2c9d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731304"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orquestradores singleton em Funções Duráveis (Azure Functions)

Para trabalhos em segundo plano, normalmente, você precisa garantir que apenas uma instância de determinado orquestrador seja executada por vez. Isso pode ser feito nas [Funções Duráveis](durable-functions-overview.md) atribuindo uma ID de instância específica a um orquestrador ao criá-lo.

## <a name="singleton-example"></a>Exemplo de singleton

Os exemplos de C# e JavaScript a seguir mostram uma função de gatilho HTTP que cria uma orquestração singleton de trabalho em segundo plano. O código garante que apenas uma instância exista para uma ID de instância especificada.

### <a name="c"></a>C#

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionsName;

    // Check if an instance with the specified ID already exists.
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance) {
        // An instance with the specified ID doesn't exist, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

Por padrão, IDs de instância são GUIDs gerados aleatoriamente. Mas, nesse caso, a ID de instância é passada nos dados da rota da URL. O código chama [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) (C#) ou `getStatus` (JavaScript) para verificar se uma instância com a ID especificada já está em execução. Se não estiver, uma instância será criada com essa ID.

> [!WARNING]
> Ao fazer o desenvolvimento local em JavaScript, você precisará definir a variável de ambiente `WEBSITE_HOSTNAME` como `localhost:<port>`, por exemplo, `localhost:7071` para usar métodos em `DurableOrchestrationClient`. Para obter mais informações sobre esse requisito, confira o [problema no GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

> [!NOTE]
> Há uma condição de corrida potencial neste exemplo. Se duas instâncias de **HttpStartSingle** forem executadas simultaneamente, as chamadas de função relatarão êxito, mas somente uma instância de orquestração será realmente iniciada. Dependendo dos seus requisitos, isso pode ter efeitos colaterais indesejáveis. Por esse motivo, é importante garantir que nenhum duas solicitações podem executar essa função de gatilho simultaneamente.

Os detalhes da implementação da função de orquestrador, na verdade, não importam. Poderia ser uma função de orquestrador regular que é iniciada e concluída ou poderia ser uma que é executada eternamente (ou seja, um [Orquestração Eterna](durable-functions-eternal-orchestrations.md)). O aspecto importante é que há somente uma instância em execução por vez.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Saiba como chamar suborquestrações](durable-functions-sub-orchestrations.md)

<!-- Update_Description: wording update -->
