---
title: "Associações de armazenamento de filas do Azure Functions | Microsoft Docs"
description: "Entenda como usar gatilhos e associações do Armazenamento do Azure em Azure Functions."
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: 4e6a837d-e64f-45a0-87b7-aa02688a75f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/30/2017
ms.author: glenga
ms.openlocfilehash: 8f7227071edc519e588d55a9f06b77aeaf31aa37
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-queue-storage-bindings"></a>Associações de Armazenamento de Filas do Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Esse artigo descreve como configurar e codificar associações de armazenamento de filas do Azure no Azure Functions. O Azure Functions dá suporte a associações de gatilho e de saída para filas do Azure. Para os recursos que estão disponíveis em todas as associações, veja [Gatilhos e conceitos de associações do Azure Functions](functions-triggers-bindings.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="queue-storage-trigger"></a>Gatilho de armazenamento de filas
O gatilho do armazenamento de filas do Azure permite que você monitore um armazenamento de fila para novas mensagens e responda a elas. 

Defina um gatilho de fila usando a guia **Integrar** no portal do Functions. O portal cria a seguinte definição na seção **Ligações** de *function.json*:

```json
{
    "type": "queueTrigger",
    "direction": "in",
    "name": "<The name used to identify the trigger data in your code>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>"
}
```

* A propriedade `connection` deve conter o nome de uma configuração de aplicativo que contenha uma cadeia de conexão de armazenamento. No Portal do Azure, o editor padrão na guia **Integrar** define essa configuração de aplicativo para você ao selecionar uma conta de armazenamento.

[Configurações adicionais](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) podem ser fornecidas em um arquivo host.json para ajustar ainda mais gatilhos de armazenamento de filas. Por exemplo, você pode alterar o intervalo de sondagem de fila no host.json.

<a name="triggerusage"></a>

## <a name="using-a-queue-trigger"></a>Usando um gatilho de fila
Em funções do Node.js, acesse os dados da fila usando `context.bindings.<name>`.


Nas funções do .NET, acesse o conteúdo da fila usando um parâmetro de método, como `CloudQueueMessage paramName`. Aqui, `paramName` é o valor especificado na [configuração do gatilho](#trigger). A mensagem da fila pode ser desserializada para qualquer um destes tipos:

* Objeto POCO. Use se o conteúdo da fila for um objeto JSON. O tempo de execução do Functions desserializa o conteúdo no objeto POCO. 
* `string`
* `byte[]`
* [`CloudQueueMessage`]

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>Metadados de gatilho de fila
O gatilho de fila fornece várias propriedades de metadados. Essas propriedades podem ser usadas como parte de expressões de associação em outras associações ou como parâmetros em seu código. Os valores têm a mesma semântica que [`CloudQueueMessage`].

* **QueueTrigger** – conteúdo da fila (se for uma cadeia de caracteres válida)
* **DequeueCount** – Tipo `int`. O número de vezes que essa mensagem foi removida da fila.
* **ExpirationTime** – Tipo `DateTimeOffset?`. A hora em que a mensagem expira.
* **Id** – Tipo `string`. ID da mensagem da fila.
* **InsertionTime** – Tipo `DateTimeOffset?`. A hora em que a mensagem foi adicionada à fila.
* **NextVisibleTime** – Tipo `DateTimeOffset?`. A hora em que a mensagem estará visível.
* **PopReceipt** – Tipo `string`. Recebimento pop da mensagem.

Confira como usar os metadados de fila no [Exemplo de gatilho](#triggersample).

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Exemplo de gatilho
Suponha que você tem o seguinte function.json, que define um gatilho da fila:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionString"
        }
    ]
}
```

Confira o exemplo específico a um idioma que recupera e registra os metadados da fila.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Exemplo de gatilho em C# #
```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger sample in F# ## 
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Exemplo de gatilho em Node.js

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id=', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

### <a name="handling-poison-queue-messages"></a>Tratamento de mensagens suspeitas na fila
Quando uma função do gatilho de fila falhar, o Azure Functions repetirá essa função até cinco vezes para uma determinada mensagem da fila, incluindo a primeira tentativa. Se todas as cinco tentativas falharem, o tempo de execução das funções adicionará uma mensagem em um armazenamento de filas chamada *&lt;originalqueuename>-poison*. Você pode gravar uma função para processar as mensagens da fila de mensagens suspeitas registrando-as ou enviando uma notificação de que a atenção manual é necessária. 

Para tratar mensagens suspeitas manualmente, verifique o `dequeueCount` da mensagem da fila (veja [Metadados de gatilho de fila](#meta)).

<a name="output"></a>

## <a name="queue-storage-output-binding"></a>Associação de saída de armazenamento de filas
A associação de saída do armazenamento de filas do Azure permite que você escreva mensagens em uma fila. 

Defina uma associação de saída de fila usando a guia **Integrar** no portal do Functions. O portal cria a seguinte definição na seção **Ligações** de *function.json*:

```json
{
   "type": "queue",
   "direction": "out",
   "name": "<The name used to identify the trigger data in your code>",
   "queueName": "<Name of queue to write to>",
   "connection":"<Name of app setting - see below>"
}
```

* A propriedade `connection` deve conter o nome de uma configuração de aplicativo que contenha uma cadeia de conexão de armazenamento. No Portal do Azure, o editor padrão na guia **Integrar** define essa configuração de aplicativo para você ao selecionar uma conta de armazenamento.

<a name="outputusage"></a>

## <a name="using-a-queue-output-binding"></a>Usando uma associação de saída da fila
Nas funções do Node.js, você acessa a fila de saída usando `context.bindings.<name>`.

Em funções do .NET, é possível executar a saída para qualquer um dos seguintes tipos. Quando há um parâmetro de tipo `T`, o `T` deve ser um dos tipos de saída com suporte, como `string` ou um POCO.

* `out T` (serializado como JSON)
* `out string`
* `out byte[]`
* `out` [`CloudQueueMessage`] 
* `ICollector<T>`
* `IAsyncCollector<T>`
* [`CloudQueue`](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

Você também pode usar o tipo de retorno do método como a associação de saída.

<a name="outputsample"></a>

## <a name="queue-output-sample"></a>Amostra de saída da fila
O *function.json* a seguir define um gatilho de HTTP com uma associação de saída de fila:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionString",
    }
  ]
}
``` 

Veja a amostra específica do idioma que gera uma mensagem da fila com o conteúdo de HTTP de entrada.

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="queue-output-sample-in-c"></a>Amostra de saída da fila em C# #

```cs
// C# example of HTTP trigger binding to a custom POCO, with a queue output binding
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, TraceWriter log)
{
    return input;
}
```

Para enviar várias mensagens, use um `ICollector`:

```cs
public static void Run(CustomQueueMessage input, ICollector<CustomQueueMessage> myQueueItem, TraceWriter log)
{
    myQueueItem.Add(input);
    myQueueItem.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

<a name="outnodejs"></a>

### <a name="queue-output-sample-in-nodejs"></a>Amostra de saída de fila em Node.js

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Ou, para enviar várias mensagens,

```javascript
module.exports = function(context) {
    // Define a message array for the myQueueItem output binding. 
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="next-steps"></a>Próximas etapas

Para obter um exemplo de uma função que usa gatilhos de armazenamento de fila e associações, veja [Criar um Azure Function conectado a um serviço do Azure](functions-create-an-azure-connected-function.md).

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

<!-- LINKS -->

[`CloudQueueMessage`]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage
