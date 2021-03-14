---
title: Copiar dados em massa com o PowerShell
description: Use o Azure Data Factory com a atividade de cópia para copiar dados em massa de um armazenamento de dados de origem para um armazenamento de dados de destino.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: fc539ababf4cb240fbe78de0d87b1f127807f604
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740419"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory-using-powershell"></a>Copiar várias tabelas em massa usando o Azure Data Factory usando o PowerShell

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este tutorial demonstra como **copiar uma série de tabelas do Banco de Dados SQL do Azure para o Azure Synapse Analytics**. Você também pode aplicar o mesmo padrão em outros cenários de cópia. Por exemplo, copiando tabelas do SQL Server/Oracle para o Banco de Dados SQL do Azure/Data Warehouse/Blob do Azure, copiando diferentes caminhos do Blob para tabelas do Banco de Dados SQL do Azure.

De forma mais abrangente, este tutorial envolve as seguintes etapas:

> [!div class="checklist"]
> * Criar um data factory.
> * Criar o Banco de Dados SQL do Azure, o Azure Synapse Analytics e os serviços vinculados do Armazenamento do Azure.
> * Criar conjuntos de dados do Banco de Dados SQL do Azure e do Azure Synapse Analytics.
> * Crie um pipeline para consultar as tabelas a serem copiadas e outro pipeline para executar a operação de cópia propriamente dita. 
> * Iniciar uma execução de pipeline.
> * Monitore as execuções de pipeline e de atividade.

Este tutorial usa o Azure PowerShell. Para obter informações sobre como usar outras ferramentas/SDKs para criar um data factory, consulte [Guias de início rápido](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Fluxos de trabalho completos
Nesse cenário, temos uma série de tabelas do Banco de Dados SQL do Azure que desejamos para copiar para o Azure Synapse Analytics. Aqui está a sequência lógica de etapas no fluxo de trabalho que ocorre em pipelines:

![Fluxo de trabalho](media/tutorial-bulk-copy/tutorial-copy-multiple-tables.png)

* O primeiro pipeline verifica a lista de tabelas que precisam ser copiadas nos armazenamentos de dados do coletor.  Alternativamente, você pode manter uma tabela de metadados que lista todas as tabelas a serem copiadas para o armazenamento de dados de coletor. Em seguida, o pipeline dispara outro pipeline, que faz iteração por cada tabela no banco de dados e executa a operação de cópia de dados.
* O segundo pipeline realiza a cópia propriamente dita. Ele usa a lista de tabelas como um parâmetro. Para cada tabela da lista, copie a tabela específica no Banco de Dados SQL do Azure para a tabela correspondente no Azure Synapse Analytics usando a [cópia preparada por meio do Armazenamento de Blobs e do PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics) para obter o melhor desempenho. Neste exemplo, o primeiro pipeline envia a lista de tabelas como um valor para o parâmetro. 

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure PowerShell**. Siga as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-Az-ps).
* **Conta de Armazenamento do Azure**. A conta de Armazenamento do Azure é usada como Armazenamento de Blobs de preparo na operação de cópia em massa. 
* **Banco de dados SQL do Azure**. Este banco de dados contém os dados de origem. 
* **Azure Synapse Analytics**. Esse data warehouse contém os dados copiados do Banco de Dados SQL. 

### <a name="prepare-sql-database-and-azure-synapse-analytics"></a>Preparar o Banco de Dados SQL e o Azure Synapse Analytics

**Prepare o Banco de Dados SQL do Azure de origem**:

Crie um banco de dados contendo dados de exemplo do Adventure Works LT no Banco de Dados SQL, seguindo o artigo [Criar um banco de dados no Banco de Dados SQL do Azure](../azure-sql/database/single-database-create-quickstart.md) a seguir. Este tutorial copia todas as tabelas desse exemplo de banco de dados para o Azure Synapse Analytics.

**Preparar o Azure Synapse Analytics coletor**:

1. Se você não tiver um workspace do Azure Synapse Analytics, confira o artigo [Introdução ao Azure Synapse Analytics](..\synapse-analytics\get-started.md) para obter as etapas necessárias para criar um.

2. Crie esquemas de tabela correspondentes no Azure Synapse Analytics. Você usa o Azure Data Factory para migrar/copiar dados em uma etapa posterior.

## <a name="azure-services-to-access-sql-server"></a>Permitir que os serviços do Azure acessem o SQL Server

Para o Banco de Dados SQL e o Azure Synapse Analytics, permita que os serviços do Azure acessem o SQL Server. Verifique se a configuração **Permitir acesso aos serviços do Azure** está **ATIVADA** para o servidor. Essa configuração permite que o serviço Data Factory leia dados do Banco de Dados SQL do Azure e grave-os no Azure Synapse Analytics. Para verificar e ativar essa configuração, faça as seguintes etapas:

1. Clique em **Todos os serviços** à esquerda e selecione **Servidores SQL**.
2. Selecione seu servidor e clique em **Firewall** em **CONFIGURAÇÕES**.
3. Na página **Configurações de Firewall**, clique em **ATIVADO** para **Permitir acesso aos serviços do Azure**.

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Inicie o **PowerShell**. Mantenha o Azure PowerShell aberto até o fim deste tutorial. Se você fechar e reabrir, precisará executar os comandos novamente.

    Execute o comando a seguir e insira o nome de usuário e senha usados para entrar no portal do Azure:
        
    ```powershell
    Connect-AzAccount
    ```
    Execute o comando abaixo para exibir todas as assinaturas dessa conta:

    ```powershell
    Get-AzSubscription
    ```
    Execute o comando a seguir para selecionar a assinatura com a qual deseja trabalhar. Substitua **SubscriptionId** pela ID da assinatura do Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. Execute o cmdlet **Set-AzDataFactoryV2** para criar um data factory. Substitua os espaços reservados por seus próprios valores antes de executar o comando. 

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Observe os seguintes pontos:

    * O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro a seguir, altere o nome e tente novamente.

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * Para criar instâncias do Data Factory, você precisa ser um colaborador ou administrador da assinatura do Azure.
    * Para obter uma lista de regiões do Azure no qual o Data Factory está disponível no momento, selecione as regiões que relevantes para você na página a seguir e, em seguida, expanda **Análise** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.

## <a name="create-linked-services"></a>Criar serviços vinculados

Neste tutorial, você cria três serviços vinculados respectivamente para origem, coletor e blob de preparo, que inclui conexões os armazenamento de dados:

### <a name="create-the-source-azure-sql-database-linked-service"></a>Criar o serviço vinculado do Banco de Dados SQL do Azure de origem

1. Crie um arquivo JSON chamado **AzureSqlDatabaseLinkedService.json** na pasta **C:\ADFv2TutorialBulkCopy** com o seguinte conteúdo: (Crie a pasta ADFv2TutorialBulkCopy caso ela ainda não exista.)

    > [!IMPORTANT]
    > Substitua &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; e &lt;password&gt; pelos valores de seu Banco de Dados SQL do Azure antes de salvar o arquivo.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. No **Azure PowerShell**, mude para a pasta **ADFv2TutorialBulkCopy**.

3. Execute o cmdlet **Set-AzDataFactoryV2LinkedService** para criar o serviço vinculado: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

    Veja o exemplo de saída:

    ```console
    LinkedServiceName : AzureSqlDatabaseLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

### <a name="create-the-sink-azure-synapse-analytics-linked-service"></a>Criar o serviço vinculado do Azure Synapse Analytics coletor

1. Crie um arquivo JSON denominado **AzureSqlDWLinkedService.json** na pasta **C:\ADFv2TutorialBulkCopy**, com o conteúdo a seguir:

    > [!IMPORTANT]
    > Substitua &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; e &lt;password&gt; pelos valores de seu Banco de Dados SQL do Azure antes de salvar o arquivo.

    ```json
    {
        "name": "AzureSqlDWLinkedService",
        "properties": {
            "type": "AzureSqlDW",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. Para criar o serviço vinculado: **AzureSqlDWLinkedService**, execute o cmdlet **Set-AzDataFactoryV2LinkedService**.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWLinkedService" -File ".\AzureSqlDWLinkedService.json"
    ```

    Veja o exemplo de saída:

    ```console
    LinkedServiceName : AzureSqlDWLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDWLinkedService
    ```

### <a name="create-the-staging-azure-storage-linked-service"></a>Criar o serviço vinculado do Armazenamento do Azure de preparo

Neste tutorial, você usa o Armazenamento de Blobs do Azure como uma área de preparação intermediária para habilitar o PolyBase para um melhor desempenho de cópia.

1. Crie um arquivo JSON denominado **AzureStorageLinkedService.json** na pasta **C:\ADFv2TutorialBulkCopy**, com o conteúdo a seguir:

    > [!IMPORTANT]
    > Antes de salvar o arquivo, substitua &lt;accountName&gt; e &lt;accountKey&gt; pelo nome e pela chave da sua conta de armazenamento do Azure, respectivamente.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
            }
        }
    }
    ```

2. Para criar o serviço vinculado: **AzureStorageLinkedService**, execute o cmdlet **Set-AzDataFactoryV2LinkedService**.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Veja o exemplo de saída:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-datasets"></a>Criar conjuntos de dados

Neste tutorial você cria os conjuntos de dados de origem e de coletor, que especificam o local em que os dados são armazenados:

### <a name="create-a-dataset-for-source-sql-database"></a>Criar um conjunto de dados para o Banco de Dados SQL de origem

1. Crie um arquivo JSON denominado **AzureSqlDatabaseDataset.json** na pasta **C:\ADFv2TutorialBulkCopy**, com o conteúdo a seguir. O "tableName" é um arquivo fictício, já que posteriormente você usa a consulta SQL na atividade de cópia para recuperar dados.

    ```json
    {
        "name": "AzureSqlDatabaseDataset",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": "dummy"
            }
        }
    }
    ```

2. Para criar o conjunto de dados: **AzureSqlDatabaseDataset**, execute o cmdlet **Set-AzDataFactoryV2Dataset**.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseDataset" -File ".\AzureSqlDatabaseDataset.json"
    ```

    Veja o exemplo de saída:

    ```console
    DatasetName       : AzureSqlDatabaseDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-synapse-analytics"></a>Crie um conjunto de dados para o Azure Synapse Analytics coletor

1. Crie um arquivo JSON chamado **AzureSqlDWDataset.json** na pasta **C:\ADFv2TutorialBulkCopy** com o seguinte conteúdo: O "tableName" é definido como um parâmetro; posteriormente, a atividade de cópia que referencia esse conjunto de dados passará o valor real para o conjunto de dados.

    ```json
    {
        "name": "AzureSqlDWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDWLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().DWTableName}",
                    "type": "Expression"
                }
            },
            "parameters":{
                "DWTableName":{
                    "type":"String"
                }
            }
        }
    }
    ```

2. Para criar o conjunto de dados: **AzureSqlDWDataset**, execute o cmdlet **Set-AzDataFactoryV2Dataset**.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWDataset" -File ".\AzureSqlDWDataset.json"
    ```

    Veja o exemplo de saída:

    ```console
    DatasetName       : AzureSqlDWDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDwTableDataset
    ```

## <a name="create-pipelines"></a>Criar pipelines

Neste tutorial, você cria dois pipelines:

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Criar o pipeline "IterateAndCopySQLTables"

Esse pipeline usa uma lista de tabelas como um parâmetro. Para cada tabela da lista, ele copia dados da tabela no Banco de Dados SQL do Azure para o Azure Synapse Analytics usando a cópia preparada e o PolyBase.

1. Crie um arquivo JSON denominado **IterateAndCopySQLTables.json** na pasta **C:\ADFv2TutorialBulkCopy**, com o conteúdo a seguir:

    ```json
    {
        "name": "IterateAndCopySQLTables",
        "properties": {
            "activities": [
                {
                    "name": "IterateSQLTables",
                    "type": "ForEach",
                    "typeProperties": {
                        "isSequential": "false",
                        "items": {
                            "value": "@pipeline().parameters.tableList",
                            "type": "Expression"
                        },
                        "activities": [
                            {
                                "name": "CopyData",
                                "description": "Copy data from Azure SQL Database to Azure Synapse Analytics",
                                "type": "Copy",
                                "inputs": [
                                    {
                                        "referenceName": "AzureSqlDatabaseDataset",
                                        "type": "DatasetReference"
                                    }
                                ],
                                "outputs": [
                                    {
                                        "referenceName": "AzureSqlDWDataset",
                                        "type": "DatasetReference",
                                        "parameters": {
                                            "DWTableName": "[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                        }
                                    }
                                ],
                                "typeProperties": {
                                    "source": {
                                        "type": "SqlSource",
                                        "sqlReaderQuery": "SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                    },
                                    "sink": {
                                        "type": "SqlDWSink",
                                        "preCopyScript": "TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]",
                                        "allowPolyBase": true
                                    },
                                    "enableStaging": true,
                                    "stagingSettings": {
                                        "linkedServiceName": {
                                            "referenceName": "AzureStorageLinkedService",
                                            "type": "LinkedServiceReference"
                                        }
                                    }
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```

2. Para criar o pipeline: **IterateAndCopySQLTables**, execute o cmdlet **Set-AzDataFactoryV2Pipeline**.

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IterateAndCopySQLTables" -File ".\IterateAndCopySQLTables.json"
    ```

    Veja o exemplo de saída:

    ```console
    PipelineName      : IterateAndCopySQLTables
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Criar o pipeline "GetTableListAndTriggerCopyData"

Esse pipeline realiza duas etapas:

* Pesquisa a tabela do sistema do Banco de Dados SQL do Azure para obter a lista de tabelas a serem copiadas.
* Dispara o pipeline "IterateAndCopySQLTables" para fazer a cópia de dados propriamente dita.

1. Crie um arquivo JSON denominado **GetTableListAndTriggerCopyData.json** na pasta **C:\ADFv2TutorialBulkCopy**, com o conteúdo a seguir:

    ```json
    {
        "name":"GetTableListAndTriggerCopyData",
        "properties":{
            "activities":[
                { 
                    "name": "LookupTableList",
                    "description": "Retrieve the table list from Azure SQL dataabse",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'"
                        },
                        "dataset": {
                            "referenceName": "AzureSqlDatabaseDataset",
                            "type": "DatasetReference"
                        },
                        "firstRowOnly": false
                    }
                },
                {
                    "name": "TriggerCopy",
                    "type": "ExecutePipeline",
                    "typeProperties": {
                        "parameters": {
                            "tableList": {
                                "value": "@activity('LookupTableList').output.value",
                                "type": "Expression"
                            }
                        },
                        "pipeline": {
                            "referenceName": "IterateAndCopySQLTables",
                            "type": "PipelineReference"
                        },
                        "waitOnCompletion": true
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupTableList",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Para criar o pipeline: **GetTableListAndTriggerCopyData**, execute o cmdlet **Set-AzDataFactoryV2Pipeline**.

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "GetTableListAndTriggerCopyData" -File ".\GetTableListAndTriggerCopyData.json"
    ```

    Veja o exemplo de saída:

    ```console
    PipelineName      : GetTableListAndTriggerCopyData
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {LookupTableList, TriggerCopy}
    Parameters        :
    ```

## <a name="start-and-monitor-pipeline-run"></a>Iniciar e monitorar uma execução de pipeline

1. Inicie uma execução de pipeline para o pipeline principal "GetTableListAndTriggerCopyData" e capture a ID da execução de pipeline para monitoramento futuro. Abaixo, ele dispara a execução do pipeline "IterateAndCopySQLTables", conforme especificado na atividade ExecutePipeline.

    ```powershell
    $runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'GetTableListAndTriggerCopyData'
    ```

2.  Execute o script a seguir para verificar continuamente o status de execução do pipeline **GetTableListAndTriggerCopyData** e imprima o resultado final da execução de atividade e da execução do pipeline.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                Write-Host "Pipeline run details:" -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 15
    }

    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result
    ```

    Aqui está a saída da execução de exemplo:

    ```console
    Pipeline run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    RunId             : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    LastUpdated       : 9/18/2017 4:08:15 PM
    Parameters        : {}
    RunStart          : 9/18/2017 4:06:44 PM
    RunEnd            : 9/18/2017 4:08:15 PM
    DurationInMs      : 90637
    Status            : Succeeded
    Message           : 

    Activity run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : LookupTableList
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {source, dataset, firstRowOnly}
    Output            : {count, value, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:06:46 PM
    ActivityRunEnd    : 9/18/2017 4:07:09 PM
    DurationInMs      : 22995
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : TriggerCopy
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {pipeline, parameters, waitOnCompletion}
    Output            : {pipelineRunId}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:07:11 PM
    ActivityRunEnd    : 9/18/2017 4:08:14 PM
    DurationInMs      : 62581
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Você pode obter a ID da execução de pipeline "**IterateAndCopySQLTables**" e verificar o resultado detalhado da execução da atividade conforme demonstrado a seguir.

    ```powershell
    Write-Host "Pipeline 'IterateAndCopySQLTables' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "TriggerCopy"}).Output.ToString()
    ```

    Aqui está a saída da execução de exemplo:

    ```json
    {
        "pipelineRunId": "7514d165-14bf-41fb-b5fb-789bea6c9e58"
    }
    ```

    ```powershell
    $result2 = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId <copy above run ID> -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result2
    ```

3. Conecte-se ao Azure Synapse Analytics coletor e confirme se os dados foram corretamente copiados do Banco de Dados SQL do Azure.

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
>[Copiar dados incrementalmente](tutorial-incremental-copy-powershell.md)
