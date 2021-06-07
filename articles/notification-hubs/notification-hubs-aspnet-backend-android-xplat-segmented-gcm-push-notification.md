---
title: Enviar notificações para dispositivos específicos Android usando os Hubs de Notificação do Azure e o Google Cloud Messaging | Microsoft Docs
description: Saiba como usar Hubs de Notificação para enviar notificações por push para dispositivos específicos Android usando Hubs de Notificação do Azure e Google Cloud Messaging.
services: notification-hubs
documentationcenter: android
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc, devx-track-java
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: c0c0018ac3007f77da820b9b0cecbb69c68bef31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92308294"
---
# <a name="tutorial-send-push-notifications-to-specific-android-devices-using-google-cloud-messaging-deprecated"></a>Tutorial: Enviar notificações por push para dispositivos Android específicos usando o Google Cloud Messaging (preterido)

> [!WARNING]
> Em 10 de abril de 2018, o Google preteriu o GCM (Google Cloud Messaging). As APIs de cliente e servidor do GCM foram preteridas e serão removidas até 29 de maio de 2019. Para saber mais, confira [Perguntas frequentes sobre o GCM e o FCM](https://developers.google.com/cloud-messaging/faq).

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Visão geral

Este tutorial mostra como usar os Hubs de Notificação do Azure para transmitir notificações de últimas notícias para um aplicativo Android. Ao concluir, você poderá se registrar nas categorias de últimas notícias que desejar e receber notificações por push apenas para essas categorias. Esse cenário é um padrão comum para muitos aplicativos nos quais as notificações precisam ser enviadas para grupos de usuários que tenham anteriormente expressado seu interesse por elas, por exemplo, leitor de RSS, aplicativos para fãs de música, etc.

Os cenários de transmissão são habilitados por meio da inclusão de uma ou mais *marcas* durante a criação de um registro no hub de notificação. Quando as notificações são enviadas para um rótulo, todos os dispositivos que foram registrados para o rótulo receberão a notificação. Como os rótulos são simplesmente cadeias de caracteres, eles não precisam ser provisionados com antecedência. Para obter mais informações sobre marcações, consulte [Roteamento e expressões de marcação dos Hubs de Notificação](notification-hubs-tags-segment-push-message.md).

Neste tutorial, você executa as seguintes ações:

> [!div class="checklist"]
> * Adicionar a seleção de categorias ao aplicativo móvel.
> * Registrado para receber notificações com marcas.
> * Enviar notificações marcadas.
> * Testar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial se baseia no aplicativo criado no [Tutorial: Enviar notificações por push para dispositivos Android usando Hubs de Notificação do Microsoft Azure e o Google Cloud Messaging][get-started]. Antes de iniciar este tutorial, conclua o [Tutorial: Enviar notificações por push para dispositivos Android usando Hubs de Notificação do Microsoft Azure e o Google Cloud Messaging][get-started].

## <a name="add-category-selection-to-the-app"></a>Adicionar a seleção de categorias ao aplicativo

A primeira etapa é adicionar os elementos da interface do usuário na atividade principal existente, o que permite ao usuário selecionar categorias para o registro. As categorias selecionadas por um usuário são armazenadas no dispositivo. Quando o aplicativo é iniciado, o registro do dispositivo é criado no seu hub de notificação com as categorias selecionadas como rótulos.

1. Abra o `res/layout/activity_main.xml file` e substitua o conteúdo pelo seguinte:

    ```xml
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context="com.example.breakingnews.MainActivity"
        android:orientation="vertical">

            <CheckBox
                android:id="@+id/worldBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_world" />
            <CheckBox
                android:id="@+id/politicsBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_politics" />
            <CheckBox
                android:id="@+id/businessBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_business" />
            <CheckBox
                android:id="@+id/technologyBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_technology" />
            <CheckBox
                android:id="@+id/scienceBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_science" />
            <CheckBox
                android:id="@+id/sportsBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_sports" />
            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="subscribe"
                android:text="@string/button_subscribe" />
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Hello World!"
                android:id="@+id/text_hello"
            />
    </LinearLayout>
    ```
2. Abra o arquivo `res/values/strings.xml` e adicione as seguintes linhas:

    ```xml
    <string name="button_subscribe">Subscribe</string>
    <string name="label_world">World</string>
    <string name="label_politics">Politics</string>
    <string name="label_business">Business</string>
    <string name="label_technology">Technology</string>
    <string name="label_science">Science</string>
    <string name="label_sports">Sports</string>
    ```

    O layout geográfico `main_activity.xml` deve ter a aparência como a da seguinte imagem:

    ![Captura de tela de um ambiente de desenvolvimento, com uma tela de aplicativo visível. O aplicativo lista as categorias de notícias que foram adicionadas ao código.][A1]
3. Crie uma classe `Notifications` no mesmo pacote que a classe `MainActivity`.

    ```java
    import java.util.HashSet;
    import java.util.Set;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.os.AsyncTask;
    import android.util.Log;
    import android.widget.Toast;
    import android.view.View;

    import com.google.android.gms.gcm.GoogleCloudMessaging;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class Notifications {
        private static final String PREFS_NAME = "BreakingNewsCategories";
        private GoogleCloudMessaging gcm;
        private NotificationHub hub;
        private Context context;
        private String senderId;

        public Notifications(Context context, String senderId, String hubName,
                                String listenConnectionString) {
            this.context = context;
            this.senderId = senderId;

            gcm = GoogleCloudMessaging.getInstance(context);
            hub = new NotificationHub(hubName, listenConnectionString, context);
        }

        public void storeCategoriesAndSubscribe(Set<String> categories)
        {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            settings.edit().putStringSet("categories", categories).commit();
            subscribeToCategories(categories);
        }

        public Set<String> retrieveCategories() {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            return settings.getStringSet("categories", new HashSet<String>());
        }

        public void subscribeToCategories(final Set<String> categories) {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        String regid = gcm.register(senderId);

                        String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                        hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM,
                            categories.toArray(new String[categories.size()]));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to register - " + e.getMessage());
                        return e;
                    }
                    return null;
                }

                protected void onPostExecute(Object result) {
                    String message = "Subscribed for categories: "
                            + categories.toString();
                    Toast.makeText(context, message,
                            Toast.LENGTH_LONG).show();
                }
            }.execute(null, null, null);
        }

    }
    ```

    Essa classe usa o armazenamento local para armazenar as categorias de notícias que este dispositivo deverá receber. Ela também contém métodos para se registrar nessas categorias.
4. Na classe `MainActivity`, remova os campos particulares de `NotificationHub` e `GoogleCloudMessaging` e adicione um campo de `Notifications`:

    ```java
    // private GoogleCloudMessaging gcm;
    // private NotificationHub hub;
    private Notifications notifications;
    ```
5. Em seguida, no método `onCreate`, remova a inicialização do campo `hub` e o método `registerWithNotificationHubs`. Depois, adicione as linhas a seguir, que inicializam uma instância da classe `Notifications`.

    ```java
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mainActivity = this;

        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId,
                MyHandler.class);

        notifications = new Notifications(this, NotificationSettings.SenderId, NotificationSettings.HubName, NotificationSettings.HubListenConnectionString);

        notifications.subscribeToCategories(notifications.retrieveCategories());
    }
    ```

    Confirme se o nome do hub e a cadeia de caracteres de conexão estão definidas corretamente na classe NotificationSettings.

    > [!NOTE]
    > Como as credenciais que são distribuídas com um aplicativo cliente não são geralmente seguras, você só deve distribuir a chave para acesso de escuta com o aplicativo cliente. O acesso de escuta permite que seu aplicativo se registre para receber notificações, mas os registros existentes não podem ser modificados e as notificações não podem ser enviadas. A chave de acesso completo é usada em um serviço back-end protegido para enviar notificações e alterar os registros existentes.

6. Em seguida, adicione as seguintes importações:

    ```java
    import android.widget.CheckBox;
    import java.util.HashSet;
    import java.util.Set;
    ```
7. Adicione o seguinte método `subscribe` para manipular o evento de clique do botão assinar:

    ```java
    public void subscribe(View sender) {
        final Set<String> categories = new HashSet<String>();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        if (world.isChecked())
            categories.add("world");
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        if (politics.isChecked())
            categories.add("politics");
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        if (business.isChecked())
            categories.add("business");
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        if (technology.isChecked())
            categories.add("technology");
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        if (science.isChecked())
            categories.add("science");
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        if (sports.isChecked())
            categories.add("sports");

        notifications.storeCategoriesAndSubscribe(categories);
    }
    ```

    Esse método cria uma lista de categorias e usa a classe `Notifications` para armazenar a lista no armazenamento local e registrar as marcas correspondentes com o hub de notificação. Quando as categorias são alteradas, o registro é recriado com as novas categorias.

Seu aplicativo agora é capaz de armazenar um conjunto de categorias no armazenamento local do dispositivo e registrar com o hub de notificação, sempre que o usuário alterar a seleção de categorias.

## <a name="register-for-notifications"></a>Registrar-se para receber notificações

Estas etapas registram com o hub de notificação na inicialização, usando as categorias que foram armazenadas no armazenamento local.

> [!NOTE]
> Como o registrationId atribuído pelo Google Cloud Messaging (GCM) pode ser alterado a qualquer momento, você deve se registrar para receber notificações com frequência para evitar falhas de notificação. Este exemplo registra a notificação a cada vez que o aplicativo é iniciado. Para os aplicativos que são executados com frequência, mais de uma vez por dia, é possível ignorar o registro para preservar a largura de banda se tiver passado menos de um dia desde o registro anterior.

1. Adicione o código a seguir no final do método `onCreate` na classe `MainActivity`:

    ```java
    notifications.subscribeToCategories(notifications.retrieveCategories());
    ```

    Esse código garante que o aplicativo recupere as categorias do armazenamento local e solicite um registro para essas categorias toda vez que ele for iniciado.
2. Em seguida, atualize o método `onStart()` na classe `MainActivity` como a seguir:

    ```java
    @Override
    protected void onStart() {

        super.onStart();
        isVisible = true;

        Set<String> categories = notifications.retrieveCategories();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }
    ```

    Esse código atualiza a atividade principal com base no status de categorias salvas anteriormente.

O aplicativo agora está completo e pode armazenar um conjunto de categorias no armazenamento local do dispositivo utilizado para registrá-las com o hub de notificação, sempre que o usuário alterar a seleção de categorias. Em seguida, defina um back-end que possa enviar notificações de categoria para esse aplicativo.

## <a name="send-tagged-notifications"></a>Enviar notificações marcadas

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>Testar o aplicativo

1. No Android Studio, execute o aplicativo no emulador ou dispositivo Android. A interface do usuário do aplicativo fornece um conjunto de alternâncias que permite escolher as categorias às quais assinar.
2. Habilite uma ou mais alternâncias de categorias e depois clique em **Assinar**. O aplicativo converte as categorias selecionadas em rótulos e solicita um novo registro do dispositivo para os rótulos selecionados do hub de notificação. As categorias registradas são retornadas e exibidas em uma caixa de diálogo.

    ![Inscrever-se para categorias](./media/notification-hubs-aspnet-backend-android-breaking-news/subscribe-for-categories.png)
3. Execute o aplicativo de console do .NET, que envia notificações para cada categoria. As notificações para as categorias selecionadas são exibidas como notificações do sistema.

    ![Notificações de notícias de tecnologia](./media/notification-hubs-aspnet-backend-android-breaking-news/technolgy-news-notification.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você enviou notificações de notícias a dispositivos específicos Android que foram registrados para as categorias. Para saber como enviar notificações por push a usuários específicos, avance ao seguinte tutorial:

> [!div class="nextstepaction"]
>[Enviar notificações por push para usuários específicos](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md)

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure portal]: https://portal.azure.com
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)