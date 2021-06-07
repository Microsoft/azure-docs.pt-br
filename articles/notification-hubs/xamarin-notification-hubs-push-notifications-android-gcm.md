---
title: Enviar notificações por push para aplicativos Xamarin.Android usando os Hubs de Notificação do Azure | Microsoft Docs
description: Neste tutorial, você aprende a usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo Xamarin Android.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/12/2021
ms.author: matthewp
ms.reviewer: jowargo
ms.lastreviewed: 08/01/2019
ms.openlocfilehash: c97da77619a395a8e2839ea672fe7a78f0501c47
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575829"
---
# <a name="tutorial-send-push-notifications-to-xamarinandroid-apps-using-notification-hubs"></a>Tutorial: Enviar notificações por push para aplicativos Xamarin.Android usando os Hubs de Notificação

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Visão geral

Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo Xamarin.Android. Você cria um aplicativo Xamarin.Android em branco que recebe notificações por push usando o FCM (Firebase Cloud Messaging). Você usa seu hub de notificação para transmitir notificações por push a todos os dispositivos que executam seu aplicativo. O código concluído está disponível na amostra do [aplicativo NotificationHubs](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/Xamarin/GetStartedXamarinAndroid).

Neste tutorial, você deve executar as seguintes etapas:

> [!div class="checklist"]
> * Criar um projeto de Firebase e habilitar Firebase Cloud Messaging
> * Criar um hub de notificação
> * Criar um aplicativo Xamarin.Android e conectar-se ao hub de notificação
> * Enviar notificações por push de teste do portal do Azure

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**. Caso você não tenha uma assinatura do Azure, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* [Visual Studio com Xamarin] no Windows ou [Visual Studio para Mac] no OS X.
* Conta ativa do Google

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Criar um projeto de Firebase e habilitar Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging-xamarin.md)]

## <a name="create-a-notification-hub"></a>Criar um hub de notificação

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcmfcm-settings-for-the-notification-hub"></a>Configurar as definições do GCM/FCM para o hub de notificação

1. Selecione **Google (GCM/FCM)** na seção **Configurações** no menu à esquerda.
2. Insira a **chave do servidor** que você anotou do Google Firebase Console.
3. Selecione **Salvar** na barra de ferramentas.

    ![Captura de tela do hub de notificação no portal do Azure com a opção G C M F C M do Google realçada e delineada em vermelho.](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Seu hub de notificação está configurado para funcionar com o FCM e você tem as cadeias de conexão para registrar o aplicativo para receber notificações e enviar notificações por push.

## <a name="create-a-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Criar um aplicativo Xamarin.Android e conectá-lo ao hub de notificação

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Criar um projeto do Visual Studio e adicionar pacotes NuGet

> [!NOTE]
> As etapas documentadas neste tutorial são para o Visual Studio 2017. 

1. No Visual Studio, abra o menu **Arquivo**, selecione **Novo** e, em seguida, **Projeto**. Na janela **Novo Projeto**, execute estas etapas:
    1. Expanda **Instalado**, **Visual C#** e clique em **Android**.
    2. Selecione **aplicativo Android (Xamarin)** na lista.
    3. Insira um **nome** para o projeto.
    4. Selecione um **local** para o projeto.
    5. Selecione **OK**

        ![Caixa de diálogo Novo Projeto](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog-new.png)
2. Na caixa de diálogo **Novo aplicativo Android**, selecione **Aplicativo em branco** e selecione **OK**.

    ![Captura de tela que realça o modelo Aplicativo em Branco.](./media/partner-xamarin-notification-hubs-android-get-started/new-android-app-dialog.png)
3. Na janela do **Gerenciador de Soluções**, expanda **Propriedades** e clique em **AndroidManifest.xml**. Atualize o nome do pacote para corresponder ao nome do pacote inserido ao adicionar Firebase Cloud Messaging ao seu projeto no Google Firebase Console.

    ![Nome do pacote no GMC](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
4. Defina a versão de destino do Android para o projeto como **Android 10.0** seguindo estas etapas: 
    1. Clique com o botão direito do mouse no projeto e selecione **Propriedades**. 
    1. Para o campo **Compilar usando a versão do Android: (estrutura de destino)** , selecione **Android 10.0**. 
    1. Selecione **Sim** na caixa de mensagem para continuar com a alteração da estrutura de destino.
1. Adicione os pacotes NuGet necessários ao projeto seguindo estas etapas:
    1. Clique o botão direito do mouse no projeto e selecione **Gerenciar Pacotes NuGet...** .
    1. Alterne para a guia **Instalado**, selecione **Xamarin.Android.Support.Design** e selecione **Atualizar** no painel direito para atualizar o pacote para a versão mais recente.
    1. Alterne para a guia **Procurar**. Pesquise por **Xamarin.GooglePlayServices.Base**. Selecione **Xamarin.GooglePlayServices.Base** na lista de resultados. Em seguida, selecione **Instalar**.

        ![Google Play Services NuGet](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
    6. Na janela do **Gerenciador de Pacotes do NuGet**, pesquise por **Xamarin.Firebase.Messaging**. Selecione **Xamarin.Firebase.Messaging** na lista de resultados. Em seguida, selecione **Instalar**.
    7. Agora, pesquise por **Xamarin.Azure.NotificationHubs.Android**. Selecione **Xamarin.Azure.NotificationHubs.Android** na lista de resultados. Em seguida, selecione **Instalar**.

### <a name="add-the-google-services-json-file"></a>Adicione o arquivo JSON de Serviços do Google

1. Copie o arquivo `google-services.json` que você baixou do Google Firebase Console para a pasta do projeto.
2. Adicione `google-services.json` ao projeto.
3. Selecione `google-services.json` na janela **Gerenciador de Soluções**.
4. No painel **Propriedades**, defina a ação de compilação como **GoogleServicesJson**. Se você não vir **GoogleServicesJson**, feche o Visual Studio, reinicialize-o, reabra o projeto e tente novamente.

    ![Ação de compilação GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Configurar hubs de notificação em seu projeto

#### <a name="registering-with-firebase-cloud-messaging"></a>Registrando com o Firebase Cloud Messaging

1. Se você estiver fazendo a migração do Google Cloud Messaging para o Firebase, o arquivo `AndroidManifest.xml` do projeto poderá conter uma configuração desatualizada do GCM, o que poderá causar a duplicação de notificação. Edite o arquivo e remova as seguintes linhas dentro da seção `<application>`, se presente:

    ```xml
    <receiver
        android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
        android:exported="false" />
    <receiver
        android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
        android:exported="true"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

2. Adicione as seguintes instruções **antes do elemento** do aplicativo.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    ```

3. Colete as informações a seguir para o aplicativo Android e o hub de notificação:

   * **Escutar cadeia de conexão**: no painel, no [Azure portal], escolha **Exibir cadeias de conexão**. Copie a cadeia de conexão `DefaultListenSharedAccessSignature` para esse valor.
   * **Nome do hub**: nome do seu hub no [Azure portal]. Por exemplo, *mynotificationhub2*.
4. Na janela **Gerenciador de Soluções**, clique com o botão direito do mouse no **projeto**, selecione **Adicionar** e, em seguida, **Classe**.
5. Crie uma classe `Constants.cs` para seu projeto Xamarin e defina os valores de constantes a seguir na classe. Substitua os espaços reservados pelos seus valores.

    ```csharp
    public static class Constants
    {
        public const string ListenConnectionString = "<Listen connection string>";
        public const string NotificationHubName = "<hub name>";
    }
    ```

6. Adicione o seguinte usando as instruções para `MainActivity.cs`:

    ```csharp
    using WindowsAzure.Messaging.NotificationHubs;
    ```

7. Adicione as seguintes propriedades à classe MainActivity:

    ```csharp
    internal static readonly string CHANNEL_ID = "my_notification_channel";

8. In `MainActivity.cs`, add the following code to `OnCreate` after `base.OnCreate(savedInstanceState)`:

    ```csharp
    // Listen for push notifications
    NotificationHub.SetListener(new AzureListener());

    // Start the SDK
    NotificationHub.Start(this.Application, HubName, ConnectionString);
    ```

9. Adicione uma classe denominada `AzureListener` ao projeto.
10. Adicione o seguinte usando as instruções para `AzureListener.cs`.

    ```csharp
    using Android.Content;
    using WindowsAzure.Messaging.NotificationHubs;
    ```

11. Adicione o código acima da declaração de classe e faça com que a classe herde de `Java.Lang.Object` e implemente o `INotificationListener`:

    ```csharp
    public class AzureListener : Java.Lang.Object, INotificationListener
    ```

12. Adicione o código a seguir à classe `MyFirebaseMessagingService` para processar as mensagens recebidas.

    ```csharp
        public void OnPushNotificationReceived(Context context, INotificationMessage message)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);
    
            var notificationBuilder = new NotificationCompat.Builder(this, MainActivity.CHANNEL_ID);
    
            notificationBuilder.SetContentTitle(message.Title)
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(message.Body)
                        .SetAutoCancel(true)
                        .SetShowWhen(false)
                        .SetContentIntent(pendingIntent);
    
            var notificationManager = NotificationManager.FromContext(this);
    
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

1. **Compile** o seu novo projeto.
1. **Executar** aplicativo no dispositivo ou emulador carregado

## <a name="send-test-notification-from-the-azure-portal"></a>Enviar notificação por push de teste do portal do Azure

Você pode testar o recebimento de notificações no aplicativo com a opção **Envio de Teste** no [Azure portal]. Isso envia uma notificação por push de teste para seu dispositivo.

![Portal do Azure – Envio de Teste](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

As notificações por push são geralmente enviadas em um serviço back-end, como Serviços Móveis ou ASP.NET, por meio de uma biblioteca compatível. Se uma biblioteca não estiver disponível para o back-end, você também poderá usar a API REST diretamente para enviar mensagens de notificação.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você envia notificações de transmissão para todos os seus dispositivos Android registrados com o back-end. Para saber como enviar notificações por push para dispositivos Android específicos, vá para o tutorial a seguir:

> [!div class="nextstepaction"]
>[Notificações por push para especificar dispositivos](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio com Xamarin]: /visualstudio/install/install-visual-studio
[Visual Studio para Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Azure portal]: https://portal.azure.com/
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Android]: /previous-versions/azure/dn282661(v=azure.100)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-android
