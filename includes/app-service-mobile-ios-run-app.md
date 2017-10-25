
1. Em seu Mac, visite o [Portal do Azure]. Clique em **Procurar Tudo** > **Aplicativos Móveis** > o back-end que você acabou de criar. Nas configurações do aplicativo móvel, clique em **Início Rápido** > **iOS (Objective-C)**. Se você preferir o Swift, clique em **Início Rápido** > **iOS (Swift)**. Em **Baixar e executar seu projeto iOS**, clique em **Baixar**. Isso baixa um projeto completo do Xcode para um aplicativo previamente configurado, para conexão com o back-end. Abra o projeto usando Xcode.
2. Pressione o botão **Executar** para criar o projeto e iniciar o aplicativo no simulador de iOS.
3. No aplicativo, digite um texto significativo, como *Concluir o tutorial* e clique no ícone de adição (**+**). Isso envia uma solicitação POST para o back-end do Azure implantado anteriormente. Os back-end insere dados da solicitação na tabela TodoItem SQL e retorna informações sobre os itens armazenados recentemente no aplicativo móvel. O aplicativo móvel exibe esses dados na lista. 

   ![Aplicativo de início rápido em execução no iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Portal do Azure]: https://portal.azure.com/
