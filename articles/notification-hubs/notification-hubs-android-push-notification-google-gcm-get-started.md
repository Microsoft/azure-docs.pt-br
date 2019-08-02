---
title: Notificações por push para aplicativos Android usando Hubs de Notificação do Azure e Google Cloud Messaging | Microsoft Docs
description: Neste tutorial, você aprende a usar os Hubs de Notificação do Azure e o Google Firebase Cloud Messaging para enviar notificações por push a dispositivos Android.
services: notification-hubs
documentationcenter: android
keywords: notificações por push, notificação por push, notificação por push do android
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 8268c6ef-af63-433c-b14e-a20b04a0342a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 341f5a30fe03de4c69b5a7e18703931988d2d185
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063625"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-cloud-messaging-deprecated"></a>Tutorial: Enviar notificações por push para dispositivos Android usando Hubs de Notificação do Microsoft Azure e o Google Cloud Messaging (preterido)

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

> [!WARNING]
> Em 10 de abril de 2018, o Google preteriu o GCM (Google Cloud Messaging). As APIs de cliente e servidor do GCM foram preteridas e serão removidas até 29 de maio de 2019. Para saber mais, confira [Perguntas frequentes sobre o GCM e o FCM](https://developers.google.com/cloud-messaging/faq).

## <a name="overview"></a>Visão geral

Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo Android.
Você cria um aplicativo para Android em branco que recebe notificações por push usando o GCM (Google Cloud Messaging).

> [!IMPORTANT]
> O GCM (Google Cloud Messaging) foi preterido e será removido [em breve](https://developers.google.com/cloud-messaging/faq).

> [!IMPORTANT]
> Este tópico demonstra as notificações por push com o Google Cloud Messaging (GCM). Se você estiver usando o Firebase Cloud Messaging (FCM) do Google, confira [Como enviar notificações por push para Android com Hubs de Notificação do Azure e FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

O código completo para este tutorial pode ser baixado no GitHub [clicando aqui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStarted).

Neste tutorial, você executa as seguintes ações:

> [!div class="checklist"]
> * Criar um projeto que dê suporte ao Google Cloud Messaging.
> * Criar um hub de notificação
> * Conectar seu aplicativo ao hub de notificação
> * Testar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**. Caso você não tenha uma assinatura do Azure, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797).

## <a name="creating-a-project-that-supports-google-cloud-messaging"></a>Como criar um projeto que dê suporte ao Google Cloud Messaging

[!INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>Criar um hub de notificação

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-setting-for-the-notification-hub"></a>Configurar o GCM para o hub de notificação

1. Selecione **Google (GCM)** em **CONFIGURAÇÕES DE NOTIFICAÇÃO**.
2. Digite a **Chave de API** que você obteve no Google Cloud Console.
3. Selecione **Salvar** na barra de ferramentas.

    ![Hubs de Notificação do Azure - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Agora, o hub de notificação está configurado para funcionar com o GCM, e você tem as cadeias de conexão para registrar seu aplicativo para receber e enviar notificações por push.

## <a id="connecting-app"></a>Conectar seu aplicativo ao hub de notificação

### <a name="create-a-new-android-project"></a>Criar um novo aplicativo Android

1. No Android Studio, inicie um novo projeto Android Studio.

   ![Android Studio - novo projeto][13]
2. Escolha o fator forma **Telefone e Tablet** e o **SDK Mínimo** ao qual você deseja oferecer suporte. Em seguida, clique em **Próximo**.

   ![Android Studio - fluxo de trabalho de criação de projeto][14]
3. Escolha **Atividade Vazia** para a atividade principal, clique em **Avançar** e em **Concluir**.

### <a name="add-google-play-services-to-the-project"></a>Adicionar serviços do Google Play ao projeto

[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Como adicionar bibliotecas dos Hubs de Notificação do Azure

1. No arquivo `Build.Gradle` para o **aplicativo**, adicione as linhas a seguir à seção **dependencies**.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```
2. Adicione o seguinte repositório após a seção **dependências** .

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="updating-the-projects-androidmanifestxml"></a>Atualização do arquivo AndroidManifest.xml do projeto

1. Para oferecer suporte ao GCM, implemente um serviço de escuta de ID da Instância no código que será usado para [obter os tokens do registro](https://developers.google.com/cloud-messaging/) usando a [API de ID da Instância do Google](https://developers.google.com/instance-id/). Neste tutorial, o nome da classe é `MyInstanceIDService`.

    Adicione a seguinte definição de serviço ao arquivo Androidmanifest.xml, dentro da marcação `<application>` . Substitua o espaço reservado `<your package>` pelo nome do pacote real mostrado na parte superior do arquivo `AndroidManifest.xml`.
  
    ```xml
    <service android:name="<your package>.MyInstanceIDService" android:exported="false">
        <intent-filter>
            <action android:name="com.google.android.gms.iid.InstanceID"/>
        </intent-filter>
    </service>
    ```
2. Depois que o aplicativo recebe o token de registro do GCM da API do ID da Instância, ele usa o token para [registrar no Hub de Notificação do Azure](notification-hubs-push-notification-registration-management.md). O registro em segundo plano é feito usando um `IntentService` com o nome `RegistrationIntentService`. Esse serviço é responsável por [atualizar o token de registro do GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens).

    Adicione a seguinte definição de serviço ao arquivo Androidmanifest.xml, dentro da marcação `<application>` . Substitua o espaço reservado `<your package>` pelo nome do pacote real mostrado na parte superior do arquivo `AndroidManifest.xml`.

    ```xml
    <service
        android:name="<your package>.RegistrationIntentService"
        android:exported="false">
    </service>
    ```
3. Defina um receptor para receber as notificações. Adicione a seguinte definição do receptor ao arquivo AndroidManifest.xml, dentro da marcação `<application>` . Substitua o espaço reservado `<your package>` pelo nome do pacote real mostrado na parte superior do arquivo `AndroidManifest.xml`.

    ```xml
    <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <category android:name="<your package name>" />
        </intent-filter>
    </receiver>
    ```
4. Adicione as seguintes permissões do GCM necessárias abaixo da marcação `</application>`. Substitua `<your package>` pelo nome do pacote mostrado na parte superior do arquivo `AndroidManifest.xml`.

    Para ter mais informações sobre essas permissões, consulte [Configurar um aplicativo Cliente GCM para o Android](https://developers.google.com/cloud-messaging/).

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="android.permission.WAKE_LOCK"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

    <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
    <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>
    ```

### <a name="adding-code"></a>Como adicionar um código

1. Na Exibição de Projeto, expanda **app** > **src** > **main** > **java**. Clique na pasta do seu pacote em **java**, clique em **Novo** e então clique em **Classe Java**. Adicione uma nova classe denominada `NotificationSettings`.

    ![Android Studio - nova classe de Java][6]

    Atualize os três espaços reservados no código a seguir para a classe `NotificationSettings`:

   * `SenderId`: o número do projeto obtido anteriormente no [Console do Google Cloud](https://cloud.google.com/console).
   * `HubListenConnectionString`: a cadeia de conexão `DefaultListenAccessSignature` para seu hub. Você pode copiar essa cadeia de conexão clicando em **Políticas de Acesso** na página **Configurações** do hub no [Portal do Azure].
   * `HubName`: use o nome do hub de notificação que aparece na página do hub no [Portal do Azure].

     `NotificationSettings` :

     ```java
     public class NotificationSettings {
        public static String SenderId = "<Your project number>";
        public static String HubName = "<Your HubName>";
        public static String HubListenConnectionString = "<Your default listen connection string>";
     }
     ```
2. Adicione outra classe nova denominada `MyInstanceIDService`. Essa classe é a implementação de serviço de ouvinte de ID da Instância.

    O código para essa classe chama `IntentService` para [atualizar o token GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) em segundo plano.

    ```java
    import android.content.Intent;
    import android.util.Log;
    import com.google.android.gms.iid.InstanceIDListenerService;

    public class MyInstanceIDService extends InstanceIDListenerService {

        private static final String TAG = "MyInstanceIDService";

        @Override
        public void onTokenRefresh() {

            Log.i(TAG, "Refreshing GCM Registration Token");

            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    };
    ```
3. Adicione outra classe nova ao projeto denominada `RegistrationIntentService`. Essa classe implementa `IntentService` que processa a [atualização do token GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) e o [registro no hub de notificação](notification-hubs-push-notification-registration-management.md).

    Use o código a seguir para essa classe.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;

    import com.google.android.gms.gcm.GoogleCloudMessaging;
    import com.google.android.gms.iid.InstanceID;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";

        private NotificationHub hub;

        public RegistrationIntentService() {
            super(TAG);
        }

        @Override
        protected void onHandleIntent(Intent intent) {
            SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
            String resultString = null;
            String regID = null;

            try {
                InstanceID instanceID = InstanceID.getInstance(this);
                String token = instanceID.getToken(NotificationSettings.SenderId,
                        GoogleCloudMessaging.INSTANCE_ID_SCOPE);
                Log.i(TAG, "Got GCM Registration Token: " + token);

                // Storing the registration id that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server,
                // otherwise your server should have already received the token.
                if ((regID=sharedPreferences.getString("registrationID", null)) == null) {
                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.i(TAG, "Attempting to register with NH using token : " + token);

                    regID = hub.register(token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1", "tag2").getRegistrationId();

                    resultString = "Registered Successfully - RegId : " + regID;
                    Log.i(TAG, resultString);
                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                } else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete token refresh", e);
                // If an exception happens while fetching the new token or updating the registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```
4. Em sua classe `MainActivity`, adicione as seguintes instruções `import` no início da classe.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import com.google.android.gms.gcm.*;
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    import android.content.Intent;
    ```
5. Adicione os seguintes membros privados na parte superior da classe. Este código [verifica a disponibilidade do Google Play Services, conforme recomendado pelo Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private GoogleCloudMessaging gcm;
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    private static final String TAG = "MainActivity";
    ```
6. Em sua classe `MainActivity` , adicione o seguinte método à disponibilidade dos Serviços do Google Play.

    ```java
    /**
        * Check the device to make sure it has the Google Play Services APK. If
        * it doesn't, display a dialog that allows users to download the APK from
        * the Google Play Store or enable it in the device's system settings.
        */
    private boolean checkPlayServices() {
        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.SUCCESS) {
            if (apiAvailability.isUserResolvableError(resultCode)) {
                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                        .show();
            } else {
                Log.i(TAG, "This device is not supported by Google Play Services.");
                ToastNotify("This device is not supported by Google Play Services.");
                finish();
            }
            return false;
        }
        return true;
    }
    ```
7. Na classe `MainActivity`, adicione o seguinte código que verifica o Google Play Services antes de chamar o `IntentService` para obter seu token de registro do GCM e registrá-lo com o hub de notificação.

    ```java
    public void registerWithNotificationHubs()
    {
        Log.i(TAG, " Registering with Notification Hubs");

        if (checkPlayServices()) {
            // Start IntentService to register this application with GCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```
8. No método `OnCreate` da classe `MainActivity`, adicione o código a seguir para começar o processo de registro quando a atividade for criada.

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
        registerWithNotificationHubs();
    }
    ```
9. Adicione estes outros métodos a `MainActivity` para verificar o estado do aplicativo e informar o status em seu aplicativo.

    ```java
    @Override
    protected void onStart() {
        super.onStart();
        isVisible = true;
    }

    @Override
    protected void onPause() {
        super.onPause();
        isVisible = false;
    }

    @Override
    protected void onResume() {
        super.onResume();
        isVisible = true;
    }

    @Override
    protected void onStop() {
        super.onStop();
        isVisible = false;
    }

    public void ToastNotify(final String notificationMessage) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                TextView helloText = (TextView) findViewById(R.id.text_hello);
                helloText.setText(notificationMessage);
            }
        });
    }
    ```
10. O método `ToastNotify` usa o controle *"Hello World"* `TextView` para informar de forma persistente o status e as notificações no aplicativo. No layout activity_main.xml, adicione a seguinte ID para esse controle.

    ```xml
    android:id="@+id/text_hello"
    ```
11. Adicione uma subclasse para o receptor definido em AndroidManifest.xml. Adicione outra classe nova ao projeto denominada `MyHandler`.
12. Adicione as seguintes instruções de importação na parte superior de `MyHandler.java`:

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.os.Bundle;
    import android.support.v4.app.NotificationCompat;
    import com.microsoft.windowsazure.notifications.NotificationsHandler;
    import android.net.Uri;
    import android.media.RingtoneManager;
    ```
13. Adicione o código a seguir à classe `MyHandler`, tornando-a uma subclasse de `com.microsoft.windowsazure.notifications.NotificationsHandler`.

    Esse código substitui o método `OnReceive` para que o manipulador informe as notificações recebidas. O manipulador também envia a notificação por push ao gerenciador de notificações do Android usando o método `sendNotification()` . O método `sendNotification()` deve ser executado quando o aplicativo não está em execução e uma notificação é recebida.

    ```java
    public class MyHandler extends NotificationsHandler {
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("message");
            sendNotification(nhMessage);
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
        }

        private void sendNotification(String msg) {

            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);

            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);

            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder mBuilder =
                    new NotificationCompat.Builder(ctx)
                            .setSmallIcon(R.mipmap.ic_launcher)
                            .setContentTitle("Notification Hub Demo")
                            .setStyle(new NotificationCompat.BigTextStyle()
                                    .bigText(msg))
                            .setSound(defaultSoundUri)
                            .setContentText(msg);

            mBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }
    }
    ```
14. No Android Studio, na barra de menus, clique em **Compilar** > **Recompilar Projeto** para garantir que não haja erros presentes no código.

## <a name="testing-your-app"></a>Testando seu aplicativo

### <a name="run-the-mobile-application"></a>Executar o aplicativo móvel

1. Execute o aplicativo e observe que a ID de registro é relatada para um registro bem-sucedido.

      ![Como testar no Android - registro de canal][18]
2. Insira uma mensagem de notificação a ser enviada para todos os dispositivos Android registrados no hub.

      ![Como testar no Android - envio de uma mensagem][19]

3. Pressione **Enviar Notificação**. Qualquer dispositivo com o aplicativo em execução mostrará uma instância de `AlertDialog` com a mensagem de notificação por push. Dispositivos que não tiverem o aplicativo em execução, mas foram registrados anteriormente para notificações por push, receberão uma notificação no Gerenciador de Notificações do Android. As mensagens de notificação podem ser exibidas passando o dedo do canto superior esquerdo para baixo.

      ![Como testar no Android - notificações][21]

### <a name="test-send-push-notifications-from-the-azure-portal"></a>Testar e enviar notificações por push do portal do Azure

Você pode testar o recebimento de notificações por push em seu aplicativo enviando-as por meio do [portal do Azure].

1. Na seção **Solução de problemas**, selecione **Envio de Teste**.
2. Para **Plataformas**, selecione **Android**.
3. Selecione **Enviar** para enviar a notificação de teste.
4. Confirme que você vê a mensagem de notificação no dispositivo Android.

    ![Hubs de notificação do Azure - Testar Enviar](./media/notification-hubs-android-get-started/notification-hubs-test-send.png)

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

#### <a name="push-notifications-in-the-emulator"></a>Notificações por push no emulador

Para testar notificações por push em um emulador, verifique se a imagem de emulador dá suporte ao nível de API do Google que você escolheu para o aplicativo. Se sua imagem não for compatível com as APIs nativas do Google, você verá a exceção **SERVICE\_NOT\_AVAILABLE**.

Além disso, verifique se você adicionou a conta do Google ao emulador em execução em **Configurações** > **Contas**. Caso contrário, suas tentativas de se registrar no GCM podem resultar na exceção **AUTHENTICATION\_FAILED**.

## <a name="optional-send-push-notifications-directly-from-the-app"></a>(Opcional) Enviar notificações por push diretamente do aplicativo

Normalmente, você enviaria notificações usando um servidor back-end. Em alguns casos, talvez você queira ser capaz de enviar notificações por push diretamente do aplicativo cliente. Esta seção explica como enviar as notificações a partir do cliente usando a [API REST do Hub de Notificação do Azure](https://msdn.microsoft.com/library/azure/dn223264.aspx).

1. Na Exibição de Projeto do Android Studio, expanda **App** > **src** > **main** > **res** > **layout**. Abra o arquivo de layout `activity_main.xml` e clique na guia **Texto** para atualizar o conteúdo de texto do arquivo. Atualize-o com o código abaixo, que adiciona novos controles `Button` e `EditText` para enviar as mensagens de notificação por push ao hub de notificação. Adicione este código à parte inferior imediatamente antes de `</RelativeLayout>`.

    ```xml
    <Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/send_button"
    android:id="@+id/sendbutton"
    android:layout_centerVertical="true"
    android:layout_centerHorizontal="true"
    android:onClick="sendNotificationButtonOnClick" />

    <EditText
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:id="@+id/editTextNotificationMessage"
    android:layout_above="@+id/sendbutton"
    android:layout_centerHorizontal="true"
    android:layout_marginBottom="42dp"
    android:hint="@string/notification_message_hint" />
    ```
2. Na Exibição de Projeto do Android Studio, expanda **App** > **src** > **main** > **res** > **values**. Abra o arquivo `strings.xml` e adicione os valores da cadeia de caracteres referenciados pelos novos controles `Button` e `EditText`. Adicione as seguintes linhas à parte inferior do arquivo imediatamente antes de `</resources>`.

    ```xml
    <string name="send_button">Send Notification</string>
    <string name="notification_message_hint">Enter notification message text</string>
    ```
3. Em seu arquivo `NotificationSetting.java`, adicione a seguinte configuração à classe `NotificationSettings`.

    Atualize `HubFullAccess` com a cadeia de conexão **DefaultFullSharedAccessSignature** para o hub. Essa cadeia de conexão pode ser copiada do [portal do Azure] clicando em **Políticas de Acesso** na página **Configurações** do hub de notificação.

    ```java
    public static String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";
    ```
4. No arquivo `MainActivity.java`, adicione as seguintes instruções `import` no início do arquivo.

    ```java
    import java.io.BufferedOutputStream;
    import java.io.BufferedReader;
    import java.io.InputStreamReader;
    import java.io.OutputStream;
    import java.net.HttpURLConnection;
    import java.net.URL;
    import java.net.URLEncoder;
    import javax.crypto.Mac;
    import javax.crypto.spec.SecretKeySpec;
    import android.util.Base64;
    import android.view.View;
    import android.widget.EditText;
    ```
5. No arquivo `MainActivity.java`, adicione os membros a seguir na parte superior da classe `MainActivity`.

    ```java
    private String HubEndpoint = null;
    private String HubSasKeyName = null;
    private String HubSasKeyValue = null;
    ```
6. Crie um token SaS (Software Access Signature) para autenticar uma solicitação POST para o envio de mensagens para o seu hub de notificação. Analise os principais dados da cadeia de conexão e criando o token SaS, como mencionado na referência da API REST [Conceitos comuns](https://msdn.microsoft.com/library/azure/dn495627.aspx). O código a seguir é um exemplo de implementação.

    Em `MainActivity.java`, adicione o método a seguir à classe `MainActivity` para analisar a cadeia de conexão.

    ```java
    /**
        * Example code from https://msdn.microsoft.com/library/azure/dn495627.aspx
        * to parse the connection string so a SaS authentication token can be
        * constructed.
        *
        * @param connectionString This must be the DefaultFullSharedAccess connection
        *                         string for this example.
        */
    private void ParseConnectionString(String connectionString)
    {
        String[] parts = connectionString.split(";");
        if (parts.length != 3)
            throw new RuntimeException("Error parsing connection string: "
                    + connectionString);

        for (int i = 0; i < parts.length; i++) {
            if (parts[i].startsWith("Endpoint")) {
                this.HubEndpoint = "https" + parts[i].substring(11);
            } else if (parts[i].startsWith("SharedAccessKeyName")) {
                this.HubSasKeyName = parts[i].substring(20);
            } else if (parts[i].startsWith("SharedAccessKey")) {
                this.HubSasKeyValue = parts[i].substring(16);
            }
        }
    }
    ```
7. Em `MainActivity.java`, adicione o método a seguir à classe `MainActivity` para criar um token de autenticação SaS.

    ```java
    /**
        * Example code from https://msdn.microsoft.com/library/azure/dn495627.aspx to
        * construct a SaS token from the access key to authenticate a request.
        *
        * @param uri The unencoded resource URI string for this operation. The resource
        *            URI is the full URI of the Service Bus resource to which access is
        *            claimed. For example,
        *            "http://<namespace>.servicebus.windows.net/<hubName>"
        */
    private String generateSasToken(String uri) {

        String targetUri;
        String token = null;
        try {
            targetUri = URLEncoder
                    .encode(uri.toString().toLowerCase(), "UTF-8")
                    .toLowerCase();

            long expiresOnDate = System.currentTimeMillis();
            int expiresInMins = 60; // 1 hour
            expiresOnDate += expiresInMins * 60 * 1000;
            long expires = expiresOnDate / 1000;
            String toSign = targetUri + "\n" + expires;

            // Get an hmac_sha1 key from the raw key bytes
            byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
            SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

            // Get an hmac_sha1 Mac instance and initialize with the signing key
            Mac mac = Mac.getInstance("HmacSHA256");
            mac.init(signingKey);

            // Compute the hmac on input data bytes
            byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

            // Using android.util.Base64 for Android Studio instead of
            // Apache commons codec
            String signature = URLEncoder.encode(
                    Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");

            // Construct authorization string
            token = "SharedAccessSignature sr=" + targetUri + "&sig="
                    + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
        } catch (Exception e) {
            if (isVisible) {
                ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
            }
        }

        return token;
    }
    ```
8. No `MainActivity.java`, adicione o método a seguir à classe `MainActivity` para lidar com o clique no botão **Enviar Notificação** e enviar a mensagem de notificação por push ao hub usando a API REST interna.

    ```java
    /**
        * Send Notification button click handler. This method parses the
        * DefaultFullSharedAccess connection string and generates a SaS token. The
        * token is added to the Authorization header on the POST request to the
        * notification hub. The text in the editTextNotificationMessage control
        * is added as the JSON body for the request to add a GCM message to the hub.
        *
        * @param v
        */
    public void sendNotificationButtonOnClick(View v) {
        EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
        final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";

        new Thread()
        {
            public void run()
            {
                try
                {
                    // Based on reference documentation...
                    // https://msdn.microsoft.com/library/azure/dn223273.aspx
                    ParseConnectionString(NotificationSettings.HubFullAccess);
                    URL url = new URL(HubEndpoint + NotificationSettings.HubName +
                            "/messages/?api-version=2015-01");

                    HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();

                    try {
                        // POST request
                        urlConnection.setDoOutput(true);

                        // Authenticate the POST request with the SaS token
                        urlConnection.setRequestProperty("Authorization",
                            generateSasToken(url.toString()));

                        // Notification format should be GCM
                        urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");

                        // Include any tags
                        // Example below targets 3 specific tags
                        // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                        // urlConnection.setRequestProperty("ServiceBusNotification-Tags",
                        //        "tag1 || tag2 || tag3");

                        // Send notification message
                        urlConnection.setFixedLengthStreamingMode(json.length());
                        OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
                        bodyStream.write(json.getBytes());
                        bodyStream.close();

                        // Get response
                        urlConnection.connect();
                        int responseCode = urlConnection.getResponseCode();
                        if ((responseCode != 200) && (responseCode != 201)) {
                            BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                            String line;
                            StringBuilder builder = new StringBuilder("Send Notification returned " +
                                    responseCode + " : ")  ;
                            while ((line = br.readLine()) != null) {
                                builder.append(line);
                            }

                            ToastNotify(builder.toString());
                        }
                    } finally {
                        urlConnection.disconnect();
                    }
                }
                catch(Exception e)
                {
                    if (isVisible) {
                        ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
                    }
                }
            }
        }.start();
    }
    ```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você envia notificações de transmissão para todos os seus dispositivos Android registrados com o back-end. Para saber como enviar notificações por push para dispositivos Android específicos, vá para o tutorial a seguir:  

 > [!div class="nextstepaction"]
 > [Notificações por push para especificar dispositivos](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png
[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png
[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png
[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md 
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Portal do Azure]: https://portal.azure.com
