---
title: "Associação do Hub de Notificação do Azure Functions | Microsoft Docs"
description: "Entenda como usar a associação de Hub de Notificação do Azure no Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: 0ff0c949-20bf-430b-8dd5-d72b7b6ee6f7
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: glenga
ms.openlocfilehash: 02d01d0f6e945ed54dbe766aec2a0fd7c17c510f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-notification-hub-output-binding"></a>Associação de saída do Hub de Notificação do Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e codificar associações do Hub de Notificação no Azure Functions. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Suas funções podem enviar notificações por push usando um Hub de Notificação do Azure configurado com poucas linhas de código. No entanto, o Hub de Notificação do Azure deve estar configurado para os PNS (Serviços de Notificações de Plataforma) que você deseja usar. Para saber mais sobre a configuração de um Hub de Notificação do Azure e sobre o desenvolvimento de aplicativos cliente que se registram para receber notificações, consulte [Introdução aos Hubs de Notificação](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) e clique em sua plataforma de cliente de destino na parte superior.

As notificações enviadas podem ser notificações nativas ou notificações de modelo. Notificações nativas são destinadas a uma plataforma específica de notificação, conforme configurado na propriedade `platform` da associação de saída. Uma notificação de modelo pode ser usada para ter como destino várias plataformas.   

## <a name="notification-hub-output-binding-properties"></a>Propriedades de associação de saída do hub de notificação
O arquivo function.json fornece as seguintes propriedades:


|Propriedade  |Descrição  |
|---------|---------|
|**name** | Nome da variável usada no código de função para a mensagem do hub de notificação. |
|**tipo** | Deve ser definido como `notificationHub`. |
|**tagExpression** | As expressões de marca permitem que você especifique que as notificações sejam entregues a um conjunto de dispositivos registrados para receber notificações que correspondem à expressão de marca.  Para saber mais, veja [Expressões de marca e de roteamento](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | Nome do recurso de hub de notificação no portal do Azure. |
|**conexão** | Essa cadeia de conexão deve ser uma cadeia de conexão de **Configuração de Aplicativo** definida com o valor *DefaultFullSharedAccessSignature* para seu hub de notificação. |
|**direction** | Deve ser definido como `out`. | 
|**platform** | A propriedade platform indica a plataforma de notificação à qual sua notificação se destina. Por padrão, se a propriedade da plataforma é omitida da associação de saída, as notificações de modelo podem ser usadas para atingir qualquer plataforma configurada no Hub de Notificação do Azure. Para obter mais informações sobre como usar modelos em geral para enviar várias notificações de plataforma com um Hub de Notificação do Azure, consulte [Modelos](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Quando definida, _platform_ deve ser um dos seguintes valores: <ul><li><code>apns</code>&mdash;Apple Push Notification Service. Para obter mais informações sobre como configurar o hub de notificação do APNS e receber a notificação em um aplicativo cliente, consulte [Enviar notificações por push para iOS com os Hubs de Notificação do Azure](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Para obter mais informações sobre como configurar o hub de notificação para ADM e receber a notificação de um aplicativo Kindle, consulte [Introdução aos Hubs de Notificação para aplicativos Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>gcm</code>&mdash;[Google Cloud Messaging](https://developers.google.com/cloud-messaging/). Também há suporte para o Firebase Cloud Messaging, que é a nova versão do GCM. Para mais informações, consulte [Como enviar notificações por push para Android com Hubs de Notificação do Azure](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).</li><li><code>wns</code>&mdash;[Serviços de Notificação por Push do Windows](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) visando plataformas Windows. Também há suporte para Windows Phone 8.1 e posterior pelo WNS. Para mais informações, consulte [Introdução aos Hubs de Notificação para aplicativos da Plataforma Universal do Windows](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[Serviço de Notificação por Push da Microsoft](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx). Essa plataforma dá suporte a plataformas mais antigas do Windows Phone e Windows Phone 8. Para mais informações, consulte [Como enviar notificações por push com Hubs de Notificação do Azure no Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

function.json de exemplo:

```json
{
  "bindings": [
    {
      "name": "notification",
      "type": "notificationHub",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "gcm",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## <a name="notification-hub-connection-string-setup"></a>Configuração da cadeia de conexão do hub de notificação
Para usar uma associação de saída de um hub de notificação, você deve configurar a cadeia de conexão para o hub. Você pode selecionar um hub de notificação existente ou criar um novo diretamente na guia *Integrar* na sua função. Você também pode configurar a cadeia de conexão manualmente. 

Para configurar a cadeia de conexão em um hub de notificação existente:

1. Navegue até seu hub de notificação no [portal do Azure](https://portal.azure.com), escolha **Políticas de acesso** e selecione o botão Copiar ao lado da política **DefaultFullSharedAccessSignature**. Isso copia a cadeia de conexão para a política *DefaultFullSharedAccessSignature* ao hub de notificação. Essa cadeia de conexão fornece sua permissão de acesso à função para enviar mensagens de notificação. 
    ![Copiar a cadeia de conexão do hub de notificação](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Navegue até seu aplicativo de funções no portal do Azure, escolha **Configurações do aplicativo**, adicione uma chave como `MyHubConnectionString`, cole o *DefaultFullSharedAccessSignature* copiado em seu hub de notificação como o valor e, em seguida, clique em **Salvar**.

Agora você pode usar essa configuração de aplicativo nomeado que define a conexão de hub de notificação na associação de saída.

## <a name="apns-native-notifications-with-c-queue-triggers"></a>Notificações nativas do APNS com gatilhos de fila do C#
Este exemplo mostra como usar tipos definidos na [Biblioteca de Hubs de Notificações do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar uma notificação nativa do APNS. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.Info($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="gcm-native-notifications-with-c-queue-triggers"></a>Notificações nativas do GCM com gatilhos de fila do C#
Este exemplo mostra como usar tipos definidos na [Biblioteca de Hubs de Notificações do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar uma notificação nativa do GCM. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native GCM notification is ...
    // { "data": { "message": "notification message" }}  

    log.Info($"Sending GCM notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{gcmNotificationPayload}");
    await notification.AddAsync(new GcmNotification(gcmNotificationPayload));        
}
```

## <a name="wns-native-notifications-with-c-queue-triggers"></a>Notificações nativas do WNS com gatilhos de fila do C#
Este exemplo mostra como usar tipos definidos na [Biblioteca de Hubs de Notificações do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar uma notificação nativa WNS do sistema. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="template-example-for-nodejs-timer-triggers"></a>Exemplo de modelo para gatilhos de temporizador do Node.js
Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém `location` e `message`.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="template-example-for-f-timer-triggers"></a>Exemplo de modelo para gatilhos de temporizador do F#
Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém `location` e `message`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

## <a name="template-example-using-an-out-parameter"></a>Exemplo de modelo usando um parâmetro de saída
Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém um espaço reservado `message` no modelo.

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

## <a name="template-example-with-asynchronous-function"></a>Exemplo de modelo com a função assíncrona
Se você estiver usando o código assíncrono, os parâmetros de saída não serão permitidos. Nesse caso, use `IAsyncCollector` para retornar a notificação de modelo. O código a seguir é um exemplo de código assíncrono acima. 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

## <a name="template-example-using-json"></a>Exemplo de modelo usando JSON
Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém um espaço reservado `message` no modelo usando uma cadeia de caracteres JSON.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

## <a name="template-example-using-notification-hubs-library-types"></a>Exemplo de modelo usando tipos de biblioteca de Hubs de Notificação
Este exemplo mostra como usar tipos definidos na [Biblioteca de Hubs de Notificações do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

