---
title: Padrões de funções duráveis e conceitos técnicos no Azure Functions
description: Saiba como a extensão de funções duráveis no Azure Functions oferece os benefícios da execução do código com monitoração de estado na nuvem.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: aa9563266f6b43e3bc2f21fbc0b340c86c5895ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60861974"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Padrões de funções duráveis e conceitos técnicos (Azure Functions)

As funções duráveis é uma extensão da [Azure Functions](../functions-overview.md) e [WebJobs do Azure](../../app-service/web-sites-create-web-jobs.md). Você pode usar as funções duráveis para gravar funções com monitoração de estado em um ambiente sem servidor. A extensão gerencia estado, pontos de verificação e reinicializações para você. 

Este artigo fornece informações detalhadas sobre os comportamentos de extensão de funções duráveis para Azure Functions e os padrões comuns de implementação. As informações podem ajudar a determinar como usar as funções duráveis para ajudar a resolver seus desafios de desenvolvimento.

> [!NOTE]
> As funções duráveis é uma extensão avançada do Azure Functions que não é adequada para todos os aplicativos. Este artigo pressupõe que você tenha uma forte familiaridade com conceitos [Azure Functions](../functions-overview.md) e os desafios envolvidos no desenvolvimento de aplicativos sem servidor.

## <a name="patterns"></a>Padrões

Esta seção descreve alguns padrões comuns de aplicativos em que as funções duráveis pode ser útil.

### <a name="chaining"></a>Padrão 1: Encadeamento de funções

A padrão de encadeamento de funções, uma sequência de funções é executado em uma ordem específica. Nesse padrão, a saída de uma função é aplicada à entrada de outra função.

![Um diagrama da função padrão de encadeamento](./media/durable-functions-concepts/function-chaining.png)

Você pode usar as funções duráveis para implementar a função de encadeamento de padrão de forma concisa, conforme mostrado no exemplo a seguir:

#### <a name="c-script"></a>Script C#

```csharp
public static async Task<object> Run(DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

> [!NOTE]
> Há diferenças sutis entre escrever uma função durável pré-compilados C# e escrever uma função durável pré-compilado no C# script que é mostrado no exemplo. Em um C# função, pré-compilada duráveis parâmetros devem ser decorados com os respectivos atributos. Um exemplo é o `[OrchestrationTrigger]` de atributo para o `DurableOrchestrationContext` parâmetro. Em um C# pré-compilado função durável, se os parâmetros não são decorados adequadamente, o tempo de execução não pode injetar as variáveis em uma função e ocorre um erro. Para obter mais exemplos, consulte o [azure-functions-durable-extension exemplos no GitHub](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples).

#### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

Neste exemplo, os valores `F1`, `F2`, `F3`, e `F4` são os nomes das outras funções no aplicativo de funções. Você pode implementar o fluxo de controle usando construções de codificação imperativa normal. Código é executado de cima para baixo. O código pode envolver a semântica existente de fluxo de controle de linguagem, como condicionais e loops. Você pode incluir a lógica de tratamento de erros `try` / `catch` / `finally` blocos.

Você pode usar o `context` parâmetro [DurableOrchestrationContext] \(.NET\) e o `context.df` object (JavaScript) para invocar outras funções por nome, passar parâmetros e retornar a função saída. Cada vez que o código chama `await` (C#) ou `yield` (JavaScript), os pontos de verificação do framework de funções duráveis o progresso da instância da função atual. Se o processo ou a VM for reciclada no meio da execução, a instância de função continuará da anterior `await` ou `yield` chamar. Para obter mais informações, consulte a próxima seção, o padrão de n º 2: Ventilador out/fan-in.

> [!NOTE]
> O `context` objeto em JavaScript representa todo o [contexto de função](../functions-reference-node.md#context-object), não apenas o [DurableOrchestrationContext] parâmetro.

### <a name="fan-in-out"></a>Padrão 2: Ventilador out/fan-in

O ventilador out/fan-in padrão, executar várias funções em paralelo e aguarde até que todas as funções concluir. Geralmente, algum trabalho de agregação é feito nos resultados retornados de funções.

![Um diagrama do ventilador out/fan padrão](./media/durable-functions-concepts/fan-out-fan-in.png)

Com funções normais, você pode realizar fan-out fazendo com que a função enviar várias mensagens para uma fila. Fan-volta é muito mais desafiador. Para realizar fan-in, em uma função normal, você escrever código para controlar quando o final de funções disparadas pela fila e armazenamento, em seguida, saídas de função. 

A extensão funções duráveis lida com esse padrão com um código relativamente simples:

#### <a name="c-script"></a>Script C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

O trabalho de fan-out é distribuído para várias instâncias do `F2` função. O trabalho é controlado por meio de uma lista dinâmica de tarefas. O .NET `Task.WhenAll` API ou JavaScript `context.df.Task.all` API é chamada para aguardar todas as funções chamadas sejam concluídas. Em seguida, o `F2` saídas de função são agregadas de lista de tarefas dinâmicas e passadas para o `F3` função.

A verificação pontual automática que ocorre durante o `await` ou `yield` chamar `Task.WhenAll` ou `context.df.Task.all` garante que uma potencial falha no Centro de ou a reinicialização não requerem a reinicialização de uma tarefa já concluída.

### <a name="async-http"></a>Padrão 3: APIs HTTP assíncronas

O padrão de APIs HTTP assíncrono resolve o problema de coordenar o estado das operações de execução longa com clientes externos. Uma maneira comum de implementar esse padrão é fazendo com que um HTTP chamar disparar a ação de execução longa. Em seguida, redirecione o cliente para um ponto de extremidade de status que o cliente sonda para saber quando a operação for concluída.

![Um diagrama do padrão de API HTTP](./media/durable-functions-concepts/async-http-api.png)

As funções duráveis fornecem APIs internas que simplificam o código que você escreve para interagir com execuções de função de execução longa. Os exemplos de início rápido de funções duráveis ([ C# ](durable-functions-create-first-csharp.md) e [JavaScript](quickstart-js-vscode.md)) mostram um comando REST simples que você pode usar para iniciar novas instâncias de função do orchestrator. Depois que uma instância for iniciada, a extensão expõe APIs HTTP que consultar o status da função de orquestrador de webhook. 

O exemplo a seguir mostra os comandos REST que iniciam um orquestrador e consultar seu status. Para maior clareza, alguns detalhes foram omitidos do exemplo.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

Como o tempo de execução de funções duráveis gerencia o estado, você não precisa implementar seu próprio mecanismo de controle de status.

A extensão funções duráveis tenha webhooks internos que gerenciar orquestrações de longa execução. Você pode implementar esse padrão usando seus próprios gatilhos de função (por exemplo, HTTP, uma fila ou Hubs de eventos) e o `orchestrationClient` associação. Por exemplo, você pode usar uma mensagem da fila para disparar o encerramento. Ou, você pode usar um gatilho HTTP que é protegido por uma política de autenticação do Active Directory do Azure, em vez de webhooks internos que usam uma chave gerada para autenticação.

Aqui estão alguns exemplos de como usar o API HTTP padrão:

#### <a name="c"></a>C#

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // The function name comes from the request URL.
    // The function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
// An HTTP-triggered function starts a new orchestrator function instance.
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);

    // The function name comes from the request URL.
    // The function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(req, instanceId);
};
```

> [!WARNING]
> Quando você desenvolve localmente no JavaScript, para usar os métodos na `DurableOrchestrationClient`, você deve definir a variável de ambiente `WEBSITE_HOSTNAME` à `localhost:<port>` (por exemplo, `localhost:7071`). Para obter mais informações sobre esse requisito, consulte [problema de GitHub 28](https://github.com/Azure/azure-functions-durable-js/issues/28).

Em .NET, o parâmetro de [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` é um valor da associação de saída `orchestrationClient`, que faz parte da extensão de Durable Functions. Em JavaScript, esse objeto é retornado ao chamar `df.getClient(context)`. Esses objetos fornecem métodos que podem ser usados para iniciar, enviar eventos para, encerrar e consultar instâncias de função de orquestrador novo ou existente.

Nos exemplos anteriores, uma função disparada por HTTP assume um `functionName` o valor da URL de entrada e passa o valor para [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). O [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) API de associação, em seguida, retorna uma resposta que contém um `Location` cabeçalho e informações adicionais sobre a instância. Você pode usar as informações mais tarde para pesquisar o status da instância iniciada ou para encerrar a instância.

### <a name="monitoring"></a>Padrão 4: Monitoramento

O padrão de monitoramento refere-se a um processo flexível e recorrente em um fluxo de trabalho. Um exemplo está sondando até que condições específicas forem atendidas. Você pode usar uma expressão [gatilho de temporizador](../functions-bindings-timer.md) para resolver um basic cenário, como um trabalho de limpeza periódico, mas seu intervalo é estático e gerenciar tempos de vida da instância se torna complexo. Você pode usar as funções duráveis para criar intervalos de recorrência flexíveis, tempos de vida da tarefa de gerenciar e criar o monitor de vários processos de uma única orquestração.

Um exemplo do padrão de monitor é reverter o cenário anterior da API HTTP assíncrona. Em vez de expor um ponto de extremidade para um cliente externo monitorar uma operação de longa execução, o monitor de execução longa consome um ponto de extremidade externo e, em seguida, aguarda uma alteração de estado.

![Um diagrama do padrão de monitor](./media/durable-functions-concepts/monitor.png)

Em algumas linhas de código, você pode usar funções duráveis para criar vários monitores que observam pontos de extremidade arbitrários. Os monitores podem encerrar a execução quando uma condição for atendida, ou o [DurableOrchestrationClient](durable-functions-instance-management.md) podem encerrar os monitores. Você pode alterar um monitor `wait` intervalo com base em uma condição específica (por exemplo, retirada exponencial.) 

O código a seguir implementa um monitor básico:

#### <a name="c-script"></a>Script C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

Quando uma solicitação é recebida, uma nova instância de orquestração é criada para essa ID do trabalho. A instância sonda um status até que uma condição seja atendida e o loop seja encerrado. Um temporizador durável controla o intervalo de sondagem. Em seguida, podem ser executadas mais de trabalho ou a orquestração pode encerrar. Quando o `context.CurrentUtcDateTime` (.NET) ou `context.df.currentUtcDateTime` (JavaScript) excede o `expiryTime` de valor, o monitor encerra.

### <a name="human"></a>Padrão 5: Interação humana

Muitos processos automatizados envolvem algum tipo de interação humana. Envolver pessoas em um processo automatizado é complicado porque as pessoas não são tão dinâmicos quanto os serviços de nuvem e como altamente disponível. Um processo automatizado pode permitir isso usando a lógica de tempos limite e compensação.

Um processo de aprovação é um exemplo de um processo de negócios que envolve interação humana. Aprovação de um gerente pode ser necessária para um relatório de despesas que ultrapasse um determinado valor em dólar. Se o Gerenciador não aprovar o relatório de despesas dentro de 72 horas (talvez o manager entrou em férias), um processo de escalonamento de bloqueios é acionado para obter a aprovação de outra pessoa (talvez o gerente do gerente).

![Um diagrama do padrão de interação humana](./media/durable-functions-concepts/approval.png)

Você pode implementar o padrão neste exemplo, usando uma função de orquestrador. O orchestrator usa um [temporizador durável](durable-functions-timers.md) para aprovação de solicitação. O orquestrador de escala se o tempo limite ocorre. O orquestrador aguarda um [evento externo](durable-functions-external-events.md), como uma notificação que é gerada por uma interação humana.

Esses exemplos criam um processo de aprovação para demonstrar o padrão de interação humana:

#### <a name="c-script"></a>Script C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

Para criar o timer durável, chame `context.CreateTimer` (.NET) ou `context.df.createTimer` (JavaScript). A notificação é recebida pelo `context.WaitForExternalEvent` (.NET) ou `context.df.waitForExternalEvent` (JavaScript). Em seguida, `Task.WhenAny` (.NET) ou `context.df.Task.any` (JavaScript) é chamado para decidir se deseja escalonar (o tempo limite acontece primeiro) ou processar a aprovação (aprovação é recebida antes do tempo limite).

Um cliente externo pode entregar a notificação de eventos para uma função de orquestrador em espera usando qualquer um de [HTTP APIs internas](durable-functions-http-api.md#raise-event) ou usando o [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) API do outra função:

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

## <a name="the-technology"></a>A tecnologia

Nos bastidores, a extensão funções duráveis é criada sobre o [Framework de tarefa durável](https://github.com/Azure/durabletask), uma biblioteca de código-fonte aberto no GitHub que é usado para compilar orquestrações de tarefa durável. Como o Azure Functions é a evolução sem servidor de WebJobs do Azure, as funções duráveis é a evolução sem servidor do Framework de tarefa durável. Microsoft e outras organizações usam o Framework de tarefa durável extensivamente para automatizar processos essenciais. Ele é uma opção natural para o ambiente sem servidor do Azure Functions.

### <a name="event-sourcing-checkpointing-and-replay"></a>Fornecimento de eventos, ponto de verificação e reprodução

Funções de orquestrador mantêm seu estado de execução usando o [fornecimento do evento](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) padrão de design. Em vez de armazenar diretamente o estado atual de uma orquestração, a extensão de funções duráveis usa um repositório somente de acréscimo para registrar a série completa de ações que leva a orquestração de função. Um repositório somente de acréscimo traz muitos benefícios em comparação com "despejar" o estado de tempo de execução total. Benefícios incluem melhor desempenho, escalabilidade e capacidade de resposta. Você também pode obter a consistência eventual para dados transacionais e trilhas de auditoria completas e histórico. As trilhas de auditoria dão suporte a ações de compensação confiáveis.

As funções duráveis usa o evento de fornecimento de forma transparente. Nos bastidores, o `await` (C#) ou `yield` operador (JavaScript) em uma função de orquestrador transfere o controle do thread orchestrator de volta para o dispatcher do Framework de tarefa durável. O dispatcher, em seguida, confirma novas ações agendadas pela função orquestradora (como chamar uma ou mais funções filho ou agendar um temporizador durável) no armazenamento. Acrescenta a ação de confirmação transparente para o histórico de execução da instância de orquestração. O histórico é armazenado na tabela de armazenamento. A ação de confirmação, em seguida, adiciona mensagens a uma fila para agendar o trabalho de fato. Neste ponto, a função orquestradora pode ser descarregada da memória. 

Interrompe a cobrança para a função de orquestrador se você estiver usando o plano de consumo do Azure Functions. Quando há mais trabalho a fazer, as reinicializações de função, e seu estado é reconstruído.

Quando uma função de orquestração recebe mais trabalho a fazer (por exemplo, uma mensagem de resposta é recebida ou um temporizador durável expira), o orquestrador é ativado e executa novamente a função inteira desde o início para reconstruir o estado local. 

Durante a reprodução, se o código tenta chamar uma função (ou fazer qualquer outra async trabalho), o Framework de tarefa durável consulta o histórico de execução da orquestração atual. Se detectar que o [função de atividade](durable-functions-types-features-overview.md#activity-functions) já executada e gerou um resultado, ele reproduzirá o resultado da função e o código do orquestrador continuará a ser executado. Reprodução continua até que o código de função seja concluído ou até que ele está agendado novo trabalho assíncrono.

### <a name="orchestrator-code-constraints"></a>Restrições de código do orquestrador

O comportamento do código do orquestrador de reprodução cria restrições quanto ao tipo de código que você pode gravar em uma função de orquestrador. Por exemplo, código orquestrador deve ser determinístico porque ele será reproduzido várias vezes e deve produzir o mesmo resultado toda vez. Para obter uma lista de restrições, consulte [restrições de código do orquestrador](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints).

## <a name="monitoring-and-diagnostics"></a>Monitoramento e diagnóstico

A extensão funções duráveis emite automaticamente dados de acompanhamento estruturados para [Application Insights](../functions-monitoring.md) se você configurar seu aplicativo de funções com uma chave de instrumentação do Application Insights do Azure. Você pode usar os dados de rastreamento para monitorar as ações e o progresso de suas orquestrações.

Aqui está um exemplo de como os eventos de rastreamento de funções duráveis parecer no portal do Application Insights quando você usa [Application Insights Analytics](../../application-insights/app-insights-analytics.md):

![Resultados da consulta do Application Insights](./media/durable-functions-concepts/app-insights-1.png)

Você pode encontrar dados estruturados úteis no `customDimensions` campo em cada entrada de log. Aqui está um exemplo de uma entrada que está totalmente expandido:

![O campo customDimensions em uma consulta do Application Insights](./media/durable-functions-concepts/app-insights-2.png)

Devido ao comportamento de reprodução do dispatcher do Framework de Tarefa Durável, você pode esperar ver entradas de log redundantes para ações reproduzidas. Entradas de log redundantes podem ajudar você a entender o comportamento de reprodução do mecanismo central. O [diagnóstico](durable-functions-diagnostics.md) artigo mostra exemplos de consultas que filtram logs de reprodução, para que você possa ver apenas os logs "em tempo real".

## <a name="storage-and-scalability"></a>Armazenamento e a escalabilidade

A extensão de funções duráveis usa filas, tabelas e blobs no armazenamento do Azure para manter a execução da função execução histórico estado e o gatilho. Você pode usar a conta de armazenamento padrão para o aplicativo de funções, ou você pode configurar uma conta de armazenamento separada. Talvez você queira uma conta separada com base em limites de taxa de transferência de armazenamento. O código do orquestrador que você escreve não interage com as entidades nessas contas de armazenamento. O Framework de tarefa durável gerencia as entidades diretamente como um detalhe de implementação.

As funções orquestradoras agendam funções de atividade e recebem suas respostas por meio de mensagens de fila interna. Quando um aplicativo de funções é executado no plano de consumo do Azure Functions, o [controlador de escala do Azure Functions](../functions-scale.md#how-the-consumption-and-premium-plans-work) monitora essas filas. Novas instâncias de computação são adicionadas conforme necessário. Ao escalar horizontalmente para várias VMs, uma função de orquestrador pode executar em uma VM, enquanto as funções de atividade que as chamadas de função de orquestrador possam ser executadas em várias VMs diferentes. Para obter mais informações sobre o comportamento de escala das funções duráveis, consulte [desempenho e escala](durable-functions-perf-and-scale.md).

O histórico de execução para contas do orchestrator é armazenado no armazenamento de tabela. Sempre que uma instância for reidratada em uma VM específica, o orquestrador buscará seu histórico de execução do armazenamento de tabelas para que possa recriar seu estado local. Um aspecto conveniente de ter o histórico disponível no armazenamento de tabelas é que você pode usar ferramentas como [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) para ver o histórico de suas orquestrações.

Blobs de armazenamento são usados principalmente como um mecanismo de concessão para coordenar a expansão de instâncias de orquestração em várias VMs. Blobs de armazenamento contêm dados para mensagens grandes não podem ser armazenados diretamente em tabelas ou filas.

![Uma captura de tela do Gerenciador de armazenamento do Azure](./media/durable-functions-concepts/storage-explorer.png)

> [!WARNING]
> Embora seja fácil e conveniente ver o histórico de execução no armazenamento de tabela, não faça quaisquer dependências nesta tabela. A tabela pode mudar à medida que a extensão funções duráveis evoluir.

## <a name="known-issues"></a>Problemas conhecidos

Todos os problemas conhecidos devem estar registrados na lista de [Problemas no GitHub](https://github.com/Azure/azure-functions-durable-extension/issues). Se você encontrar um problema e não é possível localizar o problema no GitHub, abra um novo problema. Inclua uma descrição detalhada do problema.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as funções duráveis, consulte [tipos e recursos de função de funções duráveis](durable-functions-types-features-overview.md). 

Introdução:

> [!div class="nextstepaction"]
> [Crie sua primeira função durável](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html
