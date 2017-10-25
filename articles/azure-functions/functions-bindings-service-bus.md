---
title: "Gatilhos e associações do Barramento de Serviço do Azure Functions | Microsoft Docs"
description: "Entenda como usar gatilhos e associações do Barramento de Serviço do Azure no Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/01/2017
ms.author: glenga
ms.openlocfilehash: 71149aaacc940a62e085cf1ce103a0214d05bd1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-service-bus-bindings"></a>Associações do Barramento de Serviço do Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e trabalhar com associações do Barramento de Serviço do Azure no Azure Functions. 

O Azure Functions dá suporte a gatilhos e a associações de saída para filas e tópicos do Barramento de Serviço.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="service-bus-trigger"></a>Gatilho do Barramento de Serviço
Use o gatilho do Barramento de Serviço para responder às mensagens de uma fila ou tópico do Barramento de Serviço. 

Os gatilhos de fila e de tópico do Barramento de Serviço são definidos pelos seguintes objetos JSON na matriz `bindings` de function.json:

* Gatilho de *fila*:

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

* Gatilho de *tópico*:

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

Observe o seguinte:

* Para `connection`, [crie uma configuração de aplicativo em seu aplicativo de função](functions-how-to-use-azure-function-app-settings.md) que contenha a cadeia de conexão até o namespace de seu Barramento de Serviço, depois especifique o nome da configuração de aplicativo na propriedade `connection` em seu gatilho. Obtenha a cadeia de conexão, seguindo as etapas mostradas em [Obter as credenciais de gerenciamento](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials).
  A cadeia de conexão deve ser voltada para um namespace do Barramento de Serviço, não limitada a uma fila ou tópico específico.
  Se você deixar `connection` vazio, o gatilho assumirá que uma cadeia de conexão do Barramento de Serviço padrão foi especificada em uma configuração de aplicativo chamada `AzureWebJobsServiceBus`.
* Para `accessRights`, os valores disponíveis são `manage` e `listen`. O padrão é `manage`, que indica que o `connection` tem a permissão **Gerenciar**. Se você usar uma cadeia de conexão que não tenha a permissão **Gerenciar**, defina `accessRights` como `listen`. Caso contrário, o tempo de execução do Functions talvez falhe ao tentar executar operações que exigem o gerenciamento de direitos.

## <a name="trigger-behavior"></a>Comportamento do gatilho
* **Threading simples** - por padrão, o tempo de execução do Functions processa várias mensagens simultaneamente. Para direcionar o tempo de execução para processar uma única fila ou mensagem de tópico de cada vez, defina `serviceBus.maxConcurrentCalls` como 1 em *host.json* . 
  Para obter informações sobre o *host.json*, consulte [Estrutura da pasta](functions-reference.md#folder-structure) e [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).
* **Manipulação de mensagens suspeitas** - o Barramento de Serviço faz seu próprio tratamento de mensagens suspeitas que não pode ser controlado ou definido na configuração ou código do Azure Functions. 
* **Comportamento PeekLock** - o tempo de execução do Functions recebe uma mensagem no modo [`PeekLock` ](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode) e chama `Complete` na mensagem se a função for concluída com êxito, ou chama `Abandon` se a função falhar. 
  Se a função for executada por mais tempo que o limite `PeekLock` , o bloqueio é renovado automaticamente.

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Uso de gatilho
Esta seção mostra como usar o gatilho do Barramento de Serviço no código de sua função. 

Em C# e F#, a mensagem do gatilho do Barramento de Serviço pode ser desserializada para qualquer um destes tipos de entrada:

* `string` - útil para mensagens de cadeia de caracteres
* `byte[]` - útil para dados binários
* Qualquer [Objeto](https://msdn.microsoft.com/library/system.object.aspx) - útil para dados JSON serializados.
  Se você declarar um tipo de entrada personalizado, por exemplo, `CustomType`, o Azure Functions tentará desserializar os dados JSON para o tipo especificado.
* `BrokeredMessage` -fornece a você a mensagem desserializada com o método [BrokeredMessage.GetBody<T>()](https://msdn.microsoft.com/library/hh144211.aspx).

No Node.js, a mensagem de gatilho do Barramento de Serviço é passada em uma função como uma cadeia de caracteres ou um objeto JSON.

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Exemplo de gatilho
Vamos supor que você tenha o seguinte function.json:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Veja o exemplo específico à linguagem que processa uma mensagem de fila do Barramento de Serviço.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Exemplo de gatilho em C# #

```cs
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Exemplo de gatilho em F# #

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Exemplo de gatilho em Node.js

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

<a name="output"></a>

## <a name="service-bus-output-binding"></a>Associação de saída do Barramento de Serviço
A saída da fila e do tópico do Barramento de Serviço para uma função usa os seguintes objetos JSON na matriz `bindings` de function.json:

* Saída da *fila*:

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```
* Saída do *tópico*:

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```

Observe o seguinte:

* Para `connection`, [crie uma configuração de aplicativo em seu aplicativo de função](functions-how-to-use-azure-function-app-settings.md) que contenha a cadeia de conexão até o namespace de seu Barramento de Serviço, depois especifique o nome da configuração de aplicativo na propriedade `connection` em sua associação de saída. Obtenha a cadeia de conexão, seguindo as etapas mostradas em [Obter as credenciais de gerenciamento](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials).
  A cadeia de conexão deve ser voltada para um namespace do Barramento de Serviço, não limitada a uma fila ou tópico específico.
  Se você deixar `connection` vazio, a associação da saída assumirá que uma cadeia de conexão do Barramento de Serviço padrão foi especificada em uma configuração de aplicativo chamada `AzureWebJobsServiceBus`.
* Para `accessRights`, os valores disponíveis são `manage` e `listen`. O padrão é `manage`, que indica que o `connection` tem a permissão **Gerenciar**. Se você usar uma cadeia de conexão que não tenha a permissão **Gerenciar**, defina `accessRights` como `listen`. Caso contrário, o tempo de execução do Functions talvez falhe ao tentar executar operações que exigem o gerenciamento de direitos.

<a name="outputusage"></a>

## <a name="output-usage"></a>Uso de saída
Em C# e F#, o Azure Functions pode criar uma mensagem de fila do Barramento de Serviço a partir de qualquer um dos tipos a seguir:

* Qualquer [Objeto](https://msdn.microsoft.com/library/system.object.aspx) - A definição do parâmetro se parece com `out T paramName` (C#).
  O Functions desserializa o objeto em uma mensagem JSON. Se o valor de saída for nulo quando a função existir, o Functions cria a mensagem com um objeto nulo.
* `string` - A definição de parâmetro se parece com `out string paraName` (C#). Se o valor de parâmetro não for nulo quando a função existir, o Functions criará uma mensagem.
* `byte[]` - A definição de parâmetro se parece com `out byte[] paraName` (C#). Se o valor de parâmetro não for nulo quando a função existir, o Functions criará uma mensagem.
* `BrokeredMessage` A definição de parâmetro se parece com `out BrokeredMessage paraName` (C#). Se o valor de parâmetro não for nulo quando a função existir, o Functions criará uma mensagem.

Para criar várias mensagens em uma função C#, você pode usar `ICollector<T>` ou `IAsyncCollector<T>`. Uma mensagem é criada quando você chama o método `Add` .

No Node.js, você pode atribuir uma cadeia de caracteres, uma matriz de bytes ou um objeto Javascript (desserializado em JSON) para `context.binding.<paramName>`.

<a name="outputsample"></a>

## <a name="output-sample"></a>Amostra de saída
Vamos supor que você tenha o seguinte function.json, que define uma saída de fila do Barramento de Serviço:

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

Veja o exemplo específico à linguagem que envia uma mensagem à fila do barramento de serviço.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Amostra de saída em C# #

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

Ou, para criar várias mensagens:

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Amostra de saída em F# #

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Amostra de saída no Node.js

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

Ou, para criar várias mensagens:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = [];
    context.bindings.outputSbQueueMsg.push("1 " + message);
    context.bindings.outputSbQueueMsg.push("2 " + message);
    context.done();
};
```

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

