---
title: Copiar incrementalmente várias tabelas usando o portal do Azure
description: Neste tutorial, você criará um Azure Data Factory com um pipeline que carrega os dados delta de várias tabelas em um banco de dados do SQL Server para um banco de dados no Banco de Dados SQL do Azure.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 1fad6274b1dbbc4bf255caabd79352b3c836e352
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606678"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-a-database-in-azure-sql-database-using-the-azure-portal"></a>Carregar dados de maneira incremental de várias tabelas no SQL Server para um banco de dados no Banco de Dados SQL do Azure usando o portal do Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Neste tutorial, você criará um Azure Data Factory com um pipeline que carrega os dados delta de várias tabelas em um banco de dados do SQL Server para um banco de dados no Banco de Dados SQL do Azure.    

Neste tutorial, você realizará os seguintes procedimentos:

> [!div class="checklist"]
> * Preparará os armazenamentos de dados de origem e destino.
> * Criar um data factory.
> * Criar um runtime de integração auto-hospedada.
> * Instalar o Integration Runtime. 
> * Criar serviços vinculados. 
> * Criar os conjuntos de dados de origem, de coletor e de marca-d'água.
> * Crie, execute e monitore um pipeline.
> * Revise os resultados.
> * Adicionar ou atualizar dados nas tabelas de origem.
> * Executar novamente e monitorar o pipeline.
> * Examinar os resultados finais.

## <a name="overview"></a>Visão geral
Aqui estão as etapas importantes ao criar essa solução: 

1. **Selecione a coluna de marca-d'água**.
    
    Selecione uma coluna para cada tabela no armazenamento de dados de origem. Posteriormente, essa coluna pode ser usada para identificar os registros novos ou atualizados de cada execução. Normalmente, os dados nessa coluna selecionada (por exemplo, ID ou last_modify_time) seguem crescendo quando linhas são criadas ou atualizadas. O valor máximo dessa coluna é usado como uma marca-d'água.

1. **Prepare um armazenamento de dados para armazenar o valor de marca-d'água**.   
    
    Neste tutorial, você deve armazenar o valor de marca-d'água em um banco de dados SQL.

1. **Crie um pipeline com as seguintes atividades**: 
    
    a. Crie uma atividade ForEach que itere em uma lista de nomes de tabela de origem passada como um parâmetro para o pipeline. Para cada tabela de origem, são chamadas as próximas atividades necessárias para o carregamento delta.

    b. Crie duas atividades de pesquisa. Use a primeira atividade de Pesquisa para recuperar o último valor de marca-d'água. Use a segunda atividade de Pesquisa para recuperar o novo valor de marca-d'água. Esses valores de marca-d'água são passados para a atividade de Cópia.

    c. Crie uma atividade de cópia que copie linhas do armazenamento de dados de origem com o valor da coluna de marca-d'água maior do que o antigo valor de marca-d'água e menor que o novo valor de marca-d'água. Em seguida, ela copia os dados delta do armazenamento de dados de origem para um Armazenamento de Blobs do Azure como um novo arquivo.

    d. Crie uma atividade de StoredProcedure que atualize o valor de marca-d'água para o pipeline que for executado da próxima vez. 

    A seguir está diagrama da solução de alto nível: 

    ![Carregar dados incrementalmente](media/tutorial-incremental-copy-multiple-tables-portal/high-level-solution-diagram.png)


Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* **SQL Server**. Você usa um banco de dados do SQL Server como o armazenamento de dados de origem neste tutorial. 
* **Banco de dados SQL do Azure**. Você usa um banco de dados no Banco de Dados SQL do Azure como o armazenamento de dados do coletor. Se você não tiver um banco de dados no Banco de Dados SQL, confira [Criar um banco de dados no Banco de Dados SQL do Azure](../azure-sql/database/single-database-create-quickstart.md) para ver as etapas de criação. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Criar tabelas de origem no banco de dados do SQL Server

1. Abra o SQL Server Management Studio e conecte-se ao banco de dados do SQL Server.

1. No **Gerenciador de Servidores**, clique com o botão direito do mouse no banco de dados e escolha **Nova consulta**.

1. Execute o seguinte comando SQL no banco de dados para criar as tabelas `customer_table` e `project_table`:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-database"></a>Criar tabelas de destino no banco de dados

1. Abra o SQL Server Management Studio e conecte-se ao banco de dados no Banco de Dados SQL do Azure.

1. No **Gerenciador de Servidores**, clique com o botão direito do mouse no banco de dados e escolha **Nova consulta**.

1. Execute o seguinte comando SQL no banco de dados para criar as tabelas `customer_table` e `project_table`:  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-your-database-to-store-the-high-watermark-value"></a>Criar outra tabela no banco de dados para armazenar o valor de marca d'água alta

1. Execute o seguinte comando SQL no banco de dados para criar uma tabela chamada `watermarktable` para armazenar o valor de marca-d'água: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. Insira valores de marca d'água iniciais para ambas as tabelas de origem na tabela de marca d'água.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-your-database"></a>Criar um procedimento armazenado no banco de dados

Execute o comando a seguir para criar um procedimento armazenado no banco de dados. Esse procedimento armazenado atualiza o valor de marca d'água após cada execução de pipeline. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

UPDATE watermarktable
SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-your-database"></a>Criar tipos de dados e procedimentos armazenados adicionais no seu banco de dados

Execute a consulta a seguir para criar dois tipos de dados e dois procedimentos armazenados no banco de dados. Eles são usados para mesclar os dados das tabelas de origem nas tabelas de destino.

Para facilitar o início da jornada, usamos diretamente esses procedimentos armazenados passando os dados delta por meio de uma variável de tabela e, em seguida, mesclamos esses dados no repositório de destino. Tenha cuidado, pois ele não espera um "grande" número de linhas delta (mais de 100) a ser armazenado na variável de tabela.  

Caso precise mesclar um grande número de linhas delta no repositório de destino, sugerimos que você use a atividade de cópia para copiar todos os dados delta para uma tabela temporária de "preparo" no repositório de destino primeiro e, em seguida, criar seu próprio procedimento armazenado sem o uso da variável de tabela para mesclá-los da tabela de “preparo” para a tabela “final”. 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
2. No menu à esquerda, selecione **Criar um recurso** > **Integração** > **Data Factory**: 
   
   ![Seleção de Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Na página **Novo data factory**, insira **ADFMultiIncCopyTutorialDF** como o **nome**. 
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se for exibido um ponto de exclamação vermelho com um erro, altere o nome de data factory (por exemplo, seunomeADFIncCopyTutorialDF) e tente criá-lo novamente. Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
   `Data factory name "ADFIncCopyTutorialDF" is not available`

4. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
5. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
    - Selecione **Usar existente** e selecione um grupo de recursos existente na lista suspensa. 
    - Selecione **Criar novo** e insira o nome de um grupo de recursos.   
    Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md).  
6. Selecione **V2** para a **versão**.
7. Selecione o **local** do data factory. Apenas os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.
8. Clique em **Criar**.      
9. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem.
   
    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Home page do Azure Data Factory com o bloco Criar e Monitorar.":::
10. Clique no bloco **Criar e Monitorar** para iniciar a interface do usuário do Azure Data Factory em uma guia separada.

## <a name="create-self-hosted-integration-runtime"></a>Criar um Integration Runtime auto-hospedado
Conforme você move dados de um armazenamento de dados em uma rede privada (local) para um armazenamento de dados do Azure, instale um RI (runtime de integração) auto-hospedado em seu ambiente local. O IR auto-hospedado move dados entre sua rede privada e o Azure. 

1. Na página **Vamos começar** da interface do usuário do Azure Data Factory, selecione a [guia Gerenciar](./author-management-hub.md) no painel mais à esquerda.

   ![O botão Gerenciar da home page](media/doc-common-process/get-started-page-manage-button.png)

1. Selecione **Runtimes de integração** no painel esquerdo e, em seguida, selecione **+ Novo**.

   ![Criar um Integration Runtime](media/doc-common-process/manage-new-integration-runtime.png)

1. Na janela **Instalação do Integration Runtime**, selecione **Executar atividades de movimentação e expedição de dados para cálculos externos** e clique em **Continuar**. 

1. Selecione **Auto-hospedada** e clique em **Continuar**. 
1. Insira **MySelfHostedIR** em **Nome** e clique em **Criar**. 

1. Clique em **Clique aqui para inicializar a instalação expressa para este computador** na seção **Opção 1: instalação expressa**. 

   ![Clique no link de instalação Expressa](./media/tutorial-incremental-copy-multiple-tables-portal/click-express-setup.png)
1. Na janela **Instalação Expressa do Microsoft Integration Runtime (auto-hospedado)** , clique em **Fechar**. 

   ![Instalação do runtime de integração - êxito](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
1. No navegador da Web, na janela **Configuração do Integration Runtime**, clique em **Concluir**. 

 
1. Confirme que você vê **MySelfHostedIR** na lista de runtimes de integração.

## <a name="create-linked-services"></a>Criar serviços vinculados
Os serviços vinculados são criados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Nesta seção, você criará serviços vinculados para o banco de dados do SQL Server e o seu banco de dados no Banco de Dados SQL do Azure. 

### <a name="create-the-sql-server-linked-service"></a>Criar um serviço vinculado do SQL Server
Nesta etapa, você vincula seu Banco de Dados do SQL Server ao data factory.

1. Na janela **Conexões**, alterne da guia **Integration Runtime** para a guia **Serviços Associados** e clique em **+ Novo**.

    :::image type="content" source="./media/doc-common-process/new-linked-service.png" alt-text="Novo serviço vinculado.":::
1. Na janela **Novo Serviço Vinculado**, selecione **SQL Server** e clique em **Continuar**. 

1. Na janela **Novo Serviço Vinculado**, execute estas etapas:

    1. Insira **SqlServerLinkedService** como **Nome**. 
    1. Selecione **MySelfHostedIR** para **Conectar por meio do runtime de integração**. Esta é uma etapa **importante**. O runtime de integração padrão não pode se conectar a um armazenamento de dados local. Use o runtime de integração auto-hospedada criado anteriormente. 
    1. Como **Nome do servidor**, digite o nome do computador que tem o banco de dados do SQL Server.
    1. Como **Nome do banco de dados**, digite o nome do banco de dados no SQL Server que contém os dados de origem. Você criou uma tabela e dados inseridos neste banco de dados como parte dos pré-requisitos. 
    1. Como **Tipo de autenticação**, selecione o **tipo de autenticação** que você deseja usar para se conectar ao banco de dados. 
    1. Como **Nome de usuário**, digite o nome do usuário que tem acesso ao banco de dados do SQL Server. Se precisar usar um caractere de barra (`\`) em sua conta de usuário e nome de servidor, use o caractere de escape (`\`). Um exemplo é `mydomain\\myuser`.
    1. Para **Senha**, insira a **senha** do usuário. 
    1. Para testar se o data factory pode se conectar ao banco de dados do SQL Server, clique em **Testar conexão**. Corrija os erros até que a conexão seja bem-sucedida. 
    1. Para salvar o serviço vinculado, clique em **Concluir**.

### <a name="create-the-azure-sql-database-linked-service"></a>Criar o serviço vinculado do Banco de Dados SQL do Azure
Na última etapa, você deve criar um serviço vinculado para vincular o banco de dados do SQL Server de origem ao data factory. Nesta etapa, você vincula o banco de dados de destino/coletor ao data factory. 

1. Na janela **Conexões**, alterne da guia **Integration Runtime** para a guia **Serviços Associados** e clique em **+ Novo**.
1. Na janela **Novo Serviço Vinculado**, selecione **Banco de Dados SQL do Azure** e clique em **Continuar**. 
1. Na janela **Novo Serviço Vinculado**, execute estas etapas:

    1. Insira **AzureSqlDatabaseLinkedService** para o **Nome**. 
    1. Como **Nome do servidor**, selecione o nome do servidor na lista suspensa. 
    1. Como **Nome do banco de dados**, selecione o banco de dados em que você criou customer_table e project_table como parte dos pré-requisitos. 
    1. Para **Nome de usuário**, insira o nome do usuário que tem acesso ao banco de dados. 
    1. Para **Senha**, insira a **senha** do usuário. 
    1. Para testar se o data factory pode se conectar ao banco de dados do SQL Server, clique em **Testar conexão**. Corrija os erros até que a conexão seja bem-sucedida. 
    1. Para salvar o serviço vinculado, clique em **Concluir**.

1. Confirme que você vê os dois serviços vinculados na lista. 
   
    ![Dois serviços vinculados](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>Criar conjuntos de dados
Nesta etapa, você cria conjuntos de dados para representar a fonte de dados, o destino de dados e o local para armazenar a marca d'água.

### <a name="create-a-source-dataset"></a>Criar um conjunto de dados de origem

1. No painel esquerdo, clique em **+ (adição)** e clique em **Conjunto de Dados**.

1. Na janela **Novo Conjunto de Dados**, selecione **SQL Server** e clique em **Continuar**. 

1. Você vê uma nova guia aberta no navegador da Web para configurar o conjunto de dados. Você também verá um conjunto de dados no modo de exibição de árvore. Na guia **Geral** da janela Propriedades, na parte inferior, insira **SourceDataset** como **Nome**. 

1. Alterne para a guia **Conexão** na janela Propriedades e selecione **SqlServerLinkedService** como **Serviço vinculado**. Você não seleciona uma tabela aqui. A atividade de Cópia no pipeline usa uma consulta SQL para carregar os dados em vez de carregar a tabela inteira.

   ![Conjunto de Dados de Origem - conexão](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>Criar um conjunto de dados de coletor
1. No painel esquerdo, clique em **+ (adição)** e clique em **Conjunto de Dados**.

1. Na janela **Novo Conjunto de dados**, selecione **Banco de Dados SQL do Azure** e clique em **Continuar**. 

1. Você vê uma nova guia aberta no navegador da Web para configurar o conjunto de dados. Você também verá um conjunto de dados no modo de exibição de árvore. Na guia **Geral** da janela Propriedades para o conjunto de dados, na parte inferior, insira **SinkDataset** como **Nome**.

1. Alterne para a guia **Parâmetros** na janela Propriedades e execute as seguintes etapas: 

    1. Clique em **Novo** na seção **Criar/atualizar parâmetros**. 
    1. Digite **SinkTableName** como **nome** e **Cadeia de Caracteres** como **tipo**. Este conjunto de dados usa **SinkTableName** como um parâmetro. O parâmetro SinkTableName é definido pelo pipeline dinamicamente no runtime. A atividade ForEach no pipeline itera por meio de uma lista de nomes de tabela e passa o nome da tabela para esse conjunto de dados em cada iteração.
   
        ![Conjunto de Dados do Coletor - propriedades](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)
1. Alterne para a guia **Conexão** na janela Propriedades e selecione **AzureSqlDatabaseLinkedService** em **Serviço vinculado**. Para a propriedade **Tabela**, clique em **Adicionar conteúdo dinâmico**.   
    
1. Na janela **Adicionar Conteúdo Dinâmico**, selecione **SinkTableName** na seção **Parâmetros**. 
 
1. Depois de clicar em **Concluir**, você verá "@dataset().SinkTableName" como o nome da tabela.

   ![Conjunto de Dados do Coletor - conexão](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-completion.png)

### <a name="create-a-dataset-for-a-watermark"></a>Criar um conjunto de dados para uma marca-d'água
Nesta etapa, você deve criar um conjunto de dados para armazenar um valor de marca d'água alta. 

1. No painel esquerdo, clique em **+ (adição)** e clique em **Conjunto de Dados**.

1. Na janela **Novo Conjunto de dados**, selecione **Banco de Dados SQL do Azure** e clique em **Continuar**. 

1. Na guia **Geral** da janela Propriedades para o conjunto de dados, na parte inferior, insira **WatermarkDataset** como **Nome**.
1. Alterne para a guia **Conexão** e siga estas etapas: 

    1. Selecione **AzureSqlDatabaseLinkedService** para **Serviço vinculado**.
    1. Selecione **[dbo].[watermarktable]** para **Tabela**.

        ![Conjunto de Dados de Marca d'Água - conexão](./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png)

## <a name="create-a-pipeline"></a>Criar um pipeline
O pipeline usa uma lista de nomes de tabela como um parâmetro. A atividade ForEach itera na lista de nomes de tabela e executa as seguintes operações: 

1. Use a atividade de Pesquisa para recuperar o valor de marca d'água antigo (o valor inicial ou o que foi usado na última iteração).

1. Use a atividade de Pesquisa para recuperar o novo valor de marca d'água (o valor máximo da coluna da marca d'água na tabela de origem).

1. Use a atividade de Cópia para copiar os dados entre esses dois valores de marca d'água do banco de dados de origem para o banco de dados de destino.

1. Use a atividade StoredProcedure para atualizar o valor da marca d'água antiga a ser usado na primeira etapa da próxima iteração. 

### <a name="create-the-pipeline"></a>Criar o pipeline

1. No painel esquerdo, clique em **+ (adição)** e clique em **Pipeline**.

1. No painel geral, em **Propriedades**, especifique **IncrementalCopyPipeline** para **Nome**. Em seguida, recolha o painel clicando no ícone Propriedades no canto superior direito.  

1. Na guia **Parâmetros**, execute as seguintes etapas: 

    1. Clique em **+ Novo**. 
    1. Digite **tableList** para o parâmetro **nome**. 
    1. Selecione **Matriz** para o parâmetro **type**.

1. Na caixa de ferramentas **Atividades**, expanda **Iteração e condicionais** e arraste e solte a atividade **ForEach** para a superfície de designer do pipeline. Na guia **Geral** da janela **Propriedades**, insira **IterateSQLTables**. 

1. Alterne para a guia **Configurações** e digite `@pipeline().parameters.tableList` para **Itens**. A atividade ForEach itera por meio de uma lista de tabelas e executa a operação de cópia incremental. 

    ![Atividade ForEach - configurações](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)

1. Selecione a atividade **ForEach** no pipeline, se já não estiver selecionada. Clique no botão **Editar (ícone de lápis)** .

1. Na caixa de ferramentas **Atividades**, expanda **Geral** e arraste e solte a atividade de **Pesquisa** para a superfície do designer de pipeline, e insira **LookupOldWaterMarkActivity** para o **Nome**.

1. Alterne para a guia **Configurações** na janela **Propriedades** e execute as seguintes etapas: 

    1. Selecione **WatermarkDataset** como **Conjunto de Dados de Origem**.
    1. Selecione **Consulta** para **Usar consulta**. 
    1. Insira a seguinte consulta SQL para **Consulta**. 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![Primeira Atividade de Pesquisa - configurações](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
1. Arraste e solte a atividade **Pesquisar** na caixa de ferramentas **Atividades** e digite **LookupNewWaterMarkActivity** como **Nome**.
        
1. Alterne para a guia **Configurações** .

    1. Selecione **SourceDataset** para o **Conjunto de dados de origem**. 
    1. Selecione **Consulta** para **Usar consulta**.
    1. Insira a seguinte consulta SQL para **Consulta**.

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![Segunda Atividade de Pesquisa - configurações](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
1. Arraste e solte a atividade de **Cópia** da caixa de ferramentas **Atividades** e digite **IncrementalCopyActivity** como **Nome**. 

1. Conecte as atividades de **Pesquisa** à atividade de **Cópia**, uma por uma. Para se conectar, comece arrastando a caixa **verde** anexada à atividade de **Pesquisa** e solte-a na atividade de **Cópia**. Solte o botão do mouse quando visualizar a cor da borda da atividade de Cópia ficar **azul**.

    ![Conetar atividades de Pesquisa à atividade de Cópia](./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png)
1. Selecione a atividade de **Cópia** no pipeline. Alterne para a guia **Origem** na janela **Propriedades**. 

    1. Selecione **SourceDataset** para o **Conjunto de dados de origem**. 
    1. Selecione **Consulta** para **Usar consulta**. 
    1. Insira a seguinte consulta SQL para **Consulta**.

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        ![Atividade de cópia - configurações da fonte](./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png)
1. Alterne para a guia **Coletor** e selecione **SinkDataset** para o campo **Conjunto de Dados do Coletor**. 
        
1. Execute as seguintes etapas:

    1. Nas **Propriedades do conjunto de dados**, no parâmetro **SinkTableName**, insira `@{item().TABLE_NAME}`.
    1. Na propriedade **Nome do Procedimento Armazenado**, insira `@{item().StoredProcedureNameForMergeOperation}`.
    1. Na propriedade **Tipo de tabela**, insira `@{item().TableType}`.
    1. Em **Nome do parâmetro do tipo de tabela**, insira `@{item().TABLE_NAME}`.

        ![Atividade de cópia - parâmetros](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
1. Arraste e solte a atividade de **Procedimento armazenado** da caixa de ferramentas **Atividades** para a superfície do designer de pipeline. Conecte a atividade de **Cópia** à atividade de **Procedimento Armazenado**. 

1. Selecione a atividade **Procedimento Armazenado** no pipeline e digite **StoredProceduretoWriteWatermarkActivity** como **Nome** na guia **Geral** da janela **Propriedades**. 

1. Alterne para a guia **Conta SQL** e selecione **AzureSqlDatabaseLinkedService** para o **Serviço Vinculado**.

    ![Atividade de procedimento armazenado - Conta SQL](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
1. Alterne para a guia **Procedimento armazenado** e execute as etapas a seguir:

    1. Para o **Nome do procedimento armazenado**, selecione `[dbo].[usp_write_watermark]`. 
    1. Selecione **Parâmetro de importação**. 
    1. Especifique os seguintes valores para os parâmetros: 

        | Nome | Type | Valor | 
        | ---- | ---- | ----- |
        | LastModifiedtime | Datetime | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | TableName | String | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![Atividade de Procedimento armazenado - configurações de procedimento armazenado](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
1. Selecione **Publicar todos** para publicar as entidades que você criou no serviço Data Factory. 

1. Aguarde até que você veja a mensagem **Publicado com êxito**. Para ver as notificações, clique no link **Mostrar Notificações**. Feche a janela de notificações clicando em **X**.

 
## <a name="run-the-pipeline"></a>Executar o pipeline

1. Na barra de ferramentas do pipeline, clique em **Adicionar gatilho** e em **Disparar Agora**.     

1. Na janela **Execução de Pipeline**, digite o valor a seguir para o parâmetro **tableList** e clique em **Concluir**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

    ![Argumentos de Execução do Pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png)

## <a name="monitor-the-pipeline"></a>Monitorar o Pipeline

1. Alterne para a guia **Monitorar** à esquerda. Você pode ver a execução do pipeline disparado pelo **gatilho manual**. Você pode usar os links na coluna **PIPELINE NAME** para ver detalhes da atividade e executar o pipeline novamente.

1. Para ver as execuções de atividade associadas à execução do pipeline, selecione o link na coluna **PIPELINE NAME**. Para obter detalhes sobre as execuções de atividade, selecione o link **Detalhes** (ícone de óculos) na coluna **ACTIVITY NAME**. 

1. Selecione **Todas as execuções de pipeline** na parte superior para voltar à exibição Execuções de Pipeline. Para atualizar a exibição, selecione **Atualizar**.


## <a name="review-the-results"></a>Revise os resultados
No SQL Server Management Studio, execute as seguintes consultas no banco de dados SQL de destino para verificar se os dados foram copiados das tabelas de origem para as tabelas de destino: 

**Consulta** 
```sql
select * from customer_table
```

**Saída**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Consulta**

```sql
select * from project_table
```

**Saída**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Consulta**

```sql
select * from watermarktable
```

**Saída**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Observe que os valores de marca d'água para ambas as tabelas foram atualizados. 

## <a name="add-more-data-to-the-source-tables"></a>Adicionar mais dados às tabelas de origem

Execute a consulta a seguir feita no banco de dados do SQL Server de origem para atualizar uma linha existente na customer_table. Insera uma nova linha em project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Executar novamente o pipeline
1. Na janela do navegador da Web, alterne para a guia **Editar** à esquerda. 
1. Na barra de ferramentas do pipeline, clique em **Adicionar gatilho** e em **Disparar Agora**.   
1. Na janela **Execução de Pipeline**, digite o valor a seguir para o parâmetro **tableList** e clique em **Concluir**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline-again"></a>Monitorar o pipeline novamente

1. Alterne para a guia **Monitorar** à esquerda. Você pode ver a execução do pipeline disparado pelo **gatilho manual**. Você pode usar os links na coluna **PIPELINE NAME** para ver detalhes da atividade e executar o pipeline novamente.

1. Para ver as execuções de atividade associadas à execução do pipeline, selecione o link na coluna **PIPELINE NAME**. Para obter detalhes sobre as execuções de atividade, selecione o link **Detalhes** (ícone de óculos) na coluna **ACTIVITY NAME**. 

1. Selecione **Todas as execuções de pipeline** na parte superior para voltar à exibição Execuções de Pipeline. Para atualizar a exibição, selecione **Atualizar**.

## <a name="review-the-final-results"></a>Examine os resultados finais
No SQL Server Management Studio, execute as seguintes consultas no banco de dados SQL de destino para verificar se os dados atualizados/novos foram copiados das tabelas de origem para as tabelas de destino. 

**Consulta** 
```sql
select * from customer_table
```

**Saída**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Observe os novos valores de **Name** e **LastModifytime** para **PersonID** para o número 3. 

**Consulta**

```sql
select * from project_table
```

**Saída**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Observe que a entrada **NewProject** foi adicionada a project_table. 

**Consulta**

```sql
select * from watermarktable
```

**Saída**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Observe que os valores de marca d'água para ambas as tabelas foram atualizados.
     
## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você realizará os seguintes procedimentos: 

> [!div class="checklist"]
> * Preparará os armazenamentos de dados de origem e destino.
> * Criar um data factory.
> * Criar um Integration Runtime (IR) auto-hospedado.
> * Instalar o Integration Runtime.
> * Criar serviços vinculados. 
> * Criar os conjuntos de dados de origem, de coletor e de marca-d'água.
> * Crie, execute e monitore um pipeline.
> * Revise os resultados.
> * Adicionar ou atualizar dados nas tabelas de origem.
> * Executar novamente e monitorar o pipeline.
> * Examinar os resultados finais.

Avance para o tutorial a seguir para saber mais sobre como transformar dados usando um cluster Spark no Azure:

> [!div class="nextstepaction"]
>[Carregar incrementalmente os dados do Banco de Dados SQL do Azure para o Armazenamento de Blobs do Azure usando a tecnologia Controle de Alterações](tutorial-incremental-copy-change-tracking-feature-portal.md)