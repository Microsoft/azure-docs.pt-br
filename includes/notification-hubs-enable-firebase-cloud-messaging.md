

1. Faça logon no [console do Firebase](https://firebase.google.com/console/). Crie um novo projeto do Firebase se você ainda não tiver um.
2. Depois que o projeto for criado, clique em **Adicionar Firebase ao seu aplicativo Android** e siga as instruções fornecidas.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. No Console do Firebase, clique na engrenagem para seu projeto e, em seguida, clique em **Configurações do Projeto**.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Clique na guia **Cloud Messaging** nas configurações do projeto e copie o valor da **Chave do servidor** e da **ID do remetente**. Esses valores serão usados mais tarde para configurar a Política de Acesso do hub de notificação e o manipulador de notificações no aplicativo.
