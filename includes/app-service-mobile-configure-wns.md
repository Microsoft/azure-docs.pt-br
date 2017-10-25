
1. No [portal do Azure](https://portal.azure.com/), clique em **Procurar Tudo** > **Serviços de Aplicativos** e clique no back-end dos Aplicativos Móveis. Em **Configurações**, clique em **Push do Serviço de Aplicativo** e clique no nome do hub de notificação.
2. Vá para **Windows (WNS)**, digite a **Chave de segurança** (segredo do cliente) e o **SID do pacote** que você obteve no site dos Live Services e clique em **Salvar**.

    ![Definir a chave WNS no portal](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

O back-end agora está configurado para usar o WNS a fim de enviar notificações por push.
