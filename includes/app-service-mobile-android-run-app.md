
1. Visite o [Portal do Azure]. Clique em **Procurar Tudo** > **Aplicativos Móveis** > o back-end que você acabou de criar. Nas configurações do aplicativo móvel, clique em **Início Rápido** > **(Android)**. Em **Configurar seu aplicativo cliente**, clique em **Baixar**. Isso baixa um projeto completo do Android para um aplicativo pré-configurado, para conexão com o back-end. 
2. Abra o projeto usando o **Android Studio**, usando **Importar projeto (Eclipse ADT, Gradle, etc.)**. Certifique-se de fazer essa seleção de importação para evitar erros de JDK.
3. Pressione o botão **Executar “aplicativo”** para compilar o projeto e iniciar o aplicativo no simulador de Android.
4. No aplicativo, digite um texto significativo, como *Concluir o tutorial* e depois clique no botão Adicionar. Isso envia uma solicitação POST para o back-end do Azure implantado anteriormente. Os back-end insere dados da solicitação na tabela TodoItem SQL e retorna informações sobre os itens armazenados recentemente no aplicativo móvel. O aplicativo móvel exibe esses dados na lista. 
   
    ![](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)

[Portal do Azure]: https://portal.azure.com/
