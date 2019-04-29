---
title: Tipos de função e recursos na extensão de funções duráveis do Azure Functions
description: Saiba mais sobre os tipos de funções e funções que dão suporte à comunicação de função de função em uma orquestração de funções duráveis no Azure Functions.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 12/07/2018
ms.date: 03/19/2019
ms.author: v-junlch
ms.openlocfilehash: 76b6f013333113d5a24b744bc962d36b1c0e21b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731101"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Tipos de funções duráveis e recursos (Azure Functions)

As funções duráveis é uma extensão da [Azure Functions](../functions-overview.md). Você pode usar as funções duráveis para orquestração com monitoração de estado de execução da função. Uma função durável é uma solução que é composta por diferentes funções do Azure. Funções podem ter diferentes funções em uma orquestração de função durável. 

Este artigo fornece uma visão geral dos tipos de funções que você pode usar em uma orquestração de funções duráveis. O artigo inclui alguns padrões comuns que você pode usar para se conectar a funções. Saiba como as funções duráveis pode ajudá-lo a resolver seus desafios de desenvolvimento de aplicativo.

![Uma imagem que mostra os tipos de funções duráveis][1]  

## <a name="types-of-durable-functions"></a>Tipos de funções duráveis

Você pode usar três tipos de função durável no Azure Functions: atividade, o orchestrator e o cliente.

### <a name="activity-functions"></a>Funções de atividade

Funções de atividade são a unidade básica de trabalho em uma orquestração de função durável. Funções de atividade são as funções e tarefas que são organizadas no processo. Por exemplo, você pode criar uma função durável para processar um pedido. As tarefas envolvem a verificação de inventário, cobrar o cliente e criar uma remessa. Cada tarefa seria uma função de atividade. 

Funções de atividade não são restritos no tipo de trabalho que você pode fazer no-los. Você pode escrever uma função de atividade em qualquer [linguagem que dão suporte a funções duráveis](durable-functions-overview.md#language-support). A estrutura de tarefa durável garante que cada função de atividade chamada seja executada pelo menos uma vez durante uma orquestração.

Use uma [gatilho de atividade](durable-functions-bindings.md#activity-triggers) para disparar uma função de atividade. Funções do .NET recebem uma [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) como um parâmetro. Você também pode associar o gatilho a qualquer outro objeto para passar entradas para a função. No JavaScript, você pode acessar uma entrada por meio de `<activity trigger binding name>` propriedade no [ `context.bindings` objeto](../functions-reference-node.md#bindings).

Sua função de atividade também pode retornar valores para o orchestrator. Se você enviar ou retorna um grande número de valores de uma função de atividade, você pode usar [tuplas ou matrizes](durable-functions-bindings.md#passing-multiple-parameters). Você pode disparar uma função de atividade apenas de uma instância de orquestração. Embora uma função de atividade e a outra função (como uma função disparada por HTTP) podem compartilhar alguns códigos, cada função pode ter apenas um gatilho.

Para obter mais informações e exemplos, consulte [funções de atividade](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Funções de orquestrador

Funções de orquestrador descrevem como as ações são executadas e a ordem na qual as ações são executadas. Funções de orquestrador descrevem a orquestração no código (C# ou JavaScript) conforme mostrado na [padrões de funções duráveis e conceitos técnicos](durable-functions-concepts.md). Uma orquestração pode ter muitos tipos diferentes de ações, incluindo [funções de atividade](#activity-functions), [suborquestrações](#sub-orchestrations), [aguardando por eventos externos](#external-events)e [temporizadores](#durable-timers). 

Uma função de orquestrador precisa ser disparada por um [gatilho de orquestração](durable-functions-bindings.md#orchestration-triggers).

Um orquestrador é iniciado por um [cliente orchestrator](#client-functions). Você pode disparar o orquestrador de qualquer fonte (HTTP, fila, fluxo de eventos). Cada instância de uma orquestração tem um identificador de instância. O identificador de instância pode ser gerados automaticamente (recomendado) ou gerado pelo usuário. Você pode usar o identificador de instância para [gerenciar instâncias](durable-functions-instance-management.md) da orquestração.

Para obter mais informações e exemplos, consulte [gatilhos de orquestração](durable-functions-bindings.md#orchestration-triggers).

### <a name="client-functions"></a>Funções do cliente

Funções de cliente são as funções disparadas que criam novas instâncias de uma orquestração. Funções de cliente são o ponto de entrada para a criação de uma instância de uma orquestração de funções duráveis. Você pode disparar uma função de cliente de qualquer fonte (HTTP, fila, fluxo de eventos). Você pode escrever uma função de cliente em qualquer linguagem compatível com o aplicativo. 

Funções de cliente também têm uma [cliente de orquestração](durable-functions-bindings.md#orchestration-client) associação. Uma função de cliente pode usar o cliente de orquestração de associação para criar e gerenciar orquestrações duráveis. 

O exemplo mais básico de uma função de cliente é uma função disparada por HTTP que inicia uma função de orquestrador e, em seguida, retorna uma resposta de status de verificação. Por exemplo, consulte [descoberta de URL da API HTTP](durable-functions-http-api.md#http-api-url-discovery).

Para obter mais informações e exemplos, consulte [cliente de orquestração](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Recursos e padrões

As próximas seções descrevem os recursos e padrões de tipos de funções duráveis.

### <a name="sub-orchestrations"></a>Subtipo de orquestrações

Funções de orquestrador podem chamar funções de atividade, mas eles também podem chamar outras funções de orquestrador. Por exemplo, você pode criar uma orquestração maior usando uma biblioteca de funções de orquestrador. Ou, você pode executar várias instâncias de uma função de orquestrador em paralelo.

Para obter mais informações e exemplos, consulte [suborquestrações](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Temporizadores duráveis

[As funções duráveis](durable-functions-overview.md) fornece *temporizadores duráveis* que você pode usar em funções de orquestrador para implementar atrasos ou para configurar tempos limite em ações assíncronas. Usar temporizadores duráveis em funções de orquestrador em vez de `Thread.Sleep` e `Task.Delay` (C#) ou `setTimeout()` e `setInterval()` (JavaScript).

Para obter mais informações e exemplos, consulte [temporizadores duráveis](durable-functions-timers.md).

### <a name="external-events"></a>Eventos externos

As funções de orquestrador podem aguardar os eventos externos atualizarem uma instância de orquestração. Esse recurso de funções duráveis geralmente é útil para lidar com uma interação humana ou outros retornos de chamada externos.

Para obter mais informações e exemplos, consulte [eventos externos](durable-functions-external-events.md).

### <a name="error-handling"></a>Tratamento de erros

Use o código para implementar orquestrações de funções duráveis. Você pode usar os recursos de tratamento de erros da linguagem de programação. Padrões, como `try` / `catch` funcionam na sua orquestração. 

As funções duráveis também são fornecidos com as políticas de repetição interna. Uma ação pode atrasar e tente novamente as atividades automaticamente quando ocorre uma exceção. Você pode usar as novas tentativas para lidar com exceções transitórias, sem o abandono de orquestração.

Para obter mais informações e exemplos, consulte [tratamento de erro](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Comunicação do aplicativo entre funções

Embora uma orquestração durável é executado no contexto de um único aplicativo de funções, você pode usar padrões para coordenar orquestrações em muitos aplicativos de funções. Comunicação entre aplicativos pode ocorrer por HTTP, mas usando a estrutura durável para cada atividade significa que você ainda pode manter um processo durável entre dois aplicativos.

Os exemplos a seguir demonstram a orquestração de aplicativo de funções no C# e JavaScript. Em cada exemplo, uma atividade começa a orquestração externa. Outra atividade recupera e retorna o status. O orquestrador aguarda o status ser `Complete` antes de continuar.

Aqui estão alguns exemplos de orquestração de aplicativo de funções entre:

#### <a name="c"></a>C#

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration.
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now, go do more work...
}

[FunctionName("StartRemoteOrchestration")]
public static async Task<string> StartRemoteOrchestration([ActivityTrigger] string orchestratorName)
{
    using (var response = await HttpClient.PostAsync(
        $"https://appB.chinacloudsites.cn/orchestrations/{orchestratorName}",
        new StringContent("")))
    {
        string statusUrl = await response.Content.ReadAsAsync<string>();
        return statusUrl;
    }
}

[FunctionName("CheckIsComplete")]
public static async Task<bool> CheckIsComplete([ActivityTrigger] string statusUrl)
{
    using (var response = await HttpClient.GetAsync(statusUrl))
    {
        // 200 = Complete, 202 = Running
        return response.StatusCode == HttpStatusCode.OK;
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    // Do some work...

    // Call a remote orchestration.
    const statusUrl = yield context.df.callActivity("StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true) {
        const isComplete = yield context.df.callActivity("CheckIsComplete", statusUrl);
        if (isComplete) {
            break;
        }

        const waitTime = moment(context.df.currentUtcDateTime).add(1, "m").toDate();
        yield context.df.createTimer(waitTime);
    }

    // B is done. Now, go do more work...
});
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, orchestratorName) {
    const options = {
        method: "POST",
        uri: `https://appB.chinacloudsites.cn/orchestrations/${orchestratorName}`,
        body: ""
    };

    const statusUrl = await request(options);
    return statusUrl;
};
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, statusUrl) {
    const options = {
        method: "GET",
        uri: statusUrl,
        resolveWithFullResponse: true,
    };

    const response = await request(options);
    // 200 = Complete, 202 = Running
    return response.statusCode === 200;
};
```

## <a name="next-steps"></a>Próximas etapas

Para começar, crie sua primeira função durável na [ C# ](durable-functions-create-first-csharp.md) ou [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Leia mais sobre as funções duráveis](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png

<!-- Update_Description: wording update -->