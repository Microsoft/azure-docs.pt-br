---
title: Copiar dados locais para a nuvem usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados de um armazenamento de dados local para a nuvem do Azure usando o Integration Runtime auto-hospedado no Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/14/2017
ms.author: jingwang
ms.openlocfilehash: 74e2a57aa933c7025db952fa09de236f5dabb8c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-between-on-premises-and-cloud"></a>Mover dados entre o local e a nuvem
O Azure Data Factory é um serviço de integração de dados baseado em nuvem que permite que você crie fluxos de trabalho controlados por dados na nuvem para orquestrar e automatizar a movimentação e a transformação de dados. Usando o Azure Data Factory, você pode criar e agendar fluxos de trabalho orientados a dados (chamados de pipelines) que podem ingerir dados de repositórios de dados diferentes, processar/transformr os dados usando serviços de computação como o Hadoop do Azure HDInsight, Spark, Azure Data Lake Analytics e Azure Machine Learning e publicar os dados de saída em repositórios de dados como o SQL Data Warehouse do Azure para consumo pelos aplicativos de business intelligence (BI). 

Neste tutorial, você usa o Azure PowerShell para criar um pipeline do Data Factory que copia dados de um Banco de Dados do SQL Server local para um Armazenamento de Blobs do Azure. Você cria e usa um IR (Integration Runtime) auto-hospedado do Azure Data Factory, que permite a integração de armazenamentos de dados locais e armazenamentos de dados em nuvem.  Para obter informações sobre como usar outras ferramentas/SDKs para criar um data factory, consulte [Guias de início rápido](quickstart-create-data-factory-dot-net.md). 

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Crie um data factory.
> * Crie um Integration Runtime auto-hospedado.
> * Crie e criptografe o serviço vinculado do SQL Server local em um Integration Runtime auto-hospedado.
> * Crie um serviço vinculado do Armazenamento do Azure.
> * Crie conjuntos de dados do SQL Server e do Armazenamento do Azure.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Inicie uma execução de pipeline.
> * Monitore as execuções de pipeline e de atividade.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **SQL Server**. Neste tutorial, você utiliza um Banco de Dados do SQL Server local como um armazenamento de dados de **origem**. 
* **Conta de Armazenamento do Azure**. Você usa o Armazenamento de Blobs do Azure como um armazenamento de dados **destino/coletor** neste tutorial. Se você não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para conhecer as etapas para criar um.
* **PowerShell do Azure**. Siga as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Inicie o **PowerShell**. Mantenha o Azure PowerShell aberto até o fim deste tutorial. Se você fechar e reabrir, precisará executar os comandos novamente.

    Execute o comando a seguir e insira o nome de usuário e senha usados para entrar no portal do Azure:
    ```powershell
    Login-AzureRmAccount
    ```
    Execute o comando abaixo para exibir todas as assinaturas dessa conta:

    ```powershell
    Get-AzureRmSubscription
    ```
    Execute o comando a seguir para selecionar a assinatura com a qual deseja trabalhar. Substitua **SubscriptionId** pela ID da assinatura do Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. Execute o cmdlet **Set-AzureRmDataFactoryV2** para criar um data factory. Substitua os espaços reservados por seus próprios valores antes de executar o comando.

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $df = Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Observe os seguintes pontos:

    * O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro a seguir, altere o nome e tente novamente.

        ```
        Data factory name "<data factory name>" is not available.
        ```

    * Para criar instâncias do Data Factory, você precisa ser um colaborador ou administrador da assinatura do Azure.
    * Atualmente, o Data Factory permite que você crie o data factory somente na região Leste dos EUA 2. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.

## <a name="create-a-self-hosted-ir"></a>Criar um IR auto-hospedado

Nesta seção, você pode criar um Integration Runtime auto-hospedado e associá-lo a um nó local (computador).

1. Crie um Integration Runtime auto-hospedado. Use um nome exclusivo no caso exista outro Integration Runtime com o mesmo nome.

   ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Veja o exemplo de saída:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. Execute o comando a seguir para recuperar o status do Integration Runtime criado.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Veja o exemplo de saída:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption : 
   Version                   : 
   Capabilities              : {}
   ScheduledUpdateDate       : 
   UpdateDelayOffset         : 
   LocalTimeZoneOffset       : 
   AutoUpdate                : 
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. Execute o comando a seguir para recuperar as chaves de autenticação para registrar o Integration Runtime auto-hospedado com o serviço Data Factory na nuvem. Copie uma das chaves para registrar o Integration Runtime auto-hospedado.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Veja o exemplo de saída: 

   ```json
   {
       "AuthKey1":  "IR@8437c862-d6a9-4fb3-87dd-7d4865a9e845@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@8437c862-d6a9-4fb3-85dd-7d4865a9e845@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

4. [Baixe](https://www.microsoft.com/download/details.aspx?id=39717) o Integration Runtime auto-hospedado em um computador Windows local e use a chave de autenticação obtida na etapa anterior para registrar manualmente o Integration Runtime auto-hospedado. 

   ![Registrar Integration Runtime](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

   Você verá a seguinte mensagem quando o Integration Runtime auto-hospedado for registrado com êxito: 

   ![Registrado com êxito](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

   Você verá a página a seguir quando o nó estiver conectado ao serviço de nuvem: 
    
   ![O nó está conectado](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>Criar serviços vinculados 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Criar um serviço vinculado do Armazenamento do Azure (destino/coletor)

1. Crie um arquivo JSON denominado **AzureStorageLinkedService.json** na pasta **C:\ADFv2Tutorial**, com o conteúdo a seguir. Crie a pasta ADFv2Tutorial se ela ainda não existir.  Substitua &lt;accountname&gt; e &lt;accountkey&gt; pelo nome e pela chave da sua conta de Armazenamento do Azure, respectivamente.

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": { 
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

2. No **Azure PowerShell**, mude para a pasta **ADFv2Tutorial**.

   Execute o cmdlet **Set-AzureRmDataFactoryV2LinkedService** para criar o serviço vinculado **AzureStorageLinkedService**. Os cmdlets usados neste tutorial usam valores para os parâmetros **ResourceGroupName** e **DataFactoryName**. Como alternativa, você pode passar o objeto **DataFactory** retornado pelo cmdlet Set-AzureRmDataFactoryV2 sem precisar digitar ResourceGroupName e DataFactoryName sempre que executar um cmdlet.

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Aqui está uma amostra de saída:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Criar e criptografar um serviço vinculado do SQL Server (origem)

1. Criar um arquivo JSON chamado **SqlServerLinkedService.json** na pasta **C:\ADFv2Tutorial** com o seguinte conteúdo: substituir **&lt;servername>**, **&lt;databasename>**, **&lt;username>**, **&lt;servername>** e **&lt;password>** por valores do SQL Server antes de salvar o arquivo. Substitua **&lt;integration** **runtime** **name>** pelo nome de seu Integration Runtime. 

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```
2. Para criptografar os dados confidenciais do conteúdo JSON no Integration Runtime auto-hospedado local, podemos executar **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** e passar o conteúdo JSON acima. Essa criptografia assegura que as credenciais sejam criptografadas usando DPAPI (proteção de dados de interface de programação de aplicativo) e armazenadas localmente no nó de Integration Runtime auto-hospedado. O conteúdo de saída pode ser redirecionado para outro arquivo JSON (no caso, 'encryptedLinkedService.json') que contém credenciais criptografadas. 

    Substitua **&lt;integration runtime name&gt;** pelo nome de seu Integration Runtime antes de executar o comando.

   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName <integration runtime name> -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Execute o comando a seguir usando JSON da etapa anterior para criar o **SqlServerLinkedService**:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json" 
   ```


## <a name="create-datasets"></a>Criar conjuntos de dados

### <a name="prepare-an-on-premises-sql-server-for-the-tutorial"></a>Preparar um SQL Server local para o tutorial

Nesta etapa, você cria conjuntos de dados de entrada e saída que representam dados de entrada e saída da operação de cópia (banco de dados SQL Server local = > armazenamento de blobs do Azure). Antes de criar conjuntos de dados, primeiro é necessário fazer o seguinte (etapas detalhadas seguem a lista):

- Criar uma tabela chamada **emp** no banco de dados SQL Server adicionado como um serviço vinculado à data factory e inserir alguns exemplos de entradas na tabela.
- Crie um contêiner de blob chamado **adftutorial** na conta de armazenamento de blob do Azure que você adicionou como um serviço vinculado à data factory.


1. No banco de dados especificado para o serviço vinculado do SQL Server local (**SqlServerLinkedService**), use o seguinte script SQL para criar a tabela **emp** no banco de dados.

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Insira algum exemplo na tabela:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```



### <a name="create-a-dataset-for-source-sql-database"></a>Criar um conjunto de dados para o Banco de Dados SQL de origem

1. Crie um arquivo JSON denominado **SqlServerDataset.json** na pasta **C:\ADFv2Tutorial** com o seguinte conteúdo:  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. Para criar o conjunto de dados: **SqlServerDataset**, execute o cmdlet **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Veja o exemplo de saída:

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-blob-storage"></a>Criar um conjunto de dados para o Armazenamento de Blobs do Azure de coletor

1. Crie um arquivo JSON denominado **AzureBlobDataset.json** na pasta **C:\ADFv2Tutorial** com o seguinte conteúdo: 

    > [!IMPORTANT]
    > Esse código de exemplo supõe que você tenha um contêiner denominado **adftutorial** no Armazenamento de Blobs do Azure.

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. Para criar o conjunto de dados: **AzureBlobDataset**, execute o cmdlet **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Veja o exemplo de saída:

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-pipelines"></a>Criar pipelines

### <a name="create-the-pipeline-sqlservertoblobpipeline"></a>Criar o pipeline "SqlServerToBlobPipeline"

1. Crie um arquivo JSON denominado **SqlServerToBlobPipeline.json** na pasta **C:\ADFv2Tutorial** com o seguinte conteúdo:

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Para criar o pipeline **SQLServerToBlobPipeline**, execute o cmdlet **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Veja o exemplo de saída:

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```



## <a name="start-and-monitor-a-pipeline-run"></a>Iniciar e monitorar uma execução de pipeline

1. Inicie a execução de um pipeline para o pipeline "SQLServerToBlobPipeline" e capture a ID da execução de pipeline para monitoramento futuro.  

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
    ```

2. Execute o script a seguir para verificar continuamente o status de execução do pipeline "**SQLServerToBlobPipeline**" e imprimir o resultado final.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    } 
    ```

    Aqui está a saída da execução de exemplo:

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName : 
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Você pode obter a ID da execução de pipeline "**SQLServerToBlobPipeline**" e verificar o resultado detalhado da execução da atividade conforme demonstrado a seguir.

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Aqui está a saída da execução de exemplo:

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 4,
      "throughput": 0.01,
      "errors": []
    }
    ```
4. Conecte-se ao Armazenamento de Blobs do Azure de coletor e confirme que dados foram corretamente copiados do Banco de Dados SQL do Azure.

## <a name="next-steps"></a>Próximas etapas
O pipeline nessa amostra copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Você aprendeu como: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um Integration Runtime auto-hospedado
> * Criar e criptografar o serviço vinculado do SQL Server local em um Integration Runtime auto-hospedado
> * Crie um serviço vinculado do Armazenamento do Azure.
> * Crie conjuntos de dados do SQL Server e do Armazenamento do Azure.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Inicie uma execução de pipeline.
> * Monitore as execuções de pipeline e de atividade.

Veja o artigo [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) para ver a lista dos armazenamentos de dados com suporte pelo Azure Data Factory como origens e coletores.

Avance para o tutorial a seguir para saber mais sobre a cópia de dados em massa de uma origem para um destino:

> [!div class="nextstepaction"]
>[Copiar dados em massa](tutorial-bulk-copy.md)
