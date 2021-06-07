---
title: Copiar dados em massa usando o portal do Azure
description: Use o Azure Data Factory e atividade de cópia para copiar dados em massa de um armazenamento de dados de origem para um armazenamento de dados de destino.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/29/2021
ms.openlocfilehash: bca2158f448f74ba596114fce5d1631249124a92
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606728"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory-in-the-azure-portal"></a>Copiar várias tabelas em massa usando o Azure Data Factory no portal do Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este tutorial demonstra como **copiar uma série de tabelas do Banco de Dados SQL do Azure para o Azure Synapse Analytics**. Você também pode aplicar o mesmo padrão em outros cenários de cópia. Por exemplo, copiando tabelas do SQL Server/Oracle para o Banco de Dados SQL do Azure/Azure Synapse Analytics/Blob do Azure e copiando diferentes caminhos do Blob para tabelas do Banco de Dados SQL do Azure.

> [!NOTE]
> Se estiver se familiarizando com o Azure Data Factory, confira [Introdução ao Azure Data Factory](introduction.md).

De forma mais abrangente, este tutorial envolve as seguintes etapas:

> [!div class="checklist"]
> * Criar um data factory.
> * Criar o Banco de Dados SQL do Azure, o Azure Synapse Analytics e os serviços vinculados do Armazenamento do Azure.
> * Criar conjuntos de dados do Banco de Dados SQL do Azure e do Azure Synapse Analytics.
> * Crie um pipeline para consultar as tabelas a serem copiadas e outro pipeline para executar a operação de cópia propriamente dita. 
> * Iniciar uma execução de pipeline.
> * Monitore as execuções de pipeline e de atividade.

Este tutorial usa o portal do Azure. Para obter informações sobre como usar outras ferramentas/SDKs para criar um data factory, consulte [Guias de início rápido](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Fluxos de trabalho completos
Nesse cenário, você tem várias tabelas no Banco de Dados SQL do Azure que deseja copiar para o Azure Synapse Analytics. Aqui está a sequência lógica de etapas no fluxo de trabalho que ocorre em pipelines:

![Fluxo de trabalho](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* O primeiro pipeline verifica a lista de tabelas que precisam ser copiadas nos armazenamentos de dados do coletor.  Alternativamente, você pode manter uma tabela de metadados que lista todas as tabelas a serem copiadas para o armazenamento de dados de coletor. Em seguida, o pipeline dispara outro pipeline, que faz iteração por cada tabela no banco de dados e executa a operação de cópia de dados.
* O segundo pipeline realiza a cópia propriamente dita. Ele usa a lista de tabelas como um parâmetro. Para cada tabela da lista, copie a tabela específica no Banco de Dados SQL do Azure para a tabela correspondente no Azure Synapse Analytics usando a [cópia preparada por meio do Armazenamento de Blobs e do PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics) para obter o melhor desempenho. Neste exemplo, o primeiro pipeline envia a lista de tabelas como um valor para o parâmetro. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* **Conta de Armazenamento do Azure**. A conta de Armazenamento do Azure é usada como Armazenamento de Blobs de preparo na operação de cópia em massa. 
* **Banco de dados SQL do Azure**. Este banco de dados contém os dados de origem. Crie um banco de dados contendo dados de exemplo do Adventure Works LT no Banco de Dados SQL, seguindo o artigo [Criar um banco de dados no Banco de Dados SQL do Azure](../azure-sql/database/single-database-create-quickstart.md) a seguir. Este tutorial copia todas as tabelas desse banco de dados de exemplo para o Azure Synapse Analytics.
* **Azure Synapse Analytics**. Esse data warehouse contém os dados copiados do Banco de Dados SQL. Se você não tiver um workspace do Azure Synapse Analytics, confira o artigo [Introdução ao Azure Synapse Analytics](..\synapse-analytics\get-started.md) para obter as etapas necessárias para criar um.

## <a name="azure-services-to-access-sql-server"></a>Permitir que os serviços do Azure acessem o SQL Server

Para o Banco de Dados SQL e o Azure Synapse Analytics, permita que os serviços do Azure acessem o SQL Server. Verifique se a configuração **Permitir acesso aos serviços e recursos do Azure para acessar este servidor** está **ATIVADA** para seu servidor. Essa configuração permite que o serviço Data Factory leia dados do Banco de Dados SQL do Azure e grave-os no Azure Synapse Analytics. 

Para verificar e ativar essa configuração, acesse seu servidor > Segurança > Firewalls e redes virtuais > defina a opção **Permitir que os serviços e recursos do Azure acessem este servidor** como **ATIVADA**.

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
1. Vá para o [Portal do Azure](https://portal.azure.com). 
1. À esquerda do menu do portal do Azure, selecione **Criar um recurso** > **Integração** > **Data Factory**. 

   ![Seleção de Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Na página **Novo data factory**, insira **ADFTutorialBulkCopyDF** como o **nome**. 
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se você se deparar com o seguinte erro para o campo nome, altere o nome do data factory (por exemplo, seunomeADFTutorialBulkCopyDF). Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
    ```text
    Data factory name "ADFTutorialBulkCopyDF" is not available
    ```
1. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
1. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
   - Selecione **Usar existente** e selecione um grupo de recursos existente na lista suspensa. 
   - Selecione **Criar novo** e insira o nome de um grupo de recursos.   
         
     Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md).  
1. Selecione **V2** para a **versão**.
1. Selecione o **local** do data factory. Para obter uma lista de regiões do Azure no qual o Data Factory está disponível no momento, selecione as regiões que relevantes para você na página a seguir e, em seguida, expanda **Análise** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.
1. Clique em **Criar**.
1. Após a conclusão da criação, selecione **Ir para o recurso** para navegar até a página do **Data Factory**. 
   
1. Clique no bloco **Autor & Monitor** para iniciar o aplicativo IU do Data Factory em uma guia separada.


## <a name="create-linked-services"></a>Criar serviços vinculados
Você cria os serviços vinculados para vincular seus armazenamentos de dados e serviços de computação ao data factory. Um serviço vinculado possui as informações de conexão que o serviço do Data Factory usa para conectar-se ao armazenamento de dados no runtime. 

Neste tutorial, você vinculará o Banco de Dados SQL do Azure, o Azure Synapse Analytics e os armazenamentos de dados do Armazenamento de Blobs do Azure ao seu data factory. O Banco de Dados SQL do Azure é o armazenamento de dados de origem. O Azure Synapse Analytics é o armazenamento de dados de coletor/destino. O Armazenamento de Blobs do Azure serve para preparar os dados antes de eles serem carregados no Azure Synapse Analytics usando o PolyBase. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Criar o serviço vinculado do Banco de Dados SQL do Azure de origem
Nesta etapa, você criará um serviço vinculado para vincular seu banco de dados no Banco de Dados SQL do Azure ao data factory. 

1. Abra a [guia Gerenciar](./author-management-hub.md) no painel esquerdo.

1. Na página Serviços vinculados, selecione **+Novo** para criar um serviço vinculado.

   :::image type="content" source="./media/doc-common-process/new-linked-service.png" alt-text="Novo serviço vinculado.":::
1. Na janela **Novo Serviço Vinculado**, selecione **Banco de Dados SQL do Azure** e clique em **Continuar**. 
1. Na janela **Novo serviço vinculado (Banco de Dados SQL do Azure)** , execute as etapas a seguir: 

    a. Insira **AzureSqlDatabaseLinkedService** para o **Nome**.

    b. Selecione o seu servidor para o **Nome do servidor**
    
    c. Em **Nome do banco de dados**, selecione seu banco de dados. 
    
    d. Digite **nome do usuário** para se conectar ao banco de dados. 
    
    e. Insira a **senha** do usuário. 

    f. Para testar a conexão ao banco de dados usando as informações especificadas, clique em **Testar conexão**.
  
    g. Clique em **Criar** para salvar o serviço vinculado.


### <a name="create-the-sink-azure-synapse-analytics-linked-service"></a>Criar o serviço vinculado do Azure Synapse Analytics coletor

1. Na guia **Conexões**, clique em **+ Novo** na barra de ferramentas novamente. 
1. Na janela **Novo Serviço Vinculado**, selecione **Azure Synapse Analytics** e clique em **Continuar**. 
1. Na janela **Novo Serviço Vinculado (Azure Synapse Analytics)** , execute as seguintes etapas: 
   
    a. Insira **AzureSqlDWLinkedService** para o **Nome**.
     
    b. Selecione o seu servidor para o **Nome do servidor**
     
    c. Em **Nome do banco de dados**, selecione seu banco de dados. 
     
    d. Insira **Nome de usuário** para se conectar ao seu banco de dados. 
     
    e. Insira a **Senha** do usuário. 
     
    f. Para testar a conexão ao banco de dados usando as informações especificadas, clique em **Testar conexão**.
     
    g. Clique em **Criar**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Criar o serviço vinculado do Armazenamento do Azure de preparo
Neste tutorial, você usa o Armazenamento de Blobs do Azure como uma área de preparação intermediária para habilitar o PolyBase para um melhor desempenho de cópia.

1. Na guia **Conexões**, clique em **+ Novo** na barra de ferramentas novamente. 
1. Na janela **Novo Serviço Vinculado**, selecione **Armazenamento de Blobs do Azure** e clique em **Continuar**. 
1. Na janela **Novo serviço vinculado (Armazenamento de Blobs do Azure)** , execute as etapas a seguir: 

    a. Insira **AzureStorageLinkedService** como o **Nome**.                                                 
    b. Selecione sua **conta de Armazenamento do Azure** como o **Nome da conta de armazenamento**.
    
    c. Clique em **Criar**.

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste tutorial você cria os conjuntos de dados de origem e do coletor, que especificam o local onde os dados são armazenados. 

O conjunto de dados de entrada **AzureSqlDatabaseDataset** refere-se a **AzureSqlDatabaseLinkedService**. O serviço vinculado especifica a cadeia de conexão para se conectar ao banco de dados. O conjunto de dados especifica o nome do banco de dados e da tabela que contêm os dados de origem. 

O conjunto de dados de saída **AzureSqlDWDataset** refere-se a **AzureSqlDWLinkedService**. O serviço vinculado especifica a cadeia de conexão para se conectar ao Azure Synapse Analytics. O conjunto de dados especifica o banco de dados e a tabela para a qual os dados são copiados. 

Neste tutorial, as tabelas SQL de origem e de destino não são embutidas nas definições de conjunto de dados. Em vez disso, a atividade ForEach passa o nome da tabela em runtime para a atividade de Cópia. 

### <a name="create-a-dataset-for-source-sql-database"></a>Criar um conjunto de dados para o Banco de Dados SQL de origem

1. Selecione a guia **Criar** no painel esquerdo.

1. Selecione o **+** (sinal de adição) no painel esquerdo e escolha **Conjunto de dados**. 

    ![Menu Novo conjunto de dados](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. Na janela **Novo Conjunto de dados**, selecione **Banco de Dados SQL do Azure** e clique em **Continuar**. 
    
1. Na janela **Definir propriedades**, em **Nome**, insira **AzureSqlDatabaseDataset**. Em **Serviço vinculado**, selecione **AzureSqlDatabaseLinkedService**. Em seguida, clique em **OK**.

1. Alterne para a guia **Conexão** e selecione qualquer tabela para **Tabela**. Esta é uma tabela fictícia. Você pode especificar uma consulta no conjunto de dados de origem ao criar um pipeline. A consulta é usada para extrair dados do seu banco de dados. Como alternativa, você pode clicar na caixa de seleção **Editar** e inserir **dbo.dummyName** como o nome da tabela. 
 

### <a name="create-a-dataset-for-sink-azure-synapse-analytics"></a>Crie um conjunto de dados para o Azure Synapse Analytics coletor 

1. Clique em **+ (adição)** no painel esquerdo e clique em **Conjunto de dados**. 
1. Na janela **Novo Conjunto de Dados**, selecione **Azure Synapse Analytics** e clique em **Continuar**.
1. Na janela **Definir propriedades**, em **Nome**, insira **AzureSqlDWDataset**. Em **Serviço vinculado**, selecione **AzureSqlDWLinkedService**. Em seguida, clique em **OK**.
1. Alterne para a guia **Parâmetros**, clique em **+ Novo** e digite **DWTableName** para o nome do parâmetro. Clique em **+ Novo** mais uma vez e digite **DWSchema** para o nome do parâmetro. Se você copiar/colar esse nome da página, exclua todos os **caracteres de espaço à direita** ao final de *DWTableName* e *DWSchema*. 
1. Alterne para a guia **Conexão**, 

    1. Para **Tabela**, marque a opção **Editar**. Selecione o interior da primeira caixa de entrada e clique no link **Adicionar conteúdo dinâmico** abaixo. Na página **Adicionar conteúdo dinâmico**, clique em **DWSchema** em **Parâmetros**, que preencherá automaticamente a caixa de texto de expressão `@dataset().DWSchema` na parte superior, depois clique em **Concluir**.  
    
        ![Nome da tabela da conexão de conjunto de dados](./media/tutorial-bulk-copy-portal/dataset-connection-tablename.png)

    1. Selecione o interior da segunda caixa de entrada e clique no link **Adicionar conteúdo dinâmico** abaixo. Na página **Adicionar conteúdo dinâmico**, clique em **DWTAbleName** em **Parâmetros**, que preencherá automaticamente a caixa de texto de expressão `@dataset().DWTableName` na parte superior e, em seguida, clique em **Concluir**. 
    
    1. A propriedade **tableName** do conjunto de dados é definida como os valores que são passados como argumentos para os parâmetros **DWSchema** e **DWTableName**. A atividade ForEach itera por meio de uma lista de tabelas e passa uma por uma para a atividade de Cópia. 
    

## <a name="create-pipelines"></a>Criar pipelines
Neste tutorial, você cria dois pipelines: **IterateAndCopySQLTables** e **GetTableListAndTriggerCopyData**. 

O pipeline **GetTableListAndTriggerCopyData** pipeline executa duas ações:

* Pesquisa a tabela do sistema do Banco de Dados SQL do Azure para obter a lista de tabelas a serem copiadas.
* Dispara o pipeline **IterateAndCopySQLTables** para fazer a cópia de dados propriamente dita.

O pipeline **IterateAndCopySQLTables** usa uma lista de tabelas como um parâmetro. Para cada tabela da lista, ele copia dados da tabela no Banco de Dados SQL do Azure para o Azure Synapse Analytics usando a cópia preparada e o PolyBase.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Criar o pipeline IterateAndCopySQLTables

1. No painel esquerdo, clique em **+ (adição)** e clique em **Pipeline**.

    ![Menu do novo pipeline](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
 
1. No painel Geral, em **Propriedades**, especifique **IterateAndCopySQLTables** para o **Nome**. Em seguida, recolha o painel clicando no ícone Propriedades no canto superior direito.

1. Alterne para a guia **Parâmetros** e faça o seguinte: 

    a. Clique em **+ Novo**. 
    
    b. Insira **tableList** para o parâmetro **Name**.
    
    c. Selecione **Matriz** para **Tipo**.

1. Na caixa de ferramentas **Atividades**, expanda **Iteração e condições** e arraste e solte a atividade **ForEach** para a superfície de designer do pipeline. Você também pode pesquisar atividades na caixa de ferramentas **Atividades**. 

    a. Na guia **Geral**, na parte inferior, digite **IterateSQLTables** para o **Nome**. 

    b. Alterne para a guia **Configurações**, clique na caixa de entrada para **Itens**, em seguida, clique no link **Adicionar conteúdo dinâmico** abaixo. 

    c. Na página **Adicionar Conteúdo Dinâmico**, recolha as seções **Variáveis do Sistema** e **Funções**, clique em **tableList** em **Parâmetros**, que preencherá automaticamente a caixa de texto de expressão superior para `@pipeline().parameter.tableList`. Em seguida, clique em **Concluir**. 

    ![Construtor do parâmetro foreach](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Alterne para a guia **Atividades**, clique no **ícone de lápis** para adicionar uma atividade filho para a atividade **ForEach**.
    
    ![Construtor de atividades ForEach](./media/tutorial-bulk-copy-portal/for-each-activity-builder.png)

1. Na caixa de ferramentas **Atividades**, expanda **Mover e Transferir** e arraste e solte a atividade **Copiar dados** para a superfície do designer de pipeline. Observe o menu de navegação estrutural na parte superior. **IterateAndCopySQLTable** é o nome do pipeline e **IterateSQLTables** é o nome da atividade ForEach. O designer está no escopo da atividade. Para voltar ao editor do pipeline do editor de ForEach, você pode clicar no link no menu de navegação estrutural. 

    ![Cópia no ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)

1. Alterne para a guia **Origem** e siga estas etapas:

    1. Selecione **AzureSqlDatabaseDataset** para **Conjunto de dados de origem**. 
    1. Selecione a opção de **Consulta** para **Usar consulta**. 
    1. Clique na caixa de entrada **Consulta** -> selecione **Adicionar conteúdo dinâmico** abaixo -> insira a seguinte expressão para **Consulta** -> selecione **Concluir**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 

1. Alterne para a guia **Coletor** e siga estas etapas: 

    1. Selecione **AzureSqlDWDataset** para **Conjunto de dados do coletor**.
    1. Clique na caixa de entrada para o VALUE do parâmetro DWTableName -> selecione **Adicionar conteúdo dinâmico** abaixo, insira a expressão `@item().TABLE_NAME` como script -> selecione **Concluir**.
    1. Clique na caixa de entrada para VALUE do parâmetro DWSchema -> selecione **Adicionar conteúdo dinâmico** abaixo, insira a expressão `@item().TABLE_SCHEMA` como script -> selecione **Concluir**.
    1. Para o método Copy, selecione **PolyBase**. 
    1. Desmarque a opção **Usar padrão do tipo**. 
    1. Para a opção Tabela, a configuração padrão é "Nenhum". Se você não tiver tabelas criadas previamente no coletor do Azure Synapse Analytics, habilite a opção **Criação automática de tabela**, a atividade Copy então criará automaticamente tabelas para você com base nos dados de origem. Para obter detalhes, confira [Criação automática de tabelas de coletor](copy-activity-overview.md#auto-create-sink-tables). 
    1. Clique na caixa de entrada **Pré-copiar Script** -> selecione **Adicionar conteúdo dinâmico** abaixo -> insira a seguinte expressão como script -> selecione **Concluir**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Copiar as configurações do coletor](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)

1. Alterne para a guia **Configurações** e siga estas etapas: 

    1. Marque a caixa de seleção para **Habilitar Preparo**.
    1. Selecione **AzureStorageLinkedService** como **Armazenar serviço vinculado da conta**.

1. Para validar as configurações de pipeline, clique em **Validar** na barra de ferramentas para o pipeline na parte superior. Verifique se não houve nenhum erro de validação. Para fechar o **Relatório de Validação de Pipeline**, clique nos colchetes duplos **>>** .

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Criar o pipeline GetTableListAndTriggerCopyData

Esse pipeline executa duas ações:

* Pesquisa a tabela do sistema do Banco de Dados SQL do Azure para obter a lista de tabelas a serem copiadas.
* Dispara o pipeline "IterateAndCopySQLTables" para fazer a cópia de dados propriamente dita.

Estas são as etapas necessárias para criar o pipeline:

1. No painel esquerdo, clique em **+ (adição)** e clique em **Pipeline**.
1. No painel Geral, sob **Propriedades**, altere o nome do pipeline para **GetTableListAndTriggerCopyData**. 

1. Na caixa de ferramentas **Atividades**, expanda **Geral** e arraste e solte a atividade de **Pesquisa** para a superfície do designer de pipeline e execute as seguintes etapas:

    1. Digite **LookupTableList** para **Nome**. 
    1. Digite **Recuperar a lista de tabelas do meu banco de dados** para **Descrição**.

1. Alterne para a guia **Configurações** e siga estas etapas:

    1. Selecione **AzureSqlDatabaseDataset** para **Conjunto de dados de origem**. 
    1. Selecione **Consulta** para **Usar consulta**. 
    1. Insira a seguinte consulta SQL para **Consulta**.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Desmarque a caixa de seleção para o campo **Somente primeira linha**.

        ![Atividade de pesquisa - página configurações](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Arraste e solte a atividade **Executar pipeline** da caixa de ferramentas Atividades para a superfície do designer do pipeline e defina o nome como **TriggerCopy**.

1. Para **Conectar** a atividade de **Pesquisa** à atividade **Executar Pipeline**, arraste a **caixa verde** associada à atividade de Pesquisa à esquerda da atividade Executar Pipeline.

    ![Conecte as atividades de Pesquisa e Executar pipeline](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)

1. Alterne para a guia **Configurações** da janela **Pipeline** e execute as seguintes etapas: 

    1. Selecione **IterateAndCopySQLTables** para **Pipeline invocado**. 
    1. Desmarque a caixa de seleção **Aguardar a conclusão**.
    1. Na seção **Parâmetros**, clique na caixa de entrada em VALUE -> selecione **Adicionar conteúdo dinâmico** abaixo -> insira `@activity('LookupTableList').output.value` como o valor do nome da tabela -> selecione **Concluir**. Você está configurando a lista de resultados da atividade de Pesquisa como uma entrada para o segundo pipeline. A lista de resultados contém a lista de tabelas cujos dados precisam ser copiados para o destino. 

        ![Atividade Executar pipeline - página de configurações](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)

1. Para validar o pipeline, clique em **Validar** na barra de ferramentas. Confirme se não houver nenhum erro de validação. Para fechar o **Relatório de validação do pipeline** clique em **>>** .

1. Para publicar entidades (conjuntos de dados, pipelines etc.) no serviço Data Factory, clique em **Publicar tudo** na parte superior da janela. Aguarde até que a publicação seja bem-sucedida. 

## <a name="trigger-a-pipeline-run"></a>Disparar uma execução de pipeline

1. Vá até o pipeline **GetTableListAndTriggerCopyData**, clique em **Adicionar Gatilho** na barra de ferramentas do pipeline superior e, em seguida, clique em **Disparar agora**. 

1. Confirme a execução na página **Execução do pipeline** e selecione **Concluir**.

## <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline

1. Alterne para a guia **Monitorar**. Clique em **Atualizar** até que você veja as execuções para ambos os pipelines em sua solução. Continue atualizando a lista até que você veja o status **Bem-sucedido**. 

1. Para exibir execuções de atividade associadas ao pipeline **GetTableListAndTriggerCopyData**, clique no link do nome do pipeline para o pipeline. Você deve ver duas execuções de atividade para essa execução de pipeline. 
    ![Monitorar execução do pipeline](./media/tutorial-bulk-copy-portal/monitor-pipeline.png)
1. Para exibir a saída da atividade de **Pesquisa**, clique no link **Saída** ao lado da atividade, sob a coluna **NOME DA ATIVIDADE**. Você pode maximizar e restaurar a janela **Saída**. Depois de revisar, clique em **X** para fechar a janela **Saída**.

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
1. Para voltar para a exibição **Execuções de Pipeline**, clique em **Todas as execuções de pipeline** na parte superior do menu de navegação estrutural. Clique no link **IterateAndCopySQLTables** (na coluna **NOME DO PIPELINE**) para exibir as execuções de atividade do pipeline. Observe que há uma execução de atividade de **Cópia** para cada tabela na saída da atividade **Pesquisa**. 

1. Confirme se os dados foram copiados para o Azure Synapse Analytics de destino usado neste tutorial. 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você realizará os seguintes procedimentos: 

> [!div class="checklist"]
> * Criar um data factory.
> * Criar o Banco de Dados SQL do Azure, o Azure Synapse Analytics e os serviços vinculados do Armazenamento do Azure.
> * Criar conjuntos de dados do Banco de Dados SQL do Azure e do Azure Synapse Analytics.
> * Crie um pipeline para consultar as tabelas a serem copiadas e outro pipeline para executar a operação de cópia propriamente dita. 
> * Iniciar uma execução de pipeline.
> * Monitore as execuções de pipeline e de atividade.

Avance para o tutorial a seguir para saber mais sobre como copiar dados incrementalmente de uma origem para um destino:
> [!div class="nextstepaction"]
>[Copiar dados incrementalmente](tutorial-incremental-copy-portal.md)
