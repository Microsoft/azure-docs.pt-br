## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento

Esta seção o orienta para configurar o ambiente de desenvolvimento, incluindo a criação de um aplicativo ASP.NET MVC, a adição de uma conexão de serviços conectados, a adição de um controlador e a especificação das diretivas de namespace necessárias.

### <a name="create-an-aspnet-mvc-app-project"></a>Criar um projeto de aplicativo ASP.NET MVC

1. Abra o Visual Studio.

1. Selecione **Arquivo-> Novo-> Projeto** no menu principal

1. Na caixa de diálogo **Novo Projeto**, especifique as opções conforme realçado na seguinte figura:

    ![Criar o projeto do ASP.NET](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. Selecione **OK**.

1. Na caixa de diálogo **Novo Projeto ASP.NET**, especifique as opções conforme realçado na seguinte figura:

    ![Especificar o MVC](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

1. Selecione **OK**.

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Usar Serviços Conectados para se conectar a uma conta de armazenamento do Azure

1. No **Solution Explorer**, clique com o botão direito do mouse no projeto e, no menu de contexto, selecione **Adicionar-> Serviço Conectado**.

1. Na caixa de diálogo **Adicionar Serviço Conectado**, selecione **Armazenamento do Azure**e **Configurar**.

    ![Caixa de diálogo de Serviço Conectado](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. Na caixa de diálogo **Armazenamento do Azure**, selecione a conta de armazenamento do Azure com a qual você deseja trabalhar e selecione **Adicionar**.
