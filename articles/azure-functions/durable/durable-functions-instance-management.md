---
title: Gerenciar instâncias nas Funções Duráveis – Azure
description: Saiba como gerenciar instâncias na extensão de Funções Duráveis do Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 12/07/2018
ms.date: 03/19/2019
ms.author: v-junlch
ms.openlocfilehash: ee96bc5e17051ab37be34eecbb8e4fe35599cd5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730762"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Gerenciar instâncias em Durable Functions no Azure

Se você estiver usando o [funções duráveis](durable-functions-overview.md) extensão para o Azure Functions ou você queira começar a fazer isso, certifique-se de que você esteja tirando o máximo proveito dele. Você pode otimizar suas instâncias de orquestração de funções duráveis Aprendendo mais sobre como gerenciá-los. Este artigo mostra os detalhes de cada operação de gerenciamento de instância.

Você pode iniciar e encerrar as instâncias, por exemplo, e você pode consultar instâncias, incluindo a habilidade para consultar todas as instâncias e consultas com filtros. Além disso, você pode enviar eventos para instâncias, aguardar a conclusão da orquestração e recuperar URLs de webhook de gerenciamento de HTTP. Este artigo aborda as outras operações de gerenciamento, também, incluindo retroceder instâncias, limpeza de histórico de instância e exclusão de um hub de tarefas.

Nas funções duráveis, você tem opções para como você deseja implementar cada uma dessas operações de gerenciamento. Este artigo fornece exemplos que usam o [as ferramentas básicas do Azure Functions](../functions-run-local.md) para ambas as .NET (C#) e JavaScript.

## <a name="start-instances"></a>Iniciar instâncias

É importante ser capaz de iniciar uma instância de orquestração. Normalmente, isso é feito quando você estiver usando uma associação de funções duráveis no gatilho da função para outra.

O [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) método o [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) ou `startNew` no `DurableOrchestrationClient` (JavaScript) inicia uma nova instância. Adquirir instâncias dessa classe usando o `orchestrationClient` associação. Internamente, esse método enfileira uma mensagem na fila de controle, que por sua vez dispara o início de uma função com o nome especificado que usa a associação de gatilho `orchestrationTrigger`.

Essa operação assíncrona é concluída quando o processo de orquestração é agendado com êxito. O processo de orquestração deve começar dentro de 30 segundos. Se demorar mais, você verá um `TimeoutException`.

> [!WARNING]
> Ao desenvolver localmente em JavaScript, defina a variável de ambiente `WEBSITE_HOSTNAME` à `localhost:<port>` (por exemplo, `localhost:7071`) para usar métodos no `DurableOrchestrationClient`. Para obter mais informações sobre esse requisito, confira o [Problema no GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="net"></a>.NET

Os parâmetros para [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) são os seguintes:

* **Nome**: O nome da função de orquestrador a ser agendada.
* **Entrada**: Qualquer dado serializável em JSON, que deve ser passado como a entrada para a função de orquestrador.
* **InstanceId**: (opcional) a ID exclusiva da instância. Se você não especificar esse parâmetro, o método usa uma ID de aleatório.

Veja um exemplo simples em C#:

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

Os parâmetros para `startNew` são os seguintes:

* **Nome**: O nome da função de orquestrador a ser agendada.
* **InstanceId**: (opcional) a ID exclusiva da instância. Se você não especificar esse parâmetro, o método usa uma ID de aleatório.
* **Entrada**: (opcional) Qualquer dado serializável em JSON, que deve ser passado como a entrada para a função de orquestrador.

Aqui está um exemplo de JavaScript simples:

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

> [!TIP]
> Use um identificador aleatório para a ID de instância. Isso ajuda a garantir uma distribuição de carga igual, quando você estiver dimensionando de funções de orquestrador entre várias VMs. O momento adequado para usar IDs de instância não aleatórias é quando a ID deve vir de uma fonte externa, ou quando você estiver implementando o [orquestrador singleton](durable-functions-singletons.md) padrão.

### <a name="azure-functions-core-tools"></a>Ferramentas básicas do Azure Functions

Você também pode iniciar uma instância diretamente, usando o [as ferramentas básicas do Azure Functions](../functions-run-local.md) `durable start-new` comando. Ele usa os seguintes parâmetros:

* **`function-name` (obrigatório)**: Nome da função iniciar.
* **`input` (opcional)**: Para a função, de forma embutida ou por meio de um arquivo JSON de entrada. Para arquivos, adicionar um prefixo para o caminho para o arquivo com `@`, tais como `@path/to/file.json`.
* **`id` (opcional)**: ID da instância de orquestração. Se você não especificar esse parâmetro, o comando usa um GUID aleatório.
* **`connection-string-setting` (opcional)**: Nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é AzureWebJobsStorage.
* **`task-hub-name` (opcional)**: Nome do hub de tarefas das funções duráveis para usar. O padrão é DurableFunctionsHub. Você também pode definir isso na [host. JSON](durable-functions-bindings.md#host-json) usando durableTask:HubName.

> [!NOTE]
> Comandos de ferramentas principais pressupõem que você está executando-os no diretório raiz de um aplicativo de funções. Se você fornecer explicitamente o `connection-string-setting` e `task-hub-name` parâmetros, você pode executar os comandos de qualquer diretório. Embora seja possível executar esses comandos sem um host de aplicativo de função em execução, você pode achar que não é possível observar alguns efeitos, a menos que o host está em execução. Por exemplo, o `start-new` enfileira uma mensagem de início para o hub de tarefas de destino, mas a orquestração, na verdade, não é executado a menos que haja um processo de host do aplicativo de função em execução que pode processar a mensagem de comando.

O comando a seguir inicia a função chamada HelloWorld e passa o conteúdo do arquivo `counter-data.json` a ele:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Instâncias de consulta

Como parte do esforço para gerenciar suas orquestrações, provavelmente você precisará coletar informações sobre o status de uma instância de orquestração (por exemplo, se ele foi concluída normalmente ou falhou).

O método [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) na classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) ou o método `getStatus` na classe `DurableOrchestrationClient` (JavaScript) consulta o status de uma instância de orquestração.

Ele usa um `instanceId` (obrigatório), `showHistory` (opcional) e `showHistoryOutput` (opcional) e `showInput` (opcional, .NET apenas) como parâmetros.

* **`showHistory`**: Se definido como `true`, a resposta contém o histórico de execução.
* **`showHistoryOutput`**: Se definido como `true`, o histórico de execução contém saídas de atividade.
* **`showInput`**: Se definido como `false`, a resposta não conterá a entrada da função. O valor padrão é `true`. (.NET apenas)

O método retorna um objeto JSON com as seguintes propriedades:

* **Nome**: O nome da função de orquestrador.
* **InstanceId**: A ID da instância da orquestração (deve ser a mesma da entrada `instanceId`).
* **CreatedTime**: a hora em que a função de orquestrador começou a ser executada.
* **LastUpdatedTime**: A hora em que a orquestração passou por uma verificação pontual pela última vez.
* **Entrada**: A entrada da função como um valor JSON. Este campo não é populado se `showInput` é false.
* **CustomStatus**: Status de orquestração personalizado no formato JSON.
* **Saída**: A saída da função como um valor JSON (se a função tiver sido concluída). Se a função de orquestrador tiver falhado, essa propriedade inclui os detalhes da falha. Se a função de orquestrador foi encerrada, essa propriedade inclui o motivo para o encerramento (se houver).
* **RuntimeStatus**: Um dos seguintes valores:
  * **Pendente**: A instância foi agendada, mas ainda não foi iniciada em execução.
  * **Executando**: A instância começou a ser executada.
  * **Concluído**: A instância foi concluída normalmente.
  * **ContinuedAsNew**: A instância reiniciou a si mesma com um novo histórico. Esse estado é temporário.
  * **Falha**: A instância falhou com um erro.
  * **Encerrado**: A instância foi interrompida abruptamente.
* **Histórico**: Histórico de execução de orquestração. Este campo é preenchido somente se `showHistory` é definido como `true`.

Este método retornará `null` se a instância não existir ou não tiver começado a ser executada.

### <a name="c"></a>C#

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="azure-functions-core-tools"></a>Ferramentas básicas do Azure Functions

Também é possível obter o status de uma instância de orquestração diretamente, usando o [as ferramentas básicas do Azure Functions](../functions-run-local.md) `durable get-runtime-status` comando. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)**: ID da instância de orquestração.
* **`show-input` (opcional)**: Se definido como `true`, a resposta contém a entrada da função. O valor padrão é `false`.
* **`show-output` (opcional)**: Se definido como `true`, a resposta contém a saída da função. O valor padrão é `false`.
* **`connection-string-setting` (opcional)**: Nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: Nome do hub de tarefas das funções duráveis para usar. O padrão é `DurableFunctionsHub`. Ele também pode ser definido no [host. JSON](durable-functions-bindings.md#host-json), usando durableTask:HubName.

O comando a seguir recupera o status de uma instância com uma ID de instância de orquestração de 0ab8c55a66644d68a3a8b220b12d209c (incluindo a entrada e saída). Ele pressupõe que você está executando o `func` comando do diretório raiz do aplicativo de funções:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Você pode usar o `durable get-history` comando para recuperar o histórico de uma instância de orquestração. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)**: ID da instância de orquestração.
* **`connection-string-setting` (opcional)**: Nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: Nome do hub de tarefas das funções duráveis para usar. O padrão é `DurableFunctionsHub`. Ele também pode ser definido no host. JSON, usando durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Todas as instâncias de consulta

Em vez de uma instância sua orquestração em um tempo de consulta, você talvez ache mais eficiente para consultar todos eles ao mesmo tempo.

É possível usar o método `GetStatusAsync` (.NET) ou `getStatusAll` (JavaScript) para consultar os status de todas as instâncias de orquestração. No .NET, você pode passar um `CancellationToken` objeto caso você deseje para cancelá-la. O método retorna objetos com as mesmas propriedades que o método `GetStatusAsync` com parâmetros.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

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

### <a name="azure-functions-core-tools"></a>Ferramentas básicas do Azure Functions

Também é possível para instâncias de consulta diretamente, usando o [as ferramentas básicas do Azure Functions](../functions-run-local.md) `durable get-instances` comando. Ele usa os seguintes parâmetros:

* **`top` (opcional)**: Este comando dá suporte à paginação. Esse parâmetro corresponde ao número de instâncias recuperadas por solicitação. O padrão é 10.
* **`continuation-token` (opcional)**: Um token para indicar qual página ou seção de instâncias a serem recuperados. Cada `get-instances` execução retorna um token para o próximo conjunto de instâncias.
* **`connection-string-setting` (opcional)**: Nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: Nome do hub de tarefas das funções duráveis para usar. O padrão é `DurableFunctionsHub`. Ele também pode ser definido no [host. JSON](durable-functions-bindings.md#host-json), usando durableTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Instâncias de consulta com filtros

E se você não precisa realmente todas as informações que uma consulta de instância padrão pode fornecer? Por exemplo, se você estiver apenas procurando a hora de criação de orquestração ou o status de tempo de execução de orquestração? Você pode restringir sua consulta aplicando filtros.

Use o `GetStatusAsync` (.NET) ou `getStatusBy` predefinidos de método (JavaScript) para obter uma lista de instâncias de orquestração que correspondem a um conjunto de filtros.

### <a name="c"></a>C#

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IEnumerable<OrchestrationRuntimeStatus> runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

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

### <a name="azure-functions-core-tools"></a>Ferramentas básicas do Azure Functions

Em ferramentas básicas do Azure Functions, você também pode usar o `durable get-instances` com filtros. Além dos mencionados anteriormente `top`, `continuation-token`, `connection-string-setting`, e `task-hub-name` parâmetros, você pode usar três parâmetros de filtro (`created-after`, `created-before`, e `runtime-status`).

* **`created-after` (opcional)**: Recuperar as instâncias criadas após essa data/hora (UTC). Datetimes formato ISO 8601 aceito.
* **`created-before` (opcional)**: Recuperar as instâncias criadas antes dessa data/hora (UTC). Datetimes formato ISO 8601 aceito.
* **`runtime-status` (opcional)**: Recupere as instâncias com um status específico (por exemplo, em execução ou concluído). Pode fornecer o status de vários (separado por espaço).
* **`top` (opcional)**: Número de instâncias recuperadas por solicitação. O padrão é 10.
* **`continuation-token` (opcional)**: Um token para indicar qual página ou seção de instâncias a serem recuperados. Cada `get-instances` execução retorna um token para o próximo conjunto de instâncias.
* **`connection-string-setting` (opcional)**: Nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: Nome do hub de tarefas das funções duráveis para usar. O padrão é `DurableFunctionsHub`. Ele também pode ser definido no [host. JSON](durable-functions-bindings.md#host-json), usando durableTask:HubName.

Se você não fornecer todos os filtros (`created-after`, `created-before`, ou `runtime-status`), o comando simplesmente recupera `top` instâncias, sem considerar para o horário de criação ou status de tempo de execução.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Encerrar as instâncias

Se você tiver uma instância de orquestração estiver demorando muito para ser executado, ou você só precisa interrompê-lo antes de ser concluída por algum motivo, você tem a opção de encerrá-lo.

Você pode usar o [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) método o [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe (.NET), ou o `terminate` método do `DurableOrchestrationClient` classe (JavaScript). Os dois parâmetros são um `instanceId` e um `reason` cadeia de caracteres que são gravados nos logs e o status da instância. Uma instância encerrada deixará de ser executado assim que atingir o próximo `await` (.NET) ou `yield` ponto (JavaScript) ou ele é encerrado imediatamente se já estiver um `await` ou `yield`.

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> Encerramento de instância não propaga no momento. Funções de atividade e as suborquestrações executadas até a conclusão, independentemente se você tiver finalizado a instância de orquestração que os chamou.

### <a name="azure-functions-core-tools"></a>Ferramentas básicas do Azure Functions

Você também pode terminar uma instância de orquestração diretamente, usando o [as ferramentas básicas do Azure Functions](../functions-run-local.md) `durable terminate` comando. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)**: ID da instância de orquestração para encerrar.
* **`reason` (opcional)**: Motivo do término.
* **`connection-string-setting` (opcional)**: Nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: Nome do hub de tarefas das funções duráveis para usar. O padrão é `DurableFunctionsHub`. Ele também pode ser definido no [host. JSON](durable-functions-bindings.md#host-json), usando durableTask:HubName.

O comando a seguir encerra uma instância de orquestração com uma ID de 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Enviar eventos para instâncias

Em alguns cenários, é importante para suas funções de orquestrador ser capaz de aguardar e escutar eventos externos. Isso inclui [monitorar funções](durable-functions-concepts.md#monitoring) e as funções que estão aguardando [interação humana](durable-functions-concepts.md#human).

Enviar notificações de eventos para instâncias em execução usando o [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) método da [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe (.NET) ou o `raiseEvent` método da `DurableOrchestrationClient` classe ( JavaScript). Instâncias que podem lidar com esses eventos são aquelas que estão aguardando uma chamada para [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) ou `waitForExternalEvent` (JavaScript).

Os parâmetros para [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) e `raiseEvent` (JavaScript) são conforme a seguir:

* **InstanceId**: A ID exclusiva da instância.
* **EventName**: Nome do evento a ser enviado.
* **EventData**: Uma carga serializável em JSON para enviar para a instância.

### <a name="c"></a>C#

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!IMPORTANT]
> Se não houver nenhuma instância de orquestração com a ID de instância especificado ou se a instância não estiver aguardando o nome do evento especificado, a mensagem de evento é descartada. Para obter mais informações sobre esse comportamento, consulte o [Problema no GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="azure-functions-core-tools"></a>Ferramentas básicas do Azure Functions

Você também pode disparar um evento para uma instância de orquestração diretamente, usando o [as ferramentas básicas do Azure Functions](../functions-run-local.md) `durable raise-event` comando. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)**: ID da instância de orquestração.
* **`event-name` (opcional)**: Nome do evento para gerar. O padrão é `$"Event_{RandomGUID}"`.
* **`event-data` (opcional)**: Dados a serem enviados para a instância de orquestração. Isso pode ser o caminho para um arquivo JSON, ou você pode fornecer os dados diretamente na linha de comando.
* **`connection-string-setting` (opcional)**: Nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: Nome do hub de tarefas das funções duráveis para usar. O padrão é `DurableFunctionsHub`. Ele também pode ser definido no [host. JSON](durable-functions-bindings.md#host-json), usando durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Aguardar a conclusão da orquestração

Orquestrações de longa execução, convém aguardar e obter os resultados de uma orquestração. Nesses casos, também é útil ser capaz de definir um período de tempo limite na orquestração. Se o tempo limite for excedido, o estado da orquestração deve ser retornado em vez dos resultados.

O [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe expõe um [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API no .NET. Você pode usar essa API para obter a saída real de uma instância de orquestração de forma síncrona. No JavaScript, a `DurableOrchestrationClient` classe expõe uma `waitForCompletionOrCreateCheckStatusResponse` API para a mesma finalidade. Quando eles não estiverem definidos, os métodos usam um valor padrão de 10 segundos para `timeout`e 1 segundo para `retryInterval`.  

Veja um exemplo de função de gatilho HTTP que demonstra como usar essa API:

```C#
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace VSSample
{
    public static class HttpSyncStart
    {
        private const string Timeout = "timeout";
        private const string RetryInterval = "retryInterval";

        [FunctionName("HttpSyncStart")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/wait")]
            HttpRequestMessage req,
            [OrchestrationClient] DurableOrchestrationClientBase starter,
            string functionName,
            ILogger log)
        {
            // Function input comes from the request content.
            dynamic eventData = await req.Content.ReadAsAsync<object>();
            string instanceId = await starter.StartNewAsync(functionName, eventData);

            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

            TimeSpan timeout = GetTimeSpan(req, Timeout) ?? TimeSpan.FromSeconds(30);
            TimeSpan retryInterval = GetTimeSpan(req, RetryInterval) ?? TimeSpan.FromSeconds(1);
            
            return await starter.WaitForCompletionOrCreateCheckStatusResponseAsync(
                req,
                instanceId,
                timeout,
                retryInterval);
        }

        private static TimeSpan? GetTimeSpan(HttpRequestMessage request, string queryParameterName)
        {
            string queryParameterStringValue = request.RequestUri.ParseQueryString()[queryParameterName];
            if (string.IsNullOrEmpty(queryParameterStringValue))
            {
                return null;
            }

            return TimeSpan.FromSeconds(double.Parse(queryParameterStringValue));
        }
    }
}
```

```Javascript
const df = require("durable-functions");

const timeout = "timeout";
const retryInterval = "retryInterval";

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = await client.startNew(req.params.functionName, undefined, req.body);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    const timeoutInMilliseconds = getTimeInSeconds(req, timeout) || 30000;
    const retryIntervalInMilliseconds = getTimeInSeconds(req, retryInterval) || 1000;

    return client.waitForCompletionOrCreateCheckStatusResponse(
        context.bindingData.req,
        instanceId,
        timeoutInMilliseconds,
        retryIntervalInMilliseconds);
};

function getTimeInSeconds (req, queryParameterName) {
    const queryValue = req.query[queryParameterName];
    return queryValue
        ? queryValue // expected to be in seconds
        * 1000 : undefined;
}
```

Chame a função com a seguinte linha. Use 2 segundos para o tempo limite e 0,5 segundos para o intervalo de repetição:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Dependendo do tempo necessário para obter a resposta da instância de orquestração, há dois casos:

* As instâncias de orquestração concluída dentro do tempo limite definido (no caso 2 segundos) e a resposta é a saída de instância de orquestração real, entregue sincronicamente:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* As instâncias de orquestração não pode ser concluída dentro do tempo limite definido, e a resposta é o padrão descrito em [descoberta de URL da API HTTP](durable-functions-http-api.md):

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}",
            "rewindPostUri": "https://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/rewind?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> O formato das URLs de webhook pode diferir, dependendo de qual versão do host do Azure Functions em execução. O exemplo acima refere-se ao host do Azure Functions 2.x.

## <a name="retrieve-http-management-webhook-urls"></a>Recuperar URLs de webhook de gerenciamento de HTTP

Você pode usar um sistema externo monitorar ou gerar eventos para uma orquestração. Sistemas externos podem se comunicar com as funções duráveis por meio de URLs de webhook que fazem parte da resposta padrão descrita em [descoberta de URL da API HTTP](durable-functions-http-api.md). No entanto, as URLs de webhook também podem ser acessadas programaticamente no cliente de orquestração ou em uma função de atividade. Faça isso usando o [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) método da [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe (.NET), ou o `createHttpManagementPayload` método da `DurableOrchestrationClient` classe (JavaScript).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) e `createHttpManagementPayload` têm um parâmetro:

* **instanceId**: A ID exclusiva da instância.

Os métodos retornam uma instância do [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) ou um objeto (JavaScript), com as seguintes propriedades de cadeia de caracteres:

* **Id**: A ID da instância da orquestração (deve ser a mesma da entrada `InstanceId`).
* **StatusQueryGetUri**: A URL de status da instância de orquestração.
* **SendEventPostUri**: A URL "acionar evento" da instância de orquestração.
* **TerminatePostUri**: A URL "encerrar" da instância de orquestração.
* **RewindPostUri**: A URL para “retroceder” do status da instância de orquestração.

As funções de atividade podem enviar uma instância desses objetos para sistemas externos para monitorar ou elevar eventos para uma orquestração:

### <a name="c"></a>C#

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] DurableActivityContext ctx,
    [OrchestrationClient] DurableOrchestrationClient client,
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

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

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

## <a name="rewind-instances-preview"></a>Instâncias de retrocesso (versão prévia)

Se você tiver uma falha de orquestração por um motivo inesperado, você pode *rewind* a instância a um estado íntegro anteriormente usando uma API criada para essa finalidade.

> [!NOTE]
> Essa API não pretende ser uma substituição para as políticas de repetição e de tratamento de erros apropriadas. Em vez disso, destina-se a ser usada apenas em casos em que as instâncias de orquestração falhem por motivos inesperados. Para obter mais detalhes sobre políticas de repetição e tratamento de erro, consulte o [tratamento de erro](durable-functions-error-handling.md) tópico.

Use o [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) ou `rewindAsync` API (JavaScript) para colocar a orquestração de volta para o *executando* estado. Execute novamente as falhas de execução de atividade ou suborchestration que causou a falha de orquestração.

Por exemplo, digamos que você tenha um fluxo de trabalho que envolve uma série de [aprovações humanas](durable-functions-concepts.md#human). Suponha que há uma série de funções de atividade que notifique alguém que sua aprovação for necessária e espera-out a resposta em tempo real. Afinal de contas da aprovação atividades tem recebido as respostas ou atingiu o tempo limite, suponha que outra atividade falha devido a um erro de configuração de aplicativo, como uma cadeia de caracteres de conexão de banco de dados inválido. O resultado é uma falha de orquestração profundamente no fluxo de trabalho. Com o `RewindAsync` (.NET) ou `rewindAsync` administrador (JavaScript) API, um aplicativo pode corrigir o erro de configuração e retroceder a orquestração com falha para o estado imediatamente antes da falha. Nenhuma das etapas de interação humana precisa ser Reaprovado e a orquestração pode agora ser concluída com êxito.

> [!NOTE]
> O *rewind* recurso não dá suporte a instâncias de orquestração retroceder que usam temporizadores duráveis.

### <a name="c"></a>C#

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="azure-functions-core-tools"></a>Ferramentas básicas do Azure Functions

Você também pode retroceder a uma instância de orquestração diretamente usando o [as ferramentas básicas do Azure Functions](../functions-run-local.md) `durable rewind` comando. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)**: ID da instância de orquestração.
* **`reason` (opcional)**: Motivo para retroceder a instância de orquestração.
* **`connection-string-setting` (opcional)**: Nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: Nome do hub de tarefas das funções duráveis para usar. O padrão é `DurableFunctionsHub`. Ele também pode ser definido no [host. JSON](durable-functions-bindings.md#host-json), usando durableTask:HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Limpar o histórico de instância

Para remover todos os dados associados com uma orquestração, você pode limpar o histórico de instância. Por exemplo, convém se livrar das linhas da tabela do Azure e blobs de mensagens grandes, se eles existirem. Para fazer isso, use o [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) API.

> [!NOTE]
> A `PurgeInstanceHistoryAsync` API está disponível atualmente apenas para C#.

 Esse método tem duas sobrecargas. O primeiro deles limpa histórico pela ID da instância de orquestração:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

O segundo exemplo mostra uma função disparada por temporizador que limpa o histórico de todas as instâncias de orquestração concluída após o intervalo de tempo especificado. Nesse caso, ele remove os dados para todas as instâncias concluídas 30 ou mais dias atrás. Ele está agendado para executar uma vez por dia à meia-noite:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
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
> Para o processo de função disparada por tempo seja bem-sucedida, o status de tempo de execução deve ser **Completed**, **encerrado**, ou **falha**.

### <a name="azure-functions-core-tools"></a>Ferramentas básicas do Azure Functions

Você pode limpar o histórico de uma instância de orquestração usando o [as ferramentas básicas do Azure Functions](../functions-run-local.md) `durable purge-history` comando. Semelhante ao segundo C# exemplo na seção anterior, ele limpa o histórico de todas as instâncias de orquestração criadas durante um intervalo de tempo especificado. Você pode filtrar ainda mais limpas instâncias por status de tempo de execução. O comando tem vários parâmetros:

* **`created-after` (opcional)**: Limpar as instâncias criadas após essa data/hora (UTC). Datetimes formato ISO 8601 aceito.
* **`created-before` (opcional)**: Limpar as instâncias criadas antes dessa data/hora (UTC). Datetimes formato ISO 8601 aceito.
* **`runtime-status` (opcional)**: Limpe o histórico de instâncias com um status específico (por exemplo, em execução ou concluído). Pode fornecer o status de vários (separado por espaço).
* **`connection-string-setting` (opcional)**: Nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: Nome do hub de tarefas das funções duráveis para usar. O padrão é `DurableFunctionsHub`. Ele também pode ser definido no [host. JSON](durable-functions-bindings.md#host-json), usando durableTask:HubName.

O comando a seguir exclui o histórico de todas as instâncias com falha criados antes de 14 de novembro de 2018 no 7:35 PM (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Excluir um hub de tarefas

Usando o [as ferramentas básicas do Azure Functions](../functions-run-local.md) `durable delete-task-hub` de comando, você pode excluir todos os artefatos de armazenamento associados a um hub de tarefas específico. Isso inclui tabelas de armazenamento, filas e blobs do Azure. O comando tem dois parâmetros:

* **`connection-string-setting` (opcional)**: Nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (opcional)**: Nome do hub de tarefas das funções duráveis para usar. O padrão é `DurableFunctionsHub`. Ele também pode ser definido no [host. JSON](durable-functions-bindings.md#host-json), usando durableTask:HubName.

O comando a seguir exclui todos os dados de armazenamento do Azure associados a `UserTest` hub de tarefas.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como usar APIs HTTP para o gerenciamento de instâncias](durable-functions-http-api.md)

<!-- Update_Description: wording update -->