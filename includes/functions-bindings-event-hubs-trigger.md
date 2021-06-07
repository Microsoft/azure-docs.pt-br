---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 32f98eb9b98168bdab270ecff07446c31f8d706d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729888"
---
Use o gatilho de função para responder a um evento enviado para um fluxo de eventos do hub de eventos. Você precisa ter acesso de leitura ao hub de eventos subjacente para configurar o gatilho. Quando a função for disparada, a mensagem passada para a função será digitada como uma cadeia de caracteres.

## <a name="scaling"></a>Scaling

Cada instância de uma função disparada pelo evento tem suporte apenas de uma instância [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor). O gatilho (da plataforma Hubs de Eventos) garante que apenas uma instância [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) possa obter uma concessão em uma determinada partição.

Por exemplo, considere um Hub de eventos da seguinte maneira:

* Dez partições
* Mil eventos distribuídos uniformemente em todas as partições, com 100 mensagens em cada partição

Quando sua função é habilitada pela primeira vez, há apenas uma instância da função. Vamos chamar a primeira instância de função `Function_0`. A função `Function_0` tem uma instância de [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) que tem uma concessão para todas as dez partições. Esta instância está lendo eventos das partições 0 a 9. Deste ponto em diante, uma destas opções ocorre:

* **Novas instâncias de função não são necessárias**: `Function_0` pode processar os mil eventos antes de a lógica de escala do Functions entrar em vigor. Nesse caso, as mil mensagens são processadas pelo `Function_0`.

* **Uma instância de função adicional é adicionada**: se a lógica de escala do Functions determinar que `Function_0` tem mais mensagens do que pode processar, uma instância de aplicativo de funções (`Function_1`) será criada. Essa nova função também tem uma instância associada de [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor). Enquanto os Hubs de Eventos subjacentes detectam que uma nova instância de host está tentando ler mensagens, ela balanceia a carga das partições nas instâncias de host. Por exemplo, as partições de 0 a 4 podem ser atribuídas a `Function_0` e as partições de 5 a 9 para `Function_1`.

* **Mais N instâncias de função são adicionadas**: se a lógica de escala do Functions determinar que `Function_0` e `Function_1` têm mais mensagens do que podem processar, serão criadas instâncias de aplicativo de funções `Functions_N`.  Os aplicativos são criados até `N` ser maior que o número de partições do hub de eventos. Em nosso exemplo, os Hubs de Eventos balanceiam a carga das partições novamente e, nesse caso, entre as instâncias `Function_0` e `Functions_9`.

Quando ocorrer uma escala, `N` instâncias será um número maior que o número de partições do hub de eventos. Esse padrão é usado para verificar se as instâncias [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) estão disponíveis para obter bloqueios em partições conforme elas ficam disponíveis de outras instâncias. Você paga apenas pelos recursos usados quando a instância de função é executada. Em outras palavras, você não será cobrado por esse superprovisionamento.

Quando todas as execuções de função são concluídas (com ou sem erros), os pontos de verificação são adicionados à conta de armazenamento associada. Quando a verificação for bem-sucedida, nenhuma das mil mensagens será recuperada novamente.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](../articles/azure-functions/functions-dotnet-class-library.md) que registra o corpo da mensagem do gatilho dos hub de eventos.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Para obter acesso aos [metadados](#event-metadata) no código da função, associe um objeto de [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (requer o uso de uma instrução para `Microsoft.Azure.EventHubs`). Você também pode acessar as mesmas propriedades usando expressões de associação na assinatura do método.  O exemplo a seguir mostra duas formas de obter os mesmos dados:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

Para receber eventos em um lote, faça `string` ou `EventData` uma matriz.  

> [!NOTE]
> Ao receber em um lote, não é possível associar aos parâmetros do método, como no exemplo acima com `DateTime enqueuedTimeUtc`, e deve recebê-los de cada objeto `EventData`  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho de hub de eventos em um arquivo *function.json* e uma [função C# script](../articles/azure-functions/functions-reference-csharp.md) que usa a associação. A função registra em log o corpo da mensagem do gatilho de hub de eventos.

Os exemplos a seguir mostram dados de associação de Hubs de eventos no *function.json* arquivo.

### <a name="version-2x-and-higher"></a>Versão 2.x e posterior

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Versão 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Aqui está o código de script do C#:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Para obter acesso aos [metadados](#event-metadata) no código da função, associe um objeto de [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (requer o uso de uma instrução para `Microsoft.Azure.EventHubs`). Você também pode acessar as mesmas propriedades usando expressões de associação na assinatura do método.  O exemplo a seguir mostra duas formas de obter os mesmos dados:

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Para receber eventos em um lote, faça `string` ou `EventData` uma matriz:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de gatilho de hub de eventos em um arquivo *function.json* e uma [função JavaScript](../articles/azure-functions/functions-reference-node.md) que usa a associação. A função lê os [metadados de evento](#event-metadata) e registra a mensagem.

Os exemplos a seguir mostram dados de associação de Hubs de eventos no *function.json* arquivo.

### <a name="version-2x-and-higher"></a>Versão 2.x e posterior

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Versão 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Para receber eventos em um lote, defina `cardinality` para `many` no arquivo *function.json*, conforme mostrado nos exemplos a seguir.

### <a name="version-2x-and-higher"></a>Versão 2.x e posterior

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Versão 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);

    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma ligação de acionador do Hub de Eventos em um arquivo *function.json* e uma [função Python](../articles/azure-functions/functions-reference-python.md) que usa a ligação. A função lê os [metadados de evento](#event-metadata) e registra a mensagem.

Os exemplos a seguir mostram dados de associação de Hubs de eventos no *function.json* arquivo.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info(f'Function triggered to process a message: {event.get_body().decode()}')
    logging.info(f'  EnqueuedTimeUtc = {event.enqueued_time}')
    logging.info(f'  SequenceNumber = {event.sequence_number}')
    logging.info(f'  Offset = {event.offset}')

    # Metadata
    for key in event.metadata:
        logging.info(f'Metadata: {key} = {event.metadata[key]}')
```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra uma associação de gatilho do Hub de Eventos que registra o corpo da mensagem do gatilho do Hub de Eventos.

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
```

 No [biblioteca de runtime de funções Java](/java/api/overview/azure/functions/runtime), use o `EventHubTrigger` anotação em parâmetros cujo valor virá do Hub de eventos. Parâmetros com essas anotações fazem com que a função seja executada quando um evento é recebido.  Essa anotação pode ser usada com tipos nativos do Java, POJOs ou valores que permitem valor nulos usando `Optional<T>`.

 ---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Em [bibliotecas de classes de C#](../articles/azure-functions/functions-dotnet-class-library.md), utilize o atributo [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs).

O construtor do atributo usa os nomes do hub de eventos, do grupo de consumidores e de uma configuração de aplicativo que contenham a cadeia de caracteres de conexão. Para obter mais informações sobre essas configurações, consulte a [seção sobre configuração do gatilho](#configuration). Este é um `EventHubTriggerAttribute` exemplo de atributo:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Para ver um exemplo completo, consulte [Gatilho – exemplo de C#](#example).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O script C# não dá suporte a atributos.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript não dá suporte a atributos.

# <a name="python"></a>[Python](#tab/python)

O Python não dá suporte a atributos.

# <a name="java"></a>[Java](#tab/java)

Na [biblioteca de runtime de funções](/java/api/overview/azure/functions/runtime) do Java, use a anotação [EventHubTrigger](/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) em parâmetros cujo valor é proveniente do Hub de Eventos. Parâmetros com essas anotações fazem com que a função seja executada quando um evento é recebido. Essa anotação pode ser usada com tipos nativos do Java, POJOs ou valores que permitem valor nulos usando `Optional<T>`.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `EventHubTrigger`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `eventHubTrigger`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | n/d | Deve ser definido como `in`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | n/d | O nome da variável que representa o item de evento no código de função. |
|**path** |**EventHubName** | Funciona apenas 1. x. O nome do hub de eventos. Quando o nome do hub de eventos também estiver presente na cadeia de conexão, esse valor substitui essa propriedade em runtime. |
|**eventHubName** |**EventHubName** | Functions 2.x e posterior. O nome do hub de eventos. Quando o nome do hub de eventos também estiver presente na cadeia de conexão, esse valor substitui essa propriedade em runtime. Pode ser referenciado por meio das [configurações de aplicativo](../articles/azure-functions/functions-bindings-expressions-patterns.md#binding-expressions---app-settings) do `%eventHubName%` |
|**consumerGroup** |**ConsumerGroup** | É uma propriedade opcional que define o [grupo de consumidores ](../articles/event-hubs/event-hubs-features.md#event-consumers) usado para assinar eventos no hub. Se omitido, o grupo de consumidores `$Default` será usado. |
|**cardinalidade** | N/D | Usada para todas as linguagens diferentes de C#. Definido como `many` para habilitar o envio em lote.  Se for omitida ou definida como `one`, uma mensagem será passada para a função.<br><br>No C#, essa propriedade é automaticamente atribuída sempre que o gatilho tem uma matriz para o tipo.|
|**connection** |**Conexão** | É o nome de uma configuração de aplicativo que contém a cadeia de conexão para o namespace do hub de eventos. Copie essa cadeia de conexão clicando no botão **Informações de Conexão** do [namespace](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), não no próprio hub de eventos. Essa cadeia de conexão deve ter, pelo menos, permissões de leitura para ativar o gatilho.<br><br>Caso esteja usando a [versão 5.x ou posterior da extensão](../articles/azure-functions/functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher), em vez de uma cadeia de conexão, será possível fornecer uma referência a uma seção de configuração que definirá a conexão. Confira a opção [Conexões](../articles/azure-functions/functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Padrão

É possível usar os seguintes tipos de parâmetros para disparar o Hub de Eventos:

* `string`
* `byte[]`
* `POCO`
* `EventData` – As propriedades padrão de EventData serão fornecidas para o [namespace Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs.eventdata).

### <a name="additional-types"></a>Tipos adicionais 
Os aplicativos que usam a versão 5.0.0 ou posterior da extensão do Hub de Eventos usarão o tipo `EventData` no [Azure.Messaging.EventHubs](/dotnet/api/azure.messaging.eventhubs.eventdata) em vez do tipo usado no [namespace Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs.eventdata). Essa versão removerá o suporte do tipo `Body` herdado para beneficiar os seguintes tipos:

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody)

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

### <a name="default"></a>Padrão

É possível usar os seguintes tipos de parâmetros para disparar o Hub de Eventos:

* `string`
* `byte[]`
* `POCO`
* `EventData` – As propriedades padrão de EventData serão fornecidas para o [namespace Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs.eventdata).

### <a name="additional-types"></a>Tipos adicionais 
Os aplicativos que usam a versão 5.0.0 ou posterior da extensão do Hub de Eventos usarão o tipo `EventData` no [Azure.Messaging.EventHubs](/dotnet/api/azure.messaging.eventhubs.eventdata) em vez do tipo usado no [namespace Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs.eventdata). Essa versão removerá o suporte do tipo `Body` herdado para beneficiar os seguintes tipos:

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody)

# <a name="java"></a>[Java](#tab/java)

Confira o [exemplo de gatilho](#example) do Java para obter detalhes.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Confira o [exemplo de gatilho](#example) do Javascript para obter detalhes.

# <a name="python"></a>[Python](#tab/python)

Confira o [exemplo de gatilho](#example) do Python para obter detalhes.


---


## <a name="event-metadata"></a>Metadados de evento

O gatilho dos Hubs de Evento fornece várias propriedades de [metadados](../articles/azure-functions/./functions-bindings-expressions-patterns.md). As propriedades de metadados podem ser usadas como parte de expressões de associação em outras associações ou como parâmetros no seu código. As propriedades são provenientes da classe [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata).

|Propriedade|Type|Descrição|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|A instância `PartitionContext`.|
|`EnqueuedTimeUtc`|`DateTime`|O tempo de enfileiramento no UTC.|
|`Offset`|`string`|O deslocamento dos dados em relação ao fluxo de partição do Hub de Eventos. O deslocamento é um marcador ou um identificador para um evento dentro do fluxo do Hubs de Eventos. O identificador é exclusivo dentro de uma partição do fluxo de Hubs de Eventos.|
|`PartitionKey`|`string`|A partição para os dados de evento deve ser enviada.|
|`Properties`|`IDictionary<String,Object>`|Propriedades do usuário dos dados do evento.|
|`SequenceNumber`|`Int64`|O número de sequência lógica do evento.|
|`SystemProperties`|`IDictionary<String,Object>`|Propriedades do sistema, incluindo dos dados do evento.|

Consulte [exemplos de código](#example) que usam essas propriedades neste artigo.
