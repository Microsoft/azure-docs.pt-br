---
title: Copiar uma tabela incrementalmente usando o Azure Data Factory | Microsoft Docs
description: Neste tutorial, você cria um pipeline do Azure Data Factory que copia dados incrementalmente de um banco de dados SQL do Azure para um Armazenamento de Blobs do Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/11/2018
ms.author: yexu
ms.openlocfilehash: 52cb11b015bb231b91184a2270e333e4c9aa8303
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424285"
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage"></a>Carregar incrementalmente os dados do banco de dados SQL do Azure para o Armazenamento de Blobs do Azure
Neste tutorial, você cria um Azure Data Factory com um pipeline que carrega dados delta de uma tabela em um banco de dados SQL do Azure para um Armazenamento de Blobs do Azure. 

Neste tutorial, você realizará os seguintes procedimentos:

> [!div class="checklist"]
> * Prepare o armazenamento de dados para armazenar o valor de marca-d'água.
> * Criar um data factory.
> * Criar serviços vinculados. 
> * Criar os conjuntos de dados de origem, de coletor e de marca-d'água.
> * Crie um pipeline.
> * Execute o pipeline.
> * Monitorar a execução de pipeline. 
> * Analisar resultados
> * Adicionar mais dados à origem.
> * Executar o pipeline novamente.
> * Monitorar a segunda execução do pipeline
> * Examinar os resultados da segunda execução


## <a name="overview"></a>Visão geral
A seguir está diagrama da solução de alto nível: 

![Carregar dados incrementalmente](media/tutorial-Incremental-copy-portal/incrementally-load.png)

Aqui estão as etapas importantes ao criar essa solução: 

1. **Selecione a coluna de marca-d'água**.
    Selecione uma coluna no armazenamento de dados de origem, que pode ser usada para dividir os registros novos ou atualizados para cada execução. Normalmente, os dados nessa coluna selecionada (por exemplo, ID ou last_modify_time) seguem crescendo quando linhas são criadas ou atualizadas. O valor máximo dessa coluna é usado como uma marca-d'água.

2. **Prepare um armazenamento de dados para armazenar o valor de marca-d'água**. Neste tutorial, você deve armazenar o valor de marca-d'água em um banco de dados SQL.
    
3. **Criar um pipeline com o seguinte fluxo de trabalho**: 
    
    O pipeline nesta solução tem as seguintes atividades:
  
    * Crie duas atividades de Pesquisa. Use a primeira atividade de Pesquisa para recuperar o último valor de marca-d'água. Use a segunda atividade de Pesquisa para recuperar o novo valor de marca-d'água. Esses valores de marca-d'água são passados para a atividade de Cópia. 
    * Crie uma atividade de cópia que copie linhas do armazenamento de dados de origem com o valor da coluna de marca-d'água maior do que o antigo valor de marca-d'água e menor que o novo valor de marca-d'água. Em seguida, ela copia os dados delta do armazenamento de dados de origem para um Armazenamento de Blobs como um novo arquivo. 
    * Crie uma atividade de StoredProcedure que atualize o valor de marca-d'água para o pipeline que for executado da próxima vez. 


Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* **Banco de dados SQL do Azure**. Você usa o banco de dados como um armazenamento de dados de origem. Se você não tiver um banco de dados SQL, consulte [Criar um banco de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md) para saber as etapas para criar um.
* **Armazenamento do Azure**. Você usa o Armazenamento de Blobs como um armazenamento de dados de coletor. Se você não tiver uma conta de armazenamento, consulte [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md) para saber as etapas para criar uma. Crie um contêiner denominado adftutorial. 

### <a name="create-a-data-source-table-in-your-sql-database"></a>Criar uma tabela de fonte de dados no banco de dados SQL
1. Abra o SQL Server Management Studio. No **Gerenciador de Servidores**, clique com o botão direito do mouse no banco de dados e escolha **Nova consulta**.

2. Execute o comando SQL a seguir no banco de dados SQL para criar uma tabela chamada `data_source_table` como o armazenamento de fonte de dados: 
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    Neste tutorial, você usa LastModifytime como a coluna marca-d'água. Os dados no respositório de fonte de dados são mostrados na tabela a seguir:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Criar outra tabela no banco de dados SQL para armazenar o valor de marca d'água alta
1. Execute o comando SQL a seguir no banco de dados SQL para criar uma tabela chamada `watermarktable` para armazenar o valor de marca-d'água:  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Defina o valor padrão de marca d'água alta com o nome da tabela do armazenamento de dados de origem. Neste tutorial, o nome da tabela é data_source_table.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Examine os dados na tabela `watermarktable`.
    
    ```sql
    Select * from watermarktable
    ```
    Saída: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Criar um procedimento armazenado no banco de dados SQL 

Execute o comando a seguir para criar um procedimento armazenado no banco de dados SQL:

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
1. No menu à esquerda, selecione **Criar um recurso** > **Dados + Análise** > **Data Factory**: 
   
   ![Seleção de Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na página **Novo data factory**, insira **ADFIncCopyTutorialDF** como o **nome**. 
      
     ![Página de novo data factory](./media/tutorial-incremental-copy-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se for exibido um ponto de exclamação vermelho com um erro, altere o nome de data factory (por exemplo, seunomeADFIncCopyTutorialDF) e tente criá-lo novamente. Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
       `Data factory name "ADFIncCopyTutorialDF" is not available`
3. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
4. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
      - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
      - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
        Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V2** para a **versão**.
5. Selecione o **local** do data factory. Apenas os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.
6. Selecione **Fixar no painel**.     
7. Clique em **Criar**.      
8. No painel, você deve ver o seguinte bloco com status: **Implantando data factory**. 

    ![implantando bloco data factory](media/tutorial-incremental-copy-portal/deploying-data-factory.png)
9. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem.
   
   ![Página inicial do data factory](./media/tutorial-incremental-copy-portal/data-factory-home-page.png)
10. Clique no bloco **Criar e Monitorar** para iniciar a interface do usuário do Azure Data Factory em uma guia separada.

## <a name="create-a-pipeline"></a>Criar um pipeline
Neste tutorial, você cria um pipeline com duas atividades de Pesquisa, uma atividade de Cópia e uma atividade de Procedimento armazenado encadeadas em um pipeline. 

1. Na página **introdução** da interface do usuário do Data Factory, clique no bloco **Criar pipeline**. 

   ![Obter a página de introdução da interface do usuário do Data Factory](./media/tutorial-incremental-copy-portal/get-started-page.png)    
3. Na página **Geral** da janela **Propriedades** para o pipeline, digite o nome **IncrementalCopyPipeline**. 

   ![Nome do pipeline](./media/tutorial-incremental-copy-portal/pipeline-name.png)
4. Vamos adicionar a primeira atividade de pesquisa para recuperar o último valor de marca-d'água. Na caixa de ferramentas **Atividades**, expanda **Geral** e arraste e solte a atividade de **Pesquisa** para a superfície do designer de pipeline. Alterar o nome da atividade para **LookupOldWaterMarkActivity**.

   ![Primeira atividade de pesquisa - nome](./media/tutorial-incremental-copy-portal/first-lookup-name.png)
5. Alterne para a guia **Configurações** e, em seguida, clique em **+ Novo** para o **Conjunto de dados de origem**. Nesta etapa, você cria um conjuntos de dados para representar os dados na **watermarktable**. Esta tabela contém a marca d'água antiga que foi usada na operação de cópia anterior. 

   ![Menu Novo conjunto de dados - marca d'água antiga](./media/tutorial-incremental-copy-portal/new-dataset-old-watermark.png)
6. Na janela **Novo Conjunto de dados**, selecione **Banco de Dados SQL do Azure** e clique em **Concluir**. Você verá uma nova guia aberta para o conjunto de dados. 

   ![Selecione um Banco de Dados SQL do Azure](./media/tutorial-incremental-copy-portal/select-azure-sql-database-old-watermark.png)
7. Na janela Propriedades para o conjunto de dados, insira **WatermarkDataset** para o **Nome**.

   ![Conjunto de dados de marca d'água - nome](./media/tutorial-incremental-copy-portal/watermark-dataset-name.png)
8. Alterne para a guia **Conexão** e, em seguida, clique em **+ Novo** para fazer uma conexão (criar um serviço vinculado) para seu banco de dados SQL do Azure. 

   ![Botão Novo serviço vinculado](./media/tutorial-incremental-copy-portal/watermark-dataset-new-connection-button.png)
9. Na janela **Novo Serviço Vinculado**, execute estas etapas:

    1. Insira **AzureSqlDatabaseLinkedService** para o **Nome**. 
    2. Selecione o SQL Server do Azure para o **Nome do servidor**.
    3. Insira o **nome do usuário** para acessar para o SQL Server do Azure. 
    4. Insira a **senha** para o usuário. 
    5. Para testar a conexão ao Banco de Dados SQL do Azure, clique em **Testar conectividade**.
    6. Clique em **Save** (Salvar).
    7. Na guia **Conexão**, confirme se **AzureSqlDatabaseLinkedService** foi selecionado para o **Serviço vinculado**.
       
        ![Janela Novo serviço vinculado](./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png)
10. Selecione **[dbo].[watermarktable]** para **Tabela**. Se você quiser visualizar os dados na tabela, clique em **Visualizar dados**.

    ![Conjunto de dados de marca d'água - configurações de conexão](./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png)
11. Alterne para o editor de pipeline clicando na guia pipeline na parte superior ou clicando no nome do pipeline n modo de exibição de árvore à esquerda. Na janela Propriedades para a atividade de **Pesquisa**, confirme se **WatermarkDataset** está selecionado para o campo **Conjunto de Dados de Origem**. 

    ![Pipeline - conjunto de dados de marca d'água antigo](./media/tutorial-incremental-copy-portal/pipeline-old-watermark-dataset-selected.png)
12. Na caixa de ferramentas **Atividades**, expanda **Geral** e arraste e solte outra atividade de **Pesquisa** para a superfície do designer de pipeline e defina o nome como  **LookupNewWaterMarkActivity** na guia **Geral** da janela Propriedades. Esta atividade de Pesquisa obtém o novo valor de marca d'água da tabela com os dados de origem a serem copiados para o destino. 

    ![Segunda atividade de pesquisa - nome](./media/tutorial-incremental-copy-portal/second-lookup-activity-name.png)
13. Na janela Propriedades da segunda atividade de **Pesquisa**, alterne para a guia **Configurações** e clique em **Novo**. Você cria um conjunto de dados para apontar para a tabela de origem que contém o novo valor de marca d'água (valor máximo de LastModifyTime). 

    ![Segunda atividade de pesquisa - novo conjunto de dados](./media/tutorial-incremental-copy-portal/second-lookup-activity-settings-new-button.png)
14. Na janela **Novo Conjunto de dados**, selecione **Banco de Dados SQL do Azure** e clique em **Concluir**. Você verá uma nova guia aberta para esse conjunto de dados. Você também verá o conjunto de dados no modo de exibição de árvore. 
15. Na guia **Geral** da janela Propriedades, insira **SourceDataset** como **Nome**. 

    ![Conjunto de dados de origem - nome](./media/tutorial-incremental-copy-portal/source-dataset-name.png)
16. Alterne para a guia **Conexão** e siga estas etapas: 

    1. Selecione **AzureSqlDatabaseLinkedService** para **Serviço vinculado**.
    2. Selecione **[dbo].[data_source_table]** para Tabela. Você especificará uma consulta nesse conjunto de dados posteriormente com o tutorial. A consulta tem precedência sobre a tabela que você especificar nesta etapa. 

        ![Segunda atividade de pesquisa - novo conjunto de dados](./media/tutorial-incremental-copy-portal/source-dataset-connection.png)
17. Alterne para o editor de pipeline clicando na guia pipeline na parte superior ou clicando no nome do pipeline n modo de exibição de árvore à esquerda. Na janela Propriedades para a atividade de **Pesquisa**, confirme se **SourceDataset** está selecionado para o campo **Conjunto de dados de origem**. 
18. Selecione **Consulta** para o campo **Usar consulta** campo e digite a seguinte consulta: você está selecionando apenas o valor máximo de **LastModifytime** do **data_ source_table**. Verifique se você também marcou **Somente a primeira linha**.

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    ![Segunda atividade de pesquisa - consulta](./media/tutorial-incremental-copy-portal/query-for-new-watermark.png)
19. Na caixa de ferramentas **Atividades**, expanda o **Fluxo de dados** e arraste e solte a atividade de **Cópia** da caixa de ferramentas Atividades e defina o nome para **IncrementalCopyActivity**. 

    ![Atividade de cópia - nome](./media/tutorial-incremental-copy-portal/copy-activity-name.png)
20. **Conecte ambas as atividades de Pesquisa à atividade de Cópia** arrastando o **botão verde** anexado às atividades de Pesquisa para a atividade de Cópia. Solte o botão do mouse quando visualizar a cor da borda da atividade de Cópia ficar azul. 

    ![Conexão das atividades de Pesquisa à atividade de Cópia](./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png)
21. Selecione a **atividade de Cópia** e confirme se você vê as propriedades da atividade na janela **Propriedades**. 

    ![Propriedades da atividade de cópia](./media/tutorial-incremental-copy-portal/back-to-copy-activity-properties.png)
22. Alterne para a guia **Fonte** na janela **Propriedades** e execute as seguintes etapas:

    1. Selecione **SourceDataset** para o campo **Conjunto de dados de origem**. 
    2. Selecione a **Consulta** para o campo **Usar consulta**. 
    3. Insira a seguinte consulta SQL no campo **Consulta**. 

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```
    
        ![Atividade de cópia - origem](./media/tutorial-incremental-copy-portal/copy-activity-source.png)
23. Alterne para a guia **Coletor** e clique em **+ Novo** para o campo **Conjunto de dados do coletor**. 

    ![Botão Novo Conjunto de dados](./media/tutorial-incremental-copy-portal/new-sink-dataset-button.png)
24. Neste tutorial o armazenamento de dados do coletor é do tipo Armazenamento de Blobs do Azure. Portanto, selecione **Armazenamento de Blobs do Azure** e clique em **Concluir** na janela **Novo Conjunto de dados**. 

    ![Selecionar Armazenamento de Blobs do Azure](./media/tutorial-incremental-copy-portal/select-azure-blob-storage.png)
25. Na guia **Geral** da janela Propriedades para o conjunto de dados, insira **SinkDataset** para o **Nome**. 

    ![Conjunto de dados do coletor - nome](./media/tutorial-incremental-copy-portal/sink-dataset-name.png)
26. Alterne para a guia **Conexão** e, em seguida, clique em **+ Novo**. Nesta etapa, você cria uma conexão (serviço vinculado) ao **Armazenamento de Blobs do Azure**.

    ![Conjunto de dados do coletor - nova conexão](./media/tutorial-incremental-copy-portal/sink-dataset-new-connection.png)
26. Na janela **Novo Serviço Vinculado**, execute estas etapas: 

    1. Insira **AzureStorageLinkedService** como o **Nome**. 
    2. Selecione sua conta de Armazenamento do Azure como o **Nome da conta de armazenamento**.
    3. Clique em **Save** (Salvar). 

        ![Serviço vinculado do Armazenamento do Azure - configurações](./media/tutorial-incremental-copy-portal/azure-storage-linked-service-settings.png)
27. Na guia **Conexão**, siga estas etapas:

    1. Confirme se **AzureStorageLinkedService** está selecionado como **Serviço vinculado**. 
    2. Para a parte da **pasta** do campo **Caminho do arquivo**, insira **adftutorial/fromonprem**. **adftutorial** é o nome do contêiner de blobs e **incrementalcopy** é o nome da pasta. Esse snippet de código supõe que você tenha um contêiner de blob denominado adftutorial no Armazenamento de Blobs. Crie o contêiner caso ele não exista ou defina-o com o nome de um contêiner existente. O Azure Data Factory cria automaticamente a pasta de saída **incrementalcopy** se ela não existir. Você também pode usar o botão **Procurar** para o **Caminho de arquivo** para navegar até uma pasta em um contêiner de blob. .RunId, '.txt')`.
    3. Para a parte do **nome do arquivo** do campo **Caminho do arquivo**, digite `@CONCAT('Incremental-', pipeline().RunId, '.txt')`. Neste tutorial, o nome do arquivo é gerado dinamicamente pelo uso da expressão. Cada execução de pipeline possui uma ID exclusiva. A atividade de Cópia usa a ID de execução para gerar o nome do arquivo. 

        ![Conjunto de dados do coletor - configurações de conexão](./media/tutorial-incremental-copy-portal/sink-dataset-connection-settings.png)
28. Alterne para o editor de **pipeline** clicando na guia Pipeline na parte superior ou clicando no nome do pipeline no modo de exibição de árvore à esquerda. 
29. Na caixa de ferramentas **Atividades**, expanda **Geral** e arraste e solte a atividade de **Procedimento armazenado** da caixa de ferramentas **Atividades** para a superfície de designer do pipeline. **Conecte** a saída verde (Bem-sucedida) da atividade de **Cópia** à atividade de **Procedimento armazenado**. 
    
    ![Atividade de cópia - origem](./media/tutorial-incremental-copy-portal/connect-copy-to-stored-procedure-activity.png)
24. Selecione **Atividade de Procedimento Armazenado** no designer de pipeline, altere seu nome para **StoredProceduretoWriteWatermarkActivity**. 

    ![Atividade de procedimento armazenado - nome](./media/tutorial-incremental-copy-portal/stored-procedure-activity-name.png)
25. Alterne para a guia **Conta SQL** e selecione *AzureSqlDatabaseLinkedService** para o **Serviço vinculado**. 

    ![Atividade de procedimento armazenado - Conta SQL](./media/tutorial-incremental-copy-portal/sp-activity-sql-account-settings.png)
26. Alterne para a guia **Procedimento armazenado** e execute as etapas a seguir: 

    1. Para **Nome do procedimento armazenado**, selecione **usp_write_watermark**. 
    2. Para especificar valores para os parâmetros de procedimento armazenado, clique em **Importar parâmetros** e insira os seguintes valores para os parâmetros: 

        | NOME | Tipo | Valor | 
        | ---- | ---- | ----- | 
        | LastModifiedtime | Datetime | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue} |
        | TableName | Cadeia de caracteres | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

    ![Atividade de Procedimento armazenado - configurações de procedimento armazenado](./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png)
27. Para validar as configurações de pipeline, clique em **Validar** na barra de ferramentas. Confirme se não houver nenhum erro de validação. Para fechar a janela **Relatório de validação do pipeline** clique em >>.   

    ![Validar o pipeline](./media/tutorial-incremental-copy-portal/validate-pipeline.png)
28. Publique as entidades (serviços vinculados, conjuntos de dados e pipelines) para o serviço de Azure Data Factory selecionando o botão **Publicar tudo**. Aguarde até que você veja a mensagem informando que a publicação foi bem-sucedida. 

    ![Botão Publicar](./media/tutorial-incremental-copy-portal/publish-button.png)

## <a name="trigger-a-pipeline-run"></a>Disparar uma execução de pipeline
1. Clique em **Gatilho** na barra de ferramentas e clique em **Disparar agora**. 

    ![Botão Disparar agora](./media/tutorial-incremental-copy-portal/trigger-now.png)
2. Na janela **Execução de Pipeline**, selecione **Concluir**. 

## <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline

1. Alterne para a guia **Monitorar** à esquerda. Você pode ver o status da execução do pipeline disparado pelo gatilho manual. Clique no botão **Atualizar** para atualizar a lista. 
    
    ![Execuções de pipeline](./media/tutorial-incremental-copy-portal/pipeline-runs.png)
2. Para exibir execuções de atividade associadas a esta execução de pipeline, clique no primeiro link (**Visualizar execuções de atividade**) na coluna **Ações**. Você pode alternar para o modo de exibição anterior clicando em **Pipelines** na parte superior. Clique no botão **Atualizar** para atualizar a lista.

    ![Execuções de atividade](./media/tutorial-incremental-copy-portal/activity-runs.png)

## <a name="review-the-results"></a>Revise os resultados
1. Conecte-se à sua Conta de Armazenamento do Azure usando ferramentas como o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/). Verifique se um arquivo de saída foi criado na pasta **incrementalcopy** no contêiner **adftutorial**.

    ![Primeiro arquivo de Saída](./media/tutorial-incremental-copy-portal/first-output-file.png)
2. Abra o arquivo de saída e observe que todos os dados são copiado do **data_source_table** para o arquivo de blobs. 

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
3. Verifique o valor mais recente de `watermarktable`. Você verá que o valor da marca d'água foi atualizado.

    ```sql
    Select * from watermarktable
    ```
    
    Esta é a saída:
 
    | TableName | WatermarkValue |
    | --------- | -------------- |
    | data_source_table | 2017-09-05    8:06:00.000 | 

## <a name="add-more-data-to-source"></a>Adicionar mais dados à origem

Insira novos dados no banco de dados SQL (repositório de fonte de dados).

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
``` 

Os dados atualizados no banco de dados SQL são:

```
PersonID | Name | LastModifytime
-------- | ---- | --------------
1 | aaaa | 2017-09-01 00:56:00.000
2 | bbbb | 2017-09-02 05:23:00.000
3 | cccc | 2017-09-03 02:36:00.000
4 | dddd | 2017-09-04 03:21:00.000
5 | eeee | 2017-09-05 08:06:00.000
6 | newdata | 2017-09-06 02:23:00.000
7 | newdata | 2017-09-07 09:01:00.000
```


## <a name="trigger-another-pipeline-run"></a>Disparar outra execução de pipeline
1. Alterne para a guia **Editar**. Clique no pipeline no modo de exibição de árvore caso ele não esteja aberto no designer. 

    ![Botão Disparar agora](./media/tutorial-incremental-copy-portal/edit-tab.png)
2. Clique em **Gatilho** na barra de ferramentas e clique em **Disparar agora**. 

    ![Botão Disparar agora](./media/tutorial-incremental-copy-portal/trigger-now.png)

## <a name="monitor-the-second-pipeline-run"></a>Monitorar a segunda execução do pipeline

1. Alterne para a guia **Monitorar** à esquerda. Você pode ver o status da execução do pipeline disparado pelo gatilho manual. Clique no botão **Atualizar** para atualizar a lista. 
    
    ![Execuções de pipeline](./media/tutorial-incremental-copy-portal/pipeline-runs-2.png)
2. Para exibir execuções de atividade associadas a esta execução de pipeline, clique no primeiro link (**Visualizar execuções de atividade**) na coluna **Ações**. Você pode alternar para o modo de exibição anterior clicando em **Pipelines** na parte superior. Clique no botão **Atualizar** para atualizar a lista.

    ![Execuções de atividade](./media/tutorial-incremental-copy-portal/activity-runs-2.png)

## <a name="verify-the-second-output"></a>Verifique a segunda saída

1. No Armazenamento de Blobs você verá que o outro arquivo foi criado. Neste tutorial, o nome do novo arquivo é `Incremental-<GUID>.txt`. Abra esse arquivo, você verá registros de duas linhas nele.

    ```
    6,newdata,2017-09-06 02:23:00.0000000
    7,newdata,2017-09-07 09:01:00.0000000    
    ```
2. Verifique o valor mais recente de `watermarktable`. Você verá que o valor da marca d'água foi atualizado novamente.

    ```sql
    Select * from watermarktable
    ```
    exemplo de saída: 
    
    | TableName | WatermarkValue |
    | --------- | --------------- |
    | data_source_table | 2017-09-07 09:01:00.000 |


     
## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você realizará os seguintes procedimentos: 

> [!div class="checklist"]
> * Prepare o armazenamento de dados para armazenar o valor de marca-d'água.
> * Criar um data factory.
> * Criar serviços vinculados. 
> * Criar os conjuntos de dados de origem, de coletor e de marca-d'água.
> * Crie um pipeline.
> * Execute o pipeline.
> * Monitorar a execução de pipeline. 
> * Analisar resultados
> * Adicionar mais dados à origem.
> * Executar o pipeline novamente.
> * Monitorar a segunda execução do pipeline
> * Examinar os resultados da segunda execução

Neste tutorial, o pipeline copiou dados de uma única tabela em um banco de dados SQL para um Armazenamento de Blobs. Avance para o tutorial a seguir para saber mais sobre como copiar dados de várias tabelas em um banco de dados do SQL Server local para um banco de dados SQL. 

> [!div class="nextstepaction"]
>[Carregar incrementalmente os dados de várias tabelas no SQL Server para o Banco de Dados SQL do Azure](tutorial-incremental-copy-multiple-tables-portal.md)



