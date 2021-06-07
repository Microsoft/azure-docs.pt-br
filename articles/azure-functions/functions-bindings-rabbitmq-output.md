---
title: Associações de saída do RabbitMQ para o Azure Functions
description: Saiba como enviar mensagens do RabbitMQ no Azure Functions.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 1664656f82492e664b7574339893cd688f0a061d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100097306"
---
# <a name="rabbitmq-output-binding-for-azure-functions-overview"></a>Visão geral da associação de saída do RabbitMQ para o Azure Functions

> [!NOTE]
> Só há suporte completo para as associações do RabbitMQ nos planos **Premium e Dedicado**. Não há suporte no plano Consumo.

Use a associação de saída do RabbitMQ para enviar mensagens a uma fila do RabbitMQ.

Para obter informações sobre a instalação e detalhes de configuração, confira a [visão geral](functions-bindings-rabbitmq-output.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O seguinte exemplo mostra uma [função C#](functions-dotnet-class-library.md) que envia uma mensagem do RabbitMQ quando disparada por um TimerTrigger a cada 5 minutos usando o valor retornado do método como a saída:

```cs
[FunctionName("RabbitMQOutput")]
[return: RabbitMQ(QueueName = "outputQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

O exemplo a seguir mostra como usar a interface IAsyncCollector para enviar mensagens.

```cs
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] string rabbitMQEvent,
[RabbitMQ(QueueName = "destinationQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]IAsyncCollector<string> outputEvents,
ILogger log)
{
     // send the message
    await outputEvents.AddAsync(JsonConvert.SerializeObject(rabbitMQEvent));
}
```

O exemplo a seguir mostra como enviar as mensagens como POCOs.

```cs
namespace Company.Function
{
    public class TestClass
    {
        public string x { get; set; }
    }
    public static class RabbitMQOutput{
        [FunctionName("RabbitMQOutput")]
        public static async Task Run(
        [RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] TestClass rabbitMQEvent,
        [RabbitMQ(QueueName = "destinationQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]IAsyncCollector<TestClass> outputPocObj,
        ILogger log)
        {
            // send the message
            await outputPocObj.AddAsync(rabbitMQEvent);
        }
    }
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma associação de saída do RabbitMQ em um arquivo *function.json* e uma [função de script C#](functions-reference-csharp.md) que usa a associação. A função lê a mensagem de um gatilho HTTP e a gera na fila do RabbitMQ.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

Aqui está o código de script do C#:

```C#
using System;
using Microsoft.Extensions.Logging;

public static void Run(string input, out string outputMessage, ILogger log)
{
    log.LogInformation(input);
    outputMessage = input;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de saída do RabbitMQ em um arquivo *function.json* e o [código JavaScript](functions-reference-node.md) que usa a associação. A função lê a mensagem de um gatilho HTTP e a gera na fila do RabbitMQ.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

Este é o código JavaScript:

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma associação de saída do RabbitMQ em um arquivo *function.json* e uma função Python que usa a associação. A função lê a mensagem de um gatilho HTTP e a gera na fila do RabbitMQ.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "scriptFile": "__init__.py",
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "http",
            "direction": "out",
            "name": "$return"
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

Em *_\_init_\_.py*:

```python
import azure.functions as func

def main(req: func.HttpRequest, outputMessage: func.Out[str]) -> func.HttpResponse:
    input_msg = req.params.get('message')
    outputMessage.set(input_msg)
    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

A função Java a seguir usa a anotação `@RabbitMQOutput` dos [tipos RabbitMQ em Java](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq) para descrever a configuração de uma associação de saída da fila do RabbitMQ. A função envia uma mensagem para a fila do RabbitMQ quando disparada por um TimerTrigger a cada 5 minutos.

```java
@FunctionName("RabbitMQOutputExample")
public void run(
@TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
@RabbitMQOutput(connectionStringSetting = "rabbitMQConnectionAppSetting", queueName = "hello") OutputBinding<String> output,
final ExecutionContext context) {
    output.setValue("Some string");
}
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Em [Bibliotecas de classes C#](functions-dotnet-class-library.md), use [RabbitMQAttribute](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/RabbitMQAttribute.cs).

Este é um atributo `RabbitMQAttribute` em uma assinatura de método:

```csharp
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("SourceQueue", ConnectionStringSetting = "TriggerConnectionString")] string rabbitMQEvent,
[RabbitMQ("DestinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<string> outputEvents,
ILogger log)
{
    ...
}
```

Para obter um exemplo completo, confira o [exemplo](#example) em C#.

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O script C# não dá suporte a atributos.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript não dá suporte a atributos.

# <a name="python"></a>[Python](#tab/python)

O Python não dá suporte a atributos.

# <a name="java"></a>[Java](#tab/java)

A anotação `RabbitMQOutput` permite que você crie uma função que é executada durante o envio de uma mensagem do RabbitMQ. As opções de configuração disponíveis incluem nome da fila e nome da cadeia de conexão. Para obter mais detalhes sobre parâmetros, acesse as [anotações do RabbitMQOutput em Java](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQOutput.java).

Confira o [exemplo](#example) de associação de saída para obter mais detalhes.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `RabbitMQ`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Precisa ser definido como "RabbitMQ".|
|**direction** | n/d | Deve ser definido como "out". |
|**name** | n/d | O nome da variável que representa a fila no código de função. |
|**queueName**|**QueueName**| Nome da fila para a qual as mensagens serão enviadas. |
|**hostName**|**HostName**|(ignorado se ConnectStringSetting estiver sendo usado) <br>Nome do host da fila (por exemplo: 10.26.45.210)|
|**userName**|**UserName**|(ignorado se ConnectionStringSetting estiver sendo usado) <br>Nome da configuração de aplicativo que contém o nome de usuário para acessar a fila. Ex.: UserNameSetting: "< UserNameFromSettings >"|
|**password**|**Senha**|(ignorado se ConnectionStringSetting estiver sendo usado) <br>Nome da configuração de aplicativo que contém a senha para acessar a fila. Ex.: UserNameSetting: "< UserNameFromSettings >"|
|**connectionStringSetting**|**ConnectionStringSetting**|O nome da configuração de aplicativo que contém a cadeia de conexão da fila da mensagem do RabbitMQ. Observe que, se você especificar a cadeia de conexão diretamente e não por meio de uma configuração de aplicativo em local.settings.json, o gatilho não funcionará. (Por exemplo: em *function.json*: connectionStringSetting: "rabbitMQConnection" <br> Em *local.settings.json*: "rabbitMQConnection" : "< ActualConnectionstring >")|
|**port**|**Porta**|(ignorado se ConnectionStringSetting estiver sendo usado) Obtém ou define a porta usada. Usa 0 como padrão, que aponta para a configuração de porta padrão do cliente RabbitMQ: 5672.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

Use os seguintes tipos de parâmetro para a associação de saída:

* `byte[]` - Se o valor de parâmetro não for nulo quando a função sair, o Functions criará uma mensagem.
* `string` - Se o valor de parâmetro não for nulo quando a função sair, o Functions criará uma mensagem.
* `POCO`: se o valor do parâmetro não estiver formatado como um objeto C#, um erro será recebido. Para obter um exemplo completo, confira o [exemplo](#example) em C#.

Ao trabalhar com funções C#:

* As funções assíncronas precisam obter um valor retornado ou `IAsyncCollector` em vez de um parâmetro `out`.

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Use os seguintes tipos de parâmetro para a associação de saída:

* `byte[]` - Se o valor de parâmetro não for nulo quando a função sair, o Functions criará uma mensagem.
* `string` - Se o valor de parâmetro não for nulo quando a função sair, o Functions criará uma mensagem.
* `POCO`: se o valor do parâmetro não estiver formatado como um objeto C#, um erro será recebido. Para ver um exemplo completo, confira o [exemplo](#example) de script C#.

Ao trabalhar com funções de script C#:

* As funções assíncronas precisam obter um valor retornado ou `IAsyncCollector` em vez de um parâmetro `out`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A mensagem da fila está disponível por meio de context.bindings.<NAME> em que <NAME> corresponde ao nome definido em function.json. Se o conteúdo for JSON, o valor será desserializado em um objeto.

# <a name="python"></a>[Python](#tab/python)

Veja o [exemplo](#example) do Python.

# <a name="java"></a>[Java](#tab/java)

Use os seguintes tipos de parâmetro para a associação de saída:

* `byte[]` - Se o valor de parâmetro não for nulo quando a função sair, o Functions criará uma mensagem.
* `string` - Se o valor de parâmetro não for nulo quando a função sair, o Functions criará uma mensagem.
* `POJO`: se o valor do parâmetro não estiver formatado como um objeto Java, um erro será recebido.

---

## <a name="next-steps"></a>Próximas etapas

- [Executar uma função quando uma mensagem do RabbitMQ é criada (gatilho)](./functions-bindings-rabbitmq-trigger.md)
