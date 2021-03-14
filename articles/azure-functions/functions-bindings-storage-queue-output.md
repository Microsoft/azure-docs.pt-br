---
title: Associação de saída do armazenamento de filas do Azure para Azure Functions
description: Saiba como criar mensagens de armazenamento de filas do Azure no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: 5d94625e3eb121e556b28038cf59626be1332966
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455798"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Associações de saída do armazenamento de filas do Azure para Azure Functions

Azure Functions pode criar novas mensagens de armazenamento de fila do Azure Configurando uma associação de saída.

Para obter informações sobre a instalação e detalhes de configuração, confira a [visão geral](./functions-bindings-storage-queue.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que cria uma mensagem da fila para cada solicitação HTTP recebida.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho de HTTP em um arquivo *function.json* e código [script C# (.csx)](functions-reference-csharp.md) que usa a associação. A função cria um item de fila com um conteúdo de objeto **CustomQueueMessage** para cada solicitação HTTP recebida.

Aqui está o arquivo *function.json*:

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
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código script C# que cria uma mensagem de fila única:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

Você pode enviar várias mensagens ao mesmo tempo usando um parâmetro `ICollector` ou `IAsyncCollector`. Aqui está o código de script C# que envia várias mensagens, uma com os dados da solicitação HTTP e outra com valores codificados:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="java"></a>[Java](#tab/java)

 O exemplo a seguir mostra uma função Java que cria uma mensagem de fila para quando disparada por uma solicitação HTTP.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

No [biblioteca de runtime de funções Java](/java/api/overview/azure/functions/runtime), use o `@QueueOutput` anotação em parâmetros cujo valor seria gravado no armazenamento de fila.  O tipo de parâmetro deve ser `OutputBinding<T>` , em que `T` é qualquer tipo de Java nativo de um POJO.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de gatilho HTTP em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função cria um item da fila para cada solicitação HTTP recebida.

Aqui está o arquivo *function.json*:

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
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "myQueueItem",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

Você pode enviar várias mensagens de uma vez com a definição de uma matriz de mensagem para a `myQueueItem` associação de saída. O código JavaScript a seguir envia duas mensagens de fila com valores codificados para cada solicitação HTTP recebida.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Os exemplos de código a seguir demonstram como produzir uma mensagem de fila de uma função disparada por HTTP. A seção de configuração com o `type` de `queue` define a associação de saída.

```json
{
  "bindings": [
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
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "Msg",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Usando essa configuração de associação, uma função do PowerShell pode criar uma mensagem de fila usando `Push-OutputBinding` . Neste exemplo, uma mensagem é criada a partir de um parâmetro de corpo ou cadeia de caracteres de consulta.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

Para enviar várias mensagens de uma vez, defina uma matriz de mensagens e use `Push-OutputBinding` para enviar mensagens para a associação de saída da fila.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = @("message1", "message2")
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra como gerar valores únicos e múltiplos para filas de armazenamento. A configuração necessária para *function.jsno* é a mesma de qualquer forma.

Uma associação de fila de armazenamento é definida em *function.jsem* que o *tipo* está definido como `queue` .

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
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Para definir uma mensagem individual na fila, você passa um único valor para o `set` método.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Para criar várias mensagens na fila, declare um parâmetro como o tipo de lista apropriado e passe uma matriz de valores (que correspondam ao tipo de lista) para o `set` método.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

O atributo se aplica a um `out` parâmetro ou o valor de retorno da função. O construtor do atributo usa o nome da fila, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Você pode definir a `Connection` propriedade para especificar a conta de armazenamento para usar, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Para obter um exemplo completo, consulte [exemplo de saída](#example).

Você pode usar o `StorageAccount` atributo para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, confira Gatilho – atributos.

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O script C# não dá suporte a atributos.

# <a name="java"></a>[Java](#tab/java)

A `QueueOutput` anotação permite que você grave uma mensagem como a saída de uma função. O exemplo a seguir mostra uma função disparada por HTTP que cria uma mensagem de fila.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Propriedade    | Descrição |
|-------------|-----------------------------|
|`name`       | Declara o nome do parâmetro na assinatura da função. Quando a função é disparada, o valor desse parâmetro tem o conteúdo da mensagem da fila. |
|`queueName`  | Declara o nome da fila na conta de armazenamento. |
|`connection` | Aponta para a cadeia de conexão da conta de armazenamento. |

O parâmetro associado à `QueueOutput` anotação é digitado como uma instância de [saídabinding \<T\> ](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript não dá suporte a atributos.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Não há suporte para atributos pelo PowerShell.

# <a name="python"></a>[Python](#tab/python)

O Python não dá suporte a atributos.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `Queue`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `queue`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | n/d | Deve ser definido como `out`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | n/d | O nome da variável que representa a fila no código de função. Definido como `$return` para referenciar o valor de retorno da função.|
|**queueName** |**QueueName** | O nome da fila. |
|**connection** | **Conexão** |O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui.<br><br>Por exemplo, se você definir `connection` como "mystorage", o tempo de execução do Functions procurará uma configuração de aplicativo chamada "Mystorage". Se você deixar `connection` vazio, o runtime de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.<br><br>Se você estiver usando [a versão 5. x ou superior da extensão](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher), em vez de uma cadeia de conexão, você pode fornecer uma referência a uma seção de configuração que define a conexão. Consulte [conexões](./functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Padrão

Grave uma mensagem de fila única usando um parâmetro de método, como `out T paramName` . Você pode usar o tipo de retorno de método em vez de um `out` parâmetro, e `T` pode ser qualquer um dos seguintes tipos:

* Um objeto serializado como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Se você tentar associar `CloudQueueMessage` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

Em C# e script C#, grave várias mensagens de fila usando um dos seguintes tipos: 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>Tipos adicionais

Os aplicativos que usam a [versão 5.0.0 ou posterior da extensão de Armazenamento](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) também poderão usar tipos do [SDK do Azure para .NET](/dotnet/api/overview/azure/storage.queues-readme). Essa versão descarta o suporte para os `CloudQueue` tipos herdados e `CloudQueueMessage` em favor dos seguintes tipos:

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- [QueueClient](/dotnet/api/azure.storage.queues.queueclient) para gravar várias mensagens da fila

Para obter exemplos de como usar esses tipos, confira [o repositório GitHub da extensão](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

### <a name="default"></a>Padrão

Grave uma mensagem de fila única usando um parâmetro de método, como `out T paramName` . O `paramName` é o valor especificado na `name` propriedade de *function.jsem*. Você pode usar o tipo de retorno de método em vez de um `out` parâmetro, e `T` pode ser qualquer um dos seguintes tipos:

* Um objeto serializado como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Se você tentar associar `CloudQueueMessage` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

Em C# e script C#, grave várias mensagens de fila usando um dos seguintes tipos: 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>Tipos adicionais

Os aplicativos que usam a [versão 5.0.0 ou posterior da extensão de Armazenamento](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) também poderão usar tipos do [SDK do Azure para .NET](/dotnet/api/overview/azure/storage.queues-readme). Essa versão descarta o suporte para os `CloudQueue` tipos herdados e `CloudQueueMessage` em favor dos seguintes tipos:

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- [QueueClient](/dotnet/api/azure.storage.queues.queueclient) para gravar várias mensagens da fila

Para obter exemplos de como usar esses tipos, confira [o repositório GitHub da extensão](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples).

# <a name="java"></a>[Java](#tab/java)

Há duas opções para a saída de uma mensagem de fila de uma função usando a anotação [QueueOutput](/java/api/com.microsoft.azure.functions.annotation.queueoutput) :

- **Valor de retorno**: aplicando a anotação à própria função, o valor de retorno da função é persistido como uma mensagem da fila.

- **Imperativo**: para definir o valor da mensagem de modo explícito, aplique a anotação a um parâmetro específico do tipo [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding), em que `T` será um POJO ou um tipo de Java nativo. Com essa configuração, passar um valor para o `setValue` método persiste o valor como uma mensagem da fila.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O item fila de saída está disponível por meio `context.bindings.<NAME>` `<NAME>` de onde corresponde ao nome definido no *function.jsem*. Você pode usar uma cadeia de caracteres ou um objeto serializável em JSON para o conteúdo de item de fila.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A saída para a mensagem da fila está disponível por meio de `Push-OutputBinding` onde você passa argumentos que correspondem ao nome designado pelo parâmetro da Associação `name` na *function.jsno* arquivo.

# <a name="python"></a>[Python](#tab/python)

Há duas opções para a saída de uma mensagem de fila de uma função:

- **Valor retornado**: defina a propriedade `name` no *function.json* como `$return`. Com essa configuração, o valor de retorno da função é persistido como uma mensagem de armazenamento de fila.

- **Imperativo**: passe um valor para [definir](/python/api/azure-functions/azure.functions.out#set-val--t-----none) o método do parâmetro declarado como um tipo de [Saída](/python/api/azure-functions/azure.functions.out). O valor passado para `set` é persistido como uma mensagem de armazenamento de fila.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Associação |  Referência |
|---|---|
| Fila | [Fila de códigos de erro](/rest/api/storageservices/queue-service-error-codes) |
| Blob, tabela, fila | [Códigos de erro de armazenamento](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, tabela, fila |  [Solução de problemas](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Próximas etapas

- [Executar uma função como armazenamento de fila alterações de dados (gatilho)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
