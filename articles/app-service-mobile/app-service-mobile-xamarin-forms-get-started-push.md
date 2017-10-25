---
title: "Adicionar notificações por push ao aplicativo Xamarin.Forms | Microsoft Docs"
description: "Aprenda a usar os serviços do Azure para enviar notificações por push aos aplicativos Xamarin.Forms."
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: syntaxc4
editor: 
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
ms.openlocfilehash: 912367636f1b26b3b07fbd5fe3fe8ed053218fd5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Adicionar notificações por push ao aplicativo Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

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

1. No projeto **Droid**, clique com o botão direito do mouse na pasta **Componentes** e clique em **Obter Mais Componentes...**. Depois, pesquise o componente **Cliente do Google Cloud Messaging** e adicione-o ao projeto. Esse componente oferece suporte a notificações por push para um projeto Android Xamarin.
2. Abra o arquivo de projeto MainActivity.cs e adicione a seguinte instrução à parte superior do arquivo:

        using Gcm.Client;
3. Adicione o código a seguir ao método **OnCreate**, após a chamada a **LoadApplication**:

        try
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
        }
        catch (Java.Net.MalformedURLException)
        {
            CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
        }
        catch (Exception e)
        {
            CreateAndShowDialog(e.Message, "Error");
        }
4. Adicione um novo método auxiliar **CreateAndShowDialog** , desta maneira:

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage (message);
            builder.SetTitle (title);
            builder.Create().Show ();
        }
5. Adicione o código a seguir à classe **MainActivity** :

        // Create a new instance field for this activity.
        static MainActivity instance = null;

        // Return the current activity instance.
        public static MainActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }

    Isso expõe a instância **MainActivity** atual e, portanto, podemos executar no thread principal da interface do usuário.
6. Inicialize a variável `instance` no início do método **OnCreate** como mostrado a seguir.

        // Set the current instance of MainActivity.
        instance = this;
7. Adicione um novo arquivo de classe ao projeto **Droid** chamado `GcmService.cs` e verifique se as instruções **using** a seguir estão presentes na parte superior do arquivo:

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V4.App;
        using Android.Util;
        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Text;
8. Adicione as seguintes solicitações de permissão na parte superior do arquivo, após as instruções **using** e antes da declaração **namespace**.

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
9. Adicione a seguinte definição de classe ao namespace.

       [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
       public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
       {
           public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
       }

   > [!NOTE]
   > Substitua **<PROJECT_NUMBER>** pelo número do projeto que você anotou anteriormente.    
   >
   >
10. Substitua a classe **GcmService** vazia pelo código a seguir, que usa o novo receptor de difusão:

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }
11. Adicione o código a seguir à classe **GcmService**. Isso substitui o manipulador de eventos **OnRegistered** e implementa um método **Register**.

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

            MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
        }

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

    Observe que esse código usa o parâmetro `messageParam` no registro do modelo.
12. Adicione o seguinte código que implementa **OnMessage**:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

    Isso manipula as notificações recebidas e as envia para o gerenciador de notificações a ser exibido.
13. **GcmServiceBase** também exige que você implemente os métodos de manipulador **OnUnRegistered** e **OnError**, o que pode ser feito da seguinte maneira:

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
        }

Agora, você está pronto para testar as notificações por push no aplicativo em execução em um dispositivo ou no emulador Android.

### <a name="test-push-notifications-in-your-android-app"></a>Testar notificações por push em seu aplicativo Android
As duas primeiras etapas serão necessárias apenas quando o teste estiver sendo feito em um emulador.

1. Verifique se você está implantando ou depurando em um dispositivo virtual que tem as APIs do Google definidas como o destino, como mostrado abaixo no gerenciador de Dispositivo Virtual do Android.
2. Adicione uma conta do Google ao dispositivo Android clicando em **Aplicativos** > **Configurações** > **Adicionar conta**. Depois, siga os prompts para adicionar uma conta existente do Google ao dispositivo ou para criar uma nova.
3. No Visual Studio ou Xamarin Studio, clique com o botão direito do mouse no projeto **Droid** e clique em **Definir como projeto de inicialização**.
4. Clique em **Executar** para criar o projeto e iniciar o aplicativo no emulador ou no dispositivo Android.
5. No aplicativo, digite uma tarefa e clique no ícone do sinal de adição (**+**).
6. Verifique se uma notificação é recebida quando um item é adicionado.

## <a name="configure-and-run-the-ios-project-optional"></a>Configurar e executar o projeto do iOS (opcional)
Esta seção trata da execução do projeto do iOS Xamarin para dispositivos iOS. Você poderá ignorá-la se não estiver trabalhando com dispositivos iOS.

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Configurar o hub de notificação para APNS
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

A seguir, você definirá a configuração do projeto do iOS no Xamarin Studio ou Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Adicionar as notificações por push ao seu aplicativo iOS
1. No projeto **iOS**, abra AppDelegate.cs e adicione a instrução a seguir à parte superior do arquivo de código.

        using Newtonsoft.Json.Linq;
2. Na classe **AppDelegate**, adicione também uma substituição ao evento **RegisteredForRemoteNotifications** a fim de registrar para o recebimento notificações:

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
3. Em **AppDelegate**, adicione também a seguinte substituição para o manipulador de eventos **DidReceiveRemoteNotification**:

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

    Este método trata as notificações recebidas enquanto o aplicativo está em execução.
4. Na classe **AppDelegate**, adicione o seguinte código ao método **FinishedLaunching**:

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    Isso habilita o suporte para notificações remotas e solicitações de registro por push.

Seu aplicativo foi atualizado para oferecer suporte a notificações de push.

#### <a name="test-push-notifications-in-your-ios-app"></a>Testar notificações por push em seu aplicativo iOS
1. Clique com o botão direito do mouse no projeto do iOS e, depois, clique em **Definir como Projeto de Inicialização**.
2. Pressione o botão **Executar** ou **F5** no Visual Studio para criar o projeto e iniciar o aplicativo em um dispositivo iOS. Em seguida, clique em **OK** para aceitar as notificações por push.

   > [!NOTE]
   > Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.
   >
   >
3. No aplicativo, digite uma tarefa e clique no ícone do sinal de adição (**+**).
4. Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.

## <a name="configure-and-run-windows-projects-optional"></a>Configurar e executar projetos do Windows (opcional)
Esta seção trata da execução dos projetos WinApp e WinPhone81 de Xamarin.Forms para dispositivos Windows. Estas etapas também oferecem suporte a projetos da Plataforma Universal do Windows (UWP). Você poderá ignorá-la se não estiver trabalhando com dispositivos Windows.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Registrar o aplicativo Windows para receber notificações por push com o WNS (Serviço de Notificação do Windows)
[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Configurar o hub de notificação para WNS
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Adicionar notificações por push ao seu aplicativo do Windows
1. No Visual Studio, abra **App.xaml.cs** em um projeto do Windows e adicione as instruções a seguir.

        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using <your_TodoItemManager_portable_class_namespace>;

    Substitua `<your_TodoItemManager_portable_class_namespace>` pelo namespace do projeto portátil que contém a classe `TodoItemManager`.
2. No App.xaml.cs, adicione o seguinte método **InitNotificationsAsync**:

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

    Esse método obtém o canal de notificação por push e registra um modelo para receber as notificações de modelo do hub de notificação. Uma notificação de modelo que oferece suporte a *messageParam* será entregue a esse cliente.
3. No App.xaml.cs, atualize a definição de método do manipulador de eventos **OnLaunched** adicionando o modificador `async`. Depois, adicione a seguinte linha de código ao final do método:

        await InitNotificationsAsync();

    Isso garante que o registro de notificação por push é criado ou atualizado sempre que o aplicativo é iniciado. É importante fazer isso para garantir que o canal de notificação por push WNS esteja sempre ativo.  
4. No Gerenciador de Soluções do Visual Studio, abra o arquivo **Package.appxmanifest** e defina **Compatível com Notificação do Sistema** como **Sim** em **Notificações**.
5. Compile o aplicativo e verifique se não há erros. O aplicativo cliente agora deve se registrar para receber notificações de modelo do back-end dos Aplicativos Móveis. Repita esta seção para cada projeto do Windows em sua solução.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testar as notificações por push em seu aplicativo para Windows
1. No Visual Studio, clique com o botão direito do mouse no projeto do Windows e clique em **Definir como projeto de inicialização**.
2. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo.
3. No aplicativo, digite um nome para um novo todoitem e, em seguida, clique no ícone de adição (**+**) para adicioná-lo.
4. Verifique se uma notificação é recebida quando o item é adicionado.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre as notificações por push:

* [Diagnosticar problemas com notificações por push](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  há vários motivos por que as notificações podem ser abandonadas ou não irem terminar nos dispositivos. Este tópico mostra como analisar e descobrir a causa de falhas de notificação por push.

Continue também com um dos seguintes tutoriais:

* [Adicionar autenticação ao seu aplicativo ](app-service-mobile-xamarin-forms-get-started-users.md)  
  Saiba como autenticar os usuários do aplicativo com um provedor de identidade.
* [Habilitar sincronização offline para seu aplicativo](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Saiba como adicionar suporte offline para o aplicativo usando um back-end dos Aplicativos Móveis. Com a sincronização offline, os usuários podem interagir com um aplicativo móvel &mdash; exibindo, adicionando ou modificando dados &mdash; mesmo quando não há nenhuma conexão de rede.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
