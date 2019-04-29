---
title: Enviar notificações de multiplaforma para os usuários com os Hubs de Notificação do Azure (ASP.NET)
description: Saiba como usar modelos de Hubs de notificação para enviar, em uma única solicitação, uma notificação independente de plataforma que se destina a todas as plataformas.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
origin.date: 04/14/2018
ms.date: 02/25/2019
ms.author: v-biyu
ms.openlocfilehash: 0f92b49c9d77029a9624782b49eb23f7083c49aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60872247"
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Enviar notificações entre plataformas a usuários com Hubs de Notificação

Em um tutorial anterior, [Notificar usuários com os Hubs de Notificação], você aprendeu a enviar notificações por push a todos os dispositivos registrados para um usuário autenticado específico. Nesse tutorial, várias solicitações eram necessárias para enviar uma notificação a cada plataforma de cliente com suporte. Os Hubs de Notificação do Azure dão suporte a modelos, com os quais é possível especificar como um dispositivo específico deseja receber notificações. Esse método simplifica o envio de notificações de multiplaforma.

Este artigo demonstra como aproveitar modelos para enviar, em uma única solicitação, uma notificação independente de plataforma que se destina a todas as plataformas. Para obter informações detalhadas sobre modelos, confira [Visão geral de Hubs de Notificação do Azure][Templates].

> [!IMPORTANT]
> Não há suporte para projetos do Windows Phone 8.1 e anterior no Visual Studio 2017. Para saber mais, confira [Direcionamento e compatibilidade da plataforma Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

> [!NOTE]
> Com os Hubs de Notificação, um dispositivo pode registrar vários modelos com a mesma marcação. Nesse caso, uma mensagem de entrada que se destina à marcação resulta na entrega de várias notificações ao dispositivo, uma para cada modelo. Esse processo permite exibir a mesma mensagem em várias notificações visuais, como uma notificação e como uma notificação do sistema em um aplicativo da Windows Store.

## <a name="send-cross-platform-notifications-using-templates"></a>Enviar notificações multiplataforma usando modelos

Para enviar notificações de multiplaforma usando modelos, faça o seguinte:

1. No Gerenciador de Soluções do Visual Studio, expanda a pasta **Controllers** e, em seguida, abra o arquivo RegisterController.cs.

2. Localize o bloco do código no método `Put` que cria um novo registro e, em seguida, substitua o conteúdo `switch` pelo seguinte código:

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
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    Esse código chama o método da plataforma específica para criar um registro de modelo em vez de um registro nativo. Como os registros de modelo são derivados de registros nativos, você não precisa modificar os registros existentes.

3. No controlador `Notifications`, substitua o método `sendNotification` pelo código seguinte:

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

    Esse código envia uma notificação a todas as plataformas ao mesmo tempo, sem precisar especificar uma carga nativa. Os Hubs de Notificação desenvolve e fornece a carga correta para cada dispositivo com o valor *marca* fornecido, conforme especificado nos modelos registrados.

4. Publique novamente seu projeto back-end do WebApi.

5. Execute o aplicativo cliente novamente e, em seguida, verifique se o registro foi bem-sucedido.

6. (Opcional) Implante o aplicativo cliente em um segundo dispositivo e, em seguida, execute-o.
    Uma notificação é exibida em cada dispositivo.

## <a name="next-steps"></a>Próximas etapas

Agora que você concluiu este tutorial, saiba mais sobre Hubs de Notificação e modelos nestes tópicos:

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Visão geral dos Hubs de Notificação do Azure][Templates]: Contém informações mais detalhadas sobre modelos.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Notificar usuários com os Hubs de Notificação]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
