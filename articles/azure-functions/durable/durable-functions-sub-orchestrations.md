---
title: Suborquestrações para Funções Duráveis – Azure
description: Como chamar orquestrações de orquestrações na extensão de Funções Duráveis do Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 12/07/2018
ms.date: 12/25/2018
ms.author: v-junlch
ms.openlocfilehash: 1ab9a5714a7ef24b51957bd48b1b67240cf13adb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730235"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Suborquestrações em Funções Duráveis (Azure Functions)

Além de chamar funções de atividade, as funções de orquestrador podem chamar outras funções de orquestrador. Por exemplo, você pode criar uma orquestração maior usando uma biblioteca de funções de orquestrador. Ou pode executar várias instâncias de uma função de orquestrador em paralelo.

Uma função de orquestrador pode chamar outra função de orquestrador chamando os métodos [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) ou [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) no .NET ou os métodos `callSubOrchestrator` ou `callSubOrchestratorWithRetry` no JavaScript. O artigo [Tratamento de Erro e Compensação](durable-functions-error-handling.md#automatic-retry-on-failure) fornece mais informações sobre a repetição automática.

Da perspectiva do chamador, as funções de suborquestrador se comportam como funções de atividade. Elas podem retornar um valor, lançar uma exceção e podem ser aguardadas pela função de orquestrador pai.

## <a name="example"></a>Exemplo

O exemplo a seguir ilustra um cenário de IoT ("Internet das Coisas") em que vários dispositivos precisam ser provisionados. Há uma orquestração específica que deve acontecer para cada um dos dispositivos, que pode ser algo semelhante ao seguinte:

### <a name="c"></a>C#

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

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

Essa função de orquestrador pode ser usada da forma como está para o provisionamento de dispositivos individuais ou pode fazer parte de uma orquestração maior. Nesse último caso, a função de orquestrador pai pode agendar instâncias de `DeviceProvisioningOrchestration` usando a API `CallSubOrchestratorAsync` (C#) ou `callSubOrchestrator` (JavaScript).

Este é um exemplo que mostra como executar várias funções de orquestrador em paralelo.

### <a name="c"></a>C#

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    for (const deviceId of deviceIds) {
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.push(provisionTask);
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba o que são hubs de tarefas e como configurá-los](durable-functions-task-hubs.md)

