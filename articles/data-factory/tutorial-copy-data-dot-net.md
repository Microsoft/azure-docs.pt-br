---
title: Copiar dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL | Microsoft Docs
description: "Este tutorial fornece instruções passo a passo para copiar dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL do Azure."
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
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: 6f1a93c2906eaab82dcfb9bae1ee4a54dce300bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Copiar dados do Blob do Azure para o Banco de Dados SQL do Azure usando o Azure Data Factory
O Azure Data Factory é um serviço de integração de dados baseado em nuvem que permite que você crie fluxos de trabalho controlados por dados na nuvem para orquestrar e automatizar a movimentação e a transformação de dados. Usando o Azure Data Factory, você pode criar e agendar fluxos de trabalho orientados a dados (chamados de pipelines) que podem ingerir dados de repositórios de dados diferentes, processar/transformr os dados usando serviços de computação como o Hadoop do Azure HDInsight, Spark, Azure Data Lake Analytics e Azure Machine Learning e publicar os dados de saída em repositórios de dados como o SQL Data Warehouse do Azure para consumo pelos aplicativos de business intelligence (BI). 

Neste tutorial, você cria um pipeline de Data Factory que copia dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL do Azure. O padrão de configuração neste tutorial aplica-se a cópia de um armazenamento de dados baseado em arquivo para um armazenamento de dados relacional. Para obter uma lista de armazenamentos de dados com suporte como origens e coletores, consulte a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Crie serviços vinculados do Armazenamento do Azure e do Banco de Dados SQL do Azure.
> * Crie conjuntos de dados do Blob do Azure e do Banco de Dados SQL do Azure.
> * Crie um pipeline que contenha uma atividade de cópia.
> * Inicie uma execução de pipeline.
> * Monitore as execuções de pipeline e de atividade.

Este tutorial usa o .NET SDK. Você pode usar outros mecanismos para interagir com o Azure Data Factory, consulte as amostras em "Guias de início rápido".

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **Conta de Armazenamento do Azure**. Você usa o Armazenamento de Blobs como um armazenamento de dados de **origem**. Se você não tiver uma conta de Armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para conhecer as etapas para criar uma.
* **Banco de dados SQL do Azure**. Você usa o banco de dados como um armazenamento de dados de **coletor**. Se você não tiver um Banco de Dados SQL do Azure, veja o artigo [Criar um Banco de Dados SQL do Azure](../sql-database/sql-database-get-started-portal.md) para conhecer as etapas para criar um.
* **Visual Studio** 2015 ou 2017. Este artigo passo a passo usa o Visual Studio 2017.
* **Baixar e instalar o [SDK do .NET do Azure](http://azure.microsoft.com/downloads/)**.
* **Crie um aplicativo no Azure Active Directory** seguindo [esta instrução](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Anote os valores a seguir, que você usará em etapas posteriores: **ID do aplicativo**, **chave de autenticação** e **ID do locatário**. Seguindo as instruções no mesmo artigo, atribua o aplicativo à função "**Colaborador**".

### <a name="create-a-blob-and-a-sql-table"></a>Criar um blob e uma tabela SQL

Agora, prepare seu Blob do Azure e Banco de Dados SQL do Azure para o tutorial, executando as seguintes etapas:

#### <a name="create-a-source-blob"></a>Criar um blob de origem

1. Inicie o Bloco de notas. Copie o texto a seguir e salve-o como um arquivo **inputEmp.txt** no disco.

    ```
    John|Doe
    Jane|Doe
    ```

2. Use ferramentas como o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/) para criar o contêiner **adfv2tutorial** e carregar o arquivo **inputEmp.txt** no contêiner.

#### <a name="create-a-sink-sql-table"></a>Criar uma tabela do SQL de coletor

1. Use o script SQL a seguir para criar a tabela **dbo.emp** no seu Banco de Dados SQL do Azure.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Permita que os serviços do Azure acessem o SQL Server. Certifique-se de a configuração **Permitir acesso aos serviços do Azure** esteja definida como **ATIVADA** para o Azure SQL Server, permitindo que nele sejam gravados dados pelo serviço Data Factory. Para verificar e ativar essa configuração, faça as seguintes etapas:

    1. Clique no hub **Mais serviços** à esquerda e clique em **Servidores SQL**.
    2. Selecione seu servidor e clique em **Firewall** em **CONFIGURAÇÕES**.
    3. Na página **Configurações de Firewall**, clique em **ATIVADO** para **Permitir acesso aos serviços do Azure**.


## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

Usando o Visual Studio 2015/2017, crie um aplicativo de console C# .NET.

1. Inicie o **Visual Studio**.
2. Clique em **Arquivo**, aponte para **Novo** e clique em **Projeto**.
3. Selecione **Visual C#** -> **Aplicativo de Console (.NET Framework)** na lista de tipos de projeto à direita. O .NET versão 4.5.2 ou superior é necessário.
4. Digite **ADFv2Tutorial** para o Nome.
5. Clique em **OK** para criar o projeto.

## <a name="install-nuget-packages"></a>Instalar os pacotes NuGet

1. Clique em **Ferramentas** -> **Gerenciador de Pacotes do NuGet** -> **Console do Gerenciador de Pacotes**.
2. No **Console do Gerenciador de Pacotes**, execute os comandos a seguir para instalar os pacotes:

    ```
    Install-Package Microsoft.Azure.Management.DataFactory -Prerelease
    Install-Package Microsoft.Azure.Management.ResourceManager -Prerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Criar um cliente data factory

1. Abra **Program.cs** e inclua as instruções a seguir para adicionar referências aos namespaces.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;

2. Add the following code to the **Main** method that sets variables. Replace place-holders with your own values.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    // Note that the data stores (Azure Storage, Azure SQL Database, etc.) and computes (HDInsight, etc.) used by a data factory can be in other regions.
    string region = "East US";
    string dataFactoryName = "<specify the name of a data factory to create. It must be globally unique.>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString = "Server=tcp:<your server name>.database.windows.net,1433;Database=<your database name>;User ID=<your username>@<your server name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Adicione o código a seguir, que cria uma instância da classe **DataFactoryManagementClient**, ao método **Main**. Você usa esse objeto para criar um data factory, serviço vinculado, conjuntos de dados e pipeline. Você também pode usar esse objeto para monitorar os detalhes da execução de pipeline.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Criar uma data factory

Adicione o código a seguir, que cria um **data factory**, ao método **Main**.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()

};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>Criar serviços vinculados

Neste tutorial, você deverá criar dois serviços vinculados para origem e coletor, respectivamente:

### <a name="create-an-azure-storage-linked-service"></a>Criar um serviço vinculado do Armazenamento do Azure

Adicione o código a seguir, que cria um **serviço vinculado do Armazenamento do Azure**, ao método **Main**. Saiba mais sobre [Propriedades do serviço vinculado de Blob do Azure](connector-azure-blob-storage.md#linked-service-properties) nas propriedades e detalhes com suporte.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
```

### <a name="create-an-azure-sql-database-linked-service"></a>Criar um serviço vinculado do Banco de Dados SQL do Azure

Adicione o código a seguir, que cria um **serviço vinculado do Banco de Dados SQL do Azure**, ao método **Main**. Saiba mais sobre [Propriedades do serviço vinculado de Banco de Dados SQL do Azure](connector-azure-sql-database.md#linked-service-properties) nas propriedades e detalhes com suporte.

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings));
```

## <a name="create-datasets"></a>Criar conjuntos de dados

Nesta seção, você criará dois conjuntos de dados: um para a origem e o outro para o coletor. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Criar um conjunto de dados para o Blob do Azure de origem

Adicione o código a seguir, que cria um **Conjunto de Dados do Blob do Azure**, ao método **Main**. Saiba mais sobre [Propriedades de conjunto de dados de Blob do Azure](connector-azure-blob-storage.md#dataset-properties) nas propriedades e detalhes com suporte.

Você define um conjunto de dados que representa os dados de origem no Blob do Azure. Esse conjunto de dados de Blob refere-se ao serviço vinculado do Armazenamento do Azure que você criou na etapa anterior e ele descreve:

- A localização do blob da qual copiar: **FolderPath** e **FileName**;
- O formato de blob que indica como analisar o conteúdo: **TextFormat** e as respectivas configurações (por exemplo, o delimitador de coluna).
- A estrutura de dados, incluindo tipos de dados e nomes de coluna que, nesse caso, mapeiam para a tabela SQL de coletor.

```csharp
// Create a Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement
            {
                Name = "FirstName",
                Type = "String"
            },
            new DatasetDataElement
            {
                Name = "LastName",
                Type = "String"
            }
        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Criar um conjunto de dados para o Banco de Dados SQL do Azure de coletor

Adicione o código a seguir, que cria um **conjunto de dados do Banco de Dados SQL do Azure**, ao método **Main**. Saiba mais sobre [Propriedades de conjunto de dados de Banco de Dados SQL do Azure](connector-azure-sql-database.md#dataset-properties) nas propriedades e detalhes com suporte.

Você define um conjunto de dados que representa os dados de coletor no Banco de Dados SQL do Azure. Esse conjunto de dados refere-se ao serviço vinculado do Banco de Dados SQL do Azure que você criou na etapa anterior. Ela também especifica a tabela SQL que contém os dados copiados. 

```csharp
// Create a Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Criar uma pipeline

Adicione o código a seguir, que cria um **pipeline com uma atividade de cópia**, ao método **Main**. Neste tutorial, o pipeline contém uma atividade: a atividade de cópia, que usa o conjunto de dados de Blob como origem e o conjunto de dados do SQL como coletor. Saiba mais em [Visão geral da atividade de cópia](copy-activity-overview.md), nos detalhes da atividade de cópia.

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = sqlDatasetName
                }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline

Adicione o código a seguir, que **dispara uma execução de pipeline**, ao método **Main**.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Monitorar uma execução de pipeline

1. Adicione o código a seguir ao método **Main** para verificar continuamente o status do pipeline de execução até que ele termine de copiar os dados.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Adicione o código a seguir ao método **Main** para recuperar os detalhes de execução da atividade de cópia, por exemplo, o tamanho dos dados lidos/gravados.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
 

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Executar o código

Compile e inicie o aplicativo, então verifique a execução do pipeline.

O console imprime o progresso de criação de um data factory, serviço vinculado, conjuntos de dados, pipeline e execução de pipeline. Em seguida, ele verifica o status da execução de pipeline. Aguarde até ver os detalhes de execução da atividade de cópia com o tamanho dos dados lidos/gravados. Em seguida, use ferramentas como o SSMS (SQL Server Management Studio) ou o Visual Studio para se conectar ao seu Banco de Dados SQL do Azure de destino e verifique se os dados são copiados para a tabela especificada.

### <a name="sample-output"></a>Saída de exemplo

```json
Creating a data factory AdfV2Tutorial...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
      }
    }
  }
}
Creating linked service AzureSqlDbLinkedService...
{
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
    "structure": [
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
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedCloudDataMovementUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```


## <a name="next-steps"></a>Próximas etapas
O pipeline nessa amostra copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Você aprendeu como: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Crie serviços vinculados do Armazenamento do Azure e do Banco de Dados SQL do Azure.
> * Crie conjuntos de dados do Blob do Azure e do Banco de Dados SQL do Azure.
> * Crie um pipeline que contenha uma atividade de cópia.
> * Inicie uma execução de pipeline.
> * Monitore as execuções de pipeline e de atividade.


Avance para o tutorial a seguir para saber mais sobre a cópia de dados local para a nuvem: 

> [!div class="nextstepaction"]
>[Copiar dados do local para a nuvem](tutorial-hybrid-copy-powershell.md)
