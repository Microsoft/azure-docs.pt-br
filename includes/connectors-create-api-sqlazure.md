### <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Um [Banco de Dados SQL](../articles/sql-database/sql-database-get-started.md) com suas informações de conexão, incluindo o nome do servidor, nome do banco de dados e nome de usuário e senha. Essas informações estão incluídas na cadeia de conexão do Banco de Dados SQL:
  
    Server=tcp:*yoursqlservername*.database.windows.net,1433;Initial Catalog=*yourqldbname*;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
  
    Saiba mais sobre o [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database).

> [!NOTE]
> Quando você cria um banco de dados SQL do Azure, também pode criar os bancos de dados de exemplo incluídos no SQL. 
> 
> 

Antes de usar o Banco de Dados SQL do Azure em um aplicativo lógico, conecte-se ao Banco de Dados SQL. Você pode fazer isso de forma fácil usando seu aplicativo lógico no portal do Azure.  

Conecte ao seu Banco de Dados SQL do Azure usando as seguintes etapas:  

1. Crie um aplicativo lógico. No designer de Aplicativos Lógicos, adicione um gatilho e, em seguida, uma ação. Selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa e digite "sql" na caixa de pesquisa. Selecione uma das ações:  
   
    ![Etapa de criação da conexão com o SQL Azure](./media/connectors-create-api-sqlazure/sql-actions.png)
2. Se ainda não tiver criado conexões com o Banco de Dados SQL, você deverá fornecer os detalhes de conexão:  
   
    ![Etapa de criação da conexão com o SQL Azure](./media/connectors-create-api-sqlazure/connection-details.png) 
3. Insira os detalhes do Banco de Dados SQL. As propriedades com um asterisco são obrigatórias.
   
   | Propriedade | Detalhes |
   | --- | --- |
   | Conectar-se Usando Gateway |Deixe desmarcada. Essa propriedade é usada na conexão com um SQL Server local. |
   | Nome da Conexão * |Digite um nome para a conexão. |
   | Nome do SQL Server * |Insira o nome do servidor, que é algo como *servername.database.windows.net*. O nome do servidor é exibido nas propriedades do Banco de Dados SQL no portal do Azure e também na cadeia de conexão. |
   | Nome do Banco de Dados SQL * |Insira o nome que você deu a seu Banco de Dados SQL. Ele está listado nas propriedades do Banco de Dados SQL na cadeia de conexão: Initial Catalog=*yoursqldbname*. |
   | Nome de Usuário * |Insira o nome de usuário que você criou quando o Banco de Dados SQL foi criado. Ele está listado nas propriedades do Banco de Dados SQL no portal do Azure. |
   | Senha * |Insira a senha que você criou quando o Banco de Dados SQL foi criado. |
   
    Essas credenciais são usadas para autorizar o aplicativo lógico a se conectar e acessar dados do SQL. Uma vez concluída, os detalhes da conexão se parecerão com estes:  
   
    ![Etapa de criação da conexão com o SQL Azure](./media/connectors-create-api-sqlazure/sample-connection.png) 
4. Selecione **Criar**. 
5. Observe que a conexão foi criada. Agora, continue com as outras etapas no seu aplicativo lógico: 
   
    ![Etapa de criação da conexão com o SQL Azure](./media/connectors-create-api-sqlazure/table.png)

