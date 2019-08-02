---
title: Associações de armazenamento de filas do Azure Functions
description: Entenda como usar o gatilho do armazenamento de fila do Azure e a associação de saída no Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 9604ef276625d1fcc9164a9b75b94ebc22cb51e1
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480148"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Associações de armazenamento de filas do Azure Functions

Este artigo explica como trabalhar com associações de armazenamento de Fila do Azure no Azure Functions. O Azure Functions dá suporte a associações de gatilho e de saída para filas.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - Functions 1. x

As associações de armazenamento Filas são fornecidas no pacote NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), versão 2.x. O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Pacotes - Functions 2. x

As associações de Armazenamento de Filas são fornecidas no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage), versão 3.x. O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="encoding"></a>Codificação
O Functions espera uma cadeia de caracteres codificada *base64*. Todos os ajustes ao tipo de codificação (para preparar os dados como uma cadeia de caracteres codificada *base64*) precisam ser implementados no serviço de chamada.

## <a name="trigger"></a>Gatilho

Use o gatilho de fila para iniciar uma função quando um novo item é recebido em uma fila. A mensagem da fila é fornecida como entrada para a função.

## <a name="trigger---example"></a>Gatilho - exemplo

Consulte o exemplo específico a um idioma:

* [C#](#trigger---c-example)
* [Script do C# (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

### <a name="trigger---c-example"></a>Gatilho - exemplo C#

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que consulta a fila `myqueue-items` e grava um log cada vez que um item de fila é processado.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

### <a name="trigger---c-script-example"></a>Gatilho - exemplo de script C#

O exemplo a seguir mostra uma associação de gatilho de fila em um arquivo *function.json* e código [script C# (.csx)](functions-reference-csharp.md) que usa a associação. A função controla a `myqueue-items` fila e grava um log cada vez que um item de fila é processado.

Aqui está o arquivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

Aqui está o código de script do C#:

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
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
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

A seção [uso](#trigger---usage) explica `myQueueItem`, que é chamado pela `name` propriedade function.json.  A [seção de metadados de mensagem](#trigger---message-metadata) explica todas as outras variáveis mostradas.

### <a name="trigger---javascript-example"></a>Gatilho - exemplo de JavaScript

O exemplo a seguir mostra uma associação de gatilho de fila em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função controla a `myqueue-items` fila e grava um log cada vez que um item de fila é processado.

Aqui está o arquivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

> [!NOTE]
> O parâmetro nome é refletido como `context.bindings.<name>` no código JavaScript que contém a carga útil do item da fila. Essa carga útil também é passada como o segundo parâmetro para a função.

Aqui está o código JavaScript:

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

A seção [uso](#trigger---usage) explica `myQueueItem`, que é chamado pela `name` propriedade function.json.  A [seção de metadados de mensagem](#trigger---message-metadata) explica todas as outras variáveis mostradas.

### <a name="trigger---java-example"></a>Gatilho - exemplo Java

O exemplo Java a seguir mostra as funções do acionador da fila de armazenamento que registra a mensagem acionada colocada na fila `myqueuename`.

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

## <a name="trigger---attributes"></a>Gatilho – atributos

Em [bibliotecas de classes C#](functions-dotnet-class-library.md), use os seguintes atributos para configurar um gatilho de fila:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs)

  O construtor do atributo usa o nome da fila para monitorar, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Você pode definir a `Connection` propriedade para especificar a conta de armazenamento para usar, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Para ver um exemplo completo, consulte [Gatilho – exemplo de C#](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Oferece uma maneira de especificar a conta de armazenamento para usar. O construtor toma o nome de uma configuração de aplicativo que contenha uma cadeia de conexão de armazenamento. O atributo pode ser aplicado no nível de classe, método ou parâmetro. O exemplo a seguir mostra o nível de classe e método:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

A conta de armazenamento a ser usada é determinada na seguinte ordem:

* A propriedade `QueueTrigger` do atributo`Connection`.
* O `StorageAccount` atributo aplicado ao mesmo parâmetro do `QueueTrigger` atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` atributo aplicado à classe.
* A configuração do aplicativo "AzureWebJobsStorage".

## <a name="trigger---configuration"></a>Gatilho – configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `QueueTrigger`.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**type** | n/d| Deve ser definido como `queueTrigger`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction**| n/d | Apenas no arquivo *function.json*. Deve ser definido como `in`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | n/d |O nome da variável que contém o conteúdo do item de fila no código da função.  |
|**queueName** | **QueueName**| O nome da fila a ser controlada. |
|**conexão** | **Conexão** |O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se você configurar `connection` para “MyStorage”, o tempo de execução do Functions procura por uma configuração de aplicativo que esteja nomeada “AzureWebJobsMyStorage." Se você deixar `connection` vazio, o tempo de execução de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Gatilho - uso

Em C# e script C#, acesse os dados da mensagem usando um parâmetro de método, como `string paramName`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. É possível associar a qualquer um dos seguintes tipos:

* Objeto - o tempo de execução do Functions desserializa um conteúdo JSON em uma instância de uma classe arbitrária definida em seu código. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Se você tentar associar `CloudQueueMessage` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](#azure-storage-sdk-version-in-functions-1x).

Em JavaScript, use `context.bindings.<name>` para acessar o conteúdo de item de fila. Se o conteúdo for JSON, ele é desserializado em um objeto.

## <a name="trigger---message-metadata"></a>Gatilho - metadados da mensagem

O gatilho de fila fornece várias propriedades de [metadados](./functions-bindings-expressions-patterns.md#trigger-metadata). Essas propriedades podem ser usadas como parte de expressões de associação em outras associações ou como parâmetros em seu código. Essas são propriedades da classe [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage).

|Propriedade|Type|DESCRIÇÃO|
|--------|----|-----------|
|`QueueTrigger`|`string`|Conteúdo da fila (se for uma cadeia de caracteres válida). Se o conteúdo de mensagem de fila como uma cadeia de caracteres `QueueTrigger` tem o mesmo valor da variável nomeada pela `name` propriedade em *function.json*.|
|`DequeueCount`|`int`|O número de vezes que essa mensagem foi removida da fila.|
|`ExpirationTime`|`DateTimeOffset`|A hora em que a mensagem expira.|
|`Id`|`string`|ID da mensagem da fila.|
|`InsertionTime`|`DateTimeOffset`|A hora em que a mensagem foi adicionada à fila.|
|`NextVisibleTime`|`DateTimeOffset`|A hora em que a mensagem estará visível.|
|`PopReceipt`|`string`|Recebimento pop da mensagem.|

## <a name="trigger---poison-messages"></a>Gatilho - mensagens suspeitas

Quando uma função do gatilho de fila falhar, o Azure Functions repetirá essa função até cinco vezes para uma determinada mensagem da fila, incluindo a primeira tentativa. Se todas as cinco tentativas falharem, o tempo de execução das funções adicionará uma mensagem em uma fila chamada *&lt;originalqueuename>-poison*. Você pode gravar uma função para processar as mensagens da fila de mensagens suspeitas registrando-as ou enviando uma notificação de que a atenção manual é necessária.

Para tratar mensagens suspeitas manualmente, verifique o [dequeueCount](#trigger---message-metadata) da mensagem de fila.

## <a name="trigger---polling-algorithm"></a>Gatilho - algoritmo de sondagem

O gatilho de fila implementa um algoritmo exponencial aleatório de retirada para reduzir o efeito de sondagem de fila ociosa nos custos das transações de armazenamento.  Quando uma mensagem for encontrada, o tempo de execução aguarda dois segundos e, em seguida, verifica outra mensagem; quando nenhuma mensagem for encontrada, ele aguarda cerca de quatro segundos antes de tentar novamente. Após subsequentes tentativas falhas para obter uma mensagem da fila, o tempo de espera continua a aumentar até atingir o tempo de espera máximo, cujo padrão é um minuto. O tempo de espera máximo é configurável por meio da propriedade `maxPollingInterval` no [arquivo host.json](functions-host-json.md#queues).

## <a name="trigger---concurrency"></a>Gatilho - simultaneidade

Quando há várias mensagens de fila aguardando, o gatilho de fila recupera um lote de mensagens e invoca as instâncias de função ao mesmo tempo para processá-las. Por padrão, o tamanho do lote é 16. Quando o número que está sendo processado chega até 8, o tempo de execução obtém outro lote e começa a processar as mensagens. Portanto, o número máximo de mensagens simultâneas que estão sendo processadas por função em uma máquina virtual (VM) é 24. Esse limite se aplica separadamente a cada função acionada por fila em cada VM. Se aplicativo de função for escalado horizontalmente para várias VMs, cada VM aguardará gatilhos e tentará executar funções. Por exemplo, se um aplicativo de função for escalado horizontalmente para 3 VMs, o número de máximo padrão de instâncias simultâneas de uma função acionada por fila será 72.

O tamanho do lote e o limite para obtenção de um novo lote são configuráveis no [arquivo host.json](functions-host-json.md#queues). Se quiser minimizar a execução paralela para funções acionadas por fila em um aplicativo de função, você poder definir o tamanho do lote para 1. Essa configuração elimina a simultaneidade, desde que seu aplicativo de função seja executado em uma única máquina virtual (VM). 

O gatilho de fila impede automaticamente que uma função processe uma mensagem da fila várias vezes; as funções não precisam ser escritas para ser idempotentes.

## <a name="trigger---hostjson-properties"></a>Gatilho - propriedades de host.json

O arquivo [host.json](functions-host-json.md#queues) contém configurações que controlam o comportamento de gatilho de fila. Consulte a [configurações de host. JSON](#hostjson-settings) seção para obter detalhes sobre as configurações disponíveis.

## <a name="output"></a>Output

Use a associação de saída do armazenamento de Filas do Azure para que você grave mensagens em uma fila.

## <a name="output---example"></a>Saída - exemplo

Consulte o exemplo específico a um idioma:

* [C#](#output---c-example)
* [Script do C# (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)

### <a name="output---c-example"></a>Saída - exemplo C#

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que cria uma mensagem da fila para cada solicitação HTTP recebida.

```csharp
[StorageAccount("AzureWebJobsStorage")]
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

### <a name="output---c-script-example"></a>Saída - exemplo de script C#

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
      "name": "return"
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

A seção [configuração](#output---configuration) explica essas propriedades.

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

### <a name="output---javascript-example"></a>Saída - exemplo JavaScript

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
      "name": "return"
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

A seção [configuração](#output---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Você pode enviar várias mensagens de uma vez com a definição de uma matriz de mensagem para a `myQueueItem` associação de saída. O código JavaScript a seguir envia duas mensagens de fila com valores codificados para cada solicitação HTTP recebida.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

### <a name="output---java-example"></a>Saída - exemplo de Java

 O exemplo a seguir mostra uma função Java que cria uma mensagem de fila para quando acionada por uma solicitação HTTP.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "AzureWebJobsStorage")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding&lt;String&gt; result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

No [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime), use o `@QueueOutput` anotação em parâmetros cujo valor seria gravado no armazenamento de fila.  O tipo de parâmetro deve ser `OutputBinding<T>`, onde T é qualquer tipo Java nativo de um POJO.


## <a name="output---attributes"></a>Saída - atributos

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs).

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

Para ver um exemplo completo, consulte [Saída – exemplo de C#](#output---c-example).

Você pode usar o `StorageAccount` atributo para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, confira Gatilho – atributos.

## <a name="output---configuration"></a>Saída - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `Queue` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**type** | n/d | Deve ser definido como `queue`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | n/d | Deve ser definido como `out`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | n/d | O nome da variável que representa a fila no código de função. Definido como `$return` para referenciar o valor de retorno da função.|
|**queueName** |**QueueName** | O nome da fila. |
|**conexão** | **Conexão** |O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se você configurar `connection` para “MyStorage”, o tempo de execução do Functions procura por uma configuração de aplicativo que esteja nomeada “AzureWebJobsMyStorage." Se você deixar `connection` vazio, o tempo de execução de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - uso

Em C# e script C#, grave uma mensagem de fila única usando um parâmetro de método como o `out T paramName`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. Você pode usar o tipo de retorno de método em vez de um `out` parâmetro, e `T` pode ser qualquer um dos seguintes tipos:

* Um objeto serializado como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Se você tentar associar `CloudQueueMessage` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](#azure-storage-sdk-version-in-functions-1x).

Em C# e script C#, grave várias mensagens de fila usando um dos seguintes tipos: 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

Em funções em JavaScript, use `context.bindings.<name>` para acessar a mensagem de fila de saída. Você pode usar uma cadeia de caracteres ou um objeto serializável em JSON para o conteúdo de item de fila.


## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Associação |  Referência |
|---|---|
| Fila | [Fila de códigos de erro](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Blob, tabela, fila | [Códigos de erro de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, tabela, fila |  [Solução de problemas](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>configurações de host.json

Esta seção descreve as definições de configuração globais disponíveis para esta associação na versão 2.x. O arquivo host.json de exemplo abaixo contém apenas as configurações da versão 2.x para essa associação. Para obter mais informações sobre as configurações globais na versão 2.x, confira a [referência de host.json para o Azure Functions versão 2.x](functions-host-json.md).

> [!NOTE]
> Para obter uma referência de host.json no Functions 1.x, confira [Referência de host.json para o Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```


|Propriedade  |Padrão | DESCRIÇÃO |
|---------|---------|---------|
|maxPollingInterval|00:00:01|O intervalo máximo entre as sondagens de fila. Mínimo é 00:00:00.100 (100 ms) e incrementa até 01: 00:00 (1 minuto). |
|visibilityTimeout|00:00:00|O intervalo de tempo entre as repetições quando o processamento de uma mensagem falha. |
|batchSize|16|O número de mensagens em fila que o tempo de execução de Funções recupera simultaneamente e processa em paralelo. Quando o número que está sendo processado chega até `newBatchThreshold`, o tempo de execução obtém outro lote e começa a processar as mensagens. Portanto, o número máximo de mensagens simultâneas que estão sendo processadas por função é `batchSize` mais `newBatchThreshold`. Esse limite se aplica separadamente a cada função acionada por fila. <br><br>Se quiser evitar uma execução paralela para mensagens recebidas em uma fila, é possível definir `batchSize` como 1. No entanto, essa configuração elimina a simultaneidade desde que seu aplicativo de função seja executado em uma única máquina virtual (VM). Se o aplicativo de função se expande para várias VMs, cada VM pode executar uma instância de cada função acionada por fila.<br><br>O máximo `batchSize` é 32. |
|maxDequeueCount|5|O número de vezes para tentar processar uma mensagem antes de movê-la para a fila de mensagens suspeitas.|
|newBatchThreshold|batchSize/2|Sempre que o número de mensagens processadas simultaneamente chega a esse número, o tempo de execução recupera outro lote.|

## <a name="next-steps"></a>Próximas etapas

* [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [Vá para um tutorial que usa uma associação de saída de armazenamento de Fila](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
