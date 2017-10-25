
1. Clique com o botão direito do mouse no projeto do Windows Store, clique em **Definir como Projeto de Inicialização**e pressione a tecla F5 para executar o aplicativo do Windows Store.
   
    Depois que o aplicativo é iniciado, o dispositivo está registrado para notificações por push.
2. Pare o aplicativo da Windows Store e repita a etapa anterior para o aplicativo Loja do Windows Phone.
   
    Nesse ponto, ambos os dispositivos são registrados para receber as notificações por push.
3. Execute o aplicativo do Windows Store novamente, digite o texto em **Inserir um TodoItem** e clique em **Salvar**.
   
       Note that after the insert completes, both the Windows Store and the Windows Phone apps receive a push notification from WNS. The notification is displayed on Windows Phone even when the app isn't running.
   
       ![](./media/app-service-mobile-windows-universal-test-push/mobile-quickstart-push5-wp8.png)

