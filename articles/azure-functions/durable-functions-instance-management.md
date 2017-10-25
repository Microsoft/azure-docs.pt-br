---
title: "Gerenciar instâncias nas Funções Duráveis – Azure"
description: "Saiba como gerenciar instâncias na extensão de Funções Duráveis do Azure Functions."
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
ms.openlocfilehash: 8c9085042496fef4c499f57e874e0b8ba059fd65
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Gerenciar instâncias nas Funções Duráveis (Azure Functions)

As instâncias de orquestração das [Funções Duráveis](durable-functions-overview.md) podem ser iniciadas, encerradas, consultadas e podem receber eventos de notificação. Todo o gerenciamento de instâncias é feito usando a [associação de cliente de orquestração](durable-functions-bindings.md). Este artigo mostra os detalhes de cada operação de gerenciamento de instância.

## <a name="starting-instances"></a>Iniciar instâncias

O método [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) no [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) inicia uma nova instância de uma função de orquestrador. Instâncias dessa classe podem ser obtidas usando a associação `orchestrationClient`. Internamente, esse método enfileira uma mensagem na fila de controle, que por sua vez dispara o início de uma função com o nome especificado que usa a associação de gatilho `orchestrationTrigger`.

Os parâmetros são os seguintes:

* **Name**: o nome da função de orquestrador a ser agendada.
* **Input**: qualquer dado serializável em JSON, que deve ser passado como entrada para a função de orquestrador.
* **InstanceId**: (opcional) a ID exclusiva da instância. Se não for especificada, uma ID de instância aleatória será gerada.

Veja um exemplo simples em C#:

```csharp
[FunctionName("HelloWorldManualStart")]
public static Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    string instanceId = starter.StartNewAsync("HelloWorld", input);
    log.Info($"Started orchestration with ID = '{instanceId}'.");
}
```

Para as linguagens diferentes de .NET, a associação de saída da função também pode ser usada para iniciar novas instâncias. Nesse caso, qualquer objeto serializável em JSON que tem os três parâmetros acima como campos pode ser usado. Por exemplo, considere a seguinte função Node.js:

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null);
};
```

> [!NOTE]
> Geralmente, é recomendável isar um identificador aleatório para a ID de instância. Isso ajudará a garantir uma distribuição de carga igual ao dimensionar funções de orquestrador entre várias VMs. O momento adequado para usar IDs de instância não aleatórias é quando a ID precisar ser proveniente de uma fonte externa ou ao implementar o padrão de [orquestrador singleton](durable-functions-counter.md).

## <a name="querying-instances"></a>Consultar instâncias

O método [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) na classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) consulta o status de uma instância de orquestração. Ele usa um `instanceId` como parâmetro e retorna um objeto com as seguintes propriedades:

* **Name**: o nome da função de orquestrador.
* **InstanceId**: a ID da instância de orquestração (deve ser o mesmo que a entrada `instanceId`).
* **CreatedTime**: a hora em que a função de orquestrador começou a ser executada.
* **LastUpdatedTime**: a hora em que a orquestração passou por uma verificação pontual pela última vez.
* **Input**: a entrada da função como um valor JSON.
* **Output**: a saída da função como um valor JSON (se a função tiver sido concluída). Se a função de orquestrador tiver falhado, essa propriedade incluirá os detalhes da falha. Se a função de orquestrador tiver sido encerrada, essa propriedade incluirá o motivo fornecido para o encerramento (se houver).
* **RuntimeStatus**: um dos seguintes valores:
    * **Running**: a instância começou a ser executada.
    * **Completed**: a instância foi concluída normalmente.
    * **ContinuedAsNew**: a instância reiniciou a si mesma com um novo histórico. Esse estado é temporário.
    * **Failed**: a instância falhou com um erro.
    * **Terminated**: a instância foi encerrada abruptamente.
    
Este método retornará `null` se a instância não existir ou não tiver começado a ser executada.

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await checker.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> Atualmente, a consulta de instância só tem suporte para funções C#.

## <a name="terminating-instances"></a>Encerrar instâncias

Uma instância em execução pode ser encerrada usando o método [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) da classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Os dois parâmetros são um `instanceId` e uma cadeia de caracteres `reason`, que serão gravados em logs no status da instância. Uma instância encerrada deixará de ser executada assim que atingir o próximo ponto `await` ou será encerrada imediatamente se já estiver em um `await`.

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Atualmente, o encerramento de instância só tem suporte para funções C#.

## <a name="sending-events-to-instances"></a>Enviar eventos para instâncias

Notificações de eventos podem ser enviadas a instâncias em execução usando o método [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) da classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Instâncias que podem lidar com esses eventos são aquelas que estão aguardando uma chamada para [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_). As entradas são:

* **InstanceId**: a ID exclusiva da instância.
* **EventName**: o nome do evento a ser enviado.
* **EventData**: uma carga serializável em JSON para enviar para a instância.

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> Atualmente, a criação de eventos só tem suporte para funções C#.

> [!WARNING]
> Se não houver nenhuma instância de orquestração com a *ID de instância* especificada ou se a instância não estiver aguardando o *nome do evento* especificado, a mensagem de evento será descartada. Para obter mais informações sobre esse comportamento, consulte o [Problema no GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como usar APIs HTTP para o gerenciamento de instâncias](durable-functions-http-api.md)
