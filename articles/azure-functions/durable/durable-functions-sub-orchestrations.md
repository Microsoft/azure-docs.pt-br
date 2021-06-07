---
title: Suborquestrações para Funções Duráveis – Azure
description: Como chamar orquestrações de orquestrações na extensão de Funções Duráveis do Azure Functions.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 5625bc2ddfa4b6f527ca16f19f33d257a1834d4b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85340807"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Suborquestrações em Funções Duráveis (Azure Functions)

Além de chamar funções de atividade, as funções de orquestrador podem chamar outras funções de orquestrador. Por exemplo, você pode criar uma orquestração maior a partir de uma biblioteca de funções de orquestrador menores. Ou pode executar várias instâncias de uma função de orquestrador em paralelo.

Uma função de orquestrador pode chamar outra função de orquestrador usando os `CallSubOrchestratorAsync` `CallSubOrchestratorWithRetryAsync` métodos ou no .net, ou `callSubOrchestrator` os `callSubOrchestratorWithRetry` métodos ou em JavaScript. O artigo [Tratamento de Erro e Compensação](durable-functions-error-handling.md#automatic-retry-on-failure) fornece mais informações sobre a repetição automática.

Da perspectiva do chamador, as funções de suborquestrador se comportam como funções de atividade. Elas podem retornar um valor, lançar uma exceção e podem ser aguardadas pela função de orquestrador pai. 

> [!NOTE]
> No momento, as suborquestrações têm suporte no .NET e no JavaScript.

## <a name="example"></a>Exemplo

O exemplo a seguir ilustra um cenário de IoT ("Internet das Coisas") em que vários dispositivos precisam ser provisionados. A função a seguir representa o fluxo de trabalho de provisionamento que precisa ser executado para cada dispositivo:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

---

Essa função de orquestrador pode ser usada da forma como está para o provisionamento de dispositivos individuais ou pode fazer parte de uma orquestração maior. No último caso, a função de orquestrador pai pode agendar instâncias do `DeviceProvisioningOrchestration` usando a `CallSubOrchestratorAsync` API (.net) ou `callSubOrchestrator` (JavaScript).

Este é um exemplo que mostra como executar várias funções de orquestrador em paralelo.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

> [!NOTE]
> Os exemplos anteriores do C# são para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    var id = 0;
    for (const deviceId of deviceIds) {
        const child_id = context.df.instanceId+`:${id}`;
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId, child_id);
        provisioningTasks.push(provisionTask);
        id++;
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

---

> [!NOTE]
> As suborquestrações devem ser definidas no mesmo aplicativo de funções que a orquestração pai. Se você precisar chamar e aguardar orquestrações em outro aplicativo de funções, considere usar o suporte interno para APIs HTTP e o padrão de consumidor de sondagem HTTP 202. Para obter mais informações, consulte o tópico [recursos http](durable-functions-http-features.md) .

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como definir um status de orquestração personalizado](durable-functions-custom-orchestration-status.md)
