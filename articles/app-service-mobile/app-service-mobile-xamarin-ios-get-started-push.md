---
title: Adicionar notificações por push a seu aplicativo Xamarin iOS com o Serviço de Aplicativo do Azure
description: Aprenda a usar o Serviço de Aplicativo do Azure para enviar notificações por push para o aplicativo Xamarin.iOS
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 9edd5e755b4a42fe881a0863bb284039299ec713
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446276"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Adicionar notificações por push a seu aplicativo Xamarin.iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center está investindo em novos e integrados serviços essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **construir**, **teste** e **distribuir** services para configurar o pipeline de integração contínua e entrega. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso do seu aplicativo usando o **Analytics** e **diagnóstico** serviços e entre em contato com usuários usando o **enviar por Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados do aplicativo na nuvem. Fazer check-out [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-ios-get-started-push) hoje mesmo.
>

## <a name="overview"></a>Visão geral

Neste tutorial, você adicionará notificações por push ao projeto de [início rápido do Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md) de forma que sempre que um registro for inserido, uma notificação por push seja enviada.

Se você não usar o projeto baixado do início rápido do servidor, deve adicionar o pacote de extensão de notificação por push ao seu projeto. Confira [Trabalhar com o servidor back-end SDK do .NET para os Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.

## <a name="prerequisites"></a>Pré-requisitos

* Conclua o tutorial [Criar um aplicativo Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md) .
* Um dispositivo físico iOS. Não há suporte para notificações por push pelo simulador do iOS.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrar o aplicativo nas notificações por push no portal do desenvolvedor da Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Configurar o seu aplicativo móvel para enviar notificações por push

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Atualizar o projeto de servidor para enviar notificações por push

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Configure seu projeto do Xamarin.iOS

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Adicionar notificações de push para seu aplicativo

1. Em **QSTodoService**, adicione a seguinte propriedade para que o **AppDelegate** possa adquirir o cliente móvel:

    ```csharp
    public MobileServiceClient GetClient {
        get
        {
            return client;
        }
        private set
        {
            client = value;
        }
    }
    ```

2. Adicione a seguinte declaração `using` ao topo do arquivo **AppDelegate.cs** .

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. Em **AppDelegate**, substitua o evento **FinishedLaunching**:

   ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // registers for push for iOS8
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

        return true;
    }
    ```

4. No mesmo arquivo, substitua o evento `RegisteredForRemoteNotifications`. Nesse código, você está se registrando para uma notificação de modelo simples que será enviada em todas as plataformas com suporte do servidor.

    Para saber mais sobre modelos com os Hubs de Notificação, confira [Modelos](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
    {
        MobileServiceClient client = QSTodoService.DefaultService.GetClient;

        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyAPNS}
        };

        // Register for push with your mobile app
        var push = client.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

5. Então substitua o evento **DidReceivedRemoteNotification** :

   ```csharp
    public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps [new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

Seu aplicativo foi atualizado para dar suporte a notificações por push.

## <a name="test"></a>Testar notificações por push no seu aplicativo

1. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo em um dispositivo compatível com iOS; em seguida, clique em **OK** para aceitar as notificações por push.

   > [!NOTE]
   > Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.

2. No aplicativo, digite uma tarefa e clique no ícone do sinal de adição ( **+** ).
3. Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.
4. Repita a etapa 2 e imediatamente feche o aplicativo e verifique se uma notificação é exibida.

Este tutorial foi concluído com êxito.
