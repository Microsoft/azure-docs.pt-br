---
title: Usando bibliotecas de classes .NET com o Azure Functions | Microsoft Docs
description: Saiba como criar bibliotecas de classes do .NET para uso com o Azure Functions
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: 9f5db0c2-a88e-4fa8-9b59-37a7096fc828
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/10/2017
ms.author: donnam
ms.openlocfilehash: ad71a32d82e9b5aa4efda6d7ea67a9326ffcc4ff
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2017
---
# <a name="using-net-class-libraries-with-azure-functions"></a>Usando bibliotecas de classes .NET com o Azure Functions

Além dos arquivos de script, o Azure Functions oferece suporte à publicação de uma biblioteca de classes como a implementação de uma ou mais funções. Recomendamos que você use as [Ferramentas do Azure Functions para Visual Studio 2017](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/).

## <a name="prerequisites"></a>Pré-requisitos 

Este artigo tem os seguintes pré-requisitos:

- [Visual Studio 2017 versão 15.3](https://www.visualstudio.com/vs/) ou uma versão posterior.
- Instalar a carga de trabalho de **desenvolvimento do Azure**.

## <a name="functions-class-library-project"></a>Projeto de biblioteca de classes de funções

No Visual Studio, crie um novo projeto do Azure Functions. O novo modelo de projeto cria os arquivos *host.json* e *local.settings.json*. Você pode [personalizar as configurações de tempo de execução do Azure Functions no host.json](functions-host-json.md). 

O arquivo *local.settings.json* armazena as configurações do aplicativo, as cadeias de conexão e as configurações para as Ferramentas Básicas do Azure Functions. Para saber mais sobre a estrutura, consulte [Codificar e testar as funções do Azure localmente](functions-run-local.md#local-settings).

### <a name="functionname-attribute"></a>Atributo FunctionName

O atributo [`FunctionNameAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/FunctionNameAttribute.cs) marca um método como um ponto de entrada da função. Ele deve ser usado com exatamente um gatilho e 0 ou mais associações de entrada e saída.

### <a name="conversion-to-functionjson"></a>Conversão para function.json

Quando você compila um projeto do Azure Functions, um arquivo *function.json* é criado no diretório da função. O nome do diretório é o mesmo que o nome da função especificado pelo atributo `[FunctionName]`. O arquivo *function.json* contém gatilhos e associações e aponta para o arquivo de assembly do projeto.

Essa conversão é executada pelo pacote NuGet [Microsoft\.NET\.Sdk\.Functions](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). A fonte está disponível no repositório GitHub [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="triggers-and-bindings"></a>Gatilhos e associações 

A tabela a seguir lista os gatilhos e as associações que estão disponíveis em um projeto de biblioteca de classes do Azure Functions. Todos os atributos estão no namespace `Microsoft.Azure.WebJobs`.

| Associação | Atributo | Pacote NuGet |
|------   | ------    | ------        |
| [Gatilho de armazenamento de blobs, entrada, saída](#blob-storage) | [BlobAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | [O armazenamento de blobs] |
| [Gatilho do Azure Cosmos DB](#cosmos-db) | [CosmosDBTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions.DocumentDB] | 
| [Entrada e saída do Cosmos DB](#cosmos-db) | [DocumentDBAttribute] | [Microsoft.Azure.WebJobs.Extensions.DocumentDB] |
| [Gatilho de Hubs de Eventos e saída](#event-hub) | [EventHubTriggerAttribute], [EventHubAttribute] | [Microsoft.Azure.WebJobs.ServiceBus] |
| [Entrada e saída do arquivo externo](#api-hub) | [ApiHubFileAttribute] | [Microsoft.Azure.WebJobs.Extensions.ApiHub] |
| [Gatilho HTTP e webhook](#http) | [HttpTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions.Http] |
| [Entrada e saída dos Aplicativos Móveis](#mobile-apps) | [MobileTableAttribute] | [Microsoft.Azure.WebJobs.Extensions.MobileApps] | 
| [Saída dos Hubs de Notificação](#nh) | [NotificationHubAttribute] | [Microsoft.Azure.WebJobs.Extensions.NotificationHubs] | 
| [Gatilho de armazenamento de filas e saída](#queue) | [QueueAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | 
| [Saída do SendGrid](#sendgrid) | [SendGridAttribute] | [Microsoft.Azure.WebJobs.Extensions.SendGrid] | 
| [Gatilho e saída do Barramento de Serviço](#service-bus) | [ServiceBusAttribute], [ServiceBusAccountAttribute] | [Microsoft.Azure.WebJobs.ServiceBus]
| [Entrada e saída de armazenamento de tabelas](#table) | [TableAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | 
| [Gatilho de temporizador](#timer) | [TimerTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions] | 
| [Saída do Twilio](#twilio) | [TwilioSmsAttribute] | [Microsoft.Azure.WebJobs.Extensions.Twilio] | 

<a name="blob-storage"></a>

### <a name="blob-storage-trigger-input-bindings-and-output-bindings"></a>Associações de entrada, associações de saída e gatilho de armazenamento de blobs

O Azure Functions dá suporte a associações de entrada, saída e gatilho para o Azure Blob Storage. Para obter mais informações sobre expressões de associação e os metadados, consulte [Associações de armazenamento de blobs do Azure Functions](functions-bindings-storage-blob.md).

Um gatilho de blob é definido com o atributo `[BlobTrigger]`. Você pode usar o atributo `[StorageAccount]` para definir o nome da configuração do aplicativo que contém a cadeia de conexão para a conta de armazenamento que é usada por uma classe ou função inteira.

```csharp
[StorageAccount("AzureWebJobsStorage")]
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

A entrada e a saída do blob são definidos usando o atributo `[Blob]`, junto com um parâmetro `FileAccess` que indica a leitura ou gravação. O exemplo a seguir usa um gatilho de blob e uma associação de saída de blob.

```csharp
[FunctionName("ResizeImage")]
[StorageAccount("AzureWebJobsStorage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```        

<a name="cosmos-db"></a>

### <a name="cosmos-db-trigger-input-bindings-and-output-bindings"></a>Gatilho do Cosmos DB, associações de entrada e associações de saída

O Azure Functions dá suporte a gatilhos e associações de entrada e saída para o Cosmos DB. Para saber mais sobre os recursos da associação do Cosmos DB, consulte [Associações do Cosmos DB do Azure Functions](functions-bindings-documentdb.md).

Para um gatilho a partir de um documento do Cosmos DB, use o atributo `[CosmosDBTrigger]` no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.DocumentDB]. O exemplo a seguir é disparado a partir de `database` e `collection` específicos. A configuração `myCosmosDB` contém a conexão à instância do Cosmos DB. 

```csharp
[FunctionName("DocumentUpdates")]
public static void Run(
    [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
IReadOnlyList<Document> documents, TraceWriter log)
{
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
}
```

Para associar a um documento do Cosmos DB, use o atributo `[DocumentDB]` no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.DocumentDB]. O exemplo a seguir tem um gatilho de fila e uma associação de saída da API DocumentDB.

```csharp
[FunctionName("QueueToDocDB")]        
public static void Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem, 
    [DocumentDB("ToDoList", "Items", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
{
    document = new { Text = myQueueItem, id = Guid.NewGuid() };
}

```

<a name="event-hub"></a>

### <a name="event-hubs-trigger-and-output"></a>Gatilho de Hubs de Eventos e saída

O Azure Functions dá suporte a associações de gatilho e de saída para os Hubs de Eventos. Para obter mais informações, consulte [Associações do Hub de Eventos do Azure Functions](functions-bindings-event-hubs.md).

Os tipos `[Microsoft.Azure.WebJobs.ServiceBus.EventHubTriggerAttribute]` e `[Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute]` são definidos no pacote NuGet [Microsoft.Azure.WebJobs.ServiceBus]. 

O exemplo a seguir usa um gatilho de Hub de Eventos:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

O exemplo a seguir tem uma saída de Hub de Eventos, usando o valor de retorno do método como a saída:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

<a name="api-hub"></a>

### <a name="external-file-input-and-output"></a>Entrada e saída do arquivo externo

O Azure Functions dá suporte a associações de gatilho, entrada e saída para arquivos externos, tais como Google Drive, Dropbox e OneDrive. Para saber mais, consulte [Associações de Arquivo Externo do Azure Functions](functions-bindings-external-file.md). Os tipos `[ExternalFileTrigger]` e `[ExternalFile]` são definidos no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.ApiHub].

O exemplo em C# a seguir demonstra uma associação de entrada e saída do arquivo externo. O código copia o arquivo de entrada para o arquivo de saída.

```csharp
[StorageAccount("MyStorageConnection")]
[FunctionName("ExternalFile")]
[return: ApiHubFile("MyFileConnection", "samples-workitems/{queueTrigger}-Copy", FileAccess.Write)]
public static string Run([QueueTrigger("myqueue-items")] string myQueueItem, 
    [ApiHubFile("MyFileConnection", "samples-workitems/{queueTrigger}", FileAccess.Read)] string myInputFile, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    return myInputFile;
}
```

<a name="http"></a>

### <a name="http-and-webhooks"></a>HTTP e webhooks

Use o atributo `HttpTrigger` para definir um gatilho HTTP ou webhook. O atributo é definido no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.Http]. Você pode personalizar o nível de autorização, o tipo de webhook, a rota e os métodos. O exemplo a seguir define um gatilho HTTP com a autenticação anônima e o tipo de webhook _genericJson_.

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a name="mobile-apps"></a>

### <a name="mobile-apps-input-and-output"></a>Entrada e saída dos Aplicativos Móveis

O Azure Functions dá suporte a associações de entrada e saída para os Aplicativos Móveis. Para saber mais, consulte [Associações de Aplicativos Móveis do Azure Functions](functions-bindings-mobile-apps.md). O atributo `[MobileTable]` é definido no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.MobileApps].

O exemplo a seguir demonstra uma associação de saída dos Aplicativos Móveis:

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run([QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem, TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

<a name="nh"></a>

### <a name="notification-hubs-output"></a>Saída dos Hubs de Notificação

O Azure Functions oferece suporte a uma associação de saída para Hubs de Notificação. Para saber mais, consulte [Associação de saída do Hub de Notificação do Azure Functions](functions-bindings-notification-hubs.md). O atributo `[NotificationHub]` é definido no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.NotificationHubs].

<a name="queue"></a>

### <a name="queue-storage-trigger-and-output"></a>Gatilho de armazenamento de filas e saída

O Azure Functions dá suporte a associações de gatilho e de saída para filas do Azure. Para obter mais informações, consulte [Associações do Armazenamento de Filas do Azure Functions](functions-bindings-storage-queue.md).

O exemplo a seguir mostra como usar o tipo de retorno de função com uma associação de saída de fila, usando o atributo `[Queue]`. 

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    // HTTP trigger with queue output binding
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }
}

```

Para definir um gatilho de fila, use o atributo `[QueueTrigger]`.
```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    // Queue trigger
    [FunctionName("QueueTrigger")]
    [StorageAccount("AzureWebJobsStorage")]
    public static void QueueTrigger([QueueTrigger("myqueue-items")] string myQueueItem, TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}

```


<a name="sendgrid"></a>

### <a name="sendgrid-output"></a>Saída do SendGrid

O Azure Functions oferece suporte a uma associação de saída do SendGrid para enviar email programaticamente. Para saber mais, consulte [Associações do SendGrid do Azure Functions](functions-bindings-sendgrid.md).

O atributo `[SendGrid]` é definido no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.SendGrid]. Uma associação SendGrid requer um configuração de aplicativo denominada `AzureWebJobsSendGridApiKey`, que contém sua chave de API do SendGrid. Este é o nome da configuração padrão para a sua chave de API do SendGrid. Se você precisa ter mais de uma chave SendGrid ou escolher um nome de configuração diferente, você pode definir esse nome usando a propriedade `ApiKey` do atributo de associação `SendGrid`, como no exemplo a seguir:

    [SendGrid(ApiKey = "MyCustomSendGridKeyName")]

A seguir está um exemplo de usar um gatilho de fila do Barramento de Serviço e uma associação de saída SendGrid usando `SendGridMessage`:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid] out SendGridMessage message)
{
    message = new SendGridMessage();
    message.AddTo(email.To);
    message.AddContent("text/html", email.Body);
    message.SetFrom(new EmailAddress(email.From));
    message.SetSubject(email.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```
Observe que este exemplo requer a chave de API do SendGrid para ser o armazenamento em um configuração de aplicativo denominada `AzureWebJobsSendGridApiKey`.

<a name="service-bus"></a>

### <a name="service-bus-trigger-and-output"></a>Gatilho e saída do Barramento de Serviço

O Azure Functions dá suporte a gatilhos e a associações de saída para filas e tópicos do Barramento de Serviço. Para obter mais informações sobre como configurar associações, consulte [Associações do Barramento de Serviço do Azure Functions](functions-bindings-service-bus.md).

Os atributos `[ServiceBusTrigger]` e `[ServiceBus]` são definidos no pacote NuGet [Microsoft.Azure.WebJobs.ServiceBus]. 

A seguir, um exemplo de um gatilho de fila do Barramento de Serviço:

```csharp
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run([ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

Este é um exemplo de uma associação de saída do Barramento de Serviço, usando o tipo de retorno do método como a saída:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
    return input.Text;
}
```        

<a name="table"></a>

### <a name="table-storage-input-and-output"></a>Entrada e saída de armazenamento de tabelas

O Azure Functions dá suporte a associações de entrada e saída para armazenamento de Tabelas do Azure. Para saber mais, consulte [Associações de armazenamento de Tabelas do Azure Functions](functions-bindings-storage-table.md).

O exemplo a seguir é uma classe com duas funções, demonstrando associações de entrada e saída de armazenamento de tabelas. 

```csharp
[StorageAccount("AzureWebJobsStorage")]
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }

    // use the metadata parameter "queueTrigger" to bind the queue payload
    [FunctionName("TableInput")]
    public static void TableInput([QueueTrigger("table-items")] string input, [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, TraceWriter log)
    {
        log.Info($"C# function processed: {poco.Text}");
    }
}

```

<a name="timer"></a>

### <a name="timer-trigger"></a>Gatilho de temporizador

O Azure Functions tem uma associação de gatilho de timer que permite executar o código de função com base em um agendamento definido. Para saber mais sobre os recursos da associação, consulte [Agendar a execução de código com o Azure Functions](functions-bindings-timer.md).

No plano de Consumo, você pode definir agendas com uma [expressão CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression). Se você estiver usando um Plano do Serviço de Aplicativo, você também pode usar uma cadeia de caracteres do período de tempo. 

O exemplo a seguir define um gatilho de tempo que é executado a cada 5 minutos:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

<a name="twilio"></a>

### <a name="twilio-output"></a>Saída do Twilio

O Azure Functions dá suporte às associações de saída do Twilio para habilitar funções para envio de mensagens de texto SMS. Para saber mais, consulte [Enviar mensagens SMS do Azure Functions usando associações de saída do Twilio](functions-bindings-twilio.md). 

O atributo `[TwilioSms]` é definido no pacote [Microsoft.Azure.WebJobs.Extensions.Twilio].

O exemplo C# a seguir usa uma fila de gatilho e uma associação de saída do Twilio:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run([QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar funções do Azure no script de C#, consulte a [referência para o desenvolvedor de script \#C no Azure Functions](functions-reference-csharp.md).

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


<!-- NuGet packages --> 
[Microsoft.Azure.WebJobs]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.DocumentDB]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB/1.1.0-beta4
[Microsoft.Azure.WebJobs.ServiceBus]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.MobileApps]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps/1.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs/1.1.0-beta1
[Microsoft.Azure.WebJobs.ServiceBus]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.SendGrid]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.Http]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http/1.0.0-beta1
[Microsoft.Azure.WebJobs.Extensions.BotFramework]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.BotFramework/1.0.15-beta
[Microsoft.Azure.WebJobs.Extensions.ApiHub]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ApiHub/1.0.0-beta4
[Microsoft.Azure.WebJobs.Extensions.Twilio]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio/1.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/2.1.0-beta1


<!-- Links to source --> 
[DocumentDBAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs
[CosmosDBTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/Trigger/CosmosDBTriggerAttribute.cs
[EventHubAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs
[EventHubTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs
[MobileTableAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs
[NotificationHubAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs 
[ServiceBusAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs
[ServiceBusAccountAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs
[QueueAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs
[StorageAccountAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs
[BlobAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs
[TableAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs
[TwilioSmsAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs
[SendGridAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs
[HttpTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs
[ApiHubFileAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.ApiHub/ApiHubFileAttribute.cs
[TimerTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs
