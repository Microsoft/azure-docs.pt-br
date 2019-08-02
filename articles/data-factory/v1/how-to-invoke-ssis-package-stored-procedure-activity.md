---
title: Chamar pacote do SSIS usando o Azure Data Factory - Atividade de Procedimento Armazenado | Microsoft Docs
description: Este artigo descreve como chamar um pacote do SQL Server Integration Services (SSIS) de um pipeline do Azure Data Factory usando a Atividade de Procedimento Armazenado.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: 030617d3afd73c68793ca0a1d6185264c92b791f
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839892"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Chamar um pacote do SSIS usando o Azure Data Factory - Atividade de Procedimento Armazenado | Microsoft Docs
Este artigo descreve como chamar um pacote do SSIS a partir de um pipeline do Azure Data Factory usando uma atividade de procedimento armazenado. 

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Invocar pacotes SSIS usando a atividade de procedimento armazenado no ](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure 
Este artigo passo a passo usa um banco de dados SQL do Azure que hospeda o catálogo do SSIS. Você também pode usar uma Instância Gerenciada do Banco de Dados SQL do Azure.

### <a name="create-an-azure-ssis-integration-runtime"></a>Criar um Integration Runtime do Azure-SSIS
Crie um Azure-SSIS Integration Runtime, caso não tenha um, seguindo as instruções passo a passo do [Tutorial: Implantar pacotes do SSIS](../tutorial-create-azure-ssis-runtime-portal.md). Você não pode usar uma versão 1 do Data Factory para criar um Integration Runtime do Azure-SSIS. 

## <a name="azure-powershell"></a>Azure PowerShell
Nesta seção, você usa a interface do usuário do Azure PowerShell para criar um pipeline do Data Factory com uma atividade de procedimento armazenado que invoca um pacote SSIS.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Instale os módulos mais recentes do Azure PowerShell seguindo as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory"></a>Criar uma data factory
O procedimento a seguir fornece as etapas para criar uma fábrica de dados. Você cria um pipeline com uma atividade de procedimento armazenado nesta data factory. A atividade de procedimento armazenado executa um procedimento armazenado no banco de dados SSISDB para executar o seu pacote do SSIS.

1. Defina uma variável para o nome do grupo de recursos que você usa nos comandos do PowerShell posteriormente. Copie o seguinte texto de comando para o PowerShell, especifique um nome para o [grupo de recursos do Azure](../../azure-resource-manager/resource-group-overview.md) entre aspas duplas e, em seguida, execute o comando. Por exemplo: `"adfrg"`. 
   
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

5. Para criar o data factory, execute o seguinte **New-AzDataFactory** cmdlet, usando a propriedade Location e ResourceGroupName da variável $ResGrp: 
    
    ```powershell       
    $df = New-AzDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Observe os seguintes pontos:

* O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro a seguir, altere o nome e tente novamente.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Para criar instâncias de Data Factory, a conta de usuário usada para fazer logon no Azure deve ser um membro das funções **colaborador** ou **proprietário**, ou um **administrador** da assinatura do Azure.

### <a name="create-an-azure-sql-database-linked-service"></a>Criar um serviço vinculado do Banco de Dados SQL do Azure
Crie um serviço vinculado para vincular o seu banco de dados SQL do Azure que hospeda o catálogo do SSIS para sua fábrica de dados. O Data Factory usa informações nesse serviço vinculado para se conectar ao banco de dados SSISDB, e executa um procedimento armazenado para executar um pacote do SSIS. 

1. Crie um arquivo JSON denominado **AzureSqlDatabaseLinkedService.json** na pasta **C:\ADF\RunSSISPackage** com o seguinte conteúdo: 

    > [!IMPORTANT]
    > Substitua &lt;servername&gt;, &lt;username&gt;@&lt;servername&gt; e &lt;senha&gt; pelos valores do seu banco de dados SQL do Azure Database antes de salvar o arquivo.

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
3. Execute o cmdlet **New-AzDataFactoryLinkedService** para criar o serviço vinculado: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
Este conjunto de dados de saída é um conjunto de dados fictício que orienta a agenda do pipeline. Observe que a frequência está definida como Hora e o intervalo está definido como 1. Portanto, o pipeline é executado a cada hora no período de tempo entre o início e o término do pipeline. 

1. Crie um arquivo OuputDataset.json com o seguinte conteúdo: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Execute o **New-AzDataFactoryDataset** para criar um conjunto de dados. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Criar um pipeline com atividade de procedimento armazenado 
Nesta etapa, você cria um pipeline com uma atividade de procedimento armazenado. A atividade chama o procedimento armazenado sp_executesql para executar o seu pacote do SSIS. 

1. Crie um arquivo JSON denominado **MyPipeline.json** na pasta **C:\ADF\RunSSISPackage** com o seguinte conteúdo:

    > [!IMPORTANT]
    > Substitua o &lt;nome da pasta&gt;, o &lt;nome do projeto&gt;, e o &lt;nome do pacote&gt; com os nomes de pasta, projeto e pacote no catálogo do SSIS antes de salvar o arquivo.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. Para criar o pipeline: **RunSSISPackagePipeline**, execute o **AzDataFactoryPipeline New** cmdlet.

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline

1. Execute **Get-AzDataFactorySlice** para obter detalhes sobre todas as fatias da saída conjunto de dados * *, que é a tabela de saída do pipeline.

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Observe que o valor de StartDateTime especificado aqui é a mesma hora de início especificada no pipeline de JSON. 
1. Execute **Get-AzDataFactoryRun** para obter os detalhes de execuções de atividade para uma fatia específica.

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Pode continuar executando este cmdlet até ver a fatia no estado **Pronto** ou **Falha**. 

    Você pode executar a consulta a seguir no banco de dados SSISDB no seu servidor do SQL Azure para verificar se o pacote foi executado. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Próximas etapas
Para obter detalhes sobre a atividade de procedimento armazenado, consulte o artigo [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md).

