
1. Acesse o [Google Cloud Console](https://console.developers.google.com/project)e entre com suas credenciais de conta do Google. 
2. Clique em **Criar Projeto**, digite um nome de projeto, então clique em **Criar**. Se solicitado, execute a verificação de SMS e clique em **Criar** novamente.
   
    ![Criar um novo projeto](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
   
     Digite o novo **Nome do projeto** e clique em **Criar projeto**.
3. Clique no botão **Utilitários e Mais** e clique em **Informações do Projeto**. Anote o **Número do Projeto**. Será necessário definir esse valor como a variável `SenderId` no aplicativo cliente.
   
    ![Utilitários e mais](./media/mobile-services-enable-google-cloud-messaging/notification-hubs-utilities-and-more.png)
4. No painel do projeto, em **APIs Móveis**, clique em **Google Cloud Messaging** e, na próxima página, clique em **Habilitar API** e aceite os termos de serviço. 
   
    ![Habilitar o GCM](./media/mobile-services-enable-google-cloud-messaging/enable-GCM.png)
   
    ![Habilitar o GCM](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-2.png) 
5. No painel do projeto, clique em **Credenciais** > **Criar Credencial** > **Chave de API**. 
   
    ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)
6. Em **Criar uma nova chave**, clique em **Chave do servidor**, digite um nome para a chave, clique em **Criar**.
7. Anote o valor da **CHAVE DA API** .
   
    Você usará esse valor de chave de API para habilitar o Azure para autenticar com o GCM e enviar notificações por push em nome do seu aplicativo.

