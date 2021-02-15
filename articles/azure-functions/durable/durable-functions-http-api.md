---
title: APIs HTTP no Durable Functions-Azure Functions
description: Saiba como implementar APIs HTTP na extensão de Funções Duráveis do Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 4e4081ecca4714c713d105d363a83a4f96a0d3fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84697836"
---
# <a name="http-api-reference"></a>Referência de API HTTP

A extensão Durable Functions expõe um conjunto de APIs HTTP internas que podem ser usadas para executar tarefas de gerenciamento em [orquestrações](durable-functions-types-features-overview.md#orchestrator-functions), [entidades](durable-functions-types-features-overview.md#entity-functions)e hubs de [tarefas](durable-functions-task-hubs.md). Essas APIs HTTP são WebHooks de extensibilidade que são autorizadas pelo host Azure Functions, mas manipuladas diretamente pela extensão Durable Functions.

Todas as APIs HTTP implementadas pela extensão exigem os seguintes parâmetros. O tipo de dados de todos os parâmetros é `string`.

| Parâmetro        | Tipo de parâmetro  | Descrição |
|------------------|-----------------|-------------|
| **`taskHub`**    | Cadeia de consulta    | O nome do [hub de tarefas](durable-functions-task-hubs.md). Se não for especificado, o nome do hub de tarefas do aplicativo de funções será presumido. |
| **`connection`** | Cadeia de consulta    | O **nome** da cadeia de conexão para a conta de armazenamento. Se não for especificada, a cadeia de conexão padrão do aplicativo de funções será presumida. |
| **`systemKey`**  | Cadeia de consulta    | A chave de autorização necessária para invocar a API. |

`systemKey` é uma chave de autorização gerada automaticamente pelo host Azure Functions. Ela concede acesso especificamente às APIs da extensão de Tarefas Duráveis e pode ser gerenciada da mesma maneira que as [outras chaves de autorização](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Você pode gerar URLs que contêm os valores corretos `taskHub` , `connection` e de cadeia de caracteres de `systemKey` consulta usando APIs de [Associação de cliente de orquestração](durable-functions-bindings.md#orchestration-client) , como as `CreateCheckStatusResponse` `CreateHttpManagementPayload` APIs e no .net, ou as `createCheckStatusResponse` `createHttpManagementPayload` APIs e em JavaScript.

As próximas seções tratam das APIs HTTP específicas com suporte da extensão e fornecem exemplos de como elas podem ser usadas.

## <a name="start-orchestration"></a>Iniciar orquestração

Inicia a execução de uma nova instância da função de orquestrador especificada.

### <a name="request"></a>Solicitação

Para a versão 1. x do tempo de execução do functions, a solicitação é formatada da seguinte maneira (várias linhas são mostradas para maior clareza):

```http
POST /admin/extensions/DurableTaskExtension/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Na versão 2. x do tempo de execução do functions, o formato da URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
POST /runtime/webhooks/durabletask/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Parâmetros de solicitação para essa API incluem o conjunto padrão mencionado anteriormente, bem como o parâmetro exclusivo a seguir:

| Campo              | Tipo de parâmetro  | Descrição |
|--------------------|-----------------|-------------|
| **`functionName`** | URL             | O nome da função de orquestrador a ser iniciada. |
| **`instanceId`**   | URL             | Parâmetro opcional. A ID da instância de orquestração. Se não for especificado, a função de orquestrador será iniciada com uma ID de instância aleatória. |
| **`{content}`**    | Conteúdo da solicitação | Opcional. A entrada da função de orquestrador formatada em JSON. |

### <a name="response"></a>Resposta

Vários valores de código de status possíveis podem ser retornados.

* **HTTP 202 (aceito)**: a função de orquestrador especificada foi agendada para iniciar a execução. O `Location` cabeçalho de resposta contém uma URL para sondar o status de orquestração.
* **HTTP 400 (solicitação inválida)**: a função de orquestrador especificada não existe, a ID de instância especificada não era válida ou o conteúdo da solicitação não era um JSON válido.

Veja a seguir um exemplo de solicitação que inicia uma `RestartVMs` função de orquestrador e inclui a carga do objeto JSON:

```http
POST /runtime/webhooks/durabletask/orchestrators/RestartVMs?code=XXX
Content-Type: application/json
Content-Length: 83

{
    "resourceGroup": "myRG",
    "subscriptionId": "111deb5d-09df-4604-992e-a968345530a9"
}
```

A carga de resposta para os casos **HTTP 202** é um objeto JSON com os seguintes campos:

| Campo                       | Descrição                          |
|-----------------------------|--------------------------------------|
| **`id`**                    |A ID da instância de orquestração. |
| **`statusQueryGetUri`**     |A URL de status da instância de orquestração. |
| **`sendEventPostUri`**      |A URL "acionar evento" da instância de orquestração. |
| **`terminatePostUri`**      |A URL "encerrar" da instância de orquestração. |
| **`purgeHistoryDeleteUri`** |A URL "limpar histórico" da instância de orquestração. |
| **`rewindPostUri`**         |apresentação A URL de "retrocesso" da instância de orquestração. |

O tipo de dados de todos os campos é `string`.

Aqui está um exemplo de carga de resposta para uma instância de orquestração com `abc123` como sua ID (formatada para legibilidade):

```http
{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

A resposta HTTP é destinada a ser compatível com o *padrão de consumidor de sondagem*. Ele também inclui os seguintes cabeçalhos de resposta notáveis:

* **Location**: a URL do ponto de extremidade de status. Essa URL contém o mesmo valor que o `statusQueryGetUri` campo.
* **Tentar novamente**: o número de segundos de espera entre as operações de sondagem. O valor padrão é `10`.

Para obter mais informações sobre o padrão de sondagem HTTP assíncrona, consulte a documentação de [rastreamento de operação](durable-functions-http-features.md#async-operation-tracking) assíncrona http.

## <a name="get-instance-status"></a>Obter status da instância

Obtém o status de uma instância de orquestração especificada.

### <a name="request"></a>Solicitação

Para a versão 1. x do tempo de execução do functions, a solicitação é formatada da seguinte maneira (várias linhas são mostradas para maior clareza):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Na versão 2. x do tempo de execução do functions, o formato da URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Parâmetros de solicitação para essa API incluem o conjunto padrão mencionado anteriormente, bem como o parâmetro exclusivo a seguir:

| Campo                   | Tipo de parâmetro  | Descrição |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | A ID da instância de orquestração. |
| **`showInput`**         | Cadeia de consulta    | Parâmetro opcional. Se definido como `false` , a entrada da função não será incluída na carga de resposta.|
| **`showHistory`**       | Cadeia de consulta    | Parâmetro opcional. Se definido como `true`, o histórico de execução da orquestração será incluído na carga da resposta.|
| **`showHistoryOutput`** | Cadeia de consulta    | Parâmetro opcional. Se definido como `true` , as saídas de função serão incluídas no histórico de execução de orquestração.|
| **`createdTimeFrom`**   | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias retornadas que foram criadas no ou após o carimbo de data/hora ISO8601 fornecido.|
| **`createdTimeTo`**     | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias retornadas que foram criadas no ou antes do carimbo de data/hora ISO8601 fornecido.|
| **`runtimeStatus`**     | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias retornadas com base em seu status de runtime. Para ver a lista de possíveis valores de status de tempo de execução, consulte o artigo [instâncias de consulta](durable-functions-instance-management.md) . |

### <a name="response"></a>Resposta

Vários valores de código de status possíveis podem ser retornados.

* **HTTP 200 (OK)**: a instância especificada está em um estado concluído.
* **HTTP 202 (Aceito)**: a instância especificada está em andamento.
* **HTTP 400 (Solicitação incorreta)**: a instância especificada falhou ou foi encerrada.
* **HTTP 404 (Não encontrado)**: a instância especificada não existe ou não começou a ser executada.
* **HTTP 500 (erro interno do servidor)**: A instância especificada falhou com uma exceção sem tratamento.

A carga de resposta para os casos de **HTTP 200** e **HTTP 202** é um objeto JSON com os campos a seguir:

| Campo                 | Tipo de dados | Descrição |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | string    | O status de runtime da instância. Os valores incluem *Em execução*, *Pendente*, *Falha*, *Cancelado*, *Encerrado*, *Concluído*. |
| **`input`**           | JSON      | Os dados JSON usados para inicializar a instância. Este campo é `null` se o `showInput` parâmetro da cadeia de caracteres de consulta for definido para `false`.|
| **`customStatus`**    | JSON      | Os dados JSON usados para status de orquestração personalizado. Este campo é `null` se não for definido. |
| **`output`**          | JSON      | A saída JSON da instância. Este campo será `null` se a instância não estiver no estado concluído. |
| **`createdTime`**     | string    | A hora em que a instância foi criada. Usa a notação estendida ISO 8601. |
| **`lastUpdatedTime`** | string    | A hora em que a instância foi persistida pela última vez. Usa a notação estendida ISO 8601. |
| **`historyEvents`**   | JSON      | Uma matriz JSON contendo o histórico de execução da orquestração. Esse campo é `null`, exceto se o parâmetro da cadeia de caracteres de consulta `showHistory` estiver definido como `true`. |

Aqui está um exemplo de carga de resposta, incluindo o histórico de execução de orquestração e saídas de atividades (formatado para legibilidade):

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

A resposta **HTTP 202** também inclui um cabeçalho de resposta **Local** que faz referência à mesma URL que o campo `statusQueryGetUri` mencionado anteriormente.

## <a name="get-all-instances-status"></a>Obter status de todas as instâncias

Você também pode consultar o status de todas as instâncias removendo o `instanceId` da solicitação ' obter status da instância '. Nesse caso, os parâmetros básicos são os mesmos que o ' obter status da instância '. Também há suporte para parâmetros de cadeia de caracteres de consulta para filtragem.

Uma coisa a lembrar é que `connection` e `code` são opcionais. Se você tiver a autenticação anônima na função, `code` não será necessário.
Se você não quiser usar uma cadeia de conexão de armazenamento diferente da definida na configuração de aplicativo AzureWebJobsStorage, poderá ignorar com segurança o parâmetro de cadeia de caracteres de consulta de conexão.

### <a name="request"></a>Solicitação

Para a versão 1. x do tempo de execução do functions, a solicitação é formatada da seguinte maneira (várias linhas são mostradas para maior clareza):

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Na versão 2. x do tempo de execução do functions, o formato da URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Parâmetros de solicitação para essa API incluem o conjunto padrão mencionado anteriormente, bem como o parâmetro exclusivo a seguir:

| Campo                   | Tipo de parâmetro  | Descrição |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | A ID da instância de orquestração. |
| **`showInput`**         | Cadeia de consulta    | Parâmetro opcional. Se definido como `false` , a entrada da função não será incluída na carga de resposta.|
| **`showHistory`**       | Cadeia de consulta    | Parâmetro opcional. Se definido como `true`, o histórico de execução da orquestração será incluído na carga da resposta.|
| **`showHistoryOutput`** | Cadeia de consulta    | Parâmetro opcional. Se definido como `true` , as saídas de função serão incluídas no histórico de execução de orquestração.|
| **`createdTimeFrom`**   | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias retornadas que foram criadas no ou após o carimbo de data/hora ISO8601 fornecido.|
| **`createdTimeTo`**     | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias retornadas que foram criadas no ou antes do carimbo de data/hora ISO8601 fornecido.|
| **`runtimeStatus`**     | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias retornadas com base em seu status de runtime. Para ver a lista de possíveis valores de status de tempo de execução, consulte o artigo [instâncias de consulta](durable-functions-instance-management.md) . |
| **`top`**               | Cadeia de consulta    | Parâmetro opcional. Quando especificado, limita o número de instâncias retornadas pela consulta. |

### <a name="response"></a>Resposta

Aqui está um exemplo de cargas de resposta, incluindo o status de orquestração (formatado para legibilidade):

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> Essa operação pode ser muito cara em termos de E/S de Armazenamento do Microsoft Azure, se houver muitas linhas na tabela Instâncias. Mais detalhes sobre a tabela de Instâncias podem ser encontrados na documentação [Desempenho e escala nas Funções Duráveis (Azure Functions)](durable-functions-perf-and-scale.md#instances-table).
>

Se houver mais resultados, um token de continuação será retornado no cabeçalho de resposta.  O nome do cabeçalho `x-ms-continuation-token`.

Se você definir o valor do token de continuação no próximo cabeçalho de solicitação, poderá obter a próxima página de resultados. O nome do cabeçalho da solicitação também é `x-ms-continuation-token` .

## <a name="purge-single-instance-history"></a>Limpar o histórico de instância única

Exclui o histórico e os artefatos relacionados para uma instância de orquestração especificada.

### <a name="request"></a>Solicitação

Para a versão 1. x do tempo de execução do functions, a solicitação é formatada da seguinte maneira (várias linhas são mostradas para maior clareza):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Na versão 2. x do tempo de execução do functions, o formato da URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Parâmetros de solicitação para essa API incluem o conjunto padrão mencionado anteriormente, bem como o parâmetro exclusivo a seguir:

| Campo             | Tipo de parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | A ID da instância de orquestração. |

### <a name="response"></a>Resposta

Os valores de código de status HTTP a seguir podem ser retornados.

* **HTTP 200 (OK)**: o histórico de instância foi limpo com êxito.
* **HTTP 404 (não encontrado)**: a instância especificada não existe.

A carga de resposta para o caso de **HTTP 200** é um objeto JSON com o seguinte campo:

| Campo                  | Tipo de dados | Descrição |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | Número inteiro   | O número de instâncias excluídas. Para o caso de instância única, esse valor sempre deve ser `1` . |

Veja um exemplo de carga de resposta (formatada para facilitar a leitura):

```json
{
    "instancesDeleted": 1
}
```

## <a name="purge-multiple-instance-histories"></a>Limpar históricos de várias instâncias

Você também pode excluir o histórico e os artefatos relacionados para várias instâncias em um hub de tarefas removendo o `{instanceId}` da solicitação ' limpar o histórico de instância única '. Para limpar seletivamente o histórico de instância, use os mesmos filtros descritos na solicitação "obter status de todas as instâncias".

### <a name="request"></a>Solicitação

Para a versão 1. x do tempo de execução do functions, a solicitação é formatada da seguinte maneira (várias linhas são mostradas para maior clareza):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Na versão 2. x do tempo de execução do functions, o formato da URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Parâmetros de solicitação para essa API incluem o conjunto padrão mencionado anteriormente, bem como o parâmetro exclusivo a seguir:

| Campo                 | Tipo de parâmetro  | Descrição |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Cadeia de consulta    | Filtra a lista de instâncias limpas que foram criadas no ou após o carimbo de data/hora ISO8601 fornecido.|
| **`createdTimeTo`**   | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias limpas que foram criadas no ou antes do carimbo de data/hora ISO8601 fornecido.|
| **`runtimeStatus`**   | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias limpas com base em seu status de tempo de execução. Para ver a lista de possíveis valores de status de tempo de execução, consulte o artigo [instâncias de consulta](durable-functions-instance-management.md) . |

> [!NOTE]
> Essa operação pode ser muito cara em termos de e/s do armazenamento do Azure se houver muitas linhas nas tabelas de instâncias e/ou de histórico. Mais detalhes sobre essas tabelas podem ser encontrados na documentação de [desempenho e escala na Durable Functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table) .

### <a name="response"></a>Resposta

Os valores de código de status HTTP a seguir podem ser retornados.

* **HTTP 200 (OK)**: o histórico de instância foi limpo com êxito.
* **HTTP 404 (não encontrado)**: nenhuma instância encontrada corresponde à expressão de filtro.

A carga de resposta para o caso de **HTTP 200** é um objeto JSON com o seguinte campo:

| Campo                   | Tipo de dados | Descrição |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | Número inteiro   | O número de instâncias excluídas. |

Veja um exemplo de carga de resposta (formatada para facilitar a leitura):

```json
{
    "instancesDeleted": 250
}
```

## <a name="raise-event"></a>Acionar evento

Envia uma mensagem de notificação de evento para uma instância de orquestração em execução.

### <a name="request"></a>Solicitação

Para a versão 1. x do tempo de execução do functions, a solicitação é formatada da seguinte maneira (várias linhas são mostradas para maior clareza):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Na versão 2. x do tempo de execução do functions, o formato da URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Parâmetros de solicitação para essa API incluem o conjunto padrão mencionado anteriormente, bem como o parâmetro exclusivo a seguir:

| Campo             | Tipo de parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | A ID da instância de orquestração. |
| **`eventName`**   | URL             | O nome do evento que a instância de orquestração de destino está esperando. |
| **`{content}`**   | Conteúdo da solicitação | A carga do evento em formato JSON. |

### <a name="response"></a>Resposta

Vários valores de código de status possíveis podem ser retornados.

* **HTTP 202 (Aceito)**: o evento gerado foi aceito para processamento.
* **HTTP 400 (Solicitação incorreta)**: o conteúdo da solicitação não era do tipo `application/json` ou não era um JSON válido.
* **HTTP 404 (Não encontrado)**: a instância especificada não foi encontrada.
* **HTTP 410 (Não existe mais)**: a instância especificada foi concluída ou falhou e não pode processar os eventos gerados.

Veja um exemplo de solicitação que envia a cadeia de caracteres JSON `"incr"` para uma instância que aguarda um evento nomeado **operation**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

As respostas para esta API não têm nenhum conteúdo.

## <a name="terminate-instance"></a>Encerrar instância

Encerra uma instância de orquestração em execução.

### <a name="request"></a>Solicitação

Para a versão 1. x do tempo de execução do functions, a solicitação é formatada da seguinte maneira (várias linhas são mostradas para maior clareza):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Na versão 2. x do tempo de execução do functions, o formato da URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Parâmetros de solicitação para essa API incluem o conjunto padrão mencionado anteriormente, bem como o seguinte parâmetro exclusivo.

| Campo             | Tipo de parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | A ID da instância de orquestração. |
| **`reason`**      | Cadeia de consulta    | Opcional. O motivo para encerrar a instância de orquestração. |

### <a name="response"></a>Resposta

Vários valores de código de status possíveis podem ser retornados.

* **HTTP 202 (Aceito)**: a solicitação de encerramento foi aceita para processamento.
* **HTTP 404 (Não encontrado)**: a instância especificada não foi encontrada.
* **HTTP 410 (Não existe mais)**: a instância especificada foi concluída ou falhou.

Veja um exemplo de solicitação que encerra uma instância em execução e especifica o motivo **buggy**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

As respostas para esta API não têm nenhum conteúdo.

## <a name="rewind-instance-preview"></a>Instância Gerenciada (versão prévia)

Restaura uma instância de orquestração com falha em um estado de execução ao reproduzir novamente as operações com falha mais recentes.

### <a name="request"></a>Solicitação

Para a versão 1. x do tempo de execução do functions, a solicitação é formatada da seguinte maneira (várias linhas são mostradas para maior clareza):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Na versão 2. x do tempo de execução do functions, o formato da URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Parâmetros de solicitação para essa API incluem o conjunto padrão mencionado anteriormente, bem como o seguinte parâmetro exclusivo.

| Campo             | Tipo de parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | A ID da instância de orquestração. |
| **`reason`**      | Cadeia de consulta    | Opcional. O motivo para retroceder a instância de orquestração. |

### <a name="response"></a>Resposta

Vários valores de código de status possíveis podem ser retornados.

* **HTTP 202 (Aceito)**: a solicitação de retroceder foi aceita para processamento.
* **HTTP 404 (Não encontrado)**: a instância especificada não foi encontrada.
* **HTTP 410 (Não existe mais)**: a instância especificada foi concluída ou falhou.

Veja um exemplo de solicitação que retrocede uma instância com falha e especifica o motivo **corrigido**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

As respostas para esta API não têm nenhum conteúdo.

## <a name="signal-entity"></a>Entidade de sinal

Envia uma mensagem de operação unidirecional para uma [entidade durável](durable-functions-types-features-overview.md#entity-functions). Se a entidade não existir, ela será criada automaticamente.

> [!NOTE]
> As entidades duráveis estão disponíveis a partir do Durable Functions 2,0.

### <a name="request"></a>Solicitação

A solicitação HTTP é formatada da seguinte maneira (várias linhas são mostradas para maior clareza):

```http
POST /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

Parâmetros de solicitação para essa API incluem o conjunto padrão mencionado anteriormente, bem como o parâmetro exclusivo a seguir:

| Campo             | Tipo de parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`entityName`**  | URL             | O nome (tipo) da entidade. |
| **`entityKey`**   | URL             | A chave (ID exclusiva) da entidade. |
| **`op`**          | Cadeia de consulta    | Opcional. O nome da operação definida pelo usuário a ser invocada. |
| **`{content}`**   | Conteúdo da solicitação | A carga do evento em formato JSON. |

Aqui está uma solicitação de exemplo que envia uma mensagem de "adição" definida pelo usuário a uma `Counter` entidade chamada `steps` . O conteúdo da mensagem é o valor `5` . Se a entidade ainda não existir, ela será criada por essa solicitação:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

> [!NOTE]
> Por padrão, com [entidades baseadas em classe no .net](durable-functions-dotnet-entities.md#defining-entity-classes), especificar o `op` valor de `delete` irá excluir o estado de uma entidade. No entanto, se a entidade definir uma operação chamada, `delete` essa operação definida pelo usuário será invocada.

### <a name="response"></a>Resposta

Esta operação tem várias respostas possíveis:

* **HTTP 202 (aceito)**: a operação de sinal foi aceita para processamento assíncrono.
* **HTTP 400 (solicitação inválida)**: o conteúdo da solicitação não era do tipo `application/json` , não era um JSON válido ou tinha um `entityKey` valor inválido.
* **HTTP 404 (não encontrado)**: o especificado `entityName` não foi encontrado.

Uma solicitação HTTP bem-sucedida não contém nenhum conteúdo na resposta. Uma solicitação HTTP com falha pode conter informações de erro formatadas em JSON no conteúdo da resposta.

## <a name="get-entity"></a>Obter entidade

Obtém o estado da entidade especificada.

### <a name="request"></a>Solicitação

A solicitação HTTP é formatada da seguinte maneira (várias linhas são mostradas para maior clareza):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

### <a name="response"></a>Resposta

Esta operação tem duas respostas possíveis:

* **HTTP 200 (OK)**: a entidade especificada existe.
* **HTTP 404 (não encontrado)**: a entidade especificada não foi encontrada.

Uma resposta bem-sucedida contém o estado serializado em JSON da entidade como seu conteúdo.

### <a name="example"></a>Exemplo
A solicitação HTTP de exemplo a seguir obtém o estado de uma `Counter` entidade existente chamada `steps` :

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Se a `Counter` entidade contiver simplesmente uma série de etapas salvas em um `currentValue` campo, o conteúdo da resposta poderá ser semelhante ao seguinte (formatado para facilitar a leitura):

```json
{
    "currentValue": 5
}
```

## <a name="list-entities"></a>Listar entidades

Você pode consultar várias entidades pelo nome da entidade ou pela última data de operação.

### <a name="request"></a>Solicitação

A solicitação HTTP é formatada da seguinte maneira (várias linhas são mostradas para maior clareza):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &lastOperationTimeFrom={timestamp}
    &lastOperationTimeTo={timestamp}
    &fetchState=[true|false]
    &top={integer}
```

Parâmetros de solicitação para essa API incluem o conjunto padrão mencionado anteriormente, bem como o parâmetro exclusivo a seguir:

| Campo                       | Tipo de parâmetro  | Descrição |
|-----------------------------|-----------------|-------------|
| **`entityName`**            | URL             | Opcional. Quando especificado, filtra a lista de entidades retornadas pelo nome da entidade (não diferencia maiúsculas de minúsculas). |
| **`fetchState`**            | Cadeia de consulta    | Parâmetro opcional. Se definido como `true` , o estado da entidade será incluído na carga de resposta. |
| **`lastOperationTimeFrom`** | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de entidades retornadas que processaram operações após o carimbo de data/hora ISO8601 fornecido. |
| **`lastOperationTimeTo`**   | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de entidades retornadas que processaram operações antes do carimbo de data/hora ISO8601 fornecido. |
| **`top`**                   | Cadeia de consulta    | Parâmetro opcional. Quando especificado, limita o número de entidades retornadas pela consulta. |


### <a name="response"></a>Resposta

Uma resposta HTTP 200 bem-sucedida contém uma matriz serializada JSON de entidades e, opcionalmente, o estado de cada entidade.

Por padrão, a operação retorna as primeiras 100 entidades que correspondem aos critérios de consulta. O chamador pode especificar um valor de parâmetro de cadeia de caracteres de consulta para `top` retornar um número máximo de resultados diferente. Se houver mais resultados Além do que é retornado, um token de continuação também será retornado no cabeçalho de resposta. O nome do cabeçalho `x-ms-continuation-token`.

Se você definir o valor do token de continuação no próximo cabeçalho de solicitação, poderá obter a próxima página de resultados. O nome do cabeçalho da solicitação também é `x-ms-continuation-token` .

### <a name="example---list-all-entities"></a>Exemplo – listar todas as entidades

A solicitação HTTP de exemplo a seguir lista todas as entidades no Hub de tarefas:

```http
GET /runtime/webhooks/durabletask/entities
```

A resposta JSON pode ser parecida com a seguinte (formatada para facilitar a leitura):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z"
    },
    {
        "entityId": { "key": "mice", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:15.4626159Z"
    },
    {
        "entityId": { "key": "radio", "name": "device" },
        "lastOperationTime": "2019-12-18T21:46:18.2616154Z"
    },
]
```

### <a name="example---filtering-the-list-of-entities"></a>Exemplo – filtrando a lista de entidades

A solicitação HTTP de exemplo a seguir lista apenas as duas primeiras entidades do tipo `counter` e também busca seu estado:

```http
GET /runtime/webhooks/durabletask/entities/counter?top=2&fetchState=true
```

A resposta JSON pode ser parecida com a seguinte (formatada para facilitar a leitura):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
        "state": { "value": 9 }
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z",
        "state": { "value": 10 }
    }
]
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como usar Application Insights para monitorar suas funções duráveis](durable-functions-diagnostics.md)