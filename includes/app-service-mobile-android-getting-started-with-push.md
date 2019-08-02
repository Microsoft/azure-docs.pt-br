---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: e66e7d4646b650d2d811d3807db04605dfddeded
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172436"
---
1. No seu projeto de **aplicativo**, abra o arquivo `AndroidManifest.xml`. Adicione o código a seguir após o marca de abertura `application` :

    ```xml
    <service android:name=".ToDoMessagingService">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT"/>
        </intent-filter>
    </service>
    <service android:name=".ToDoInstanceIdService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. Abra o arquivo `ToDoActivity.java` e faça estas alterações:

    - Adicione a instrução de importação:

        ```java
        import com.google.firebase.iid.FirebaseInstanceId;
        ```

    - Altere a definição do `MobileServiceClient` de **privado** para **público estático**. A aparência agora é a seguinte:

        ```java
        private static MobileServiceClient mClient;
        ```

    - Adicione o método `registerPush`:

        ```java
        public static void registerPush() {
            final String token = FirebaseInstanceId.getInstance().getToken();
            if (token != null) {
                new AsyncTask<Void, Void, Void>() {
                    protected Void doInBackground(Void... params) {
                        mClient.getPush().register(token);
                        return null;
                    }
                }.execute();
            }
        }
        ```

    - Atualize o método **onCreate** da classe `ToDoActivity`. Adicione este código depois de o `MobileServiceClient` ser instanciado.

        ```java
        registerPush();
        ```

3. Adicione uma nova classe para tratar das notificações. No Explorador de Projeto, abra os nós **app** > **java** > **namespace-do-seu-projeto** e clique com o botão direito do mouse no nó do nome de pacote. Clique em **Novo** e em **Classe Java**. Em Nome, digite `ToDoMessagingService` e, em seguida, clique em OK. Em seguida, substitua a duração da classe por:

    ```java
    import android.app.Notification;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;

    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;

    public class ToDoMessagingService extends FirebaseMessagingService {

        private static final int NOTIFICATION_ID = 1;

        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            String message = remoteMessage.getData().get("message");
            if (message != null) {
                sendNotification("Notification Hub Demo", message);
            }
        }

        private void sendNotification(String title, String messageBody) {
            PendingIntent contentIntent = PendingIntent.getActivity(this, 0, new Intent(this, ToDoActivity.class), 0);
            Notification.Builder notificationBuilder = new Notification.Builder(this)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle(title)
                    .setContentText(messageBody)
                    .setContentIntent(contentIntent);
            NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
            if (notificationManager != null) {
                notificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
            }
        }
    }
    ```

4. Adicione outra classe para gerenciar as atualizações de token. Crie uma classe java `ToDoInstanceIdService` e substitua a declaração da classe por:

    ```java
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class ToDoInstanceIdService extends FirebaseInstanceIdService {

        @Override
        public void onTokenRefresh() {
            ToDoActivity.registerPush();
        }
    }
    ```

Seu aplicativo foi atualizado para dar suporte a notificações por push.
