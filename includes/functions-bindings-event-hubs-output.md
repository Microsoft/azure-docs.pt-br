---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: bc2bec364f8d752b7416ecccf0b00d0fbec4c8e8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729883"
---
Use a associação de saída dos Hubs de Eventos para gravar eventos em um fluxo de eventos. É necessário ter permissão de envio para que um hub de eventos grave eventos nele.

Verifique se as referências de pacote necessárias estão em vigor antes de tentar implementar uma associação de saída.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](../articles/azure-functions/functions-dotnet-class-library.md) que grava uma mensagem em um hub de eventos, usando o valor retornado do método como a saída:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

O exemplo a seguir mostra como usar a interface `IAsyncCollector` para enviar um lote de mensagens. Esse cenário será comum quando você estiver processando mensagens provenientes de um Hub de Eventos e enviando o resultado para outro Hub de Eventos.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho de hub de eventos em um arquivo *function.json* e uma [função C# script](../articles/azure-functions/functions-reference-csharp.md) que usa a associação. A função grava uma mensagem em um hub de eventos.

Os exemplos a seguir mostram dados de associação de Hubs de eventos no *function.json* arquivo. O primeiro exemplo será para o Functions 2.x e posterior. Já o segundo, para o Functions 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Este é o código C# script que cria uma mensagem:

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

Este é o código de script C# que cria várias mensagens:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de gatilho de hub de eventos em um arquivo *function.json* e uma [função JavaScript](../articles/azure-functions/functions-reference-node.md) que usa a associação. A função grava uma mensagem em um hub de eventos.

Os exemplos a seguir mostram dados de associação de Hubs de eventos no *function.json* arquivo. O primeiro exemplo será para o Functions 2.x e posterior. Já o segundo, para o Functions 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Este é o código JavaScript que envia uma mensagem única:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Este é o código JavaScript que envia várias mensagens:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma ligação de acionador do Hub de Eventos em um arquivo *function.json* e uma [função Python](../articles/azure-functions/functions-reference-python.md) que usa a ligação. A função grava uma mensagem em um hub de eventos.

Os exemplos a seguir mostram dados de associação de Hubs de eventos no *function.json* arquivo.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Este é o código Python que envia uma mensagem única:

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostrará uma função Java que grava uma mensagem contendo o horário atual de um Hub de Eventos.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

Na [biblioteca de runtime de funções Java](/java/api/overview/azure/functions/runtime), use a anotação `@EventHubOutput` em parâmetros cujo valor seria publicado no Hub de Eventos.  O parâmetro deve ser do tipo `OutputBinding<T>`, em que T é um POJO ou qualquer tipo Java nativo.

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Para [bibliotecas de classes de C#](../articles/azure-functions/functions-dotnet-class-library.md), utilize o atributo [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs).

O construtor do atributo usa os nomes do hub de eventos e de uma configuração de aplicativo que contenham a cadeia de caracteres de conexão. Para obter mais informações sobre essas configurações, consulte [Saída - configuração](#configuration). Este é um `EventHub` exemplo de atributo:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Para ver um exemplo completo, consulte [Saída – exemplo de C#](#example).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O script C# não dá suporte a atributos.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript não dá suporte a atributos.

# <a name="python"></a>[Python](#tab/python)

O Python não dá suporte a atributos.

# <a name="java"></a>[Java](#tab/java)

Na [biblioteca de runtime de funções Java](/java/api/overview/azure/functions/runtime), use a anotação [EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) em parâmetros cujo valor seria publicado no Hub de Eventos. O parâmetro deverá ser do tipo `OutputBinding<T>`, em que `T` é um POJO ou um tipo Java nativo.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `EventHub`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como "eventHub". |
|**direction** | n/d | Deve ser definido como "out". Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure. |
|**name** | n/d | É o nome da variável usada no código da função que representa o evento. |
|**path** |**EventHubName** | Funciona apenas 1. x. O nome do hub de eventos. Quando o nome do hub de eventos também estiver presente na cadeia de conexão, esse valor substitui essa propriedade em runtime. |
|**eventHubName** |**EventHubName** | Functions 2.x e posterior. O nome do hub de eventos. Quando o nome do hub de eventos também estiver presente na cadeia de conexão, esse valor substitui essa propriedade em runtime. |
|**connection** |**Conexão** | É o nome de uma configuração de aplicativo que contém a cadeia de conexão para o namespace do hub de eventos. Copie essa cadeia de conexão clicando no botão **Informações de Conexão** do [namespace](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), não no próprio hub de eventos. Essa cadeia de conexão deve ter permissões de envio para enviar a mensagem à transmissão do evento. <br><br>Se você estiver usando a [versão 5.x ou superior da extensão](../articles/azure-functions/functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher), em vez de uma cadeia de conexão, você poderá fornecer uma referência a uma seção de configuração que define a conexão. Confira a opção [Conexões](../articles/azure-functions/functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Padrão

É possível usar os seguintes tipos de parâmetro para a associação de saída do Hub de Eventos:

* `string`
* `byte[]`
* `POCO`
* `EventData` – As propriedades padrão de EventData são fornecidas para o [namespace Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs.eventdata).

Envie mensagens usando um parâmetro de método, como `out string paramName`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. Para gravar várias mensagens, você pode usar `ICollector<string>` ou `IAsyncCollector<string>` no lugar de `out string`.

### <a name="additional-types"></a>Tipos adicionais 
Os aplicativos que usam a versão 5.0.0 ou posterior da extensão do Hub de Eventos usarão o tipo `EventData` no [Azure.Messaging.EventHubs](/dotnet/api/azure.messaging.eventhubs.eventdata) em vez do tipo usado no [namespace Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs.eventdata). Essa versão removerá o suporte do tipo `Body` herdado para beneficiar os seguintes tipos:

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody)

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

### <a name="default"></a>Padrão

É possível usar os seguintes tipos de parâmetro para a associação de saída do Hub de Eventos:

* `string`
* `byte[]`
* `POCO`
* `EventData` – As propriedades padrão de EventData são fornecidas para o [namespace Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs.eventdata).

Envie mensagens usando um parâmetro de método, como `out string paramName`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. Para gravar várias mensagens, você pode usar `ICollector<string>` ou `IAsyncCollector<string>` no lugar de `out string`.

### <a name="additional-types"></a>Tipos adicionais 
Os aplicativos que usam a versão 5.0.0 ou posterior da extensão do Hub de Eventos usarão o tipo `EventData` no [Azure.Messaging.EventHubs](/dotnet/api/azure.messaging.eventhubs.eventdata) em vez do tipo usado no [namespace Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs.eventdata). Essa versão removerá o suporte do tipo `Body` herdado para beneficiar os seguintes tipos:

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Acesse o evento de saída usando `context.bindings.<name>`, em que `<name>` é o valor especificado na propriedade `name` de *function.json*.

# <a name="python"></a>[Python](#tab/python)

Há duas opções para gerar uma mensagem do Hub de Eventos de uma função:

- **Valor retornado**: defina a propriedade `name` no *function.json* como `$return`. Com essa configuração, o valor retornado da função será mantido como uma mensagem do Hub de Eventos.

- **Imperativo**: passe um valor para [definir](/python/api/azure-functions/azure.functions.out#set-val--t-----none) o método do parâmetro declarado como um tipo de [Saída](/python/api/azure-functions/azure.functions.out). O valor passado para `set` será mantido como uma mensagem do Hub de Eventos.

# <a name="java"></a>[Java](#tab/java)

Há duas opções para gerar uma mensagem do Hub de Eventos de uma função usando a anotação [EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput):

- **Valor retornado**: ao aplicar a anotação à própria função, o valor retornado da função será mantido como uma mensagem do Hub de Eventos.

- **Imperativo**: para definir o valor da mensagem de modo explícito, aplique a anotação a um parâmetro específico do tipo [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.OutputBinding), em que `T` será um POJO ou um tipo de Java nativo. Com essa configuração, passar um valor para o método `setValue` manterá o valor como uma mensagem do Hub de Eventos.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Associação | Referência |
|---|---|
| Hub de evento | [Guia de Operações](/rest/api/eventhub/publisher-policy-operations) |