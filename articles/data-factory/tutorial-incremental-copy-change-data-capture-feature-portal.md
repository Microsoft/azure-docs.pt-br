---
title: Copiar dados incrementalmente usando Captura de Dados de Alterações
description: Neste tutorial, você cria um pipeline de Azure Data Factory que copia os dados delta de modo incremental de uma tabela no banco de dados de Instância Gerenciada de SQL do Azure para o Armazenamento do Azure.
ms.author: nihurt
author: hurtn
ms.service: data-factory
ms.topic: tutorial
ms.date: 02/18/2021
ms.openlocfilehash: a00ec8698b188b8fa87935e498e8cfab3aeab5aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101724975"
---
# <a name="incrementally-load-data-from-azure-sql-managed-instance-to-azure-storage-using-change-data-capture-cdc"></a>Carregar incrementalmente dados da Instância Gerenciada de SQL do Azure para o Armazenamento do Azure usando CDA (captura de dados de alterações)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Neste tutorial, você cria um Azure Data Factory com um pipeline que carrega dados delta com base em informações de **CDA (captura de dados de alterações)** no banco de dados de Instância Gerenciada de SQL do Azure de origem para um armazenamento de blobs do Azure.  

Neste tutorial, você realizará os seguintes procedimentos:

> [!div class="checklist"]
> * Prepare o armazenamento de dados de origem
> * Criar um data factory.
> * Criar serviços vinculados.
> * Crie conjuntos de dados de origem e de coletor.
> * Criar, depurar e executar o pipeline para verificar se há dados alterados
> * Modificar dados na tabela de origem
> * Concluir, executar e monitorar o pipeline de cópia incremental completo

## <a name="overview"></a>Visão geral
A tecnologia de captura de dados de alterações compatível com armazenamentos de dados, como MI (instâncias gerenciadas) do Azure SQL e SQL Server, pode ser usada para identificar dados alterados.  Este tutorial descreve como usar o Azure Data Factory com a tecnologia de Captura de Dados de Alterações do SQL para carregar incrementalmente os dados delta da Instância Gerenciada de SQL do Azure no Armazenamento de Blobs do Azure.  Para obter informações mais concretas sobre a tecnologia de captura de dados de alterações do SQL, confira [Captura de dados de alterações no SQL Server](/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

## <a name="end-to-end-workflow"></a>Fluxos de trabalho completos
Estas são as etapas normais de fluxo de trabalho de ponta a ponta para carregar incrementalmente os dados usando a tecnologia de Captura de Dados de Alterações.

> [!NOTE]
> A MI do Azure SQL e o SQL Server dão suporte à tecnologia de captura de dados de alterações. Este tutorial usa a Instância Gerenciada de SQL do Azure como o armazenamento de dados de origem. Você também pode usar um SQL Server local.

## <a name="high-level-solution"></a>Solução de alto nível
Neste tutorial, você cria um pipeline que executa as seguintes operações:  

   1. Crie uma **atividade de procura** para contar o número de registros alterados na tabela CDA do Banco de Dados SQL e passá-lo para uma atividade IF Condition.
   2. Crie uma **If Condition** para verificar se há registros alterados e, se houver, invocar a atividade de cópia.
   3. Crie uma **atividade de cópia** para copiar os dados inseridos/atualizados/excluídos entre a tabela do CDA para o armazenamento de Blobs do Azure.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* **Instância Gerenciada do Banco de Dados SQL do Azure**. Você usa o banco de dados como um armazenamento de dados de **origem**. Se você não tiver um Instância Gerenciada do Banco de Dados SQL do Azure, confira o artigo [Criar um Instância Gerenciada do Banco de Dados SQL do Azure](../azure-sql/managed-instance/instance-create-quickstart.md) para conhecer as etapas para criar uma.
* **Conta de Armazenamento do Azure**. Você usa o Armazenamento de Blobs como um armazenamento de dados de **coletor**. Se você não tiver uma conta de Armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../storage/common/storage-account-create.md) para conhecer as etapas para criar uma. Crie um contêiner chamado **raw**. 

### <a name="create-a-data-source-table-in-azure-sql-database"></a>Criar uma tabela de fonte de dados no Banco de Dados SQL do Azure

1. Inicie o **SQL Server Management Studio** e conecte-se ao servidor de Instâncias Gerenciadas do SQL do Azure.
2. No **Gerenciador de Servidores**, clique com o botão direito do mouse no **banco de dados** e escolha **Nova Consulta**.
3. Execute o comando SQL a seguir no banco de dados de Instâncias Gerenciadas do SQL do Azure para criar uma tabela chamada `customers` como o repositório de fonte de dados.  

    ```sql
    create table customers 
    (
    customer_id int, 
    first_name varchar(50), 
    last_name varchar(50), 
    email varchar(100), 
    city varchar(50), CONSTRAINT "PK_Customers" PRIMARY KEY CLUSTERED ("customer_id") 
     );
    ```
4. Habilite mecanismo de **Captura de Dados de Alterações** no banco de dados e na tabela de origem (clientes) executando a seguinte consulta SQL:

    > [!NOTE]
    > - Substitua &lt;seu nome de esquema de origem&gt; pelo esquema da MI do SQL do Azure que tem a tabela de clientes.
    > - A captura de dados de alterações não faz nada como parte das transações que alteram a tabela que está sendo controlada. Em vez disso, as operações de inserção, atualização e exclusão são gravadas no log de transações. Os dados depositados nas tabelas de alteração aumentam de modo não gerenciável se você não diminuir periódica e sistematicamente os dados. Para obter mais informações, confira [Habilitar a Captura de Dados de Alterações para um banco de dados](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server#enable-change-data-capture-for-a-database)

    ```sql
    EXEC sys.sp_cdc_enable_db 
    
    EXEC sys.sp_cdc_enable_table
    @source_schema = 'dbo',
    @source_name = 'customers', 
    @role_name = 'null',
    @supports_net_changes = 1
    ```
5. Insira dados na tabela de clientes executando o seguinte comando:

    ```sql
     insert into customers 
         (customer_id, first_name, last_name, email, city) 
     values 
         (1, 'Chevy', 'Leward', 'cleward0@mapy.cz', 'Reading'),
         (2, 'Sayre', 'Ateggart', 'sateggart1@nih.gov', 'Portsmouth'),
        (3, 'Nathalia', 'Seckom', 'nseckom2@blogger.com', 'Portsmouth');
    ```

    > [!NOTE]
    > Nenhuma alteração histórica na tabela é capturada antes da habilitação da captura de dados de alterações.

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
1. No menu à esquerda, selecione **Criar um recurso** > **Dados + Análise** > **Data Factory**:

   ![Seleção de Data Factory no painel "Novo"](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory-menu.png)

2. Na página **Novo data factory**, insira **ADFTutorialDataFactory** no campo **nome**.

     ![Página de novo data factory](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory.png)

   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se você receber o seguinte erro, altere o nome de data factory (por exemplo, yournameADFTutorialDataFactory) e tente criar novamente. Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.

    *O nome de data factory "ADFTutorialDataFactory" não está disponível.*
3. Selecione **V2** para a **versão**.
4. Selecione a **assinatura** do Azure na qual você deseja criar o data factory.
5. Para o **Grupo de Recursos**, execute uma das seguintes etapas:

   1. Selecione **Usar existente** e selecione um grupo de recursos existente na lista suspensa.
   2. Selecione **Criar novo** e insira o nome de um grupo de recursos.   
         
    Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md).  
5. Selecione o **local** do data factory. Apenas os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.
6. Desmarque **Habilitar GIT**.     
7. Clique em **Criar**.
8. Depois que a implantação estiver concluída, selecione **Ir para o recurso**

   ![A captura de tela mostra uma mensagem informando que sua implantação foi concluída, além da opção de acessar o recurso.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-deploy-complete.png)
9. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem.

   ![A captura de tela mostra o data factory que você implantou.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-home-page.png)
10. Clique no bloco **Criar e Monitorar** para iniciar a interface do usuário do Azure Data Factory em uma guia separada.
11. Na página de **introdução**, alterne para a guia **Editar** no painel esquerdo, conforme mostrado na imagem a seguir:

    ![Criar botão de pipeline](./media/tutorial-incremental-copy-change-data-capture-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>Criar serviços vinculados
Os serviços vinculados são criados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Nesta seção, você cria serviços vinculados para sua conta de Armazenamento do Azure e a MI do SQL do Azure.

### <a name="create-azure-storage-linked-service"></a>Crie um serviço vinculado do Armazenamento do Azure.
Nesta etapa, você vincula a Conta de Armazenamento do Azure ao data factory.

1. Clique em **Conexões** e clique em **+ Novo**.

   ![Botão Nova conexão](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-connection-button-storage.png)
2. Na janela **Novo Serviço Vinculado**, selecione **Armazenamento de Blobs do Azure** e clique em **Continuar**.

   ![Selecionar Armazenamento de Blobs do Azure](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-storage.png)
3. Na janela **Novo Serviço Vinculado**, execute estas etapas:

   1. Insira **AzureStorageLinkedService** como o **Nome**.
   2. Selecione sua conta de Armazenamento do Azure como o **Nome da conta de armazenamento**.
   3. Clique em **Save** (Salvar).

   ![Configurações da Conta de Armazenamento do Azure](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-mi-database-linked-service"></a>Crie um serviço vinculado do Banco de Dados de MI do SQL do Azure.
Nesta etapa, você vincula o banco de dados de MI SQL do Azure ao data factory.

> [!NOTE]
> Se você usa a MI do SQL, obtenha [aqui](./connector-azure-sql-managed-instance.md#prerequisites) informações sobre o acesso via ponto de extremidade público vs. privado. Se você estiver usando um ponto de extremidade privado, será necessário executar esse pipeline usando um runtime de integração auto-hospedada. O mesmo se aplica a quem executa o SQL Server nos cenários local, de VM ou de VNet.

1. Clique em **Conexões** e clique em **+ Novo**.
2. Na janela **Novo Serviço Vinculado**, selecione **Instância Gerenciada do Banco de Dados SQL do Azure** e clique em **Continuar**.
3. Na janela **Novo Serviço Vinculado**, execute estas etapas:

   1. Insira **AzureSqlMI1** para o campo **Nome**.
   2. Selecione o SQL Server no campo **Nome do servidor**.
   4. Selecione o banco de dados SQL no campo **Nome do banco de dados**.
   5. Insira o nome do usuário no campo **Nome de usuário**.
   6. Insira a senha do usuário no campo **Senha**.
   7. Clique em **Testar conectividade** para testar a conexão.
   8. Clique em **Salvar** para salvar o serviço vinculado.

   ![Configurações do serviço vinculado para a MI do Banco de Dados SQL do Azure](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-sql-managed-instance-database-linked-service-settings.png)

## <a name="create-datasets"></a>Criar conjuntos de dados
Nesta etapa, você criará conjuntos de dados para representar a fonte de dados e o destino dos dados.

### <a name="create-a-dataset-to-represent-source-data"></a>Criar um conjunto de dados para representar dados de origem
Nesta etapa, você cria conjuntos de dados para representar os dados de origem.

1. No modo de exibição de árvore, clique em **+ (adição)** e clique em **Conjunto de dados**.

   ![Menu Novo conjunto de dados](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. Selecione **Instância Gerenciada do Banco de Dados SQL do Azure** e clique em **Continuar**.

   ![Tipo de conjunto de dados de origem - Banco de Dados SQL do Azure](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-sql-database.png)
   
3. Na guia **Definir propriedades**, defina o nome do conjunto de dados e as informações de conexão:
 
   1. Selecione **AzureSqlMI1** para **Serviço vinculado**.
   2. Selecione **[dbo].[dbo_customers_CT]** para **Nome da tabela**.  Observação: essa tabela foi criada automaticamente quando o CDA foi habilitado na tabela clientes. Os dados alterados nunca são consultados dessa tabela diretamente, mas, em vez disso, são extraídos por meio das [funções de CDA](/sql/relational-databases/system-functions/change-data-capture-functions-transact-sql).

   ![Conexão de Origem](./media/tutorial-incremental-copy-change-data-capture-feature-portal/source-dataset-configuration.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>Criar um conjunto de dados para representar dados copiados para o armazenamento de dados do coletor.
Nesta etapa, você cria um conjunto de dados para representar os dados copiados do armazenamento de dados de origem. Você cria o contêiner de data lake em seu Armazenamento de Blobs do Azure como parte dos pré-requisitos. Crie o contêiner caso ele não exista ou defina-o para o nome de um contêiner existente. Neste tutorial, o nome do arquivo de saída é gerado dinamicamente usando o tempo de disparo, que será configurado posteriormente.

1. No modo de exibição de árvore, clique em **+ (adição)** e clique em **Conjunto de dados**.

   ![Menu Novo conjunto de dados](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. Selecione **Armazenamento de Blobs do Azure** e clique em **Continuar**.

   ![Tipo de conjunto de dados do coletor - Armazenamento de Blobs do Azure](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-type.png)
3. Selecione **DelimitedText** e clique em **Continuar**.

   ![Formato do conjunto de dados do coletor – DelimitedText](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-format.png)
4. Na guia **Definir Propriedades**, defina o nome do conjunto de dados e as informações de conexão:

   1. Selecione **AzureStorageLinkedService** para **Serviço vinculado**.
   2. Insira **raw** para a parte **contêiner** do **filePath**.
   3. Habilitar **Usar primeira linha como cabeçalho**
   4. Clique em **Ok**

   ![Conjunto de dados do coletor - conexão](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration.png)

## <a name="create-a-pipeline-to-copy-the-changed-data"></a>Criar um pipeline para copiar os dados alterados
Nesta etapa, você cria um pipeline, que primeiro verifica o número de registros alterados presentes na tabela de alteração usando uma **atividade de pesquisa**. Uma atividade IF Condition verifica se o número de registros alterados é maior que zero e executa uma **atividade de cópia** para copiar os dados inseridos/atualizados/excluídos do Banco de Dados SQL do Azure para o Armazenamento de Blobs do Azure. Por fim, um gatilho de janela em cascata é configurado e os horários de início e término serão passados para as atividades como os parâmetros de janela inicial e final. 

1. Na interface de usuário do Data Factory, alterne para a guia **Editar**. Clique em **+ (adição)** no painel esquerdo e clique em **Pipeline**.

    ![Menu do novo pipeline](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-pipeline-menu.png)
2. Você verá uma nova guia para configuração do pipeline. Você também verá o pipeline no modo de exibição de árvore. Na janela **Propriedades**, altere o nome do pipeline para **IncrementalCopyPipeline**.

    ![Nome do pipeline](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-name.png)
3. Expanda **Geral** na caixa de ferramentas **Atividades** e arraste e solte a atividade de **Pesquisa** para a superfície do designer de pipeline. Defina o nome da atividade como **GetChangeCount**. Essa atividade obtém o número de registros na tabela de alteração de uma determinada janela de tempo.

    ![Atividade de pesquisa - nome](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-name.png)
4. Alterne para a guia **Configurações** na janela **Propriedades**:
   1. Especifique o nome do conjunto de dados de MI do SQL para o campo **Conjunto de Dados de Origem**.
   2. Selecione a opção de consulta e insira o seguinte na caixa de consulta:
    ```sql
    DECLARE  @from_lsn binary(10), @to_lsn binary(10);  
    SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers');  
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal',  GETDATE());
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
    ```
   3. Habilitar **Primeira linha somente**

    ![Atividade de pesquisa - configurações](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-settings.png)
5. Clique no botão **Visualizar dados** para garantir que uma saída válida seja obtida pela atividade de pesquisa

    ![Atividade de pesquisa – visualização](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-preview.png)
6. Expanda **Iteração e condicionais** na caixa de ferramenta **Atividades** e arraste e solte a atividade **If Condition** para a superfície de designer do pipeline. Defina o nome da atividade como **HasChangedRows**. 

    ![Atividade If Condition – nome](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-name.png)
7. Alterne para a **Atividades** na janela **Propriedades**:

   1. Insira a **Expressão** a seguir
   
    ```adf
    @greater(int(activity('GetChangeCount').output.firstRow.changecount),0)
    ```

   2. Clique no ícone de lápis para editar a condição True.

   ![Atividade If Condition – configurações](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-setting.png)

   3. Expanda **Geral** na caixa de ferramentas **Atividades** e arraste e solte uma atividade **Wait** para a superfície do designer do pipeline. Essa é uma atividade temporária para depurar a If Condition e será alterada posteriormente no tutorial. 

   ![If Condition True – esperar](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-wait.png)

   4. Clique na trilha IncrementalCopyPipeline para voltar ao pipeline principal.

8. Execute o pipeline no modo **Depuração** para verificar se o pipeline é executado com êxito. 

   ![Pipeline – depurar](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug.png)
9. Em seguida, retorne à etapa da condição True e exclua a atividade **Esperar**. Na caixa de ferramentas **Atividades**, expanda **Mover e Transformar** e arraste e solte a atividade **Copy** para a superfície do designer de pipeline. Defina o nome da atividade como **IncrementalCopyActivity**. 

   ![Atividade de cópia - nome](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-name.png)
10. Alterne para a guia **Fonte** na janela **Propriedades** e execute as seguintes etapas:

   1. Especifique o nome do conjunto de dados de MI do SQL para o campo **Conjunto de Dados de Origem**. 
   2. Selecione **Consulta** para **Usar consulta**.
   3. Insira o seguinte para **Consulta**.

      ```sql
      DECLARE @from_lsn binary(10), @to_lsn binary(10); 
      SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers'); 
      SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal', GETDATE());
      SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
      ```

   ![Atividade de cópia - configurações da fonte](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-settings.png)

11. Clique em visualizar para verificar se a consulta retorna as linhas alteradas corretamente.

    ![A captura de tela mostra uma visualização para verificar a consulta.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-preview.png)
12. Alterne para a guia **Coletor** e especifique o conjunto de dados de Armazenamento do Azure para o campo **Conjunto de Dados do Coletor**.

    ![A captura de tela mostra a guia Coletor.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-sink-settings.png)
13. Clique em voltar à tela do pipeline principal e conecte a atividade de **Procurar** à atividade **If Condition** uma a uma. Arraste o botão **verde** anexado à atividade de **Pesquisa** para a atividade de **If Condition**.

    ![Conecte as atividades de Pesquisa e Cópia](./media/tutorial-incremental-copy-change-data-capture-feature-portal/connect-lookup-if.png)
14. Clique em **Validar** na barra de ferramentas. Confirme se não houver nenhum erro de validação. Feche a janela **Relatório de validação do pipeline** clicando em **>>** .

    ![Botão Validar](./media/tutorial-incremental-copy-change-data-capture-feature-portal/validate-button.png)
15. Clique em Depurar para testar o pipeline e verificar se um arquivo é gerado no local de armazenamento.

    ![Depuração incremental do pipeline-2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-2.png)
16. Publique as entidades (serviços vinculados, conjuntos de dados e pipelines) para o serviço de Data Factory clicando no botão **Publicar tudo**. Aguarde até que você veja a mensagem **Publicado com êxito**.

    ![Botão Publicar](./media/tutorial-incremental-copy-change-data-capture-feature-portal/publish-button-2.png)    

### <a name="configure-the-tumbling-window-trigger-and-cdc-window-parameters"></a>Configurar o gatilho da janela em cascata e os parâmetros da janela do CDA 
Nesta etapa, você cria um gatilho de janela em cascata para executar o trabalho conforme um agendamento frequente. Você usará as variáveis de sistema WindowStart e WindowEnd do gatilho de janela em cascata e as passará como parâmetros para o pipeline a ser usado na consulta do CDA.

1. Navegue até a guia **Parâmetros** do pipeline **IncrementalCopyPipeline** e, usando o botão **+ Novo**, adicione dois parâmetros (**triggerStartTime** e **triggerEndTime**) ao pipeline, que representarão a hora de início e de término da janela em cascata. Para depuração, adicione valores padrão no formato **YYYY-MM-DD HH24:MI:SS.FFF**, verificando se triggerStartTime não é anterior ao CDA que está sendo habilitado na tabela. Caso contrário, isso resultará em um erro.

    ![Menu Disparar agora](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-parameters.png)
2. Clique na guia configurações da atividade **Procurar** e configure a consulta para usar os parâmetros de início e término. Copie o seguinte para a consulta:
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```

3. Navegue até a atividade **Copiar** no caso True da atividade **If Condition** e clique na guia **Origem**. Copie o seguinte para a consulta:
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```
4. Clique na guia **Coletor** da atividade **Copiar** e clique em **Abrir** para editar as propriedades do conjunto de dados. Clique na guia **Parâmetros** e adicione um novo parâmetro chamado **triggerStart**    

    ![A captura de tela mostra como adicionar um novo parâmetro à guia Parâmetros.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-2.png)
5. Em seguida, configure as propriedades do conjunto de dados para armazená-lo em um subdiretório **clientes/incremental** com partições baseadas em data.
   1. Clique na guia **Conexão** das propriedades do conjunto de dados e adicione conteúdo dinâmico às seções **Diretório** e **Arquivo**. 
   2. Insira a expressão a seguir na seção **Diretório** clicando no link de conteúdo dinâmico na caixa de texto:
    
    ```sql
    @concat('customers/incremental/',formatDateTime(dataset().triggerStart,'yyyy/MM/dd'))
    ```
   3. Insira a expressão a seguir na seção **Arquivo**. Isso criará nomes de arquivo com base na data e hora de início do gatilho, com o sufixo da extensão CSV:
    
    ```sql
    @concat(formatDateTime(dataset().triggerStart,'yyyyMMddHHmmssfff'),'.csv')
    ```
    ![Configuração do conjunto de dados do coletor-3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-3.png)

   4. Navegue de volta para as configurações do **Coletor** na atividade **Copiar** clicando na guia **IncrementalCopyPipeline**. 
   5. Expanda as propriedades do conjunto de dados e insira o conteúdo dinâmico no valor do parâmetro triggerStart com a seguinte expressão:
     ```sql
     @pipeline().parameters.triggerStartTime
     ```
    ![Configuração do conjunto de dados do coletor-4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-4.png)

6. Clique em Depurar para testar o pipeline e garantir que a estrutura de pastas e o arquivo de saída sejam gerados conforme o esperado. Baixe e abra o arquivo para verificar o conteúdo. 

    ![Depuração de cópia incremental-3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-3.png)
7. Verifique se os parâmetros estão sendo injetados na consulta examinando os parâmetros de entrada da execução do pipeline.

    ![Depuração de cópia incremental-4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-4.png)
8. Publique as entidades (serviços vinculados, conjuntos de dados e pipelines) para o serviço de Data Factory clicando no botão **Publicar tudo**. Aguarde até que você veja a mensagem **Publicado com êxito**.
9. Por fim, configure um gatilho de janela em cascata para executar o pipeline a intervalos regulares e definir os parâmetros de hora de início e término. 
   1. Clique no botão **Adicionar gatilho** e selecione **Novo/Editar**

   ![Adicionar Novo Gatilho](./media/tutorial-incremental-copy-change-data-capture-feature-portal/add-trigger.png)

   2. Insira um nome de gatilho e especifique uma hora de início, que é igual à hora de término da janela de depuração acima.

   ![Gatilho de janela em cascata](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger.png)

   3. Na próxima tela, especifique os valores a seguir para os parâmetros de início e término, respectivamente.
    ```sql
    @formatDateTime(trigger().outputs.windowStartTime,'yyyy-MM-dd HH:mm:ss.fff')
    @formatDateTime(trigger().outputs.windowEndTime,'yyyy-MM-dd HH:mm:ss.fff')
    ```

   ![Gatilho de janela em cascata-2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger-2.png)

> [!NOTE]
> Observe que o gatilho só será executado depois que tiver sido publicado. Além disso, o comportamento esperado da janela em cascata é executar todos os intervalos históricos da data de início até agora. Mais informações sobre os gatilhos de janela em cascata podem ser encontradas [aqui](./how-to-create-tumbling-window-trigger.md). 
  
10. Usando o **SQL Server Management Studio**, faça algumas alterações adicionais à tabela cliente executando o seguinte SQL:
    ```sql
    insert into customers (customer_id, first_name, last_name, email, city) values (4, 'Farlie', 'Hadigate', 'fhadigate3@zdnet.com', 'Reading');
    insert into customers (customer_id, first_name, last_name, email, city) values (5, 'Anet', 'MacColm', 'amaccolm4@yellowbook.com', 'Portsmouth');
    insert into customers (customer_id, first_name, last_name, email, city) values (6, 'Elonore', 'Bearham', 'ebearham5@ebay.co.uk', 'Portsmouth');
    update customers set first_name='Elon' where customer_id=6;
    delete from customers where customer_id=5;
    ```
11. Clique no botão **Publicar tudo**. Aguarde até que você veja a mensagem **Publicado com êxito**.  
12. Depois de alguns minutos, o pipeline será disparado e um novo arquivo será carregado no Armazenamento do Azure


### <a name="monitor-the-incremental-copy-pipeline"></a>Monitorar o pipeline de cópia incremental
1. Clique na guia **Monitorar** à esquerda. Você verá a execução do pipeline na lista e o seu respectivo status. Para atualizar a lista, clique em **Atualizar**. Passe o mouse perto do nome do pipeline para acessar a ação executar novamente e o relatório de consumo.

    ![Execuções de pipeline](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-pipeline-runs.png)
2. Para exibir as execuções de atividade associadas à execução do pipeline, clique no nome do pipeline. Se forem detectados dados alterados, haverá três atividades, incluindo a atividade de cópia; caso contrário, haverá apenas duas entradas na lista. Para alternar novamente para a exibição de execuções de pipeline, clique no link **Todos os Pipelines** na parte superior.

    ![Execuções de atividade](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-activity-runs.png)


### <a name="review-the-results"></a>Revise os resultados
Você verá o segundo arquivo na pasta `customers/incremental/YYYY/MM/DD` do contêiner `raw`.

![Arquivo de saída da cópia incremental](media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-run.png)
 

## <a name="next-steps"></a>Próximas etapas
Avance para o tutorial seguinte para saber mais sobre como copiar arquivos novos e alterados somente com base na LastModifiedDate:

> [!div class="nextstepaction"]
>[Copiar novos arquivos por lastmodifieddate](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
