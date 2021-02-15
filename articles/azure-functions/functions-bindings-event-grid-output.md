---
title: Associação de saída da Grade de Eventos do Azure para Azure Functions
description: Saiba como enviar um evento da Grade de Eventos no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 888afdc2764fed9f0b2c8b548c3e2b1c48e9a31e
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094669"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Associação de saída da Grade de Eventos do Azure para Azure Functions

Use a associação de saída da Grade de Eventos para gravar eventos em um tópico personalizado. Você deve ter uma [chave de acesso válida para o tópico personalizado](../event-grid/security-authenticate-publishing-clients.md).

Para obter informações sobre a instalação e detalhes de configuração, confira a [visão geral](./functions-bindings-event-grid.md).

> [!NOTE]
> A associação de saída da Grade de Eventos não oferece suporte a assinaturas de acesso compartilhado (tokens SAS). Você deve usar a chave de acesso do tópico.

> [!IMPORTANT]
> A associação de saída da Grade de Eventos só está disponível para o Functions 2.x e superiores.

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que grava uma mensagem em um tópico personalizado da Grade de Eventos usando o valor retornado do método como a saída:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

O exemplo a seguir mostra como usar a interface `IAsyncCollector` para enviar um lote de mensagens.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra os dados de associação de saída da Grade de Eventos no arquivo *function.json*.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Este é o código C# script que cria um evento:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Este é o código de script C# que cria vários eventos:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="java"></a>[Java](#tab/java)

A associação de saída da Grade de Eventos não está disponível para Java.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra os dados de associação de saída da Grade de Eventos no arquivo *function.json*.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Este é o código JavaScript que cria um único evento:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

Este é o código JavaScript que cria vários eventos:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir demonstra como configurar uma função para gerar uma mensagem de evento de grade de eventos. A seção em que é configurada `type` para configurar `eventGrid` os valores necessários para estabelecer uma associação de saída de grade de eventos.

```powershell
{
  "bindings": [
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    },
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
}
```

Em sua função, use o `Push-OutputBinding` para enviar um evento para um tópico personalizado por meio da Associação de saída da grade de eventos.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message

Push-OutputBinding -Name outputEvent -Value  @{
    id = "1"
    EventType = "testEvent"
    Subject = "testapp/testPublish"
    EventTime = "2020-08-27T21:03:07+00:00"
    Data = @{
        Message = $message
    }
    DataVersion = "1.0"
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma associação de gatilho em um arquivo *function.json* e uma [função Python](functions-reference-python.md) que usa a associação. Em seguida, ele envia em um evento para o tópico personalizado, conforme especificado pelo `topicEndpointUri` .

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    },
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Aqui está o exemplo de Python para enviar um evento a um tópico personalizado definindo `EventGridOutputEvent` :

```python
import logging
import azure.functions as func
import datetime

def main(eventGridEvent: func.EventGridEvent, 
         outputEvent: func.Out[func.EventGridOutputEvent]) -> None:

    logging.log("eventGridEvent: ", eventGridEvent)

    outputEvent.set(
        func.EventGridOutputEvent(
            id="test-id",
            data={"tag1": "value1", "tag2": "value2"},
            subject="test-subject",
            event_type="test-event-1",
            event_time=datetime.datetime.utcnow(),
            data_version="1.0"))
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Para [bibliotecas de classes de C#](functions-dotnet-class-library.md), use o atributo [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs).

O construtor do atributo usa o nome de uma configuração de aplicativo que contém o nome do tópico personalizado e o nome de uma configuração de aplicativo que contém a chave do tópico. Para obter mais informações sobre essas configurações, consulte [Saída - configuração](#configuration). Este é um `EventGrid` exemplo de atributo:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Para ver um exemplo completo, confira o [exemplo](#example).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O script C# não dá suporte a atributos.

# <a name="java"></a>[Java](#tab/java)

A associação de saída da Grade de Eventos não está disponível para Java.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript não dá suporte a atributos.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Não há suporte para atributos pelo PowerShell.

# <a name="python"></a>[Python](#tab/python)

A associação de saída da Grade de Eventos não está disponível para Python.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `EventGrid`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como "eventGrid". |
|**direction** | n/d | Deve ser definido como "out". Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure. |
|**name** | n/d | É o nome da variável usada no código da função que representa o evento. |
|**topicEndpointUri** |**TopicEndpointUri** | O nome de uma configuração de aplicativo que contém o URI para o tópico personalizado, como `MyTopicEndpointUri`. |
|**topicKeySetting** |**TopicKeySetting** | O nome de uma configuração de aplicativo que contém uma chave de acesso para o tópico personalizado. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Defina o valor da propriedade de configuração `TopicEndpointUri` como o nome de uma configuração de aplicativo que contém o URI do tópico personalizado. Não especifique o URI do tópico personalizado diretamente nesta propriedade.

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

Envie mensagens usando um parâmetro de método, como `out EventGridEvent paramName`. Para gravar várias mensagens, você pode usar `ICollector<EventGridEvent>` ou `IAsyncCollector<EventGridEvent>` no lugar de `out EventGridEvent`.

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Envie mensagens usando um parâmetro de método, como `out EventGridEvent paramName`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. Para gravar várias mensagens, você pode usar `ICollector<EventGridEvent>` ou `IAsyncCollector<EventGridEvent>` no lugar de `out EventGridEvent`.

# <a name="java"></a>[Java](#tab/java)

A associação de saída da Grade de Eventos não está disponível para Java.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Acesse o evento de saída usando `context.bindings.<name>`, em que `<name>` é o valor especificado na propriedade `name` de *function.json*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Acesse o evento de saída usando o `Push-OutputBinding` commandlet para enviar um evento para a associação de saída da grade de eventos.

# <a name="python"></a>[Python](#tab/python)

A associação de saída da Grade de Eventos não está disponível para Python.

---

## <a name="next-steps"></a>Próximas etapas

* [Enviar um evento de Grade de Eventos](./functions-bindings-event-grid-trigger.md)
