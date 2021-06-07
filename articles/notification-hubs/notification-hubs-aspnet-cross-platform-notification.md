---
title: Enviar notificações de multiplaforma para os usuários com os Hubs de Notificação do Azure (ASP.NET)
description: Saiba como usar modelos de Hubs de notificação para enviar, em uma única solicitação, uma notificação independente de plataforma que se destina a todas as plataformas.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: thsomasu
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 09/14/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 10/02/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 3f8f9357fff4773467dd93046ece8533514eb444
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92313898"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>Enviar notificações entre plataformas com os hubs de notificação do Azure

Este tutorial se baseia no tutorial anterior, [envie notificações para usuários específicos usando os hubs de notificação do Azure]. Esse tutorial descreve como enviar notificações por push para todos os dispositivos registrados para um usuário autenticado específico. Essa abordagem exigiu várias solicitações para enviar uma notificação para cada plataforma de cliente com suporte. Os Hubs de Notificação do Azure dão suporte a modelos, com os quais é possível especificar como um dispositivo específico deseja receber notificações. Esse método simplifica o envio de notificações de multiplaforma.

Este artigo demonstra como aproveitar os modelos para enviar uma notificação direcionada a todas as plataformas. Este artigo usa uma única solicitação para enviar uma notificação neutra de plataforma. Para obter informações mais detalhadas sobre modelos, consulte [visão geral dos hubs de notificação][Templates].

> [!IMPORTANT]
> Windows Phone projetos 8,1 e anteriores não têm suporte no Visual Studio 2019. Para obter mais informações, consulte [direcionamento e compatibilidade da plataforma Visual Studio 2019](/visualstudio/releases/2019/compatibility).

> [!NOTE]
> Com os Hubs de Notificação, um dispositivo pode registrar vários modelos usando a mesma tag. Nesse caso, uma mensagem de entrada direcionada à tag resulta na entrega de várias notificações ao dispositivo, uma para cada modelo. Esse processo permite exibir a mesma mensagem em várias notificações visuais, como uma notificação e como uma notificação do sistema em um aplicativo da Windows Store.

## <a name="send-cross-platform-notifications-using-templates"></a>Enviar notificações multiplataforma usando modelos

Esta seção usa o código de exemplo que você criou no tutorial [enviar notificações para usuários específicos usando hubs de notificação do Azure] . Você pode [baixar o exemplo completo do GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

Para enviar notificações entre plataformas usando modelos, faça o seguinte:

1. No Visual Studio no **Gerenciador de soluções**, expanda a pasta **controladores** e, em seguida, abra o arquivo *RegisterController. cs* .

1. Localize o bloco do código no método `Put` que cria um novo registro e, em seguida, substitua o conteúdo `switch` pelo seguinte código:

    ```csharp
    switch (deviceUpdate.Platform)
    {
        case "mpns":
            var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>$(message)</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
            registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "wns":
            toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
            registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "apns":
            var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
            registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
            break;
        case "fcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new FcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    Esse código chama o método da plataforma específica para criar um registro de modelo em vez de um registro nativo. Como os registros de modelo são derivados de registros nativos, você não precisa modificar os registros existentes.

1. No **Gerenciador de soluções**, na pasta **controladores** , abra o arquivo **NotificationsController. cs** . Substitua o método `Post` pelo seguinte código:

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    Esse código envia uma notificação para todas as plataformas ao mesmo tempo. Você não especifica uma carga nativa. Os Hubs de Notificação desenvolve e fornece a carga correta para cada dispositivo com o valor marca fornecido, conforme especificado nos modelos registrados.

1. Republique seu projeto de API Web.

1. Execute o aplicativo cliente novamente para verificar se o registro foi bem-sucedido.

1. Opcionalmente, implante o aplicativo cliente em um segundo dispositivo e, em seguida, execute o aplicativo. Uma notificação é exibida em cada dispositivo.

## <a name="next-steps"></a>Próximas etapas

Agora que você concluiu este tutorial, saiba mais sobre os hubs de notificação e os modelos nestes artigos:

* Para um cenário diferente para usar modelos, consulte o tutorial [notificações por push para dispositivos Windows em execução plataforma universal do Windows aplicativos específicos][Use Notification Hubs to send breaking news] .
* Para obter informações mais detalhadas sobre modelos, consulte [visão geral dos hubs de notificação][Templates].

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Enviar notificações para usuários específicos usando o Hubs de Notificação do Microsoft Azure]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hub How to for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)