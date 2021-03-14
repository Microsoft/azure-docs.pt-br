---
title: Gatilho RabbitMQ para Azure Functions
description: Saiba como executar uma função do Azure quando uma mensagem RabbitMQ é criada.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: be3c5bc2d178171aaebd322e13b23b3a6f79c442
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388984"
---
# <a name="rabbitmq-trigger-for-azure-functions-overview"></a>RabbitMQ do gatilho para Azure Functions visão geral

> [!NOTE]
> As associações RabbitMQ têm suporte total apenas em planos **Premium e dedicado** . Não há suporte para consumo.

Use o gatilho RabbitMQ para responder às mensagens de uma fila do RabbitMQ.

Para obter informações sobre a instalação e detalhes de configuração, confira a [visão geral](functions-bindings-rabbitmq.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que lê e registra a mensagem RabbitMQ como um [evento RabbitMQ](https://rabbitmq.github.io/rabbitmq-dotnet-client/api/RabbitMQ.Client.Events.BasicDeliverEventArgs.html):

```cs
[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] BasicDeliverEventArgs args,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(args.Body)}");
}
```

O exemplo a seguir mostra como ler a mensagem como um POCO.

```cs
namespace Company.Function
{
    public class TestClass
    {
        public string x { get; set; }
    }

    public class RabbitMQTriggerCSharp{
        [FunctionName("RabbitMQTriggerCSharp")]
        public static void RabbitMQTrigger_BasicDeliverEventArgs(
            [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] TestClass pocObj,
            ILogger logger
            )
        {
            logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {pocObj}");
        }
    }
}
```

Assim como com objetos JSON, ocorrerá um erro se a mensagem não estiver formatada corretamente como um objeto C#. Se for, ela será associada à variável pocObj, que pode ser usada para o que quer que seja necessário.

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho RabbitMQ em um *function.jsno* arquivo e uma [função de script C#](functions-reference-csharp.md) que usa a associação. A função lê e registra a mensagem RabbitMQ.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

Aqui está o código de script do C#:

```C#
using System;

public static void Run(string myQueueItem, ILogger log)
{
    log.LogInformation($"C# Script RabbitMQ trigger function processed: {myQueueItem}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de gatilho RabbitMQ em um *function.jsno* arquivo e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função lê e registra uma mensagem RabbitMQ.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

Este é o código do script do JavaScript:

```javascript
module.exports = async function (context, myQueueItem) {
    context.log('JavaScript RabbitMQ trigger function processed work item', myQueueItem);
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra como ler uma mensagem da fila do RabbitMQ por meio de um gatilho.

Uma associação RabbitMQ é definida em *function.jsem* que *Type* está definido como `RabbitMQTrigger` .

```json
{
    "scriptFile": "__init__.py",
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

```python
import logging
import azure.functions as func

def main(myQueueItem) -> None:
    logging.info('Python RabbitMQ trigger function processed a queue item: %s', myQueueItem)
```

# <a name="java"></a>[Java](#tab/java)

A função Java a seguir usa a `@RabbitMQTrigger` Anotação dos [tipos RabbitMQ do Java](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq) para descrever a configuração de um gatilho de fila do RabbitMQ. A função captura a mensagem colocada na fila e a adiciona aos logs.

```java
@FunctionName("RabbitMQTriggerExample")
public void run(
    @RabbitMQTrigger(connectionStringSetting = "rabbitMQConnectionAppSetting", queueName = "queue") String input,
    final ExecutionContext context)
{
    context.getLogger().info("Java HTTP trigger processed a request." + input);
}
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Em [bibliotecas de classes C#](functions-dotnet-class-library.md), use o atributo [RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/Trigger/RabbitMQTriggerAttribute.cs) .

Aqui está um `RabbitMQTrigger` atributo em uma assinatura de método:

```csharp
[FunctionName("RabbitMQTest")]
public static void RabbitMQTest([RabbitMQTrigger("queue")] string message, ILogger log)
{
    ...
}
```

Para obter um exemplo completo, consulte C# [example](#example).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O script C# não dá suporte a atributos.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript não dá suporte a atributos.

# <a name="python"></a>[Python](#tab/python)

O Python não dá suporte a atributos.

# <a name="java"></a>[Java](#tab/java)

A `RabbitMQTrigger` anotação permite que você crie uma função que é executada quando uma mensagem RabbitMQ é criada. As opções de configuração disponíveis incluem nome da fila e nome da cadeia de conexão. Para obter detalhes adicionais de parâmetros, visite as [anotações do Java RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQTrigger.java).

Consulte o [exemplo](#example) de gatilho para obter mais detalhes.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `RabbitMQTrigger`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como "RabbitMQTrigger".|
|**direction** | n/d | Deve ser definido como "in".|
|**name** | n/d | O nome da variável que representa a fila no código de função. |
|**queueName**|**QueueName**| Nome da fila da qual receber mensagens. |
|**Nome do host**|**HostName**|(ignorado se estiver usando ConnectStringSetting) <br>Nome do host da fila (ex: 10.26.45.210)|
|**userNameSetting**|**UserNameSetting**|(ignorado se estiver usando ConnectionStringSetting) <br>Nome da configuração do aplicativo que contém o nome de usuário para acessar a fila. Exemplo: UserNameSetting: "% < UserNameFromSettings >%"|
|**passwordSetting**|**PasswordSetting**|(ignorado se estiver usando ConnectionStringSetting) <br>Nome da configuração do aplicativo que contém a senha para acessar a fila. Exemplo: PasswordSetting: "% < PasswordFromSettings >%"|
|**connectionStringSetting**|**ConnectionStringSetting**|O nome da configuração do aplicativo que contém a cadeia de conexão da fila de mensagens RabbitMQ. Observe que, se você especificar a cadeia de conexão diretamente e não por meio de uma configuração de aplicativo no local.settings.jsem, o gatilho não funcionará. (Por exemplo: em *function.jsem*: connectionStringSetting: "rabbitMQConnection" <br> Em *local.settings.jsem*: "rabbitMQConnection": "< ActualConnectionstring >")|
|**port**|**Porta**|(ignorado se estiver usando ConnectionStringSetting) Obtém ou define a porta usada. O padrão é 0, que aponta para a configuração de porta padrão do cliente RabbitMQ: 5672.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

O tipo de mensagem padrão é [evento RabbitMQ](https://rabbitmq.github.io/rabbitmq-dotnet-client/api/RabbitMQ.Client.Events.BasicDeliverEventArgs.html)e a `Body` Propriedade do evento RabbitMQ pode ser lida como os tipos listados abaixo:

* `An object serializable as JSON` -A mensagem é entregue como uma cadeia de caracteres JSON válida.
* `string`
* `byte[]`
* `POCO` -A mensagem é formatada como um objeto C#. Para obter um exemplo completo, consulte C# [example](#example).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O tipo de mensagem padrão é [evento RabbitMQ](https://rabbitmq.github.io/rabbitmq-dotnet-client/api/RabbitMQ.Client.Events.BasicDeliverEventArgs.html)e a `Body` Propriedade do evento RabbitMQ pode ser lida como os tipos listados abaixo:

* `An object serializable as JSON` -A mensagem é entregue como uma cadeia de caracteres JSON válida.
* `string`
* `byte[]`
* `POCO` -A mensagem é formatada como um objeto C#. Para obter um exemplo completo, consulte [exemplo](#example)de script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A mensagem da fila está disponível por meio de Context. Bindings.<NAME> onde <NAME> corresponde ao nome definido em function.jsem. Se a carga for JSON, o valor será desserializado em um objeto.

# <a name="python"></a>[Python](#tab/python)

Consulte o [exemplo](#example)de Python.

# <a name="java"></a>[Java](#tab/java)

Consulte [atributos e anotações](#attributes-and-annotations)do Java.

---

## <a name="dead-letter-queues"></a>Filas de mensagens mortas
As filas e as trocas de mensagens mortas não podem ser controladas ou configuradas no gatilho RabbitMQ.  Para usar filas de mensagens mortas, pré-configure a fila usada pelo gatilho em RabbitMQ. Consulte a documentação do [RabbitMQ](https://www.rabbitmq.com/dlx.html).

## <a name="hostjson-settings"></a>configurações de host.json

Esta seção descreve as definições de configuração global disponíveis para essa associação nas versões 2. x e superior. O exemplo *host.jsno* arquivo abaixo contém apenas as configurações dessa associação. Para obter mais informações sobre definições de configuração global, consulte [host.jsem referência para Azure Functions versão](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            "prefetchCount": 100,
            "queueName": "queue",
            "connectionString": "amqp://user:password@url:port",
            "port": 10
        }
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------|
|prefetchCount|30|Obtém ou define o número de mensagens que o receptor de mensagens pode solicitar e é armazenado em cache.|
|queueName|N/D| Nome da fila da qual receber mensagens.|
|connectionString|N/D|A cadeia de conexão da fila de mensagens RabbitMQ. Observe que a cadeia de conexão é especificada diretamente aqui e não por meio de uma configuração de aplicativo.|
|porta|0|(ignorado se estiver usando connectionString) Obtém ou define a porta usada. O padrão é 0, que aponta para a configuração de porta padrão do cliente RabbitMQ: 5672.|

## <a name="local-testing"></a>Teste local

> [!NOTE]
> O connectionString tem precedência sobre "hostName", "userName" e "password". Se todos estiverem definidos, o connectionString substituirá os outros dois.

Se você estiver testando localmente sem uma cadeia de conexão, defina a configuração "hostName" e "userName" e "password" se aplicável na seção "rabbitMQ" de *host.jsem*:

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            ...
            "hostName": "localhost",
            "username": "userNameSetting",
            "password": "passwordSetting"
        }
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------|
|hostName|N/D|(ignorado se estiver usando connectionString) <br>Nome do host da fila (ex: 10.26.45.210)|
|userName|N/D|(ignorado se estiver usando connectionString) <br>Nome para acessar a fila |
|password|N/D|(ignorado se estiver usando connectionString) <br>Senha para acessar a fila|


## <a name="enable-runtime-scaling"></a>Habilitar dimensionamento de tempo de execução

Para que o gatilho RabbitMQ Escale horizontalmente para várias instâncias, a configuração de **monitoramento de escala de tempo de execução** deve ser habilitada. 

No portal, essa configuração pode ser encontrada em   >  **configurações de tempo de execução da função** de configuração para seu aplicativo de funções.

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

Na CLI, você pode habilitar o **monitoramento de escala de tempo de execução** usando o seguinte comando:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

## <a name="monitoring-rabbitmq-endpoint"></a>Monitorando ponto de extremidade RabbitMQ
Para monitorar suas filas e trocas para um determinado ponto de extremidade RabbitMQ:

* Habilitar o [plug-in de gerenciamento do RabbitMQ](https://www.rabbitmq.com/management.html)
* Navegue até http://{node-nome_do_host}: 15672 e faça logon com seu nome de usuário e senha.

## <a name="next-steps"></a>Próximas etapas

- [Enviar mensagens RabbitMQ de Azure Functions (Associação de saída)](./functions-bindings-rabbitmq-output.md)
