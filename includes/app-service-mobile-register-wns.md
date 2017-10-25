
1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto do aplicativo da Windows Store e clique em **Armazenar** > **Associar Aplicativo à Store**.

    ![Associar aplicativo com a Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. No assistente, clique em **Avançar** e entre com sua conta da Microsoft. Digite um nome para o seu aplicativo em **Reservar nome do aplicativo** e clique em **Reservar**.
3. Depois que o registro do aplicativo for criado com êxito, selecione o novo nome do aplicativo, clique em **Avançar** e em **Associar**. Isso adiciona as informações de registro necessárias da Windows Store para o manifesto do aplicativo.
4. Repita as etapas 1 e 3 para o projeto de aplicativo da Windows Phone Store usando o mesmo registro que você criou anteriormente para o aplicativo da Windows Store.  
5. Navegue até o [Centro de Desenvolvimento do Windows](https://dev.windows.com/en-us/overview) e entre com sua conta da Microsoft. Clique no novo registro de aplicativo em **Meus aplicativos** e expanda **Serviços** > **Notificações por push**.
6. Na página **Notificações por push**, clique em **site dos Live Services** em **WNS (Serviços de Notificação por Push do Windows) e Serviços Móveis do Microsoft Azure**. Anote os valores do **SID do Pacote** e o valor *atual* em **Segredo do Aplicativo**. 

    ![Configuração do aplicativo no centro do desenvolvedor](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > O segredo do aplicativo e o SID do pacote são credenciais de segurança importantes. Não compartilhe esses valores com ninguém nem os distribua com seu aplicativo.
   >
   >
