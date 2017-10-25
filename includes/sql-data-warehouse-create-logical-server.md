### <a name="create-a-new-logical-sql-server-in-the-azure-portal"></a>Criar um novo servidor lógico do SQL no portal do Azure

1. Clique em **Novo**, pesquise **servidor lógico** e pressione **ENTER**.

    ![pesquisar servidor lógico](./media/sql-data-warehouse-create-logical-server/search-logical-server.png)
2. Selecione **SQL server (servidor lógico)** 

    ![selecionar servidor lógico](./media/sql-data-warehouse-create-logical-server/select-logical-server.png)
  
3. Clique em **Criar** para abrir a nova folha do SQL Server (servidor lógico).

   <kbd> ![abrir a folha do servidor lógico](./media/sql-data-warehouse-create-logical-server/open-logical-server-blade.png) </kbd> <kbd>![folha do servidor lógico](./media/sql-data-warehouse-create-logical-server/logical-server-blade.png) </kbd>
  
3. Na caixa de texto do nome do servidor da folha SQL Server (servidor lógico), forneça um nome válido para o novo servidor lógico. Uma marca de seleção verde indica que você forneceu um nome válido.
    
    ![novo nome do servidor](./media/sql-data-warehouse-create-logical-server/new-name-logical-server.png)

    > [!IMPORTANT]
    > O nome totalmente qualificado para o novo servidor será <nome_do_servidor>.database.windows.net.
    >
    
4. Na caixa de texto de logon do administrador de servidor, forneça um nome de usuário para o logon de autenticação do SQL para este servidor. Esse logon é conhecido como o logon principal do servidor. Uma marca de seleção verde indica que você forneceu um nome válido.
    
    ![Logon de administrador do SQL](./media/sql-data-warehouse-create-logical-server/sql-admin-login.png)
5. Nas caixas de texto **Senha** e **Confirmar senha**, forneça uma senha para a conta de logon da entidade de segurança. Uma marca de seleção verde indica que você forneceu uma senha válida.
    
    ![Senha de administrador do SQL](./media/sql-data-warehouse-create-logical-server/sql-admin-password.png)
6. Selecione uma assinatura em que você tenha permissão para criar objetos.

    ![subscription](./media/sql-data-warehouse-create-logical-server/subscription.png)
7. Na caixa de texto Grupo de recursos, selecione **Criar novo** e, na caixa de texto grupo de recursos, forneça um nome válido para o novo grupo de recursos (você também pode usar um grupo de recursos existente, caso já tenha criado um). Uma marca de seleção verde indica que você forneceu um nome válido.

    ![novo grupo de recursos](./media/sql-data-warehouse-create-logical-server/new-resource-group.png)

8. Na caixa de texto **Local**, selecione um data center apropriado para seu local, como "Leste da Austrália".
    
    ![local do servidor](./media/sql-data-warehouse-create-logical-server/server-location.png)
    
    > [!TIP]
    > A caixa de seleção para **Permitir que os serviços do Azure acessem o servidor** não pode ser alterado nessa folha. Você pode alterar essa configuração na folha de firewall do servidor. Para obter mais informações, confira [Introdução à segurança](../articles/sql-database/sql-database-manage-servers-portal.md).
    >
    
9. Clique em **Criar**.

    ![botão criar](./media/sql-data-warehouse-create-logical-server/create.png)

