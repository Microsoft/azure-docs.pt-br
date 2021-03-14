---
title: Associações de saída do barramento de serviço do Azure para Azure Functions
description: Saiba como enviar mensagens do barramento de serviço do Azure de Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: ae2be8dbcb4839c7d16b864c484c3360fdcfe324
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425576"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Associação de saída do barramento de serviço do Azure para Azure Functions

Use a associação de saída do barramento de serviço do Azure para enviar mensagens de fila ou de tópico.

Para obter informações sobre a instalação e detalhes de configuração, confira a [visão geral](functions-bindings-service-bus.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

A exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que envia uma mensagem de fila do Barramento de Serviço:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma associação de saída de Barramento de Serviço em um arquivo *function.json* e uma [função script C#](functions-reference-csharp.md) que usa a associação. A função usa um gatilho de timer para enviar uma mensagem da fila a cada 15 segundos.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Aqui está o código script C# que cria uma mensagem única:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Este é o código de script C# que cria várias mensagens:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra uma função Java que envia uma mensagem para uma fila do barramento de serviço `myqueue` quando disparada por uma solicitação HTTP.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 Na [biblioteca de runtime das funções Java](/java/api/overview/azure/functions/runtime), use a anotação `@QueueOutput` nos parâmetros da função cujo valor poderia ser gravado em uma fila do Barramento de Serviço.  O tipo de parâmetro deve ser `OutputBinding<T>`, onde T é qualquer tipo Java nativo de um POJO.

As funções Java também podem gravar em um tópico do barramento de serviço. O exemplo a seguir usa a `@ServiceBusTopicOutput` anotação para descrever a configuração da Associação de saída. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de saída de Barramento de Serviço em um arquivo *function.json* e uma [função JavaScript C#](functions-reference-node.md) que usa a associação. A função usa um gatilho de timer para enviar uma mensagem da fila a cada 15 segundos.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Aqui está o código de script JavaScript que cria uma mensagem única:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Aqui está o código de script JavaScript que cria várias mensagens:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir mostra uma associação de saída do barramento de serviço em um *function.jsno* arquivo e uma [função do PowerShell](functions-reference-powershell.md) que usa a associação. 

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
  "bindings": [
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "outputSbMsg",
      "queueName": "outqueue",
      "topicName": "outtopic"
    }
  ]
}
```

Aqui está o PowerShell que cria uma mensagem como a saída da função.

```powershell
param($QueueItem, $TriggerMetadata) 

Push-OutputBinding -Name outputSbMsg -Value @{ 
    name = $QueueItem.name 
    employeeId = $QueueItem.employeeId 
    address = $QueueItem.address 
} 
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra como gravar em uma fila do barramento de serviço em Python.

Uma definição de associação do barramento de serviço é definida em *function.jsem* onde o *tipo* está definido como `serviceBus` .

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
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

Em *_\_ init_ \_ . py*, você pode gravar uma mensagem na fila passando um valor para o `set` método.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

O construtor do atributo usa o nome da fila ou o tópico e a assinatura. Você também pode especificar os direitos de acesso da conexão. Há uma explicação sobre como escolher as configuração de direitos de acesso na seção [Saída - Configuração](#configuration). Aqui está um exemplo que mostra o atributo aplicado ao valor retornado da função:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Você pode definir a `Connection` propriedade para especificar o nome de uma configuração de aplicativo que contém a cadeia de conexão do barramento de serviço a ser usada, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Para obter um exemplo completo, consulte [saída-exemplo](#example).

Você pode usar o atributo `ServiceBusAccount` para especificar a conta do Barramento de Serviço ao nível da classe, do método ou do parâmetro.  Para obter mais informações, consulte [Trigger-Attributes](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O script C# não dá suporte a atributos.

# <a name="java"></a>[Java](#tab/java)

As `ServiceBusQueueOutput` `ServiceBusTopicOutput` anotações e estão disponíveis para gravar uma mensagem como uma saída de função. O parâmetro decorado com essas anotações deve ser declarado como um `OutputBinding<T>` onde `T` é o tipo correspondente ao tipo da mensagem.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript não dá suporte a atributos.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Não há suporte para atributos pelo PowerShell.

# <a name="python"></a>[Python](#tab/python)

O Python não dá suporte a atributos.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `ServiceBus`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como "serviceBus". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | n/d | Deve ser definido como "out". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | n/d | O nome da variável que representa a fila ou mensagem de tópico no código de função. Definido como "$return" para referenciar o valor de retorno da função. |
|**queueName**|**QueueName**|Nome da fila.  Defina somente se for enviar mensagens da fila, não para um tópico.
|**topicName**|**Topicname**|Nome do tópico. Defina somente se for enviar mensagens do tópico, não para uma fila.|
|**connection**|**Conexão**|O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Barramento de Serviço para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome. Por exemplo, se você definir `connection` como "MyServiceBus", o tempo de execução do Functions procurará uma configuração de aplicativo chamada "AzureWebJobsMyServiceBus". Se você deixar `connection` vazio, o runtime de Functions usa a cadeia de caracteres de conexão do Barramento de serviço na configuração de aplicativo chamada "AzureWebJobsServiceBus".<br><br>Para obter uma cadeia de conexão, siga as etapas mostradas em [Obter as credenciais de gerenciamento](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). A cadeia de conexão deve ser voltada para um namespace do Barramento de Serviço, não limitada a uma fila ou tópico específico.|
|**accessRights** (somente v1)|**Acesso**|Direitos de acesso para a cadeia de caracteres de conexão. Os valores disponíveis são `manage` e `listen`. O padrão é `manage`, que indica que o `connection` tem a permissão **Gerenciar**. Se você usar uma cadeia de conexão que não tenha a permissão **Gerenciar**, defina `accessRights` como "escutar". Caso contrário, o runtime do Functions talvez falhe ao tentar executar operações que exigem o gerenciamento de direitos. No Azure Functions versão 2. x e superior, essa propriedade não está disponível porque a versão mais recente do SDK do barramento de serviço não dá suporte a operações de gerenciamento.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

No Azure Functions 1. x, o runtime criará a fila se ela não existir e você tiver definido `accessRights` como `manage`. Nas funções versão 2. x e superior, a fila ou o tópico já deve existir; Se você especificar uma fila ou um tópico que não existe, a função falhará. 

# <a name="c"></a>[C#](#tab/csharp)

Use os seguintes tipos de parâmetro para a associação de saída:

* `out T paramName` - `T` pode ser qualquer tipo serializável em JSON. Se o valor do parâmetro for nulo quando a função existir, o Functions criará a mensagem com um objeto nulo.
* `out string` - Se o valor de parâmetro não for nulo quando a função sair, o Functions criará uma mensagem.
* `out byte[]` - Se o valor de parâmetro não for nulo quando a função sair, o Functions criará uma mensagem.
* `out BrokeredMessage` -Se o valor do parâmetro for nulo quando a função for encerrada, as funções não criarão uma mensagem (para as funções 1. x)
* `out Message` -Se o valor do parâmetro for nulo quando a função for encerrada, as funções não criarão uma mensagem (para as funções 2. x e superior)
* `ICollector<T>` ou `IAsyncCollector<T>` (para métodos assíncronos) – para criar várias mensagens. Uma mensagem é criada quando você chama o método `Add` .

Ao trabalhar com funções C#:

* As funções assíncronas precisam de um valor de retorno ou `IAsyncCollector` em vez de um `out` parâmetro.

* Para acessar a ID da sessão, associe a um [`Message`](/dotnet/api/microsoft.azure.servicebus.message) tipo e use a `sessionId` propriedade.

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Use os seguintes tipos de parâmetro para a associação de saída:

* `out T paramName` - `T` pode ser qualquer tipo serializável em JSON. Se o valor do parâmetro for nulo quando a função existir, o Functions criará a mensagem com um objeto nulo.
* `out string` - Se o valor de parâmetro não for nulo quando a função sair, o Functions criará uma mensagem.
* `out byte[]` - Se o valor de parâmetro não for nulo quando a função sair, o Functions criará uma mensagem.
* `out BrokeredMessage` -Se o valor do parâmetro for nulo quando a função for encerrada, as funções não criarão uma mensagem (para as funções 1. x)
* `out Message` -Se o valor do parâmetro for nulo quando a função for encerrada, as funções não criarão uma mensagem (para as funções 2. x e superior)
* `ICollector<T>` ou `IAsyncCollector<T>` - Para a criação de várias mensagens. Uma mensagem é criada quando você chama o método `Add` .

Ao trabalhar com funções C#:

* As funções assíncronas precisam de um valor de retorno ou `IAsyncCollector` em vez de um `out` parâmetro.

* Para acessar a ID da sessão, associe a um [`Message`](/dotnet/api/microsoft.azure.servicebus.message) tipo e use a `sessionId` propriedade.

# <a name="java"></a>[Java](#tab/java)

Use o [SDK do barramento de serviço do Azure](../service-bus-messaging/index.yml) em vez da Associação de saída interna.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Acesse a fila ou o tópico usando `context.bindings.<name from function.json>` . Você pode atribuir uma cadeia de caracteres, uma matriz de bytes ou um objeto JavaScript (desserializado em JSON) para `context.binding.<name>` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A saída para o barramento de serviço está disponível por meio do `Push-OutputBinding` cmdlet em que você passa argumentos que correspondem ao nome designado pelo parâmetro de nome da associação na *function.jsno* arquivo.

# <a name="python"></a>[Python](#tab/python)

Use o [SDK do barramento de serviço do Azure](../service-bus-messaging/index.yml) em vez da Associação de saída interna.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Associação | Referência |
|---|---|
| Barramento de Serviço | [Códigos de erro do Barramento de Serviço do Microsoft Azure](../service-bus-messaging/service-bus-messaging-exceptions.md) |
| Barramento de Serviço | [Limites do Barramento de Serviço do Microsoft Azure](../service-bus-messaging/service-bus-quotas.md) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>configurações de host.json

Esta seção descreve as definições de configuração global disponíveis para essa associação nas versões 2. x e superior. O exemplo host.jsno arquivo abaixo contém apenas as configurações dessa associação. Para obter mais informações sobre definições de configuração global, consulte [host.jsem referência para Azure Functions versão](functions-host-json.md).

> [!NOTE]
> Para obter uma referência de host.json no Functions 1.x, confira [Referência de host.json para o Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": true,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:05:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

Se você tiver `isSessionsEnabled` definido como `true` , o `sessionHandlerOptions` será respeitado.  Se você tiver `isSessionsEnabled` definido como `false` , o `messageHandlerOptions` será respeitado.

|Propriedade  |Padrão | Descrição |
|---------|---------|---------|
|prefetchCount|0|Obtém ou define o número de mensagens que o destinatário da mensagem pode solicitar simultaneamente.|
|maxAutoRenewDuration|00:05:00|A duração máxima na qual o bloqueio de mensagem será renovado automaticamente.|
|autoComplete|true|Se o gatilho deve chamar automaticamente Complete após o processamento ou se o código de função chamará manualmente.<br><br>A configuração para `false` tem suporte apenas em C#.<br><br>Se definido como `true` , o gatilho concluirá a mensagem automaticamente se a execução da função for concluída com êxito e abandonará a mensagem de outra forma.<br><br>Quando definido como `false` , você é responsável por chamar métodos [MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver) para concluir, abandonar ou incorrer a mensagem. Se uma exceção for lançada (e nenhum dos `MessageReceiver` métodos for chamado), o bloqueio permanecerá. Depois que o bloqueio expirar, a mensagem será colocada em fila novamente com o `DeliveryCount` incremento e o bloqueio será renovado automaticamente.<br><br>Em funções não C #, as exceções na função resultam em chamadas de tempo de execução `abandonAsync` em segundo plano. Se nenhuma exceção ocorrer, `completeAsync` será chamado em segundo plano. |
|maxConcurrentCalls|16|O número máximo de chamadas simultâneas para o retorno de chamada que a bomba de mensagem deve iniciar por instância dimensionada. Por padrão, o runtime do Functions processa várias mensagens simultaneamente.|
|maxConcurrentSessions|2000|O número máximo de sessões que podem ser manipuladas simultaneamente por instância dimensionada.|

## <a name="next-steps"></a>Próximas etapas

- [Executar uma função quando uma fila ou mensagem de tópico do barramento de serviço é criada (gatilho)](./functions-bindings-service-bus-trigger.md)
