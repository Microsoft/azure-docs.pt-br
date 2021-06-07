---
title: Copiar uma tabela incrementalmente usando o PowerShell
description: Neste tutorial, você criará um pipeline do Azure Data Factory que copia dados de maneira incremental de um Banco de Dados SQL do Azure para um Armazenamento de Blobs do Azure.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 02/18/2021
ms.openlocfilehash: f4cb4807e6f2620bb76649fc7c7dcce7363cf4a4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740980"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-powershell"></a>Carregar incrementalmente os dados do Banco de Dados SQL do Azure para o Armazenamento de Blobs do Azure usando o PowerShell

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Neste tutorial, você usará o Azure Data Factory para criar um pipeline que carrega dados delta de uma tabela no Banco de Dados SQL do Azure para um Armazenamento de Blobs do Azure.

Neste tutorial, você realizará os seguintes procedimentos:

> [!div class="checklist"]
> * Prepare o armazenamento de dados para armazenar o valor de marca-d'água.
> * Criar um data factory.
> * Criar serviços vinculados.
> * Criar os conjuntos de dados de origem, de coletor e de marca-d'água.
> * Crie um pipeline.
> * Execute o pipeline.
> * Monitorar a execução de pipeline.

## <a name="overview"></a>Visão geral
A seguir está diagrama da solução de alto nível:

![Carregar dados incrementalmente](media/tutorial-Incrementally-copy-powershell/incrementally-load.png)

Aqui estão as etapas importantes ao criar essa solução:

1. **Selecione a coluna de marca-d'água**.
    Selecione uma coluna no armazenamento de dados de origem, que pode ser usada para dividir os registros novos ou atualizados para cada execução. Normalmente, os dados nessa coluna selecionada (por exemplo, ID ou last_modify_time) seguem crescendo quando linhas são criadas ou atualizadas. O valor máximo dessa coluna é usado como uma marca-d'água.

2. **Prepare um armazenamento de dados para armazenar o valor de marca-d'água**.   
    Neste tutorial, você deve armazenar o valor de marca-d'água em um banco de dados SQL.

3. **Criar um pipeline com o seguinte fluxo de trabalho**:

    O pipeline nesta solução tem as seguintes atividades:

    * Crie duas atividades de Pesquisa. Use a primeira atividade de Pesquisa para recuperar o último valor de marca-d'água. Use a segunda atividade de Pesquisa para recuperar o novo valor de marca-d'água. Esses valores de marca-d'água são passados para a atividade de Cópia.
    * Crie uma atividade de cópia que copie linhas do armazenamento de dados de origem com o valor da coluna de marca-d'água maior do que o antigo valor de marca-d'água e menor que o novo valor de marca-d'água. Em seguida, ela copia os dados delta do armazenamento de dados de origem para um Armazenamento de Blobs como um novo arquivo.
    * Crie uma atividade de StoredProcedure que atualize o valor de marca-d'água para o pipeline que for executado da próxima vez.


Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Banco de dados SQL do Azure**. Você usa o banco de dados como um armazenamento de dados de origem. Se você não tiver um banco de dados no Banco de Dados SQL do Azure, confira [Criar um banco de dados no Banco de Dados SQL do Azure](../azure-sql/database/single-database-create-quickstart.md) para ver as etapas de criação.
* **Armazenamento do Azure**. Você usa o Armazenamento de Blobs como um armazenamento de dados de coletor. Se você não tiver uma conta de armazenamento, consulte [Criar uma conta de armazenamento](../storage/common/storage-account-create.md) para saber as etapas para criar uma. Crie um contêiner denominado adftutorial. 
* **Azure PowerShell**. Siga as instruções em [Instalar e configurar o Azure PowerShell](/powershell/azure/install-Az-ps).

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
1. Defina uma variável para o nome do grupo de recursos que você usa nos comandos do PowerShell posteriormente. Copie o seguinte texto de comando para o PowerShell, especifique um nome para o [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) entre aspas duplas e, em seguida, execute o comando. Um exemplo é `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Se o grupo de recursos já existir, não convém substituí-lo. Atribua um valor diferente para a variável `$resourceGroupName` e execute o comando novamente.

2. Defina uma variável para o local do data factory.

    ```powershell
    $location = "East US"
    ```
3. Para criar o grupo de recursos do Azure, execute o seguinte comando:

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ```
    Se o grupo de recursos já existir, não convém substituí-lo. Atribua um valor diferente para a variável `$resourceGroupName` e execute o comando novamente.

4. Defina uma variável para o nome do data factory.

    > [!IMPORTANT]
    >  Atualize o Nome do data factory para que ele seja globalmente exclusivo. Por exemplo, ADFTutorialFactorySP1127.

    ```powershell
    $dataFactoryName = "ADFIncCopyTutorialFactory";
    ```
5. Para criar o data factory, execute o seguinte cmdlet **Set-AzDataFactoryV2**:

    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

Observe os seguintes pontos:

* O nome do data factory deve ser globalmente exclusivo. Se você receber o erro a seguir, altere o nome e tente novamente:

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Para criar instâncias de Data Factory, a conta de usuário usada para entrar no Azure deve ser um membro das funções colaborador ou proprietário, ou um administrador da assinatura do Azure.
* Para obter uma lista de regiões do Azure no qual o Data Factory está disponível no momento, selecione as regiões que relevantes para você na página a seguir e, em seguida, expanda **Análise** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os armazenamentos de dados (Armazenamento, Banco de Dados SQL, Instância Gerenciada de SQL do Azure etc.) e serviços de computação (Azure HDInsight etc.) usados pelo data factory podem estar em outras regiões.


## <a name="create-linked-services"></a>Criar serviços vinculados
Os serviços vinculados são criados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Nesta seção, você cria serviços vinculados para sua conta de armazenamento e para o Banco de Dados SQL.

### <a name="create-a-storage-linked-service"></a>Criar um serviço vinculado do Armazenamento
1. Crie um arquivo JSON denominado AzureStorageLinkedService.json na pasta C:\ADF, com o conteúdo a seguir. (Crie a pasta ADF se ela não existir). Substitua `<accountName>` e `<accountKey>` pelo nome e a chave da sua conta de armazenamento antes de salvar o arquivo.

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
2. No PowerShell, mude para a pasta ADF.

3. Execute o cmdlet **Set-AzDataFactoryV2LinkedService** para criar o serviço vinculado AzureStorageLinkedService. No exemplo a seguir, você passa valores para os parâmetros *ResourceGroupName* e *DataFactoryName*:

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

### <a name="create-a-sql-database-linked-service"></a>Criar um serviço vinculado para o banco de dados SQL
1. Crie um arquivo JSON denominado AzureSQLDatabaseLinkedService.json na pasta C:\ADF, com o conteúdo a seguir. (Crie a pasta ADF se ela não existir). Substitua &lt;server&gt;, &lt;database&gt;, &lt;user id&gt; e &lt;password&gt; pelo nome do seu servidor, banco de dados, ID do usuário e senha antes de salvar o arquivo.

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database>; Persist Security Info=False; User ID=<user> ; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;"
            }
        }
    }
    ```
2. No PowerShell, mude para a pasta ADF.

3. Execute o cmdlet **Set-AzDataFactoryV2LinkedService** para criar o serviço vinculado AzureSQLDatabaseLinkedService.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Veja o exemplo de saída:

    ```console
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ProvisioningState :
    ```

## <a name="create-datasets"></a>Criar conjuntos de dados
Nesta etapa, você cria conjuntos de dados para representar dados de origem e de coletor.

### <a name="create-a-source-dataset"></a>Criar um conjunto de dados de origem

1. Crie um arquivo JSON denominado SourceDataset.json na mesma pasta, com o seguinte conteúdo:

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }

    ```
    Neste tutorial, use o nome da tabela data_source_table. Substitua-o se você estiver usando uma tabela com um nome diferente.

2. Execute o cmdlet **Set-AzDataFactoryV2Dataset** para criar o conjunto de dados SourceDataset.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Aqui está a amostra de saída do cmdlet:

    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Criar um conjunto de dados de coletor

1. Crie um arquivo JSON denominado SinkDataset.json na mesma pasta, com o seguinte conteúdo:

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incrementalcopy",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }   
    ```

    > [!IMPORTANT]
    > Esse snippet pressupõe que você tenha um contêiner de blob chamado `adftutorial` no Armazenamento de Blobs. Crie o contêiner caso ele não exista ou defina-o com o nome de um contêiner existente. A pasta de saída `incrementalcopy` será criada automaticamente se o contêiner não existir. Neste tutorial, o nome do arquivo é gerado dinamicamente pelo uso da expressão `@CONCAT('Incremental-', pipeline().RunId, '.txt')`.

2. Execute o cmdlet **Set-AzDataFactoryV2Dataset** para criar o conjunto de dados SinkDataset.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Aqui está a amostra de saída do cmdlet:

    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset    
    ```

## <a name="create-a-dataset-for-a-watermark"></a>Criar um conjunto de dados para uma marca-d'água
Nesta etapa, você deve criar um conjunto de dados para armazenar um valor de marca d'água alta.

1. Crie um arquivo JSON denominado WatermarkDataset.json na mesma pasta, com o seguinte conteúdo:

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2.  Execute o cmdlet **Set-AzDataFactoryV2Dataset** para criar o conjunto de dados WatermarkDataset.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Aqui está a amostra de saída do cmdlet:

    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Criar um pipeline
Neste tutorial, você cria um pipeline com duas atividades de Pesquisa, uma atividade de Cópia e uma atividade de Procedimento armazenado encadeadas em um pipeline.


1. Crie um arquivo JSON denominado IncrementalCopyPipeline.json na mesma pasta, com o conteúdo a seguir:

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "LookupOldWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from watermarktable"
                        },

                        "dataset": {
                        "referenceName": "WatermarkDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "LookupNewWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select MAX(LastModifytime) as NewWatermarkvalue from data_source_table"
                        },

                        "dataset": {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference"
                        }
                    }
                },

                {
                    "name": "IncrementalCopyActivity",
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupNewWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        },
                        {
                            "activity": "LookupOldWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ],

                    "inputs": [
                        {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "SinkDataset",
                            "type": "DatasetReference"
                        }
                    ]
                },

                {
                    "name": "StoredProceduretoWriteWatermarkActivity",
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {

                        "storedProcedureName": "usp_write_watermark",
                        "storedProcedureParameters": {
                            "LastModifiedtime": {"value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}", "type": "datetime" },
                            "TableName":  { "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}", "type":"String"}
                        }
                    },

                    "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },

                    "dependsOn": [
                        {
                            "activity": "IncrementalCopyActivity",
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


2. Execute o **Set-AzDataFactoryV2Pipeline** para criar o pipeline IncrementalCopyPipeline.

   ```powershell
   Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ```

   Veja o exemplo de saída:

   ```console
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Activities        : {LookupOldWaterMarkActivity, LookupNewWaterMarkActivity, IncrementalCopyActivity, StoredProceduretoWriteWatermarkActivity}
    Parameters        :
   ```

## <a name="run-the-pipeline"></a>Executar o pipeline

1. Execute o pipeline IncrementalCopyPipeline usando o cmdlet **Invoke-AzDataFactoryV2Pipeline**. Substitua os espaços reservados com seus próprios nomes de grupo de recursos e de data factory.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ```
2. Verifique o status do pipeline executando o **Get-AzDataFactoryV2ActivityRun** até ver todas as atividades executando com êxito. Substitua os espaços reservados com sua própria hora apropriada para os parâmetros *RunStartedAfter* e *RunStartedBefore*. Neste tutorial, você usa *-RunStartedAfter "2017/09/14"* e *-RunStartedBefore "2017/09/15"* .

    ```powershell
    Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Veja o exemplo de saída:

    ```console
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:42:50 AM
    DurationInMs      : 7777
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:43:07 AM
    DurationInMs      : 25437
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:10 AM
    ActivityRunEnd    : 9/14/2017 7:43:29 AM
    DurationInMs      : 19769
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:32 AM
    ActivityRunEnd    : 9/14/2017 7:43:47 AM
    DurationInMs      : 14467
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```

## <a name="review-the-results"></a>Revise os resultados

1. No Armazenamento de Blobs do (repositório de coletor), você verá que os dados foram copiados para o arquivo definido no SinkDataset. No tutorial atual, o nome do arquivo é `Incremental- d4bf3ce2-5d60-43f3-9318-923155f61037.txt`. Abra o arquivo, você poderá ver os registros no arquivo que serão o mesmos que os dados no banco de dados SQL.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
2. Verifique o valor mais recente de `watermarktable`. Você verá que o valor da marca d'água foi atualizado.

    ```sql
    Select * from watermarktable
    ```

    Veja o exemplo de saída:

    TableName | WatermarkValue
    --------- | --------------
    data_source_table | 05-09-2017 8:06:00.000

### <a name="insert-data-into-the-data-source-store-to-verify-delta-data-loading"></a>Insira dados no repositório de fonte de dados para verificar o carregamento de dados delta

1. Insira novos dados no banco de dados SQL (repositório de fonte de dados).

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
2. Execute o pipeline IncrementalCopyPipeline novamente usando o cmdlet **Invoke-AzDataFactoryV2Pipeline**. Substitua os espaços reservados com seus próprios nomes de grupo de recursos e de data factory.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ```
3. Verifique o status do pipeline executando o **Get-AzDataFactoryV2ActivityRun** até ver todas as atividades executando com êxito. Substitua os espaços reservados com sua própria hora apropriada para os parâmetros *RunStartedAfter* e *RunStartedBefore*. Neste tutorial, você usa *-RunStartedAfter "2017/09/14"* e *-RunStartedBefore "2017/09/15"* .

    ```powershell
    Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Veja o exemplo de saída:

    ```console
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:58 AM
    DurationInMs      : 31758
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:52 AM
    DurationInMs      : 25497
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:00 AM
    ActivityRunEnd    : 9/14/2017 8:53:20 AM
    DurationInMs      : 20194
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:23 AM
    ActivityRunEnd    : 9/14/2017 8:53:41 AM
    DurationInMs      : 18502
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```
4. No Armazenamento de Blobs você verá que o outro arquivo foi criado. Neste tutorial, o nome do novo arquivo é `Incremental-2fc90ab8-d42c-4583-aa64-755dba9925d7.txt`. Abra esse arquivo, você verá registros de duas linhas nele.

5. Verifique o valor mais recente de `watermarktable`. Você verá que o valor da marca d'água foi atualizado novamente.

    ```sql
    Select * from watermarktable
    ```
    exemplo de saída:

    TableName | WatermarkValue
    --------- | ---------------
    data_source_table | 2017-09-07 09:01:00.000


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

Neste tutorial, o pipeline copiou dados de uma tabela no Banco de Dados SQL do Azure para um Armazenamento de Blobs. Avance para o tutorial a seguir para saber mais sobre como copiar dados de várias tabelas em um banco de dados do SQL Server para um Banco de Dados SQL.

> [!div class="nextstepaction"]
>[Carregar incrementalmente os dados de várias tabelas no SQL Server para o Banco de Dados SQL do Azure](tutorial-incremental-copy-multiple-tables-powershell.md)
