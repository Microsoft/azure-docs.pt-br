---
title: Gatilho do barramento de serviço do Azure para Azure Functions
description: Saiba como executar uma função do Azure quando as mensagens do barramento de serviço do Azure são criadas.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: f2a514af99baa2d828df1aee35a0e6339d39e617
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788546"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Gatilho do barramento de serviço do Azure para Azure Functions

Use o gatilho do Barramento de Serviço para responder às mensagens de uma fila ou tópico do Barramento de Serviço. Começando com a versão de extensão 3.1.0, você pode disparar em uma fila ou tópico habilitado para sessão.

Para obter informações sobre a instalação e detalhes de configuração, confira a [visão geral](functions-bindings-service-bus.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

A exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que lê [metadados de mensagem](#message-metadata) e registra uma mensagem de fila do Barramento de Serviço:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho de Barramento de Serviço em um arquivo *function.json* e uma [função C# script](functions-reference-csharp.md) que usa a associação. A função lê [metadados de mensagem](#message-metadata) e registra uma mensagem de fila do Barramento de Serviço do Microsoft Azure.

Aqui estão os dados de associação no arquivo *function.json*:

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

Aqui está o código de script do C#:

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

# <a name="java"></a>[Java](#tab/java)

A função Java a seguir usa a `@ServiceBusQueueTrigger` anotação da [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime) para descrever a configuração de um gatilho de fila do barramento de serviço. A função captura a mensagem colocada na fila e a adiciona aos logs.

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

As funções Java também podem ser disparadas quando uma mensagem é adicionada a um tópico do barramento de serviço. O exemplo a seguir usa a `@ServiceBusTopicTrigger` anotação para descrever a configuração do gatilho.

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de gatilho de Barramento de Serviço em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função lê [metadados de mensagem](#message-metadata) e registra uma mensagem de fila do Barramento de Serviço do Microsoft Azure.

Aqui estão os dados de associação no arquivo *function.json*:

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

Este é o código do script do JavaScript:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir mostra uma associação de gatilho do barramento de serviço em um *function.jsno* arquivo e uma [função do PowerShell](functions-reference-powershell.md) que usa a associação. 

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
  "bindings": [
    {
      "name": "mySbMsg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "topicName": "mytopic",
      "subscriptionName": "mysubscription",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

Aqui está a função que é executada quando uma mensagem do barramento de serviço é enviada.

```powershell
param([string] $mySbMsg, $TriggerMetadata)

Write-Host "PowerShell ServiceBus queue trigger function processed message: $mySbMsg"
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra como ler uma mensagem da fila do barramento de serviço por meio de um gatilho.

Uma associação do barramento de serviço é definida em *function.jsem* que o *tipo* está definido como `serviceBusTrigger` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

O código em *_\_ init_ \_ . py* declara um parâmetro como `func.ServiceBusMessage` , que permite que você leia a mensagem da fila em sua função.

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
        'metadata' : msg.metadata
    })

    logging.info(result)
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Em [bibliotecas de classes C#](functions-dotnet-class-library.md), use os seguintes atributos para configurar um gatilho do Barramento de Serviço:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  O construtor do atributo usa o nome da fila ou o tópico e a assinatura. In Azure Functions versão 1.x, você também pode especificar os direitos de acesso da conexão. Se você não especificar os direitos de acesso, o padrão é `Manage`. Para obter mais informações, consulte a seção [Gatilho - configuração](#configuration).

  Aqui está um exemplo que mostra o atributo usado com um parâmetro de cadeia de caracteres:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Como a `Connection` propriedade não está definida, o Functions procura uma configuração de aplicativo chamada `AzureWebJobsServiceBus` , que é o nome padrão para a cadeia de conexão do barramento de serviço. Você também pode definir a `Connection` propriedade para especificar o nome de uma configuração de aplicativo que contém a cadeia de conexão do barramento de serviço a ser usada, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Para obter um exemplo completo, consulte [gatilho-exemplo](#example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Oferece uma maneira de especificar a conta de Barramento de Serviço para usar. O construtor toma o nome de uma configuração de aplicativo que contenha uma cadeia de conexão de Barramento de Serviço. O atributo pode ser aplicado no nível de classe, método ou parâmetro. O exemplo a seguir mostra o nível de classe e método:

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

A conta de Barramento de Serviço a ser usada é determinada na seguinte ordem:

* A propriedade `ServiceBusTrigger` do atributo`Connection`.
* O `ServiceBusAccount` atributo aplicado ao mesmo parâmetro do `ServiceBusTrigger` atributo.
* O `ServiceBusAccount` atributo aplicado à função.
* O `ServiceBusAccount` atributo aplicado à classe.
* A configuração de aplicativo "AzureWebJobsServiceBus".

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O script C# não dá suporte a atributos.

# <a name="java"></a>[Java](#tab/java)

A `ServiceBusQueueTrigger` anotação permite que você crie uma função que é executada quando uma mensagem de fila do barramento de serviço é criada. As opções de configuração disponíveis incluem nome da fila e nome da cadeia de conexão.

A `ServiceBusTopicTrigger` anotação permite designar um tópico e uma assinatura para direcionar quais dados disparam a função.

Consulte o [exemplo](#example) de gatilho para obter mais detalhes.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript não dá suporte a atributos.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Não há suporte para atributos pelo PowerShell.

# <a name="python"></a>[Python](#tab/python)

O Python não dá suporte a atributos.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `ServiceBusTrigger`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como "serviceBusTrigger". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | n/d | Deve ser definido como "in". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | n/d | O nome da variável que representa a fila ou mensagem de tópico no código de função. |
|**queueName**|**QueueName**|Nome da fila a ser monitorada.  Defina somente se for monitorar uma fila, não para um tópico.
|**topicName**|**Topicname**|Nome do tópico a ser monitorado. Defina somente se for monitorar um tópico, não uma fila.|
|**subscriptionName**|**SubscriptionName**|Nome da assinatura a ser monitorada. Defina somente se for monitorar um tópico, não uma fila.|
|**connection**|**Conexão**|O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Barramento de Serviço para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome. Por exemplo, se você definir `connection` como "MyServiceBus", o tempo de execução do Functions procurará uma configuração de aplicativo chamada "AzureWebJobsMyServiceBus". Se você deixar `connection` vazio, o runtime de Functions usa a cadeia de caracteres de conexão do Barramento de serviço na configuração de aplicativo chamada "AzureWebJobsServiceBus".<br><br>Para obter uma cadeia de conexão, siga as etapas mostradas em [Obter as credenciais de gerenciamento](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). A cadeia de conexão deve ser voltada para um namespace do Barramento de Serviço, não limitada a uma fila ou tópico específico. |
|**accessRights**|**Acesso**|Direitos de acesso para a cadeia de caracteres de conexão. Os valores disponíveis são `manage` e `listen`. O padrão é `manage`, que indica que o `connection` tem a permissão **Gerenciar**. Se você usar uma cadeia de conexão que não tenha a permissão **Gerenciar**, defina `accessRights` como "escutar". Caso contrário, o runtime do Functions talvez falhe ao tentar executar operações que exigem o gerenciamento de direitos. No Azure Functions versão 2. x e superior, essa propriedade não está disponível porque a versão mais recente do SDK do barramento de serviço não dá suporte a operações de gerenciamento.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true` Se estiver se conectando a uma fila ou assinatura [com reconhecimento de sessão](../service-bus-messaging/message-sessions.md) . `false` caso contrário, que é o valor padrão.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

Os seguintes tipos de parâmetro estão disponíveis para a mensagem de fila ou tópico:

* `string` -Se a mensagem for de texto.
* `byte[]` - Útil para dados binários.
* Um tipo personalizado - Se a mensagem contiver JSON, funções do Azure tentará desserializar os dados JSON.
* `BrokeredMessage` – Fornece a você a mensagem desserializada com o método [BrokeredMessage. GetBody \<T> ()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1&preserve-view=true) .
* [`MessageReceiver`](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver?view=azure-dotnet&preserve-view=true) -Usado para receber e reconhecer mensagens do contêiner de mensagens (obrigatório quando [`autoComplete`](functions-bindings-service-bus-output.md#hostjson-settings) é definido como `false` )

Esses tipos de parâmetro são para Azure Functions versão 1. x; para 2. x e superior, use [`Message`](/dotnet/api/microsoft.azure.servicebus.message) em vez de `BrokeredMessage` .

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Os seguintes tipos de parâmetro estão disponíveis para a mensagem de fila ou tópico:

* `string` -Se a mensagem for de texto.
* `byte[]` - Útil para dados binários.
* Um tipo personalizado - Se a mensagem contiver JSON, funções do Azure tentará desserializar os dados JSON.
* `BrokeredMessage` – Fornece a você a mensagem desserializada com o método [BrokeredMessage. GetBody \<T> ()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1&preserve-view=true) .

Esses parâmetros são para Azure Functions versão 1. x; para 2. x e superior, use [`Message`](/dotnet/api/microsoft.azure.servicebus.message) em vez de `BrokeredMessage` .

# <a name="java"></a>[Java](#tab/java)

A mensagem do barramento de serviço de entrada está disponível por meio de um `ServiceBusQueueMessage` `ServiceBusTopicMessage` parâmetro ou.

[Consulte o exemplo para obter detalhes](#example).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Acesse a mensagem da fila ou do tópico usando `context.bindings.<name from function.json>` . A mensagem do Barramento de Serviço é passada em uma função como uma cadeia de caracteres ou um objeto JSON.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A instância do barramento de serviço está disponível por meio do parâmetro configurado no *function.jsna* propriedade nome do arquivo.

# <a name="python"></a>[Python](#tab/python)

A mensagem da fila está disponível para a função por meio de um parâmetro digitado como `func.ServiceBusMessage` . A mensagem do Barramento de Serviço é passada em uma função como uma cadeia de caracteres ou um objeto JSON.

---

## <a name="poison-messages"></a>Mensagens suspeitas

A manipulação de mensagens suspeitas não pode ser controlada ou configurada no Azure Functions. O barramento de serviço ele mesmo lida com as mensagens suspeitas.

## <a name="peeklock-behavior"></a> Comportamento de PeekLock

O runtime do Functions recebe uma mensagem no [Modo PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Ele chama `Complete` na mensagem se a função for concluída com êxito, ou chama `Abandon` se a função falhar. Se a função for executada por mais tempo que o limite `PeekLock`, o bloqueio é renovado automaticamente.

O `maxAutoRenewDuration` pode ser configurado no *host.json*, que mapeia para [OnMessageOptions.MaxAutoRenewDuration](/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet&preserve-view=true). O máximo permitido para essa configuração é 5 minutos de acordo com a documentação do Barramento de Serviço do Microsoft Azure, enquanto você pode aumentar o limite de tempo de funções do padrão de 5 minutos para 10 minutos. Para funções de Barramento de Serviço você não irá fazer isso, porque excederia o limite de renovação do Barramento de Serviço.

## <a name="message-metadata"></a>Metadados da mensagem

O gatilho Barramento de Serviço fornece várias propriedades de [metadados](./functions-bindings-expressions-patterns.md#trigger-metadata). Essas propriedades podem ser usadas como parte de expressões de associação em outras associações ou como parâmetros em seu código. Essas propriedades são membros da classe de [mensagem](/dotnet/api/microsoft.azure.servicebus.message?view=azure-dotnet&preserve-view=true) .

|Propriedade|Tipo|Descrição|
|--------|----|-----------|
|`ContentType`|`string`|Um identificador de tipo de conteúdo utilizado pelo remetente e pelo destinatário para lógica específica do aplicativo.|
|`CorrelationId`|`string`|ID de correlação.|
|`DeadLetterSource`|`string`|A origem de mensagens mortas.|
|`DeliveryCount`|`Int32`|Número total de entregas.|
|`EnqueuedTimeUtc`|`DateTime`|O tempo de enfileiramento no UTC.|
|`ExpiresAtUtc`|`DateTime`|Tempo de expiração em UTC.|
|`Label`|`string`|O rótulo específico do aplicativo.|
|`MessageId`|`string`|Um valor definido pelo usuário que o Barramento de Serviço pode usar para identificar mensagens duplicadas, se habilitado.|
|`MessageReceiver`|`MessageReceiver`|Receptor de mensagens do barramento de serviço. Pode ser usado para abandonar, concluir ou usar a mensagem de mensagens mortas.|
|`MessageSession`|`MessageSession`|Um receptor de mensagens especificamente para filas e tópicos habilitados para sessão.|
|`ReplyTo`|`string`|A resposta para o endereço da fila.|
|`SequenceNumber`|`long`|Número exclusivo atribuído a uma mensagem pelo Barramento de Serviço.|
|`To`|`string`|Enviar para o endereço.|
|`UserProperties`|`IDictionary<string, object>`|Propriedades definidas pelo remetente.|

Consulte [exemplos de código](#example) que usam essas propriedades neste artigo.

## <a name="next-steps"></a>Próximas etapas

- [Enviar mensagens do barramento de serviço do Azure de Azure Functions (Associação de saída)](./functions-bindings-service-bus-output.md)
