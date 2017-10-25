---
title: "Introdução aos Hubs de Notificação do Azure usando o Baidu | Microsoft Docs"
description: "Neste tutorial, você aprenderá a usar os Hubs de Notificação do Azure para enviar notificações por push aos dispositivos Android que usam o Baidu."
services: notification-hubs
documentationcenter: android
author: ysxu
manager: erikre
editor: 
ms.assetid: 23bde1ea-f978-43b2-9eeb-bfd7b9edc4c1
ms.service: notification-hubs
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: mobile-baidu
ms.workload: mobile
ms.date: 08/19/2016
ms.author: yuaxu
ms.openlocfilehash: df3bbda15e1245b6068c2b8290d0c96856051f1f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-notification-hubs-using-baidu"></a>Introdução aos Hubs de Notificação usando o Baidu
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Visão geral
O envio de nuvem Baidu é um serviço na nuvem chinês que você pode usar para enviar notificações por push para dispositivos móveis. Esse serviço é útil na China, onde a entrega de notificações por push para o Android é complexa devido à presença de diferentes lojas de aplicativos e serviços de notificações por push, bem como a disponibilidade dos dispositivos Android que não estão normalmente conectados ao GCM (Google Cloud Messaging).

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial exige:

* SDK do Android (supondo que você usa o Eclipse), que pode ser baixado no <a href="http://go.microsoft.com/fwlink/?LinkId=389797">site do Android</a>
* [SDK para Android de Serviços Móveis]
* [SDK do Android Push Baidu]

> [!NOTE]
> Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).
> 
> 

## <a name="create-a-baidu-account"></a>Criar uma conta de Baidu
Para usar o Baidu, você deve ter uma conta do Baidu. Se você já tiver uma, faça logon no [portal do Baidu] e vá para a próxima etapa. Caso contrário, veja as instruções abaixo sobre como criar uma conta do Baidu.  

1. Vá para o [portal do Baidu] e clique no link **登录** (**Login**). Clique em **立即注册** para iniciar o processo de registro de conta.
   
   ![][1]
2. Insira os detalhes necessários – telefone/endereço de email, senha e código de verificação – e clique em **Inscrever-se**.
   
   ![][2]
3. Você receberá um email no endereço de email inserido com um link para ativar sua conta do Baidu.
   
   ![][3]
4. Faça logon em sua conta de email, abra o email de ativação do Baidu e clique no link de ativação para ativar sua conta do Baidu.
   
   ![][4]

Depois de ativar uma conta do Baidu, faça logon no [portal do Baidu].

## <a name="register-as-a-baidu-developer"></a>Registrar-se como desenvolvedor Baidu
1. Depois de fazer logon no [portal do Baidu], clique em **更多 >>** (**mais**).
   
      ![][5]
2. Role para baixo na seção **站长与开发者服务 (Webmaster e Serviços de Desenvolvedor)** e clique em **百度开放云平台** (**Baidu - abrir a plataforma de nuvem**).
   
      ![][6]
3. Na página seguinte, clique em **开发者服务** (**Serviços de Desenvolvedor**) no canto superior direito.
   
      ![][7]
4. Na página seguinte, clique em **注册开发者** (**Desenvolvedores Registrados**) no menu no canto superior direito.
   
      ![][8]
5. Insira seu nome, uma descrição e um número de telefone celular para receber uma mensagem de texto de verificação e, em seguida, clique em **送验证码** (**Enviar Código de Verificação**). Para números de telefone internacionais, você precisa colocar o código do país entre parênteses. Por exemplo, para um número dos Estados Unidos, ele será **(1) 1234567890**.
   
      ![][9]
6. Em seguida, você deve receber uma mensagem de texto com um número de verificação, conforme mostrado no exemplo a seguir:
   
      ![][10]
7. Insira o número de verificação da mensagem no **验证码** (**Código de confirmação**).
8. Por fim, conclua o registro de desenvolvedor ao aceitar o Contrato de Baidu e clicar em **提交** (**Enviar**). Você verá a seguinte página na conclusão bem-sucedida do registro:
   
      ![][11]

## <a name="create-a-baidu-cloud-push-project"></a>Criar um projeto de envio na nuvem Baidu
Quando você cria um projeto de envio na nuvem Baidu, você recebe sua ID de aplicativo, chave API e a chave secreta.

1. Depois de fazer logon no [portal do Baidu], clique em **更多 >>** (**mais**).
   
      ![][5]
2. Role para baixo na seção **站长与开发者服务** (**Webmaster e Serviços de Desenvolvedor**) e clique em **百度开放云平台** (**Baidu - abrir a plataforma de nuvem**).
   
      ![][6]
3. Na página seguinte, clique em **开发者服务** (**Serviços de Desenvolvedor**) no canto superior direito.
   
      ![][7]
4. Na página seguinte, clique em **云推送** (**Push de nuvem**) na seção **云服务** (**Serviços de nuvem**).
   
      ![][12]
5. Quando você for um desenvolvedor registrado, verá **管理控制台** (**Console de Gerenciamento**) no menu superior. Clique em **开发者服务管理** (**Gerenciamento de Serviço de Desenvolvedores**).
   
      ![][13]
6. Na página seguinte, clique em **创建工程** (**Criar projeto**).
   
      ![][14]
7. Insira um nome de aplicativo e clique em **创建** (**Criar**).
   
      ![][15]
8. Após a criação bem-sucedida de um projeto de push da nuvem Baidu, você verá uma página com a **ID de Aplicativo**, **Chave de API** e **Chave Secreta**. Anote a chave de API e a chave secreta, que usaremos mais tarde.
   
      ![][16]
9. Configure o projeto para notificações por push clicando em **云推送** (**Push da nuvem**) no painel esquerdo.
   
      ![][31]
10. Na página seguinte, clique no botão **推送设置** (**Configurações de push**).
    
    ![][32]  
11. Na página de configuração, adicione o nome do pacote que você usará em seu projeto Android no campo **应用包名** (**Pacote de aplicativos**) e, em seguida, clique em **保存设置** (**Salvar**).  
    
    ![][33]

Você verá a **保存成功！** Mensagem (**salva com êxito!**).

## <a name="configure-your-notification-hub"></a>Configurar seu Hub de Notificação
1. Entre no [Portal Clássico do Azure]e clique em **+NOVO** na parte inferior da tela.
2. Clique em **Serviços de Aplicativos**, em **Barramento de Serviço**, em **Hub de Notificação** e em **Criação Rápida**.
3. Forneça um nome para o seu **Hub de Notificação**, selecione a **Região** e o **Namespace** onde esse hub de notificação será criado e depois clique em **Criar um Novo Hub de Notificação**.  
   
      ![][17]
4. Clique no namespace que você criou no hub de notificação e em **Hubs de Notificação** na parte superior.
   
      ![][18]
5. Selecione o hub de notificação que você criou e clique em **Configurar** no menu superior.
   
      ![][19]
6. Role para baixo até a seção **configurações de notificação do Baidu** e insira a chave de API e a chave secreta obtidas do console do Baidu anteriormente para o seu projeto de push de nuvem do Baidu. Clique em **Salvar**.
   
      ![][20]
7. Clique na guia **Painel** na parte superior do hub de notificação e depois clique em **Exibir Cadeia de Conexão**.
   
      ![][21]
8. Anote a **DefaultListenSharedAccessSignature** e a **DefaultFullSharedAccessSignature** na janela de **Informações de conexão de acesso**.
   
    ![][22]

## <a name="connect-your-app-to-the-notification-hub"></a>Conectar seu aplicativo ao hub de notificação
1. No Eclipse ADT, crie um novo projeto Android (**Arquivo** > **Novo** > **Projeto de Aplicativo Android**).
   
    ![][23]
2. Insira um **Nome de Aplicativo** e verifique se a versão do **SDK Mínimo Necessário** está definida como **API 16: Android 4.1**.
   
    ![][24]
3. Clique em **Avançar** e continue seguindo o assistente até que a janela **Criar Atividade** seja exibida. Verifique se **Atividade em branco** está selecionada e selecione **Concluir** para criar um novo Aplicativo do Android.
   
    ![][25]
4. Verifique se o **Destino da Compilação do Projeto** está definido corretamente.
   
    ![][26]
5. Baixe o arquivo notification-hubs-0.4.jar da guia **Arquivos** do [Notification-Hubs-Android-SDK no Bintray](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4). Adicione o arquivo à pasta **libs** do seu projeto Eclipse e atualize a pasta *libs* .
6. Baixe e descompacte o [SDK do Android Push Baidu], abra a pasta **libs** e copie o arquivo JAR **pushservice-x.y.z** e as pastas **armeabi** & **mips** na pasta **libs** de seu aplicativo do Android.
7. Abra o arquivo **AndroidManifest.xml** de seu projeto do Android e adicione as permissões exigidas pelo SDK do Baidu.
   
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.WRITE_SETTINGS" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
        <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
        <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />
8. Adicione a propriedade **android:name** ao elemento **application** em **AndroidManifest.xml** substituindo *yourprojectname* (por exemplo, **com.example.BaiduTest**). Verifique se esse nome de projeto corresponde ao projeto que você configurou no console do Baidu.
   
        <application android:name="yourprojectname.DemoApplication"
9. Adicione a seguinte configuração dentro do elemento de aplicativo após o elemento de atividade **.MainActivity** substituindo *yourprojectname* (por exemplo, **com.example.BaiduTest**):
   
        <receiver android:name="yourprojectname.MyPushMessageReceiver">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
                <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
                <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
            </intent-filter>
        </receiver>
   
        <receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
                <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
                <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            </intent-filter>
        </receiver>
   
        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>
        </receiver>
   
        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>
10. Adicione uma nova classe chamada **ConfigurationSettings.java** ao projeto.
    
     ![][28]
    
     ![][29]
11. Adicione os seguintes códigos a ela:
    
        public class ConfigurationSettings {
                public static String API_KEY = "...";
                public static String NotificationHubName = "...";
                public static String NotificationHubConnectionString = "...";
            }
    
    Defina o valor de **API_KEY** com o que você recuperou do projeto de nuvem do Baidu anteriormente, **NotificationHubName** com o nome do hub de notificação do Portal Clássico do Azure e **NotificationHubConnectionString** com a DefaultListenSharedAccessSignature do Portal Clássico do Azure.
12. Adicione uma nova classe chamada **DemoApplication.java**e adicione o seguinte código a ela:
    
        import com.baidu.frontia.FrontiaApplication;
    
        public class DemoApplication extends FrontiaApplication {
            @Override
            public void onCreate() {
                super.onCreate();
            }
        }
13. Adicione outra nova classe chamada **MyPushMessageReceiver.java**e adicione o código abaixo a ela. É a classe que manipula as notificações por push recebidas do servidor de push do Baidu.
    
        import java.util.List;
        import android.content.Context;
        import android.os.AsyncTask;
        import android.util.Log;
        import com.baidu.frontia.api.FrontiaPushMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub;
    
        public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
            /** TAG to Log */
            public static NotificationHub hub = null;
            public static String mChannelId, mUserId;
            public static final String TAG = MyPushMessageReceiver.class
                    .getSimpleName();
    
            @Override
            public void onBind(Context context, int errorCode, String appid,
                    String userId, String channelId, String requestId) {
                String responseString = "onBind errorCode=" + errorCode + " appid="
                        + appid + " userId=" + userId + " channelId=" + channelId
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
                mChannelId = channelId;
                mUserId = userId;
    
                try {
                    if (hub == null) {
                        hub = new NotificationHub(
                                ConfigurationSettings.NotificationHubName,
                                ConfigurationSettings.NotificationHubConnectionString,
                                context);
                        Log.i(TAG, "Notification hub initialized");
                    }
                } catch (Exception e) {
                   Log.e(TAG, e.getMessage());
                }
    
                registerWithNotificationHubs();
            }
    
            private void registerWithNotificationHubs() {
               new AsyncTask<Void, Void, Void>() {
                  @Override
                  protected Void doInBackground(Void... params) {
                     try {
                         hub.registerBaidu(mUserId, mChannelId);
                         Log.i(TAG, "Registered with Notification Hub - '"
                                 + ConfigurationSettings.NotificationHubName + "'"
                                 + " with UserId - '"
                                 + mUserId + "' and Channel Id - '"
                                 + mChannelId + "'");
                     } catch (Exception e) {
                         Log.e(TAG, e.getMessage());
                     }
                     return null;
                 }
               }.execute(null, null, null);
            }
    
            @Override
            public void onSetTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onSetTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }
    
            @Override
            public void onDelTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onDelTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }
    
            @Override
            public void onListTags(Context context, int errorCode, List<String> tags,
                    String requestId) {
                String responseString = "onListTags errorCode=" + errorCode + " tags="
                        + tags;
                Log.d(TAG, responseString);
            }
    
            @Override
            public void onUnbind(Context context, int errorCode, String requestId) {
                String responseString = "onUnbind errorCode=" + errorCode
                        + " requestId = " + requestId;
                Log.d(TAG, responseString);
            }
    
            @Override
            public void onNotificationClicked(Context context, String title,
                    String description, String customContentString) {
                String notifyString = "title=\"" + title + "\" description=\""
                        + description + "\" customContent=" + customContentString;
                Log.d(TAG, notifyString);
            }
    
            @Override
            public void onMessage(Context context, String message,
                    String customContentString) {
                String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
                Log.d(TAG, messageString);
            }
        }
14. Abra **MainActivity.java** e adicione o seguinte ao método **onCreate**:
    
            PushManager.startWork(getApplicationContext(),
                    PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);
15. Abra as seguintes instruções de importação na parte superior:
    
            import com.baidu.android.pushservice.PushConstants;
            import com.baidu.android.pushservice.PushManager;

## <a name="send-notifications-to-your-app"></a>Enviar notificações para seu aplicativo
Você pode testar rapidamente o recebimento de notificações em seu aplicativo ao enviar notificações no [Portal do Azure](https://portal.azure.com/) usando o botão **Enviar** no hub de notificação, como mostra a tela abaixo:

![](./media/notification-hubs-baidu-get-started/notification-hub-test-send-baidu.png)

Notificações por push normalmente são enviadas em um serviço de back-end como Serviços Móveis ou ASP.NET usando uma biblioteca compatível. Se a biblioteca não estiver disponível para seu back-end, você também poderá usar a API REST diretamente para enviar mensagens de notificação.

Neste tutorial, optamos pela simplicidade e só demonstraremos os testes do aplicativo cliente enviando notificações usando o SDK do .NET para hubs de notificação em um aplicativo de console em vez de um serviço de back-end. Recomendamos o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) como a próxima etapa de envio de notificações de back-end do ASP.NET. No entanto, as abordagens a seguir podem ser usadas para o envio de notificações:

* **Interface REST**: você pode dar suporte à notificação em qualquer plataforma de back-end usando a [Interface REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).
* **SDK do .NET dos Hubs de Notificações do Microsoft Azure**: no Gerenciador de Pacotes do Nuget para o Visual Studio, execute [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).
* **Node.js** : [Como usar os Hubs de Notificação de Node.js](notification-hubs-nodejs-push-notification-tutorial.md).
* **Aplicativos móveis**: para obter um exemplo de como enviar notificações de um back-end dos Aplicativos Móveis do Serviço de Aplicativo do Azure que esteja integrado com Hubs de notificação, confira [Adicionar notificação por push para seu aplicativo móvel](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).
* **Java/PHP**: para obter um exemplo de como enviar notificações usando as APIs REST, confira "Como usar os Hubs de Notificação do Java/PHP" ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

## <a name="optional-send-notifications-from-a-net-console-app"></a>(Opcional) Enviar notificações de um aplicativo de console do .NET.
Nesta seção, vamos mostrar o envio de uma notificação usando um aplicativo de console do .NET.

1. Crie um novo aplicativo de console do Visual C#:
   
    ![][30]
2. Na janela do Console do Gerenciador de Pacotes, defina o **Projeto padrão** como o seu novo projeto de aplicativo do console e execute o seguinte comando na janela do console:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    Essa instrução adiciona uma referência ao SDK dos Hubs de Notificação do Azure usando o <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacote NuGet Microsoft.Azure.Notification Hubs</a>.
   
    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)
3. Abra o arquivo **Program.cs** e adicione o seguinte usando a instrução:
   
        using Microsoft.Azure.NotificationHubs;
4. Na classe `Program`, adicione o método a seguir e substitua *DefaultFullSharedAccessSignatureSASConnectionString* e *NotificationHubName* pelos valores que você tem.
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
            string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
            var result = await hub.SendBaiduNativeNotificationAsync(message);
        }
5. Adicione as seguintes linhas ao método **Main** :
   
         SendNotificationAsync();
         Console.ReadLine();

## <a name="test-your-app"></a>Testar seu aplicativo
Para testar este aplicativo com um telefone real, basta conectá-lo ao seu computador com um cabo USB. Essa ação carregará seu aplicativo no telefone conectado.

Para testar este aplicativo com o emulador, na barra de ferramentas superior do Eclipse, clique em **Executar**e selecione seu aplicativo: ele inicia o emulador, carrega e executa o aplicativo.

O aplicativo recupera 'userId' e 'channelId' do serviço de notificação por push do Baidu e é registrado no hub de notificação.

Para enviar uma notificação de teste, você poderá usar a guia de depuração do Portal Clássico do Azure. Se você criar o aplicativo de console do .NET para o Visual Studio, bastará pressionar a tecla F5 no Visual Studio para executar o aplicativo. O aplicativo envia uma notificação que é exibida na área superior da notificação do seu dispositivo ou emulador.

<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[SDK para Android de Serviços Móveis]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[SDK do Android Push Baidu]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Portal Clássico do Azure]: https://manage.windowsazure.com/
[portal do Baidu]: http://www.baidu.com/
