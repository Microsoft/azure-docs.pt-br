
1. No [portal do Azure](https://portal.azure.com/), clique em **Procurar Tudo** > **Serviços de Aplicativos** e clique em seu back-end de Aplicativos Móveis. Em **Configurações**, clique em **Push do Serviço de Aplicativo** e clique no nome do hub de notificação.
2. Vá para **Google (GCM)**, insira o valor **Chave do Servidor** obtido do Firebase no procedimento anterior e clique em **Salvar**.

    ![Defina a chave de API do GCM no portal](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

O back-end de Aplicativos Móveis agora está configurado para usar o Firebase Cloud Messaging. Isso permite que você envie notificações por push para seu aplicativo em execução em um dispositivo Android usando o hub de notificação.

<!-- URLs. -->


<!-- images -->
