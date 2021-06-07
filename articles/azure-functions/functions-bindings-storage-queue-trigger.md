---
title: Gatilho de armazenamento de filas do Azure para Azure Functions
description: Saiba como executar uma função do Azure como alterações de dados do armazenamento de filas do Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: f4477a09f151695b826d0becf28e92ceaf3f9e85
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453199"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>Gatilho de armazenamento de filas do Azure para Azure Functions

O gatilho de armazenamento de filas executa uma função à medida que as mensagens são adicionadas ao armazenamento de filas do Azure.

## <a name="encoding"></a>Codificação

O Functions espera uma cadeia de caracteres codificada *base64*. Todos os ajustes ao tipo de codificação (para preparar os dados como uma cadeia de caracteres codificada *base64*) precisam ser implementados no serviço de chamada.

## <a name="example"></a>Exemplo

Use o gatilho de fila para iniciar uma função quando um novo item é recebido em uma fila. A mensagem da fila é fornecida como entrada para a função.

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

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

A seção [configuração](#configuration) explica essas propriedades.

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

A seção [uso](#usage) explica `myQueueItem`, que é chamado pela `name` propriedade function.json.  A [seção de metadados de mensagem](#message-metadata) explica todas as outras variáveis mostradas.

# <a name="java"></a>[Java](#tab/java)

O exemplo de Java a seguir mostra uma função de gatilho de fila de armazenamento, que registra a mensagem disparada colocada na fila `myqueuename` .

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

A seção [configuração](#configuration) explica essas propriedades.

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

A seção [uso](#usage) explica `myQueueItem`, que é chamado pela `name` propriedade function.json.  A [seção de metadados de mensagem](#message-metadata) explica todas as outras variáveis mostradas.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir demonstra como ler uma mensagem de fila passada para uma função por meio de um gatilho.

Um gatilho de fila de armazenamento é definido em *function.jsno* arquivo em que `type` está definido como `queueTrigger` .

```json
{
  "bindings": [
    {
      "name": "QueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

O código no arquivo de *Run.ps1* declara um parâmetro como `$QueueItem` , que permite que você leia a mensagem da fila em sua função.

```powershell
# Input bindings are passed in via param block.
param([string] $QueueItem, $TriggerMetadata)

# Write out the queue message and metadata to the information log.
Write-Host "PowerShell queue trigger function processed work item: $QueueItem"
Write-Host "Queue item expiration time: $($TriggerMetadata.ExpirationTime)"
Write-Host "Queue item insertion time: $($TriggerMetadata.InsertionTime)"
Write-Host "Queue item next visible time: $($TriggerMetadata.NextVisibleTime)"
Write-Host "ID: $($TriggerMetadata.Id)"
Write-Host "Pop receipt: $($TriggerMetadata.PopReceipt)"
Write-Host "Dequeue count: $($TriggerMetadata.DequeueCount)"
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra como ler uma mensagem de fila passada para uma função por meio de um gatilho.

Um gatilho de fila de armazenamento é definido em *function.jsem* que o *tipo* está definido como `queueTrigger` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

O código *_\_ init_ \_ . py* declara um parâmetro como `func.QueueMessage` , que permite que você leia a mensagem da fila em sua função.

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

 ---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Em [bibliotecas de classes C#](functions-dotnet-class-library.md), use os seguintes atributos para configurar um gatilho de fila:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

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

  Você pode definir a `Connection` propriedade para especificar a configuração do aplicativo que contém a cadeia de conexão da conta de armazenamento a ser usada, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Para ver um exemplo completo, confira o [exemplo](#example).

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

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O script C# não dá suporte a atributos.

# <a name="java"></a>[Java](#tab/java)

A `QueueTrigger` anotação fornece acesso à fila que dispara a função. O exemplo a seguir torna a mensagem da fila disponível para a função por meio do `message` parâmetro.

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| Propriedade    | Descrição |
|-------------|-----------------------------|
|`name`       | Declara o nome do parâmetro na assinatura da função. Quando a função é disparada, o valor desse parâmetro tem o conteúdo da mensagem da fila. |
|`queueName`  | Declara o nome da fila na conta de armazenamento. |
|`connection` | Aponta para a cadeia de conexão da conta de armazenamento. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript não dá suporte a atributos.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Não há suporte para atributos pelo PowerShell.

# <a name="python"></a>[Python](#tab/python)

O Python não dá suporte a atributos.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `QueueTrigger`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d| Deve ser definido como `queueTrigger`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction**| n/d | Apenas no arquivo *function.json*. Deve ser definido como `in`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | n/d |O nome da variável que contém o conteúdo do item de fila no código da função.  |
|**queueName** | **QueueName**| O nome da fila a ser controlada. |
|**connection** | **Conexão** |O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui.<br><br>Por exemplo, se você definir `connection` como "mystorage", o tempo de execução do Functions procurará uma configuração de aplicativo chamada "Mystorage". Se você deixar `connection` vazio, o runtime de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.<br><br>Caso esteja usando a [versão 5.x ou posterior da extensão](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher), em vez de uma cadeia de conexão, será possível fornecer uma referência a uma seção de configuração que definirá a conexão. Confira a opção [Conexões](./functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Padrão

Acesse os dados da mensagem usando um parâmetro de método, como `string paramName` . É possível associar a qualquer um dos seguintes tipos:

* Objeto - o runtime do Functions desserializa um conteúdo JSON em uma instância de uma classe arbitrária definida em seu código.
* `string`
* `byte[]`
* [CloudQueueMessage]

Se você tentar associar `CloudQueueMessage` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

### <a name="additional-types"></a>Tipos adicionais

Os aplicativos que usam a [versão 5.0.0 ou posterior da extensão de Armazenamento](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) também poderão usar tipos do [SDK do Azure para .NET](/dotnet/api/overview/azure/storage.queues-readme). Essa versão removerá o suporte do tipo `CloudQueueMessage` herdado para beneficiar os seguintes tipos:

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
 
Para obter exemplos de como usar esses tipos, confira [o repositório GitHub da extensão](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

### <a name="default"></a>Padrão

Acesse os dados da mensagem usando um parâmetro de método, como `string paramName` . O `paramName` é o valor especificado na `name` propriedade de *function.jsem*. É possível associar a qualquer um dos seguintes tipos:

* Objeto - o runtime do Functions desserializa um conteúdo JSON em uma instância de uma classe arbitrária definida em seu código. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Se você tentar associar `CloudQueueMessage` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

### <a name="additional-types"></a>Tipos adicionais

Os aplicativos que usam a [versão 5.0.0 ou posterior da extensão de Armazenamento](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) também poderão usar tipos do [SDK do Azure para .NET](/dotnet/api/overview/azure/storage.queues-readme). Essa versão removerá o suporte do tipo `CloudQueueMessage` herdado para beneficiar os seguintes tipos:

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)

Para obter exemplos de como usar esses tipos, confira [o repositório GitHub da extensão](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples).

# <a name="java"></a>[Java](#tab/java)

A anotação [QueueTrigger](/java/api/com.microsoft.azure.functions.annotation.queuetrigger) fornece acesso à mensagem da fila que disparou a função.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A carga do item de fila está disponível por meio `context.bindings.<NAME>` `<NAME>` de onde corresponde ao nome definido no *function.jsem*. Se a carga for JSON, o valor será desserializado em um objeto.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Acesse a mensagem da fila por meio do parâmetro de cadeia de caracteres que corresponde ao nome designado pelo parâmetro da Associação `name` na *function.jsno* arquivo.

# <a name="python"></a>[Python](#tab/python)

Acesse a mensagem da fila por meio do parâmetro digitado como [QueueMessage](/python/api/azure-functions/azure.functions.queuemessage).

---

## <a name="message-metadata"></a>Metadados da mensagem

O gatilho de fila fornece várias propriedades de [metadados](./functions-bindings-expressions-patterns.md#trigger-metadata). Essas propriedades podem ser usadas como parte de expressões de associação em outras associações ou como parâmetros em seu código. As propriedades são membros da classe [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) .

|Propriedade|Type|Descrição|
|--------|----|-----------|
|`QueueTrigger`|`string`|Conteúdo da fila (se for uma cadeia de caracteres válida). Se a carga da mensagem da fila for uma cadeia de caracteres, `QueueTrigger` o terá o mesmo valor que a variável nomeada pela `name` propriedade em *function.js*.|
|`DequeueCount`|`int`|O número de vezes que essa mensagem foi removida da fila.|
|`ExpirationTime`|`DateTimeOffset`|A hora em que a mensagem expira.|
|`Id`|`string`|ID da mensagem da fila.|
|`InsertionTime`|`DateTimeOffset`|A hora em que a mensagem foi adicionada à fila.|
|`NextVisibleTime`|`DateTimeOffset`|A hora em que a mensagem estará visível.|
|`PopReceipt`|`string`|Recebimento pop da mensagem.|

## <a name="poison-messages"></a>Mensagens suspeitas

Quando uma função do gatilho de fila falhar, o Azure Functions repetirá essa função até cinco vezes para uma determinada mensagem da fila, incluindo a primeira tentativa. Se todas as cinco tentativas falharem, o tempo de execução do Functions adicionará uma mensagem a uma fila chamada *&lt; originalqueuename>-suspeita*. Você pode gravar uma função para processar as mensagens da fila de mensagens suspeitas registrando-as ou enviando uma notificação de que a atenção manual é necessária.

Para tratar mensagens suspeitas manualmente, verifique o [dequeueCount](#message-metadata) da mensagem de fila.

## <a name="polling-algorithm"></a>Algoritmo de sondagem

O gatilho de fila implementa um algoritmo exponencial aleatório de retirada para reduzir o efeito de sondagem de fila ociosa nos custos das transações de armazenamento.

O algoritmo usa a seguinte lógica:

- Quando uma mensagem é encontrada, o tempo de execução aguarda dois segundos e, em seguida, verifica se há outra mensagem
- Quando nenhuma mensagem é encontrada, ela aguarda cerca de quatro segundos antes de tentar novamente.
- Após subsequentes tentativas falhas para obter uma mensagem da fila, o tempo de espera continua a aumentar até atingir o tempo de espera máximo, cujo padrão é um minuto.
- O tempo de espera máximo é configurável por meio da propriedade `maxPollingInterval` no [arquivo host.json](functions-host-json-v1.md#queues).

Para o desenvolvimento local, o intervalo máximo de sondagem tem como padrão dois segundos.

Em relação à cobrança, o tempo gasto na sondagem pelo tempo de execução é "gratuito" e não é contado em relação à sua conta.

## <a name="concurrency"></a>Simultaneidade

Quando há várias mensagens de fila aguardando, o gatilho de fila recupera um lote de mensagens e invoca as instâncias de função ao mesmo tempo para processá-las. Por padrão, o tamanho do lote é 16. Quando o número que está sendo processado chega até 8, o runtime obtém outro lote e começa a processar as mensagens. Portanto, o número máximo de mensagens simultâneas que estão sendo processadas por função em uma máquina virtual (VM) é 24. Esse limite se aplica separadamente a cada função acionada por fila em cada VM. Se aplicativo de função for escalado horizontalmente para várias VMs, cada VM aguardará gatilhos e tentará executar funções. Por exemplo, se um aplicativo de função for escalado horizontalmente para 3 VMs, o número de máximo padrão de instâncias simultâneas de uma função acionada por fila será 72.

O tamanho do lote e o limite para obtenção de um novo lote são configuráveis no [arquivo host.json](functions-host-json.md#queues). Se quiser minimizar a execução paralela para funções acionadas por fila em um aplicativo de função, você poder definir o tamanho do lote para 1. Essa configuração elimina a simultaneidade, desde que seu aplicativo de função seja executado em uma única máquina virtual (VM). 

O gatilho de fila impede automaticamente que uma função processe uma mensagem da fila várias vezes simultaneamente.

## <a name="hostjson-properties"></a>Propriedades de host.json

O arquivo [host.json](functions-host-json.md#queues) contém configurações que controlam o comportamento de gatilho de fila. Consulte a [host.jsna seção Configurações](functions-bindings-storage-queue.md#hostjson-settings) para obter detalhes sobre as configurações disponíveis.

## <a name="next-steps"></a>Próximas etapas

- [Gravar mensagens de armazenamento de fila (Associação de saída)](./functions-bindings-storage-queue-output.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
