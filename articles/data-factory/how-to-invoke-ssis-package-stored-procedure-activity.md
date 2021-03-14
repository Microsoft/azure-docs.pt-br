---
title: Executar pacote do SSIS com atividade de procedimento armazenado-Azure
description: Este artigo descreve como executar um pacote SSIS (SQL Server Integration Services) em um pipeline do Azure Data Factory usando a Atividade de Procedimento Armazenado.
author: swinarko
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6260606fe56d4dfc6bac93e04e726b5fd3298777
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391500"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>Executar um pacote SSIS usando a atividade de Procedimento Armazenado no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como executar um pacote SSIS em um pipeline do Azure Data Factory usando uma atividade de Procedimento Armazenado. 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure 
As instruções neste artigo usam o banco de dados SQL do Azure para hospedar o catálogo do SSIS. Você também pode usar o Azure SQL Instância Gerenciada.

## <a name="create-an-azure-ssis-integration-runtime"></a>Criar um Integration Runtime do Azure-SSIS
Crie um Integration Runtime do Azure-SSIS, caso você não tenha um, seguindo as instruções passo a passo no [Tutorial: Implantar pacotes do SSIS](./tutorial-deploy-ssis-packages-azure.md).

## <a name="data-factory-ui-azure-portal"></a>Interface do usuário do Data Factory no Portal do Azure
Nesta seção, você usa a interface do usuário do Data Factory para criar um pipeline do Data Factory com uma atividade de procedimento armazenado que invoca um pacote do SSIS.

### <a name="create-a-data-factory"></a>Criar um data factory
A primeira etapa é criar uma data factory usando o Portal do Azure. 

1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
2. Navegue até o [Portal do Azure](https://portal.azure.com). 
3. Clique em **Novo** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Na página **Novo data factory**, insira **ADFTutorialDataFactory** no campo **nome**. 
      
     ![Página de novo data factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se a seguinte mensagem de erro for exibida para o campo nome, altere o nome do data factory (por exemplo, yournameADFTutorialDataFactory). Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
     ![Erro - nome não disponível](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
4. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
   - Selecione **Usar existente** e selecione um grupo de recursos existente na lista suspensa. 
   - Selecione **Criar novo** e insira o nome de um grupo de recursos.   
         
     Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md).  
4. Selecione **V2** para a **versão**.
5. Selecione o **local** do data factory. Apenas os locais com suporte do Data Factory são mostrados na lista suspensa. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outros locais.
6. Selecione **Fixar no painel**.     
7. Clique em **Criar**.
8. No painel, você deve ver o seguinte bloco com status: **Implantando data factory**. 

     ![implantando bloco data factory](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem.
   
     ![Página inicial do data factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Clique no bloco **Criar e Monitorar** para iniciar o aplicativo de interface do usuário (IU) do Azure Data Factory em uma guia separada. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Criar um pipeline com atividade de procedimento armazenado
Nesta etapa, você usa a interface do usuário do Data Factory para criar um pipeline. Você adiciona uma atividade de procedimento armazenado ao pipeline e configura-o para executar o pacote SSIS usando o procedimento armazenado sp_executesql. 

1. Na página de introdução, clique em **criar pipeline**: 

    ![Página Introdução](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. Na caixa de ferramentas **Atividades**, expanda **Geral** e arraste e solte a atividade **Procedimento Armazenado** na superfície do designer de pipeline. 

    ![Arrastar e solta a atividade de procedimento armazenado](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. Na janela de propriedades para a atividade do procedimento armazenado, alterne para a guia **Conta SQL** e clique em **+ Novo**. Você cria uma conexão com o banco de dados no banco de dados SQL do Azure que hospeda o catálogo do SSIS (banco de dados banco SSIDB). 
   
    ![Botão Novo serviço vinculado](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. Na janela **Novo Serviço Vinculado**, execute estas etapas: 

    1. Selecione **Banco de Dados SQL do Azure** para **Tipo**.
    2. Selecione o Azure Integration Runtime **Padrão** para se conectar ao Banco de Dados SQL do Azure que hospeda o banco de dados `SSISDB`.
    3. Selecione o Banco de Dados SQL do Azure que hospeda o banco de dados SSISDB para o campo **Nome do servidor**.
    4. Selecione **SSISDB** para **Nome do Banco de Dados**.
    5. Para **Nome de usuário**, insira o nome do usuário que tem acesso ao banco de dados.
    6. Para **Senha**, insira a senha do usuário. 
    7. Teste a conexão com o banco de dados, clicando no botão **Testar conexão**.
    8. Salve o serviço vinculado. clicando no botão **Salvar**. 

        ![Captura de tela que mostra o processo de adição de um novo serviço vinculado.](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. Na janela Propriedades, alterne para a guia **procedimento armazenado** da guia **conta SQL** e execute as seguintes etapas: 

    1. Selecione **Editar**. 
    2. Para o campo **nome do procedimento armazenado** , digite `sp_executesql` . 
    3. Clique em **+ Novo** na seção **Parâmetros do procedimento armazenado**. 
    4. Para o **nome** do parâmetro, insira **stmt**. 
    5. Para o **tipo** do parâmetro, insira **Cadeia de caracteres**. 
    6. Para o **valor** do parâmetro, insira a consulta SQL a seguir:

        Na consulta SQL, especifique os valores certos para os parâmetros **nom_da_pasta**, **nome_do_projeto** e **nome_do_pacote**. 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Serviço vinculado para o Banco de Dados SQL do Azure](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. Para validar a configuração do pipeline, clique em **Validar** na barra de ferramentas. Para fechar o **Relatório de validação do pipeline** clique em **>>** .

    ![Validar o pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Publique o pipeline para Data Factory, clicando no botão **Publicar Tudo**. 

    ![Publicação](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>Executar e monitorar o pipeline
Nesta seção, você dispara uma execução do pipeline e, em seguida, faz o monitoramento. 

1. Para disparar uma execução de pipeline, clique em **gatilho** na barra de ferramentas e clique em **disparar agora**. 

    ![Disparar agora](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. Na janela **Execução de Pipeline**, selecione **Concluir**. 
3. Alterne para a guia **Monitorar** à esquerda. Você verá o pipeline de execução e seu status junto com outras informações (como a Hora de início da execução). Para atualizar o modo de exibição, clique em **Atualizar**.

    ![Execuções de pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Clique no link **Exibir Execuções da atividade** na coluna **Ações**. Você verá apenas uma execução da atividade, pois o pipeline possui apenas uma atividade (atividade de procedimento armazenado).

    ![Execuções de atividade](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. Você pode executar a **consulta** a seguir no banco de dados SSISDB no banco de dados SQL para verificar se o pacote foi executado. 

    ```sql
    select * from catalog.executions
    ```

    ![Verificar as execuções do pacote](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> Também é possível criar um gatilho agendado para o pipeline, de modo que o pipeline seja executado em um agendamento (por hora, diariamente etc.). Para um exemplo, consulte [Criar uma data factory - Interface do Usuário do Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nesta seção, você usa o Azure PowerShell para criar um pipeline do Data Factory com uma atividade de procedimento armazenado que invoca um pacote do SSIS. 

Instale os módulos mais recentes do Azure PowerShell seguindo as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps). 

### <a name="create-a-data-factory"></a>Criar um data factory
Você pode usar a mesma fábrica de dados que contém o IR do Azure-SSIS ou criar uma fábrica de dados separada. O procedimento a seguir fornece as etapas para criar uma fábrica de dados. Você cria um pipeline com uma atividade de procedimento armazenado nesta data factory. A atividade de procedimento armazenado executa um procedimento armazenado no banco de dados SSISDB para executar o seu pacote do SSIS. 

1. Defina uma variável para o nome do grupo de recursos que você usa nos comandos do PowerShell posteriormente. Copie o seguinte texto de comando para o PowerShell, especifique um nome para o [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) entre aspas duplas e, em seguida, execute o comando. Por exemplo: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Se o grupo de recursos já existir, não convém substituí-lo. Atribua um valor diferente para a variável `$ResourceGroupName` e execute o comando novamente
2. Para criar o grupo de recursos do Azure, execute o seguinte comando: 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Se o grupo de recursos já existir, não convém substituí-lo. Atribua um valor diferente para a variável `$ResourceGroupName` e execute o comando novamente. 
3. Defina uma variável para o nome do data factory. 

    > [!IMPORTANT]
    >  Atualize o Nome do data factory para ser globalmente exclusivo. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Para criar o data factory, execute o cmdlet **Set-AzDataFactoryV2** a seguir usando a propriedade Location e ResourceGroupName da variável $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Observe os seguintes pontos:

* O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro a seguir, altere o nome e tente novamente.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Para criar instâncias de Data Factory, a conta de usuário usada para fazer logon no Azure deve ser um membro das funções **colaborador** ou **proprietário**, ou um **administrador** da assinatura do Azure.
* Para obter uma lista de regiões do Azure no qual o Data Factory está disponível no momento, selecione as regiões que relevantes para você na página a seguir e, em seguida, expanda **Análise** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.

### <a name="create-an-azure-sql-database-linked-service"></a>Criar um serviço vinculado do Banco de Dados SQL do Azure
Crie um serviço vinculado para vincular seu banco de dados que hospeda o catálogo do SSIS ao seu data factory. O Data Factory usa informações nesse serviço vinculado para se conectar ao banco de dados SSISDB, e executa um procedimento armazenado para executar um pacote do SSIS. 

1. Crie um arquivo JSON denominado **AzureSqlDatabaseLinkedService.json** na pasta **C:\ADF\RunSSISPackage** com o seguinte conteúdo: 

    > [!IMPORTANT]
    > Substitua o &lt;servername&gt;, o &lt;username&gt;, e a &lt;senha&gt; pelos valores do seu banco de dados SQL do Azure antes de salvar o arquivo.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. No **Azure PowerShell**, mude para a pasta **C:\ADF\RunSSISPackage**.

3. Execute o cmdlet **Set-AzDataFactoryV2LinkedService** para criar o serviço vinculado: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Criar um pipeline com atividade de procedimento armazenado 
Nesta etapa, você cria um pipeline com uma atividade de procedimento armazenado. A atividade chama o procedimento armazenado sp_executesql para executar o seu pacote do SSIS. 

1. Crie um arquivo JSON denominado **RunSSISPackagePipeline.json** na pasta **C:\ADF\RunSSISPackage** com o seguinte conteúdo:

    > [!IMPORTANT]
    > Substitua o &lt;NOME DA PASTA&gt;, o &lt;NOME DO PROJETO&gt;, e o &lt;NOME DO PACOTE&gt; com os nomes de pasta, projeto e pacote no catálogo do SSIS antes de salvar o arquivo. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. Para criar o pipeline: **RunSSISPackagePipeline**, execute o cmdlet **set-AzDataFactoryV2Pipeline** .

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Veja o exemplo de saída:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline
Use o cmdlet **Invoke-AzDataFactoryV2Pipeline** para executar o pipeline. O cmdlet retorna a ID da execução de pipeline para monitoramento futuro.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline

Execute o script do PowerShell a seguir para verificar continuamente o status da execução de pipeline até que ela termine de copiar os dados. Copie/cole o script a seguir na janela do PowerShell e pressione ENTER. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

### <a name="create-a-trigger"></a>Escolha um gatilho
Na etapa anterior, você chamou a pipeline sob demanda. Você também pode criar um gatilho de agendamento para a agendar a execução do pipeline (por hora, diariamente, etc.).

1. Crie um arquivo JSON denominado **MyTrigger.json** na pasta **C:\ADF\RunSSISPackage** com o seguinte conteúdo: 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. No **Azure PowerShell**, mude para a pasta **C:\ADF\RunSSISPackage**.
3. Execute o cmdlet **set-AzDataFactoryV2Trigger** , que cria o gatilho. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Por padrão, o gatilho está no estado interrompido. Inicie o gatilho executando o cmdlet **Start-AzDataFactoryV2Trigger** . 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Confirme se o gatilho é iniciado executando o cmdlet **Get-AzDataFactoryV2Trigger** . 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Execute o seguinte comando após a próxima hora. Por exemplo, se a hora atual for 15:25 UTC, execute o comando às 16:00 UTC. 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    Você pode executar a consulta a seguir no banco de dados SSISDB no banco de dados SQL para verificar se o pacote foi executado. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Próximas etapas
Você também pode monitorar o pipeline usando o Portal do Azure. Para obter instruções passo a passo, consulte [Monitorar o pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).