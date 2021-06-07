---
title: Enviar notificações por push para o Xamarin usando os Hubs de Notificação do Azure | Microsoft Docs
description: Neste tutorial, você aprende a usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo Xamarin.iOS.
services: notification-hubs
keywords: notificações por push do IOS, mensagens por push, notificações por push, mensagem por push
documentationcenter: xamarin
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/12/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 05/23/2019
ms.openlocfilehash: ff1e5edad05ebd7157f71ad2e099ea88905be4f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98221129"
---
# <a name="tutorial-send-push-notifications-to-xamarinios-apps-using-azure-notification-hubs"></a>Tutorial: Enviar notificações por push a aplicativos Xamarin.iOS usando os Hubs de Notificação do Azure

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Visão geral

Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo iOS. Você cria um aplicativo Xamarin.iOS em branco que recebe notificações por push usando o [serviço APN (Apple Push Notification)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Ao finalizar, você poderá usar seu hub de notificação para transmitir notificações por push a todos os dispositivos que executam seu aplicativo. O código concluído está disponível na amostra do [aplicativo NotificationHubs][GitHub].

Neste tutorial, você cria/atualiza o código para executar as seguintes tarefas:

> [!div class="checklist"]
> * Gerar o arquivo de solicitação de assinatura de certificado
> * Registrar seu aplicativo para notificações por push
> * Criar um perfil de provisionamento para o aplicativo
> * Configurar o hub de notificação para notificações por push do iOS
> * Enviar notificações por push de teste

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**. Caso você não tenha uma assinatura do Azure, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Versão mais recente do [Xcode][Install Xcode]
* Um dispositivo compatível com o iOS 10 (ou versão posterior)
* [Programa de Desenvolvedores de iOS](https://developer.apple.com/programs/)
* [Visual Studio para Mac]
  
  > [!NOTE]
  > Devido aos requisitos de configuração das notificações por push do iOS, você deve implantar e testar o aplicativo de exemplo em um dispositivo iOS físico (iPhone ou iPad) em vez do simulador.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre Hubs de Notificação para aplicativos Xamarin.iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-app-to-the-notification-hub"></a>Conectar seu aplicativo ao hub de notificação

### <a name="create-a-new-project"></a>Criar um novo projeto

1. No Visual Studio, crie um novo projeto do iOS e selecione o modelo **Aplicativo de Modo de Exibição Único** e clique em **Avançar**

     ![Visual Studio – Selecione o Tipo de Aplicativo][31]

2. Digite o identificador Nome do Aplicativo e o Organização e clique em **Avançar**, depois em **Criar**

3. Na exibição de Solução, clique duas vezes em *Into.plist* e em **Identidade** e verifique se o Identificador de Pacote corresponde ao usado ao criar o perfil de provisionamento. Em **assinatura**, verifique se sua conta de desenvolvedor está selecionada em **Equipe**, se "Gerenciar assinatura automaticamente" está selecionado e se seu certificado de autenticação e perfil de provisionamento são selecionados automaticamente.

    ![Configuração de aplicativo do Visual Studio-iOS][32]

4. Na exibição Solução, clique duas vezes em `Entitlements.plist` e verifique se a opção **Habilitar Notificações por Push** está marcada.

    ![Visual Studio - Configuração de Direitos do iOS][33]

5. Adicione o pacote de Mensagens do Azure. Na exibição Soluções, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Adicionar Pacotes NuGet**. Pesquise por **Xamarin.Azure.NotificationHubs.iOS** e adicione o pacote ao seu projeto.

6. Adicione um novo arquivo à classe, nomeie-o com `Constants.cs` e adicione as seguintes variáveis e substitua os espaços reservados da cadeia de caracteres literal pelo `hubname` e o `DefaultListenSharedAccessSignature` que você anotou anteriormente.

    ```csharp
    // Azure app-specific connection string and hub path
    public const string ListenConnectionString = "<Azure DefaultListenSharedAccess Connection String>";
    public const string NotificationHubName = "<Azure Notification Hub Name>";
    ```

7. No `AppDelegate.cs`, adicione o seguinte usando a instrução:

    ```csharp
    using WindowsAzure.Messaging.NotificationHubs;
    using UserNotifications
    ```

8. Crie uma implementação do `MSNotificationHubDelegate` no `AppDelegate.cs`:

    ```csharp
    public class AzureNotificationHubListener : MSNotificationHubDelegate
    {
        public override void DidReceivePushNotification(MSNotificationHub notificationHub, MSNotificationHubMessage message)
        {

        }
    }
    ```

9. No `AppDelegate.cs`, atualize `FinishedLaunching()` para coincidir com o código a seguir:

    ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // Set the Message listener
        MSNotificationHub.SetDelegate(new AzureNotificationHubListener());
        
        // Start the SDK
        MSNotificationHub.Start(ListenConnectionString, NotificationHubName);

        return true;
    }
    ```

10. Em `AppDelegate.cs`, implemente o método `DidReceivePushNotification` para a classe `AzureNotificationHubListener`:

    ```csharp
    public override void DidReceivePushNotification(MSNotificationHub notificationHub, MSNotificationHubMessage message)
    {
        // This sample assumes { aps: { alert: { title: "Hello", body: "World" } } }
        var alertTitle = message.Title ?? "Notification";
        var alertBody = message.Body;

        var myAlert = UIAlertController.Create(alertTitle, alertBody, UIAlertControllerStyle.Alert);
        myAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
        UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(myAlert, true, null);
    }
    ```

11. Execute o aplicativo em seu dispositivo.

## <a name="send-test-push-notifications"></a>Enviar notificações por push de teste

Você pode testar o recebimento de notificações no aplicativo com a opção *Envio de Teste* no [Azure portal]. Isso envia uma notificação por push de teste para seu dispositivo.

![Portal do Azure – Envio de Teste][30]

As notificações por push normalmente são enviadas em um serviço de back-end como Aplicativos Móveis ou ASP.NET usando uma biblioteca compatível. Se uma biblioteca não estiver disponível para o seu back-end, você também poderá usar a API REST diretamente para enviar mensagens de notificação.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você envia notificações de transmissão para todos os seus dispositivos iOS registrados com o back-end. Para saber como enviar notificações por push para dispositivos iOS específicos, vá para o tutorial a seguir:

> [!div class="nextstepaction"]
>[Notificações por push para especificar dispositivos](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png
[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png
[33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-entitlements-settings.png

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio para Mac]: https://visualstudio.microsoft.com/vs/mac/
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Apple Push Notification Service fwlink]: https://go.microsoft.com/fwlink/p/?LinkId=272584
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Azure portal]: https://portal.azure.com
