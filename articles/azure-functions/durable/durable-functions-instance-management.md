---
title: Gerenciar instâncias nas Funções Duráveis – Azure
description: Saiba como gerenciar instâncias na extensão de Funções Duráveis do Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 7329962d547fcb0635e3a9af3d80e562da59f7f2
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199782"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Gerenciar instâncias em Durable Functions no Azure

Se você estiver usando a extensão [Durable Functions](durable-functions-overview.md) para Azure functions, ou quiser começar a fazer isso, verifique se está obtendo o melhor uso dele. Você pode otimizar suas instâncias de orquestração de Durable Functions aprendendo mais sobre como gerenciá-las. Este artigo mostra os detalhes de cada operação de gerenciamento de instância.

Você pode iniciar e encerrar instâncias, por exemplo, e pode consultar instâncias, incluindo a capacidade de consultar todas as instâncias e instâncias de consulta com filtros. Além disso, você pode enviar eventos para instâncias, aguardar a conclusão da orquestração e recuperar URLs de webhook de gerenciamento HTTP. Este artigo aborda outras operações de gerenciamento também, incluindo instâncias de rebobinamento, limpeza de histórico de instância e exclusão de um hub de tarefas.

No Durable Functions, você tem opções de como deseja implementar cada uma dessas operações de gerenciamento. Este artigo fornece exemplos que usam o [Azure Functions Core Tools](../functions-run-local.md) para .net (C#), JavaScript e Python.

## <a name="start-instances"></a>Iniciar instâncias

É importante poder iniciar uma instância de orquestração. Isso é feito normalmente quando você está usando uma associação de Durable Functions no gatilho de outra função.

O `StartNewAsync` método (.net), `startNew` (JavaScript) ou `start_new` (Python) na associação de [cliente de orquestração](durable-functions-bindings.md#orchestration-client) inicia uma nova instância. Internamente, esse método enfileira uma mensagem na fila de controle, que dispara o início de uma função com o nome especificado que usa a [Associação de gatilho de orquestração](durable-functions-bindings.md#orchestration-trigger).

Essa operação assíncrona é concluída quando o processo de orquestração é agendado com êxito.

Os parâmetros para iniciar uma nova instância de orquestração são os seguintes:

* **Name**: o nome da função de orquestrador a ser agendada.
* **Input**: Qualquer dado serializável em JSON, que deve ser passado como a entrada para a função de orquestrador.
* **InstanceId**: (opcional) a ID exclusiva da instância. Se você não especificar esse parâmetro, o método usará uma ID aleatória.

> [!TIP]
> Use um identificador aleatório para a ID de instância. As IDs de instância aleatórias ajudam a garantir uma distribuição de carga igual quando você estiver dimensionando funções de orquestrador em várias VMs. O momento adequado para usar IDs de instância não aleatórias é quando a ID deve vir de uma fonte externa, ou quando você estiver implementando o padrão de [orquestrador singleton](durable-functions-singletons.md) .

O código a seguir é uma função de exemplo que inicia uma nova instância de orquestração:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorldQueueTrigger")]
public static async Task Run(
    [QueueTrigger("start-queue")] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve usar o `DurableOrchestrationClient` tipo de parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>A menos que especificado de outra forma, os exemplos nesta página usam o gatilho HTTP com os seguintes function.jsem.

**function.json**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Este exemplo se destina Durable Functions versão 2. x. Na versão 1. x, use `orchestrationClient` em vez de `durableClient` .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

# <a name="python"></a>[Python](#tab/python)

<a name="javascript-function-json"></a>A menos que especificado de outra forma, os exemplos nesta página usam o gatilho HTTP com os seguintes function.jsem.

**function.json**

```json
{
  "scriptFile": "__init__.py",
  "bindings": [    
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Este exemplo se destina Durable Functions versão 2. x. Na versão 1. x, use `orchestrationClient` em vez de `durableClient` .

**__init__. py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    
    instance_id = await client.start_new('HelloWorld', None, None)
    logging.log(f"Started orchestration with ID = ${instance_id}.")

```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Você também pode iniciar uma instância diretamente usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable start-new` . Ele usa os seguintes parâmetros:

* **`function-name` (obrigatório)**: o nome da função a ser iniciada.
* **`input` (opcional)**: entrada para a função, seja embutida ou por meio de um arquivo JSON. Para arquivos, adicione um prefixo ao caminho para o arquivo com `@` , como `@path/to/file.json` .
* **`id` (opcional)**: ID da instância de orquestração. Se você não especificar esse parâmetro, o comando usará um GUID aleatório.
* **`connection-string-setting` (opcional)**: nome da configuração do aplicativo que contém a cadeia de conexão de armazenamento a ser usada. O padrão é AzureWebJobsStorage.
* **`task-hub-name` (opcional)**: nome do hub de tarefas Durable Functions a ser usado. O padrão é DurableFunctionsHub. Você também pode definir isso em [host.js](durable-functions-bindings.md#host-json) usando DurableTask: HubName.

> [!NOTE]
> Os comandos de ferramentas principais pressupõem que você está executando-os do diretório raiz de um aplicativo de funções. Se você fornecer explicitamente os `connection-string-setting` `task-hub-name` parâmetros e, poderá executar os comandos de qualquer diretório. Embora seja possível executar esses comandos sem um host de aplicativo de funções em execução, você pode achar que não é possível observar alguns efeitos, a menos que o host esteja em execução. Por exemplo, o `start-new` comando enfileira uma mensagem de início no Hub de tarefas de destino, mas a orquestração não é realmente executada, a menos que haja um processo de host de aplicativo de funções em execução que possa processar a mensagem.

O comando a seguir inicia a função chamada HelloWorld e passa o conteúdo do arquivo `counter-data.json` para ela:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Instâncias de consulta

Como parte de seu esforço para gerenciar suas orquestrações, você provavelmente precisará reunir informações sobre o status de uma instância de orquestração (por exemplo, se ela foi concluída normalmente ou falhou).

O `GetStatusAsync` (.net), `getStatus` (JavaScript) ou o `get_status` método (Python) na associação de [cliente de orquestração](durable-functions-bindings.md#orchestration-client) consulta o status de uma instância de orquestração.

Ele usa um `instanceId` (obrigatório), `showHistory` (opcional) e `showHistoryOutput` (opcional) e `showInput` (opcional) como parâmetros.

* **`showHistory`**: Se definido como `true` , a resposta conterá o histórico de execução.
* **`showHistoryOutput`**: Se definido como `true` , o histórico de execução conterá saídas de atividade.
* **`showInput`**: Se definido como `false` , a resposta não conterá a entrada da função. O valor padrão é `true`.

O método retorna um objeto com as seguintes propriedades:

* **Name**: o nome da função de orquestrador.
* **InstanceId**: a ID da instância de orquestração (deve ser o mesmo que a entrada `instanceId`).
* **CreatedTime**: a hora em que a função de orquestrador começou a ser executada.
* **LastUpdatedTime**: a hora em que a orquestração passou por uma verificação pontual pela última vez.
* **Input**: a entrada da função como um valor JSON. Esse campo não será preenchido se `showInput` for false.
* **CustomStatus**: status de orquestração personalizado no formato JSON.
* **Output**: a saída da função como um valor JSON (se a função tiver sido concluída). Se a função de orquestrador tiver falhado, essa propriedade incluirá os detalhes da falha. Se a função de orquestrador tiver sido encerrada, essa propriedade incluirá o motivo do encerramento (se houver).
* **RuntimeStatus**: um dos seguintes valores:
  * **Pendente**: A instância foi agendada, mas ainda não foi iniciado em execução.
  * **Running**: a instância começou a ser executada.
  * **Completed**: a instância foi concluída normalmente.
  * **ContinuedAsNew**: a instância reiniciou a si mesma com um novo histórico. Esse estado é um estado transitório.
  * **Failed**: a instância falhou com um erro.
  * **Terminated**: a instância foi interrompida abruptamente.
* **Histórico**: O histórico de execução de orquestração. Este campo é preenchido somente se `showHistory` é definido como `true`.

> [!NOTE]
> Um orquestrador não é marcado como `Completed` até que todas as suas tarefas agendadas tenham sido concluídas _e_ o orquestrador tenha retornado. Em outras palavras, não é suficiente para um orquestrador alcançar sua `return` declaração para que ele seja marcado como `Completed` . Isso é particularmente relevante para casos em que `WhenAny` é usado; esses orquestradores geralmente `return` antes de todas as tarefas agendadas terem sido executadas.

Esse método retornará `null` (.net), `undefined` (JavaScript) ou `None` (Python) se a instância não existir.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("check-status-queue")] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve usar o `DurableOrchestrationClient` tipo de parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
    // example: if status.runtimeStatus === df.OrchestrationRuntimeStatus.Running: ...
}
```

Consulte [Iniciar instâncias](#javascript-function-json) para o function.jsna configuração.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    status = await client.get_status(instance_id)
    # do something based on the current status
    # example: if (existing_instance.runtime_status is df.OrchestrationRuntimeStatus.Running) { ...
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Também é possível obter o status de uma instância de orquestração diretamente, usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` . Ele usa os seguintes parâmetros:

* **`id` (obrigatório)**: ID da instância de orquestração.
* **`show-input` (opcional)**: se definido como `true` , a resposta contém a entrada da função. O valor padrão é `false`.
* **`show-output` (opcional)**: se definido como `true` , a resposta contém a saída da função. O valor padrão é `false`.
* **`connection-string-setting` (opcional)**: nome da configuração do aplicativo que contém a cadeia de conexão de armazenamento a ser usada. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: nome do hub de tarefas Durable Functions a ser usado. O padrão é `DurableFunctionsHub`. Ele também pode ser definido no [host.jsem](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

O comando a seguir recupera o status (incluindo entrada e saída) de uma instância com uma ID de instância de orquestração de 0ab8c55a66644d68a3a8b220b12d209c. Ele pressupõe que você está executando o `func` comando do diretório raiz do aplicativo de funções:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Você pode usar o `durable get-history` comando para recuperar o histórico de uma instância de orquestração. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)**: ID da instância de orquestração.
* **`connection-string-setting` (opcional)**: nome da configuração do aplicativo que contém a cadeia de conexão de armazenamento a ser usada. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: nome do hub de tarefas Durable Functions a ser usado. O padrão é `DurableFunctionsHub`. Ele também pode ser definido no host.jsem, usando durableTask: HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Consultar todas as instâncias

Em vez de consultar uma instância em sua orquestração por vez, talvez você ache mais eficiente consultar todas elas de uma só vez.

Você pode usar o método [ListInstancesAsync](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient.listinstancesasync#Microsoft_Azure_WebJobs_Extensions_DurableTask_IDurableOrchestrationClient_ListInstancesAsync_Microsoft_Azure_WebJobs_Extensions_DurableTask_OrchestrationStatusQueryCondition_System_Threading_CancellationToken_) (.net), [getStatusAll](/javascript/api/durable-functions/durableorchestrationclient#getstatusall--) (JavaScript) ou `get_status_all` (Python) para consultar os status de todas as instâncias de orquestração. No .NET, você pode passar um `CancellationToken` objeto caso queira cancelá-lo. O método retorna uma lista de objetos que representam as instâncias de orquestração correspondentes aos parâmetros de consulta.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var noFilter = new OrchestrationStatusQueryCondition();
    OrchestrationStatusQueryResult result = await client.ListInstancesAsync(
        noFilter,
        CancellationToken.None);
    foreach (DurableOrchestrationStatus instance in result.DurableOrchestrationState)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    }
    
    // Note: ListInstancesAsync only returns the first page of results.
    // To request additional pages provide the result.ContinuationToken
    // to the OrchestrationStatusQueryCondition's ContinuationToken property.
}
```

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve usar o `DurableOrchestrationClient` tipo de parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import json
import azure.functions as func
import azure.durable_functions as df


async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instances = await client.get_status_all()

    for instance in instances:
        logging.log(json.dumps(instance))
```

Consulte [Iniciar instâncias](#javascript-function-json) para o function.jsna configuração.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Também é possível consultar instâncias diretamente, usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` . Ele usa os seguintes parâmetros:

* **`top` (opcional)**: esse comando dá suporte à paginação. Esse parâmetro corresponde ao número de instâncias recuperadas por solicitação. O padrão é 10.
* **`continuation-token` (opcional)**: um token para indicar qual página ou seção de instâncias recuperar. Cada `get-instances` execução retorna um token para o próximo conjunto de instâncias.
* **`connection-string-setting` (opcional)**: nome da configuração do aplicativo que contém a cadeia de conexão de armazenamento a ser usada. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: nome do hub de tarefas Durable Functions a ser usado. O padrão é `DurableFunctionsHub`. Ele também pode ser definido no [host.jsem](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Instâncias de consulta com filtros

E se você não precisar realmente de todas as informações que uma consulta de instância padrão pode fornecer? Por exemplo, e se você estiver apenas procurando o tempo de criação da orquestração ou o status do tempo de execução Orchestration? Você pode restringir sua consulta aplicando filtros.

Use o método [ListInstancesAsync](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient.listinstancesasync#Microsoft_Azure_WebJobs_Extensions_DurableTask_IDurableOrchestrationClient_ListInstancesAsync_Microsoft_Azure_WebJobs_Extensions_DurableTask_OrchestrationStatusQueryCondition_System_Threading_CancellationToken_) (.net) ou [getStatusBy](/javascript/api/durable-functions/durableorchestrationclient#getstatusby-date---undefined--date---undefined--orchestrationruntimestatus---) (JavaScript) para obter uma lista de instâncias de orquestração que correspondem a um conjunto de filtros predefinidos.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    // Get the first 100 running or pending instances that were created between 7 and 1 day(s) ago
    var queryFilter = new OrchestrationStatusQueryCondition
    {
        RuntimeStatus = new[]
        {
            OrchestrationRuntimeStatus.Pending,
            OrchestrationRuntimeStatus.Running,
        },
        CreatedTimeFrom = DateTime.UtcNow.Subtract(TimeSpan.FromDays(7)),
        CreatedTimeTo = DateTime.UtcNow.Subtract(TimeSpan.FromDays(1)),
        PageSize = 100,
    };
    
    OrchestrationStatusQueryResult result = await client.ListInstancesAsync(
        queryFilter,
        CancellationToken.None);
    foreach (DurableOrchestrationStatus instance in result.DurableOrchestrationState)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    }
}
```

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve usar o `DurableOrchestrationClient` tipo de parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

Consulte [Iniciar instâncias](#javascript-function-json) para o function.jsna configuração.

# <a name="python"></a>[Python](#tab/python)

```python
import logging
from datetime import datetime
import json
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.OrchestrationRuntimeStatus import OrchestrationRuntimeStatus

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    runtime_status = [OrchestrationRuntimeStatus.Completed, OrchestrationRuntimeStatus.Running]

    instances = await client.get_status_by(
        datetime(2018, 3, 10, 10, 1, 0),
        datetime(2018, 3, 10, 10, 23, 59),
        runtime_status
    )

    for instance in instances:
        logging.log(json.dumps(instance))
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

No Azure Functions Core Tools, você também pode usar o `durable get-instances` comando com filtros. Além dos `top` parâmetros,, e mencionados anteriormente `continuation-token` `connection-string-setting` `task-hub-name` , você pode usar três parâmetros de filtro ( `created-after` , `created-before` , e `runtime-status` ).

* **`created-after` (opcional)**: recuperar as instâncias criadas após esta data/hora (UTC). Datetimes formato ISO 8601 aceito.
* **`created-before` (opcional)**: recuperar as instâncias criadas antes desta data/hora (UTC). Datetimes formato ISO 8601 aceito.
* **`runtime-status` (opcional)**: recuperar as instâncias com um status específico (por exemplo, executando ou concluído). Pode fornecer o status de vários (separado por espaço).
* **`top` (opcional)**: número de instâncias recuperadas por solicitação. O padrão é 10.
* **`continuation-token` (opcional)**: um token para indicar qual página ou seção de instâncias recuperar. Cada `get-instances` execução retorna um token para o próximo conjunto de instâncias.
* **`connection-string-setting` (opcional)**: nome da configuração do aplicativo que contém a cadeia de conexão de armazenamento a ser usada. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: nome do hub de tarefas Durable Functions a ser usado. O padrão é `DurableFunctionsHub`. Ele também pode ser definido no [host.jsem](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

Se você não fornecer filtros ( `created-after` , `created-before` ou `runtime-status` ), o comando simplesmente recupera `top` instâncias, sem considerar o status do tempo de execução ou o tempo de criação.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Encerrar instâncias

Se você tiver uma instância de orquestração demorando muito para ser executada ou apenas precisar interrompê-la antes de ser concluída por qualquer motivo, você terá a opção de finalizá-la.

Você pode usar o `TerminateAsync` método (.net), `terminate` (JavaScript) ou o `terminate` (Python) da Associação de [cliente de orquestração](durable-functions-bindings.md#orchestration-client) para encerrar instâncias. Os dois parâmetros são um `instanceId` e uma `reason` cadeia de caracteres, que são gravados nos logs e no status da instância.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("terminate-queue")] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve usar o `DurableOrchestrationClient` tipo de parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

Consulte [Iniciar instâncias](#javascript-function-json) para o function.jsna configuração.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "It was time to be done."
    return client.terminate(instance_id, reason)
```

---

Uma instância terminada eventualmente fará a transição para o `Terminated` estado. No entanto, essa transição não ocorrerá imediatamente. Em vez disso, a operação de encerramento será enfileirada no Hub de tarefas junto com outras operações para essa instância. Você pode usar as APIs de [consulta de instância](#query-instances) para saber quando uma instância terminada realmente atingiu o `Terminated` estado.

> [!NOTE]
> O encerramento da instância não se propaga no momento. As funções de atividade e as suborquestrações são executadas até a conclusão, independentemente de você ter encerrado a instância de orquestração que as chamou.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Você também pode encerrar uma instância de orquestração diretamente, usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable terminate` . Ele usa os seguintes parâmetros:

* **`id` (obrigatório)**: ID da instância de orquestração a ser encerrada.
* **`reason` (opcional)**: motivo da rescisão.
* **`connection-string-setting` (opcional)**: nome da configuração do aplicativo que contém a cadeia de conexão de armazenamento a ser usada. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: nome do hub de tarefas Durable Functions a ser usado. O padrão é `DurableFunctionsHub`. Ele também pode ser definido no [host.jsem](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

O comando a seguir encerra uma instância de orquestração com uma ID de 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Enviar eventos para instâncias

Em alguns cenários, é importante que suas funções de orquestrador possam esperar e ouvir eventos externos. Isso inclui [funções](durable-functions-overview.md#monitoring) e funções de monitor que estão aguardando a [interação humana](durable-functions-overview.md#human).

Envie notificações de eventos para executar instâncias usando o `RaiseEventAsync` método (.net) ou o `raiseEvent` método (JavaScript) da [Associação de cliente de orquestração](durable-functions-bindings.md#orchestration-client). As instâncias que podem lidar com esses eventos são aquelas que estão aguardando uma chamada para `WaitForExternalEvent` (.net) ou retornando a uma `waitForExternalEvent` chamada (JavaScript).

Os parâmetros para `RaiseEventAsync` (.net) e `raiseEvent` (JavaScript) são os seguintes:

* **InstanceId**: a ID exclusiva da instância.
* **EventName**: o nome do evento a ser enviado.
* **EventData**: uma carga serializável em JSON para enviar para a instância.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("event-queue")] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve usar o `DurableOrchestrationClient` tipo de parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

Consulte [Iniciar instâncias](#javascript-function-json) para o function.jsna configuração.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    event_data = [1, 2 ,3]
    return client.raise_event(instance_id, 'MyEvent', event_data)
```

---

> [!NOTE]
> Se não houver nenhuma instância de orquestração com a ID da instância especificada, a mensagem de evento é descartada. Se uma instância existir, mas ainda não estiver aguardando o evento, o evento será armazenado no estado da instância até que esteja pronto para ser recebido e processado.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Você também pode gerar um evento para uma instância de orquestração diretamente, usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable raise-event` . Ele usa os seguintes parâmetros:

* **`id` (obrigatório)**: ID da instância de orquestração.
* **`event-name`**: O nome do evento a ser gerado.
* **`event-data` (opcional)**: dados a serem enviados para a instância de orquestração. Esse pode ser o caminho para um arquivo JSON ou você pode fornecer os dados diretamente na linha de comando.
* **`connection-string-setting` (opcional)**: nome da configuração do aplicativo que contém a cadeia de conexão de armazenamento a ser usada. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: nome do hub de tarefas Durable Functions a ser usado. O padrão é `DurableFunctionsHub`. Ele também pode ser definido no [host.jsem](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Aguardar a conclusão da orquestração

Em orquestrações de longa execução, talvez você queira esperar e obter os resultados de uma orquestração. Nesses casos, também é útil ser capaz de definir um período de tempo limite na orquestração. Se o tempo limite for excedido, o estado da orquestração deverá ser retornado em vez dos resultados.

O `WaitForCompletionOrCreateCheckStatusResponseAsync` (.net) ou o `waitForCompletionOrCreateCheckStatusResponse` método (JavaScript) pode ser usado para obter a saída real de uma instância de orquestração de forma síncrona. Por padrão, esses métodos usam um valor padrão de 10 segundos para `timeout` e 1 segundo para `retryInterval` .  

Veja um exemplo de função de gatilho HTTP que demonstra como usar essa API:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Consulte [Iniciar instâncias](#javascript-function-json) para o function.jsna configuração.

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

timeout = "timeout"
retry_interval = "retryInterval"

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instance_id = await client.start_new(req.route_params['functionName'], None, req.get_body())
    logging.log(f"Started orchestration with ID = '${instance_id}'.")

    timeout_in_milliseconds = get_time_in_seconds(req, timeout)
    timeout_in_milliseconds = timeout_in_milliseconds if timeout_in_milliseconds != None else 30000
    retry_interval_in_milliseconds = get_time_in_seconds(req, retry_interval)
    retry_interval_in_milliseconds = retry_interval_in_milliseconds if retry_interval_in_milliseconds != None else 1000

    return client.wait_for_completion_or_create_check_status_response(
        req,
        instance_id,
        timeout_in_milliseconds,
        retry_interval_in_milliseconds
    )

def get_time_in_seconds(req: func.HttpRequest, query_parameter_name: str):
    query_value = req.params.get(query_parameter_name)
    return query_value if query_value != None else 1000
```

---

Chame a função com a linha a seguir. Use 2 segundos para o tempo limite e 0,5 segundos para o intervalo de repetição:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Dependendo do tempo necessário para obter a resposta da instância de orquestração, há dois casos:

* As instâncias de orquestração são concluídas dentro do tempo limite definido (neste caso, 2 segundos) e a resposta é a saída real da instância de orquestração, entregue de forma síncrona:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:14:29 GMT
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* As instâncias de orquestração não podem ser concluídas dentro do tempo limite definido e a resposta é a padrão descrita na [descoberta de URL da API http](durable-functions-http-api.md):

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:13:51 GMT
        Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> O formato das URLs de webhook pode ser diferente, dependendo da versão do host de Azure Functions que você está executando. O exemplo acima refere-se ao host do Azure Functions 2.0.

## <a name="retrieve-http-management-webhook-urls"></a>Recuperar URLs de webhook de gerenciamento HTTP

Você pode usar um sistema externo para monitorar ou gerar eventos para uma orquestração. Os sistemas externos podem se comunicar com Durable Functions por meio de URLs de webhook que fazem parte da resposta padrão descrita na [descoberta de URL da API http](durable-functions-http-features.md#http-api-url-discovery). Como alternativa, as URLs de webhook podem ser acessadas programaticamente usando a [Associação de cliente de orquestração](durable-functions-bindings.md#orchestration-client). Os `CreateHttpManagementPayload` métodos (.net) ou `createHttpManagementPayload` (JavaScript) podem ser usados para obter um objeto serializável que contenha essas URLs de webhook.

Os `CreateHttpManagementPayload` métodos (.net) e `createHttpManagementPayload` (JavaScript) têm um parâmetro:

* **InstanceId**: a ID exclusiva da instância.

Os métodos retornam um objeto com as seguintes propriedades de cadeia de caracteres:

* **Id**: a ID da instância da orquestração (deve ser a mesma da entrada `InstanceId`).
* **StatusQueryGetUri**: a URL de status da instância de orquestração.
* **SendEventPostUri**: a URL "raise event" da instância de orquestração.
* **TerminatePostUri**: a URL "terminate" da instância de orquestração.
* **PurgeHistoryDeleteUri**: a URL "limpar histórico" da instância de orquestração.

O Functions pode enviar instâncias desses objetos a sistemas externos para monitorar ou gerar eventos nas orquestrações correspondentes, conforme mostrado nos exemplos a seguir:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableActivityContext` em vez de `IDurableActivityContext` , você deve usar o `OrchestrationClient` atributo em vez do `DurableClient` atributo, e você deve usar o tipo de `DurableOrchestrationClient` parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

Consulte [Iniciar instâncias](#javascript-function-json) para o function.jsna configuração.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.cosmosdb.cdb.Document:
    client = df.DurableOrchestrationClient(starter)

    payload = client.create_check_status_response(req, instance_id).get_body().decode()

    return func.cosmosdb.CosmosDBConverter.encode({
        id: instance_id,
        payload: payload
    })
```
---

## <a name="rewind-instances-preview"></a>Rebobinar instâncias (visualização)

Se você tiver uma falha de orquestração por um motivo inesperado, poderá *retroceder* a instância para um estado de integridade anterior usando uma API criada para essa finalidade.

> [!NOTE]
> Essa API não pretende ser uma substituição para as políticas de repetição e de tratamento de erros apropriadas. Em vez disso, destina-se a ser usada apenas em casos em que as instâncias de orquestração falhem por motivos inesperados. Para obter mais informações sobre tratamento de erros e políticas de repetição, consulte o artigo [tratamento de erros](durable-functions-error-handling.md) .

Use o `RewindAsync` método (.net) ou `rewind` (JavaScript) da [Associação de cliente de orquestração](durable-functions-bindings.md#orchestration-client) para colocar a orquestração de volta no estado de *execução* . Esse método também executará novamente as falhas de execução da atividade ou da suborquestração que causaram a falha de orquestração.

Por exemplo, digamos que você tenha um fluxo de trabalho que envolva uma série de [aprovações humanas](durable-functions-overview.md#human). Suponha que haja uma série de funções de atividade que notifique alguém de que sua aprovação é necessária e aguarde a resposta em tempo real. Depois que todas as atividades de aprovação receberam respostas ou atingiram o tempo limite, suponha que outra atividade falhe devido a uma configuração incorreta do aplicativo, como uma cadeia de conexão de banco de dados inválida. O resultado é uma falha de orquestração profundamente no fluxo de trabalho. Com a `RewindAsync` API (.net) ou `rewind` (JavaScript), um administrador de aplicativos pode corrigir o erro de configuração e rebobinar a orquestração com falha de volta para o estado imediatamente antes da falha. Nenhuma das etapas de interação humana precisa ser aprovada novamente e a orquestração agora pode ser concluída com êxito.

> [!NOTE]
> O recurso de *retrocesso* não dá suporte à rebobinagem de instâncias de orquestração que usam temporizadores duráveis.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("rewind-queue")] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve usar o `DurableOrchestrationClient` tipo de parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

Consulte [Iniciar instâncias](#javascript-function-json) para o function.jsna configuração.

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Atualmente, não há suporte para este recurso no Python.

<!-- ```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "Orchestrator failed and needs to be revived."
    return client.rewind(instance_id, reason)
``` -->

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Você também pode rebobinar uma instância de orquestração diretamente usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable rewind` . Ele usa os seguintes parâmetros:

* **`id` (obrigatório)**: ID da instância de orquestração.
* **`reason` (opcional)**: motivo para retroceder a instância de orquestração.
* **`connection-string-setting` (opcional)**: nome da configuração do aplicativo que contém a cadeia de conexão de armazenamento a ser usada. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: nome do hub de tarefas Durable Functions a ser usado. Por padrão, o nome do hub de tarefas no [host.jsno](durable-functions-bindings.md#host-json) arquivo é usado.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Limpar histórico de instância

Para remover todos os dados associados a uma orquestração, você pode limpar o histórico de instâncias. Por exemplo, talvez você queira excluir todas as linhas da tabela do Azure e blobs de mensagens grandes associadas a uma instância concluída. Para fazer isso, use o `PurgeInstanceHistoryAsync` método (.net) ou `purgeInstanceHistory` (JavaScript) da [Associação de cliente de orquestração](durable-functions-bindings.md#orchestration-client).

Esse método tem duas sobrecargas. A primeira sobrecarga limpa o histórico pela ID da instância de orquestração:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("purge-queue")] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

Consulte [Iniciar instâncias](#javascript-function-json) para o function.jsna configuração.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    return client.purge_instance_history(instance_id)
```

---

O exemplo a seguir mostra uma função disparada por temporizador que limpa o histórico de todas as instâncias de orquestração concluídas após o intervalo de tempo especificado. Nesse caso, ele remove dados de todas as instâncias concluídas 30 ou mais dias atrás. Ele está agendado para ser executado uma vez por dia, às 12:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
        DateTime.MinValue,
        DateTime.UtcNow.AddDays(-30),  
        new List<OrchestrationStatus>
        {
            OrchestrationStatus.Completed
        });
}
```

> [!NOTE]
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve usar o `DurableOrchestrationClient` tipo de parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O `purgeInstanceHistoryBy` método pode ser usado para limpar condicionalmente o histórico de instâncias para várias instâncias.

**function.json**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Este exemplo se destina Durable Functions versão 2. x. Na versão 1. x, use `orchestrationClient` em vez de `durableClient` .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.DurableOrchestrationStatus import OrchestrationRuntimeStatus
from datetime import datetime, timedelta

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    created_time_from = datetime.min
    created_time_to = datetime.today() + timedelta(days = -30)
    runtime_statuses = [OrchestrationRuntimeStatus.Completed]

    return client.purge_instance_history_by(created_time_from, created_time_to, runtime_statuses)
```
---

> [!NOTE]
> Para que a operação limpar histórico seja concluída com êxito, o status de tempo de execução da instância de destino deve ser **concluído**, **encerrado** ou **com falha**.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Você pode limpar o histórico de uma instância de orquestração usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable purge-history` . Semelhante ao segundo exemplo de C# na seção anterior, ele limpa o histórico de todas as instâncias de orquestração criadas durante um intervalo de tempo especificado. Você pode filtrar ainda mais as instâncias limpas por status de tempo de execução. O comando tem vários parâmetros:

* **`created-after` (opcional)**: limpar o histórico de instâncias criadas após esta data/hora (UTC). Datetimes formato ISO 8601 aceito.
* **`created-before` (opcional)**: limpar o histórico de instâncias criadas antes desta data/hora (UTC). Datetimes formato ISO 8601 aceito.
* **`runtime-status` (opcional)**: limpar o histórico de instâncias com um status específico (por exemplo, executando ou concluído). Pode fornecer o status de vários (separado por espaço).
* **`connection-string-setting` (opcional)**: nome da configuração do aplicativo que contém a cadeia de conexão de armazenamento a ser usada. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: nome do hub de tarefas Durable Functions a ser usado. Por padrão, o nome do hub de tarefas no [host.jsno](durable-functions-bindings.md#host-json) arquivo é usado.

O comando a seguir exclui o histórico de todas as instâncias com falha criadas antes de 14 de novembro de 2018 às 7:35 PM (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Excluir um hub de tarefas

Usando o comando [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` , você pode excluir todos os artefatos de armazenamento associados a um hub de tarefas específico, incluindo tabelas de armazenamento do Azure, filas e blobs. O comando tem dois parâmetros:

* **`connection-string-setting` (opcional)**: nome da configuração do aplicativo que contém a cadeia de conexão de armazenamento a ser usada. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: nome do hub de tarefas Durable Functions a ser usado. Por padrão, o nome do hub de tarefas no [host.jsno](durable-functions-bindings.md#host-json) arquivo é usado.

O comando a seguir exclui todos os dados do armazenamento do Azure associados ao `UserTest` Hub de tarefas.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como lidar com o controle de versão](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Referência de API HTTP interna para gerenciamento de instância](durable-functions-http-api.md)
