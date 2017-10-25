
1. Visite o [Portal do Azure].
2. Clique em **Serviços de Aplicativos** > o back-end que você criou.
3. Nas configurações do aplicativo móvel, clique em **Início Rápido** > **Cordova**.
![Portal do Azure com Início Rápido de Aplicativos Móveis realçado][quickstart]
4. Em **Configurar o aplicativo cliente**, selecione **Criar um Novo Aplicativo** e clique em **Baixar**.
2. Descompacte o arquivo ZIP baixado para um diretório no disco rígido, navegue até o arquivo de solução (.sln) e abra-o usando o Visual Studio.
3. No Visual Studio, escolha a plataforma de solução (Android, iOS ou Windows) no menu suspenso ao lado da seta de início. Selecione um emulador ou dispositivo de implantação específico clicando na lista suspensa na seta verde. Você pode usar a plataforma Android padrão e o emulador Ripple. Tutoriais mais avançados (por exemplo, notificações por push) exigirão que você selecione um emulador ou dispositivo com suporte.
4. Pressione F5 ou clique na seta verde para compilar e executar o aplicativo Cordova. Se for mostrada uma caixa de diálogo de segurança no emulador que solicita acesso à rede, aceite-a.
5. Depois que o aplicativo for iniciado no dispositivo ou emulador, digite um texto significativo em **Inserir novo texto**, como *Concluir o tutorial* e, em seguida, clique no botão **Adicionar**.

Os back-end insere dados da solicitação na tabela TodoItem no Banco de Dados SQL e retorna informações sobre os itens armazenados recentemente no aplicativo móvel. O aplicativo móvel exibe esses dados na lista.

Você pode repetir as etapas 3 a 5 para outras plataformas.

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png

<!-- URLs -->
[Portal do Azure]: https://portal.azure.com/
