1. No seu projeto de **aplicativo**, abra o arquivo `AndroidManifest.xml`. No código nas duas próximas etapas, substitua *`**my_app_package**`* pelo nome do pacote do aplicativo para o seu projeto. Esse é o valor do atributo `package` da marca `manifest`.
2. Adicione as seguintes novas permissões depois do elemento `uses-permission` :

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE"
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" />
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />
3. Adicione o código a seguir após o marca de abertura `application` :

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                         android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>
4. Abra o arquivo *ToDoItemActivity.java*e adicione a seguinte instrução de importação:

        import com.microsoft.windowsazure.notifications.NotificationsManager;
5. Adicione a seguinte variável privada à classe. Substitua *`<PROJECT_NUMBER>`* pelo número do projeto atribuído pela Google para seu aplicativo no procedimento anterior.

        public static final String SENDER_ID = "<PROJECT_NUMBER>";
6. Altere a definição do *MobileServiceClient* de **privado** para **público estático**. A aparência agora é a seguinte:

        public static MobileServiceClient mClient;
7. Adicione uma nova classe para tratar das notificações. No Explorador de Projeto, abra o nós **src** > **main** > **java** e clique com o botão direito do mouse no nó do nome de pacote. Clique em **Novo** e em **Classe Java**.
8. Em **Nome**, digite `MyHandler` e clique em **OK**.

    ![](./media/app-service-mobile-android-configure-push/android-studio-create-class.png)

9. No arquivo MyHandler, substitua a declaração de classe por:

        public class MyHandler extends NotificationsHandler {
10. Adicione as seguintes instruções de importação para a classe `MyHandler` :

        import com.microsoft.windowsazure.notifications.NotificationsHandler;
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.AsyncTask;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
11. Em seguida, adicione esse membro à classe `MyHandler` :

        public static final int NOTIFICATION_ID = 1;
12. Na classe `MyHandler` , adicione o seguinte código para substituir o método **onRegistered** , que registra o dispositivo com o hub de notificação do serviço móvel.

        @Override
        public void onRegistered(Context context,  final String gcmRegistrationId) {
           super.onRegistered(context, gcmRegistrationId);

           new AsyncTask<Void, Void, Void>() {

               protected Void doInBackground(Void... params) {
                   try {
                       ToDoActivity.mClient.getPush().register(gcmRegistrationId);
                       return null;
                   }
                   catch(Exception e) {
                       // handle error                
                   }
                   return null;              
               }
           }.execute();
       }
13. Na classe `MyHandler` , adicione o código a seguir para substituir o método **onReceive** , que faz com que a notificação seja exibida quando é recebida.

        @Override
        public void onReceive(Context context, Bundle bundle) {
               String msg = bundle.getString("message");

               PendingIntent contentIntent = PendingIntent.getActivity(context,
                       0, // requestCode
                       new Intent(context, ToDoActivity.class),
                       0); // flags

               Notification notification = new NotificationCompat.Builder(context)
                       .setSmallIcon(R.drawable.ic_launcher)
                       .setContentTitle("Notification Hub Demo")
                       .setStyle(new NotificationCompat.BigTextStyle().bigText(msg))
                       .setContentText(msg)
                       .setContentIntent(contentIntent)
                       .build();

               NotificationManager notificationManager = (NotificationManager)
                       context.getSystemService(Context.NOTIFICATION_SERVICE);
               notificationManager.notify(NOTIFICATION_ID, notification);
       }
14. No arquivo TodoActivity.java, atualize o método **onCreate** da classe *ToDoActivity* para registrar a classe de manipulador de notificação. Certifique-se de adicionar este código depois de o *MobileServiceClient* ser instanciado.

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Seu aplicativo foi atualizado para oferecer suporte a notificações de push.
