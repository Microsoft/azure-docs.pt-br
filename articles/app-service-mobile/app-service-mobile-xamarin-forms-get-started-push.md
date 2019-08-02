---
title: Adicionar notificações por push ao aplicativo Xamarin.Forms | Microsoft Docs
description: Aprenda a usar os serviços do Azure para enviar notificações por push aos aplicativos Xamarin.Forms.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: b7e2ff63211ec5891a48a585e4f69e18116cdeb3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446564"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Adicionar notificações por push ao aplicativo Xamarin.Forms

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center está investindo em novos e integrados serviços essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **construir**, **teste** e **distribuir** services para configurar o pipeline de integração contínua e entrega. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso do seu aplicativo usando o **Analytics** e **diagnóstico** serviços e entre em contato com usuários usando o **enviar por Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados do aplicativo na nuvem. Fazer check-out [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-forms-get-started-push) hoje mesmo.
>

## <a name="overview"></a>Visão geral

Neste tutorial, você adiciona notificações por push a todos os projetos resultantes do [início rápido do Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Isso significa que uma notificação por push é enviada para todos os clientes de plataforma cruzada sempre que um registro é inserido.

Se você não usar o projeto baixado do início rápido do servidor, deve adicionar o pacote de extensão de notificação por push ao seu projeto. Para saber mais, veja [Trabalhar com o SDK do servidor de back-end do .NET para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Para o iOS, você precisará de uma [associação ao Programa do Desenvolvedor da Apple](https://developer.apple.com/programs/ios/) e um dispositivo iOS físico. O [simulador do iOS não dá suporte a notificações por push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Configurar um Hub de Notificação

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Atualizar o projeto de servidor para enviar notificações por push

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Configurar e executar o projeto do Android (opcional)

Conclua esta seção para habilitar notificações por push para o projeto Droid Xamarin.Forms para Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Habilitar FCM (mensagens de nuvem Firebase)

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Configurar o back-end dos Aplicativos Móveis para enviar solicitações por push usando o FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Adicionar notificações por push ao projeto Droid

Com o back-end configurado com o FCM, é possível adicionar componentes e códigos ao cliente para registrá-lo no FCM. Também é possível se registrar para receber notificações por push com os Hubs de Notificação do Azure por meio do back-end dos Aplicativos Móveis e receber as notificações.

1. No projeto **Droid**, clique com o botão direito do mouse em **Referências > Gerenciar pacotes NuGet...** .
1. Na janela do Gerenciador de Pacotes do NuGet, procure o pacote **Xamarin.Firebase.Messaging** e adicione-o ao projeto.
1. Nas propriedades do projeto para o projeto **Droid**, configure o aplicativo para compilar usando o Android versão 7.0 ou superior.
1. Adicione o arquivo **google-services.json** baixado do console Firebase na raiz do projeto **Droid** e defina sua ação de compilação ao **GoogleServicesJson**. Para obter mais informações, consulte [Adicionar o arquivo JSON de serviços do Google](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

#### <a name="registering-with-firebase-cloud-messaging"></a>Registrando com o Firebase Cloud Messaging

1. Abra o arquivo **AndroidManifest.xml** e insira os seguintes elementos `<receiver>` no elemento `<application>`:

    ```xml
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

#### <a name="implementing-the-firebase-instance-id-service"></a>Implementando o serviço de ID da instância do Firebase

1. Adicione uma nova classe ao projeto **Droid** chamada `FirebaseRegistrationService` e verifique se as instruções de `using` a seguir estão presentes na parte superior do arquivo:

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. Substitua a classe `FirebaseRegistrationService` vazia pelo seguinte código:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class FirebaseRegistrationService : FirebaseInstanceIdService
    {
        const string TAG = "FirebaseRegistrationService";

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationTokenToAzureNotificationHub(refreshedToken);
        }

        void SendRegistrationTokenToAzureNotificationHub(string token)
        {
            // Update notification hub registration
            Task.Run(async () =>
            {
                await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
            });
        }
    }
    ```

    A classe `FirebaseRegistrationService` é responsável pela geração de tokens de segurança que autorizam o aplicativo a acessar o FCM. O método `OnTokenRefresh` é invocado quando o aplicativo recebe um token de registro do FCM. O método recupera o token a partir de `FirebaseInstanceId.Instance.Token` propriedade, que é atualizada de forma assíncrona pelo FCM. O `OnTokenRefresh` método é invocado com pouca frequência, porque o token é atualizado somente quando o aplicativo é instalado ou desinstalado, quando o usuário exclui dados de aplicativos, quando o aplicativo apaga a ID da instância, ou quando a segurança do token tiver sido comprometido. Além disso, o serviço de ID de instância do FCM solicita que o aplicativo atualize seu token periodicamente, normalmente a cada 6 meses.

    O `OnTokenRefresh` também invoca um método de `SendRegistrationTokenToAzureNotificationHub` método, que é usado para associar o token de registro do usuário com o Hub de notificação do Azure.

#### <a name="registering-with-the-azure-notification-hub"></a>Registrando com Hub de Notificação do Azure

1. Adicione uma nova classe ao projeto **Droid** chamada `AzureNotificationHubService` e verifique se as instruções de `using` a seguir estão presentes na parte superior do arquivo:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. Substitua a classe `AzureNotificationHubService` vazia pelo seguinte código:

    ```csharp
    public class AzureNotificationHubService
    {
        const string TAG = "AzureNotificationHubService";

        public static async Task RegisterAsync(Push push, string token)
        {
            try
            {
                const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBody}
                };

                await push.RegisterAsync(token, templates);
                Log.Info("Push Installation Id: ", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
            }
        }
    }
    ```

    O método `RegisterAsync` cria um modelo de mensagem de notificação simples como JSON e se registra para receber notificações de modelo do hub de notificação usando o token de registro do Firebase. Isso garante que todas as notificações enviadas a partir do Hub de Notificação do Azure terão como destino o dispositivo representado pelo token de registro.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Exibindo o conteúdo de uma notificação por Push

1. Adicione uma nova classe ao projeto **Droid** chamada `FirebaseNotificationService` e verifique se as instruções de `using` a seguir estão presentes na parte superior do arquivo:

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. Substitua a classe `FirebaseNotificationService` vazia pelo seguinte código:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class FirebaseNotificationService : FirebaseMessagingService
    {
        const string TAG = "FirebaseNotificationService";

        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);

            // Pull message body out of the template
            var messageBody = message.Data["message"];
            if (string.IsNullOrWhiteSpace(messageBody))
                return;

            Log.Debug(TAG, "Notification message body: " + messageBody);
            SendNotification(messageBody);
        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new NotificationCompat.Builder(this)
                .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                .SetContentTitle("New Todo Item")
                .SetContentText(messageBody)
                .SetContentIntent(pendingIntent)
                .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
                .SetAutoCancel(true);

            var notificationManager = NotificationManager.FromContext(this);
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    }
    ```

    O método `OnMessageReceived`, que é invocado quando um aplicativo recebe uma notificação do FCM, extrai o conteúdo da mensagem e chama o método `SendNotification`. Este método converte o conteúdo da mensagem em uma notificação de local que é iniciada enquanto o aplicativo é executado, com a notificação que aparece na área de notificação.

Agora, você está pronto para testar as notificações por push no aplicativo em execução em um dispositivo ou no emulador Android.

### <a name="test-push-notifications-in-your-android-app"></a>Testar notificações por push em seu aplicativo Android

As duas primeiras etapas serão necessárias apenas quando o teste estiver sendo feito em um emulador.

1. Certifique-se de que está implantando ou depurando em um dispositivo ou emulador configurado com o Google Play Services. Isso pode ser confirmado, verificando se os aplicativos **Play** estão instalados no dispositivo ou emulador.
2. Adicione uma conta do Google ao dispositivo Android clicando em **Aplicativos** > **Configurações** > **Adicionar conta**. Depois, siga os prompts para adicionar uma conta existente do Google ao dispositivo ou para criar uma nova.
3. No Visual Studio ou Xamarin Studio, clique com o botão direito do mouse no projeto **Droid** e clique em **Definir como projeto de inicialização**.
4. Clique em **Executar** para criar o projeto e iniciar o aplicativo no emulador ou no dispositivo Android.
5. No aplicativo, digite uma tarefa e clique no ícone do sinal de adição ( **+** ).
6. Verifique se uma notificação é recebida quando um item é adicionado.

## <a name="configure-and-run-the-ios-project-optional"></a>Configurar e executar o projeto do iOS (opcional)

Esta seção trata da execução do projeto do iOS Xamarin para dispositivos iOS. Você poderá ignorá-la se não estiver trabalhando com dispositivos iOS.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Configurar o hub de notificação para APNS

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

A seguir, você definirá a configuração do projeto do iOS no Xamarin Studio ou Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Adicionar as notificações por push ao seu aplicativo iOS

1. No projeto **iOS**, abra AppDelegate.cs e adicione a instrução a seguir à parte superior do arquivo de código.

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. Na classe **AppDelegate**, adicione também uma substituição ao evento **RegisteredForRemoteNotifications** a fim de registrar para o recebimento notificações:

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application,
        NSData deviceToken)
    {
        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

        // Register for push with your mobile app
        Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

3. Em **AppDelegate**, adicione também a seguinte substituição para o manipulador de eventos **DidReceiveRemoteNotification**:

    ```csharp
    public override void DidReceiveRemoteNotification(UIApplication application,
        NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps[new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

    Este método trata as notificações recebidas enquanto o aplicativo está em execução.

4. Na classe **AppDelegate**, adicione o seguinte código ao método **FinishedLaunching**:

    ```csharp
    // Register for push notifications.
    var settings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert
        | UIUserNotificationType.Badge
        | UIUserNotificationType.Sound,
        new NSSet());

    UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications();
    ```

    Isso habilita o suporte para notificações remotas e solicitações de registro por push.

Seu aplicativo foi atualizado para dar suporte a notificações por push.

#### <a name="test-push-notifications-in-your-ios-app"></a>Testar notificações por push em seu aplicativo iOS

1. Clique com o botão direito do mouse no projeto do iOS e, depois, clique em **Definir como Projeto de Inicialização**.
2. Pressione o botão **Executar** ou **F5** no Visual Studio para criar o projeto e iniciar o aplicativo em um dispositivo iOS. Em seguida, clique em **OK** para aceitar as notificações por push.

   > [!NOTE]
   > Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.

3. No aplicativo, digite uma tarefa e clique no ícone do sinal de adição ( **+** ).
4. Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.

## <a name="configure-and-run-windows-projects-optional"></a>Configurar e executar projetos do Windows (opcional)

Esta seção trata da execução dos projetos WinApp e WinPhone81 de Xamarin.Forms para dispositivos Windows. Estas etapas também oferecem suporte a projetos da Plataforma Universal do Windows (UWP). Você poderá ignorá-la se não estiver trabalhando com dispositivos Windows.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Registrar o aplicativo Windows para receber notificações por push com o WNS (Serviço de Notificação do Windows)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Configurar o hub de notificação para WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Adicionar notificações por push ao seu aplicativo do Windows

1. No Visual Studio, abra **App.xaml.cs** em um projeto do Windows e adicione as instruções a seguir.

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    Substitua `<your_TodoItemManager_portable_class_namespace>` pelo namespace do projeto portátil que contém a classe `TodoItemManager`.

2. No App.xaml.cs, adicione o seguinte método **InitNotificationsAsync**:

    ```csharp
    private async Task InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        const string templateBodyWNS =
            "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        JObject headers = new JObject();
        headers["X-WNS-Type"] = "wns/toast";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyWNS},
            {"headers", headers} // Needed for WNS.
        };

        await TodoItemManager.DefaultManager.CurrentClient.GetPush()
            .RegisterAsync(channel.Uri, templates);
    }
    ```

    Esse método obtém o canal de notificação por push e registra um modelo para receber as notificações de modelo do hub de notificação. Uma notificação de modelo que oferece suporte a *messageParam* será entregue a esse cliente.

3. No App.xaml.cs, atualize a definição de método do manipulador de eventos **OnLaunched** adicionando o modificador `async`. Depois, adicione a seguinte linha de código ao final do método:

    ```csharp
    await InitNotificationsAsync();
    ```

    Isso garante que o registro de notificação por push é criado ou atualizado sempre que o aplicativo é iniciado. É importante fazer isso para garantir que o canal de notificação por push WNS esteja sempre ativo.  

4. No Gerenciador de Soluções do Visual Studio, abra o arquivo **Package.appxmanifest** e defina **Compatível com Notificação do Sistema** como **Sim** em **Notificações**.
5. Compile o aplicativo e verifique se não há erros. O aplicativo cliente agora deve se registrar para receber notificações de modelo do back-end dos Aplicativos Móveis. Repita esta seção para cada projeto do Windows em sua solução.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testar as notificações por push em seu aplicativo para Windows

1. No Visual Studio, clique com o botão direito do mouse no projeto do Windows e clique em **Definir como projeto de inicialização**.
2. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo.
3. No aplicativo, digite um nome para um novo todoitem e, em seguida, clique no ícone de adição ( **+** ) para adicioná-lo.
4. Verifique se uma notificação é recebida quando o item é adicionado.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as notificações por push:

* [Enviar notificações por push a partir de aplicativos móveis do Azure](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Notificações remotas com o Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Diagnosticar problemas com notificações por push](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  há vários motivos por que as notificações podem ser abandonadas ou não irem terminar nos dispositivos. Este tópico mostra como analisar e descobrir a causa de falhas de notificação por push.

Continue também com um dos seguintes tutoriais:

* [Adicionar autenticação ao seu aplicativo](app-service-mobile-xamarin-forms-get-started-users.md)  
  Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.
* [Habilitar sincronização offline para seu aplicativo](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Saiba como adicionar suporte offline para o aplicativo usando um back-end dos Aplicativos Móveis. Com a sincronização offline, os usuários podem interagir com um aplicativo móvel &mdash; exibindo, adicionando ou modificando dados &mdash; mesmo quando não há nenhuma conexão de rede.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
