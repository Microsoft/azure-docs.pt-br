---
title: Associações para Funções Duráveis – Azure
description: Como usar gatilhos e associações para a extensão de Durable Functions do Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 678e370977cadae642207f91a02136404fb6c34e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710517"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Associações para Funções Duráveis (Azure Functions)

A extensão [Funções Duráveis](durable-functions-overview.md) introduz duas novas associações de gatilho que controlam a execução de funções de orquestrador e atividade. Ela também introduz uma associação de saída que atua como um cliente para o tempo de execução das Funções Duráveis.

## <a name="orchestration-triggers"></a>Gatilhos de orquestração

O gatilho de orquestração lhe permite criar funções de orquestrador duráveis. Esse gatilho dá suporte a iniciar novas instâncias de função de orquestrador e a retomar instâncias de função de orquestrador existentes que estão "aguardando" uma tarefa.

Quando você usa as ferramentas do Visual Studio para o Azure Functions, o gatilho de orquestração é configurado usando o atributo .NET [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html).

Quando você escreve funções de orquestrador em linguagens de script (por exemplo, script em JavaScript ou C#), o gatilho de orquestração é definido pelo seguinte objeto JSON na matriz `bindings` do arquivo *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` é o nome da orquestração. Esse é o valor que os clientes devem usar quando quiserem iniciar novas instâncias dessa função de orquestrador. Essa propriedade é opcional. Se não for especificada, o nome da função será usado.

Internamente, essa associação de gatilho sonda uma série de filas na conta de armazenamento padrão do aplicativo de funções. Essas filas são detalhes da implementação interna da extensão e, por isso, elas não são configuradas explicitamente nas propriedades de associação.

### <a name="trigger-behavior"></a>Comportamento do gatilho

Veja algumas observações sobre o gatilho de orquestração:

* **Threading único** – um único thread dispatcher é usado para toda a execução de função de orquestrador em uma única instância de host. Por esse motivo, é importante garantir que o código da função de orquestrador seja eficiente e não execute nenhuma E/S. Também é importante garantir que esse thread não faça nenhum trabalho assíncrono, exceto ao aguardar tipos de tarefas específicas das Funções Duráveis.
* **Manipulação de mensagens suspeitas** – não há suporte para mensagens suspeitas nos acionadores de orquestração.
* **Visibilidade da mensagem** – as mensagens do gatilho de orquestração são removidas da fila e mantidas invisíveis por uma duração configurável. A visibilidade dessas mensagens é renovada automaticamente, desde que o aplicativo de funções esteja em execução e íntegro.
* **Valores retornados** – os valores retornados são serializados em JSON e são persistidos na tabela de histórico de orquestração no Armazenamento de Tabelas do Azure. Esse valores retornados podem ser consultados pela associação do cliente de orquestração, descrita posteriormente.

> [!WARNING]
> Funções de orquestrador nunca devem usar nenhuma associação de entrada ou saída que não seja a associação do gatilho de orquestração. Fazer isso tem o potencial de causar problemas com a extensão de Tarefa Durável, pois essas associações podem não cumprir as regras de E/S e de thread único.

> [!WARNING]
> Funções de orquestrador JavaScript nunca devem ser declaradas como `async`.

### <a name="trigger-usage-net"></a>Uso de gatilho (.NET)

A associação de gatilho de orquestração dá suporte a entradas e saídas. Estas são algumas coisas que você precisa saber sobre a manipulação de entradas e saídas:

* **entradas** – as funções de orquestração .NET dão suporte apenas a [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) como um tipo de parâmetro. Não há suporte para entradas de desserialização diretamente na assinatura da função. O código deve usar o método [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1) (.NET) ou `getInput` (JavaScript) para buscar as entradas de função de orquestrador. Essas entradas devem ser tipos serializáveis em JSON.
* **saídas** – gatilhos de orquestração dão suporte a valores de saída, bem como entradas. O valor retornado da função é usado para atribuir o valor de saída e deve ser serializável em JSON. Se uma função .NET retornar `Task` ou `void`, um valor `null` será salvo como a saída.

### <a name="trigger-sample"></a>Exemplo de gatilho

Este é um exemplo de como a função de orquestrador "Olá, Mundo" mais simples pode ser:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> O objeto `context` no JavaScript não representa o DurableOrchestrationContext, mas o [contexto de função como um todo.](../functions-reference-node.md#context-object). Você pode acessar os métodos de orquestração por meio da propriedade `df` do objeto `context`.

> [!NOTE]
> Os orquestradores JavaScript devem usar `return`. A biblioteca `durable-functions` é responsável por chamar o método `context.done`.

A maioria das funções de orquestrador chamam funções de atividade, sendo assim, este é um exemplo de "Olá, Mundo" que demonstra como chamar uma função de atividade:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-triggers"></a>Gatilhos de atividade

O gatilho de atividade lhe permite criar funções que são chamadas por funções de orquestrador.

Se você estiver usando o Visual Studio, o gatilho de atividade será configurado usando o atributo .NET [ActivityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html).

Se você estiver usando o VS Code ou o Portal do Azure para desenvolvimento, o gatilho de atividade será definido pelo seguinte objeto JSON na matriz `bindings` de *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` é o nome da atividade. Esse é o valor que as funções de orquestrador usam para invocar esta função de atividade. Essa propriedade é opcional. Se não for especificada, o nome da função será usado.

Internamente, essa associação de gatilho sonda uma fila na conta de armazenamento padrão para o aplicativo de funções. Essa fila é um detalhe da implementação interna da extensão e, por isso, ela não é configurada explicitamente nas propriedades da associação.

### <a name="trigger-behavior"></a>Comportamento do gatilho

Veja algumas observações sobre o gatilho de atividade:

* **Threading** – diferente do gatilho de orquestração, os gatilhos de atividade não têm restrições quanto a threading ou E/S. Eles podem ser tratados como funções regulares.
* **Manipulação de mensagens suspeitas** – não há suporte para mensagens suspeitas nos gatilhos de atividade.
* **Visibilidade da mensagem** – as mensagens do gatilho de atividade são removidas da fila e mantidas invisíveis por uma duração configurável. A visibilidade dessas mensagens é renovada automaticamente, desde que o aplicativo de funções esteja em execução e íntegro.
* **Valores retornados** – os valores retornados são serializados em JSON e são persistidos na tabela de histórico de orquestração no Armazenamento de Tabelas do Azure.

> [!WARNING]
> O back-end de armazenamento das funções de atividade é um detalhe da implementação e o código do usuário não deve interagir diretamente com essas entidades de armazenamento.

### <a name="trigger-usage-net"></a>Uso de gatilho (.NET)

A associação de gatilho de atividade dá suporte a entradas e saídas, assim como no caso do gatilho de orquestração. Estas são algumas coisas que você precisa saber sobre a manipulação de entradas e saídas:

* **entradas** – nativamente, as funções de atividade .NET usam [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) como um tipo de parâmetro. Como alternativa, uma função de atividade pode ser declarada com qualquer tipo de parâmetro que possa ser serializado em JSON. Quando usa `DurableActivityContext`, você pode chamar [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) para buscar e desserializar a entrada da função de atividade.
* **saídas** – Funções de atividade dão suporte a valores de saída, bem como entradas. O valor retornado da função é usado para atribuir o valor de saída e deve ser serializável em JSON. Se uma função .NET retornar `Task` ou `void`, um valor `null` será salvo como a saída.
* **metadados** – funções de atividade .NET podem ser associadas a um parâmetro `string instanceId` para obter a ID da instância da orquestração pai.

### <a name="trigger-sample"></a>Exemplo de gatilho

Este é um exemplo de como uma função simples de atividade "Olá, Mundo" pode ser:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

O tipo de parâmetro padrão para a associação `ActivityTriggerAttribute` .NET é `DurableActivityContext`. No entanto, os gatilhos de atividade .NET também dão suporte à associação direta com tipos serializáveis em JSON (incluindo tipos primitivos), de modo que a mesma função poderia ser simplificada da seguinte forma:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

Associações de JavaScript também podem ser passadas como parâmetros adicionais para que a mesma função possa ser simplificada da seguinte maneira:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```

### <a name="passing-multiple-parameters"></a>Passando vários parâmetros

Não é possível passar vários parâmetros para uma função de atividade diretamente. Nesse caso, a recomendação é passar uma matriz de objetos ou usar objetos [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) no .NET.

O exemplo a seguir usa os novos recursos de [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) adicionados com [C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<dynamic> RunOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    dynamic courseRecommendations = await context.CallActivityAsync<dynamic>("CourseRecommendations", (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<dynamic> Mapper([ActivityTrigger] DurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

## <a name="orchestration-client"></a>Cliente de orquestração

A associação do cliente de orquestração permite que você escreva funções que interagem com funções de orquestrador. Por exemplo, você pode atuar em instâncias de orquestração das maneiras a seguir:

* Iniciá-las.
* Consultar seu status.
* Encerrá-las.
* Enviar eventos a elas durante sua execução.
* Limpar o histórico de instância.

Se estiver usando o Visual Studio, você poderá associar ao cliente de orquestração usando o atributo .NET [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html).

Se você estiver usando linguagens de script (por exemplo, arquivos *.csx* ou *.js*) para desenvolvimento, o gatilho de orquestração será definido pelo seguinte objeto JSON na matriz `bindings` de *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` – usado em cenários em que vários aplicativos de funções compartilham a mesma conta de armazenamento, mas precisam ser isolados uns dos outros. Se não for especificado, o valor padrão de `host.json` será usado. Esse valor deve corresponder ao valor usado pelas funções de orquestrador de destino.
* `connectionName` – O nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. A conta de armazenamento representada por essa cadeia de conexão deve ser a mesma usada pelas funções de orquestrador de destino. Se não for especificada, a cadeia de conexão da conta de armazenamento padrão do aplicativo de funções será usada.

> [!NOTE]
> Na maioria dos casos, é recomendável omitir essas propriedades e contar com o comportamento padrão.

### <a name="client-usage"></a>Uso do cliente

Em funções .NET, normalmente você associa a `DurableOrchestrationClient`, que oferece acesso completo a todas as APIs de cliente com suporte de Durable Functions. No JavaScript, as mesmas APIs são expostas pelo objeto `DurableOrchestrationClient` retornado de `getClient`. APIs no objeto cliente incluem:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) (atualmente, apenas .NET)

Como alternativa, as funções .NET podem associar a `IAsyncCollector<T>`, em que `T` é [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) ou `JObject`.

Consulte a documentação da API [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) para obter detalhes adicionais sobre essas operações.

> [!WARNING]
> Ao desenvolver localmente em JavaScript, você precisará definir a variável de ambiente `WEBSITE_HOSTNAME` como `localhost:<port>`, por exemplo, `localhost:7071` para usar métodos em `DurableOrchestrationClient`. Para obter mais informações sobre esse requisito, confira o [Problema no GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="client-sample-visual-studio-development"></a>Exemplo de cliente (desenvolvimento no Visual Studio)

Veja um exemplo de função disparada em fila que é iniciada com uma orquestração "Olá, Mundo".

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [OrchestrationClient] DurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

### <a name="client-sample-not-visual-studio"></a>Exemplo de cliente (fora do Visual Studio)

Se não estiver usando o Visual Studio para desenvolvimento, você poderá criar o seguinte arquivo *function.json*. Este exemplo mostra como configurar uma função disparada em fila que usa a associação de cliente de orquestração durável:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

A seguir, temos exemplos específico a um idioma que iniciam novas instâncias de função do orquestrador.

#### <a name="c-sample"></a>Exemplo de C#

O exemplo a seguir mostra como usar a associação de cliente de orquestração durável para iniciar uma nova instância de função de uma função de script em C#:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>Exemplo de JavaScript

O exemplo a seguir mostra como usar a associação de cliente de orquestração durável para iniciar uma nova instância de função de uma função JavaScript:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Mais detalhes sobre como iniciar instâncias podem ser encontrados em [Gerenciamento de instâncias](durable-functions-instance-management.md).

<a name="host-json"></a>

## <a name="hostjson-settings"></a>configurações de host.json

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre os comportamentos de ponto de verificação e reprodução](durable-functions-checkpointing-and-replay.md)