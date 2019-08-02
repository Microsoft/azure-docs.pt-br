---
title: Enviar notificações por push com os Hubs de Notificação do Azure e o Node.js
description: Saiba como usar os Hubs de notificação para enviar notificações por push de um aplicativo do Node. js.
keywords: notificação por push, notificações por push,push do node.js,push do ios
services: notification-hubs
documentationcenter: nodejs
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 129127a2a43cd9a86e0a1e1cf538358b62381257
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706220"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Enviar notificações por push com os Hubs de Notificação do Azure e o Node.js

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Visão geral

> [!IMPORTANT]
> Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, crie uma conta de avaliação gratuita em apenas alguns minutos por meio da [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

Este guia mostra como enviar notificações por push com a ajuda dos Hubs de Notificação do Azure diretamente de um aplicativo [Node.js](https://nodejs.org).

Os cenários abordados incluem o envio de notificações por push para aplicativos nas seguintes plataformas:

- Android
- iOS
- Plataforma Universal do Windows
- Windows Phone

## <a name="notification-hubs"></a>Hubs de Notificação

Os Hubs de Notificação do Azure fornecem uma infraestrutura de várias plataformas escalonáveis e fáceis de usar para o envio de notificações por push para dispositivos móveis. Para obter detalhes sobre a infraestrutura de serviço, confira a página [Hubs de Notificação do Azure](https://msdn.microsoft.com/library/windowsazure/jj927170.aspx) .

## <a name="create-a-nodejs-application"></a>Criar um aplicativo Node.js

A primeira etapa deste tutorial é a criação de um novo aplicativo Node. js em branco. Para obter instruções sobre como criar um aplicativo Node. js, consulte [criar e implantar um aplicativo Node. js no Site do Azure][nodejswebsite] , [Node.js Cloud Service][Node.js Cloud Service] usando o Windows PowerShell, ou [Site com WebMatrix] [webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Configurar seu aplicativo para usar os Hubs de Notificação

Para usar os Hubs de Notificação do Azure, você precisa baixar e usar o [pacote azure](https://www.npmjs.com/package/azure)do Node.js, que inclui um conjunto interno de bibliotecas auxiliares que se comunicam com os serviços REST de notificação por push.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Usar o NPM (gerenciador de pacotes de nós) para obter o pacote

1. Use uma interface de linha de comando, como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix) e navegue até a pasta em que você criou o aplicativo em branco.
2. Execute `npm install azure-sb` na janela Comando.
3. Você pode executar manualmente o comando `ls` ou `dir` para verificar se uma pasta `node_modules` foi criada.
4. Dentro dessa pasta, você encontrará o pacote **azure** , que contém as bibliotecas necessárias para acessar o Hub de Notificação.

> [!NOTE]
> Saiba mais sobre a instalação do NPM no [blog oficial do NPM](https://blog.npmjs.org/post/85484771375/how-to-install-npm).

### <a name="import-the-module"></a>Importar o módulo
Usando um editor de texto, adicione o seguinte à parte superior do arquivo `server.js` do aplicativo:

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Configurar uma conexão do Hub de Notificação do Azure

O objeto `NotificationHubService`permite que você trabalhe com os hubs de notificação. O código a seguir cria um objeto `NotificationHubService` para o hub de notificação denominado `hubname`. Adicione-o próximo ao início do arquivo `server.js`, após a instrução de importação do módulo Azure:

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

Obtenha o valor `connectionstring` de conexão do [portal do Azure] executando as seguintes etapas:

1. No painel de navegação esquerdo, clique em **Procurar**.
2. Selecione **Hubs de Notificação**e localize o hub que você deseja usar no exemplo. Você pode consultar a [tutorial guia de Introdução do Windows Store](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) se você precisar de ajuda com a criação de um novo Hub de notificação.
3. Escolha a opção **Configurações**.
4. Clique em **Políticas de Acesso**. Você verá as duas cadeias de conexão de acesso compartilhado e completo.

![Portal do Azure - Hubs de Notificação](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Você também pode recuperar a cadeia de conexão usando o cmdlet **Get-AzureSbNamespace** fornecido pelo [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou o comando **azure sb namespace show** com a [Interface de Linha de Comando do Azure (CLI do Azure)](../cli-install-nodejs.md).

## <a name="general-architecture"></a>Arquitetura geral

O objeto `NotificationHubService` expõe as seguintes instâncias de objeto para o envio de notificações por push a dispositivos e aplicativos específicos:

- **Android** – use o objeto `GcmService` que está disponível em `notificationHubService.gcm`
- **iOS** – use o objeto `ApnsService`, que está acessível em `notificationHubService.apns`
- **Windows Phone** – use o objeto `MpnsService`, que está disponível em `notificationHubService.mpns`
- **Plataforma Universal do Windows** – use o objeto `WnsService`, que está disponível em `notificationHubService.wns`

### <a name="how-to-send-push-notifications-to-android-applications"></a>Como: Enviar notificações por push a aplicativos Android

O objeto `GcmService` fornece um método `send` que pode ser usado para enviar notificações por push aos aplicativos Android. O método `send` aceita os seguintes parâmetros:

- **Tags** : o identificador da etiqueta. Se nenhuma marca for fornecida, a notificação será enviada a todos os clientes.
- **Payload** : o JSON ou o conteúdo bruto de cadeia de caracteres da mensagem.
- **Callback** : a função de retorno de chamada.

Para obter mais informações sobre o formato de carga, consulte o [documentação da carga](https://distriqt.github.io/ANE-PushNotifications/m.FCM-GCM%20Payload).

O código a seguir usa a instância `GcmService` exposta por `NotificationHubService` para enviar uma notificação por push para todos os clientes registrados.

```javascript
var payload = {
  data: {
    message: 'Hello!'
  }
};
notificationHubService.gcm.send(null, payload, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Como: Enviar notificações por push para aplicativos iOS

Da mesma forma como ocorre com os aplicativos Android descritos acima, o objeto `ApnsService` fornece um método `send` que pode ser usado para enviar notificações por push aos aplicativos iOS. O método `send` aceita os seguintes parâmetros:

- **Tags** : o identificador da etiqueta. Se nenhuma marca for fornecida, a notificação será enviada a todos os clientes.
- **Payload** : o JSON ou o conteúdo de cadeia de caracteres da mensagem.
- **Callback** : a função de retorno de chamada.

Para saber mais sobre o formato do conteúdo, veja a seção **Conteúdo de notificação** do documento [Guia de programação de notificações locais e por push](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) .

O código a seguir usa a instância `ApnsService` exposta por `NotificationHubService` para enviar uma mensagem de alerta a todos os clientes:

```javascript
var payload={
    alert: 'Hello!'
  };
notificationHubService.apns.send(null, payload, function(error){
  if(!error){
      // notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Como: Enviar notificações por push para aplicativos do Windows Phone

O objeto `MpnsService` fornece um método `send` que pode ser usado para enviar notificações por push aos aplicativos do Windows Phone. O método `send` aceita os seguintes parâmetros:

- **Tags** : o identificador da etiqueta. Se nenhuma marca for fornecida, a notificação será enviada a todos os clientes.
- **Payload** : o conteúdo XML da mensagem.
- **TargetName** - `toast` para notificações ‘toast’. `token` para notificações de bloco.
- **NotificationClass** : a prioridade da notificação. Veja a seção **Elementos de cabeçalhos HTTP** do documento [Notificações por push de um servidor](https://msdn.microsoft.com/library/hh221551.aspx) para obter os valores válidos.
- **Options** : cabeçalhos de solicitação opcionais.
- **Callback** : a função de retorno de chamada.

Para obter uma lista válida `TargetName`, `NotificationClass` e opções de cabeçalho, confira a página [Enviar notificações por push de um servidor](https://msdn.microsoft.com/library/hh221551.aspx).

O seguinte código de exemplo usa a instância `MpnsService` exposta por `NotificationHubService` para enviar uma notificação por push do sistema:

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Como: Enviar notificações por push para aplicativos da UWP (Plataforma Universal do Windows)

O objeto `WnsService` fornece um método `send` que pode ser usado para enviar notificações por push aos aplicativos da Plataforma Universal do Windows.  O método `send` aceita os seguintes parâmetros:

- **Tags** : o identificador da etiqueta. Se nenhuma marca for fornecida, a notificação será enviada a todos os clientes registrados.
- **Payload** : a carga da mensagem XML.
- **Type** : o tipo de notificação.
- **Options** : cabeçalhos de solicitação opcionais.
- **Callback** : a função de retorno de chamada.

Para obter uma lista de tipos e de cabeçalhos de solicitação válidos, veja [Solicitação de serviço e cabeçalhos de resposta de notificação por push](https://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

O código a seguir usa a instância `WnsService` exposta por `NotificationHubService` para enviar uma notificação por push do sistema para um aplicativo UWP:

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>Próximas etapas

Os snippets de código de exemplo acima permitem que você crie com facilidade a infraestrutura de serviço para enviar notificações por push para uma ampla variedade de dispositivos. Agora que você aprendeu os conceitos básicos do uso de Hubs de Notificação com o node.js, siga estes links para saber mais sobre como é possível estender ainda mais esses recursos.

- Veja a Referência do MSDN para os [Hubs de Notificação do Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx).
- Visite o repositório [SDK do Azure para o Node] no GitHub para obter mais exemplos e detalhes de implementação.

[SDK do Azure para o Node]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
[Create a Service Namespace]: #create-a-service-namespace
[Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
[Create a Node.js Application]: #Create_a_Nodejs_Application
[Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
[How to: Create a Topic]: #How_to_Create_a_Topic
[How to: Create Subscriptions]: #How_to_Create_Subscriptions
[How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
[How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
[How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
[1]: #Next_Steps
[Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Azure Service Bus Notification Hubs]: https://msdn.microsoft.com/library/windowsazure/jj927170.aspx
[SqlFilter]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs
[webmatrix]: https://docs.microsoft.com/aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Portal do Azure]: https://portal.azure.com
