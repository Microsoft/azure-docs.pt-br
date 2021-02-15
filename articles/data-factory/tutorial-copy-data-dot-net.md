---
title: Copiar dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL do Azure
description: Este tutorial fornece instruções passo a passo para copiar dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL do Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 11/08/2019
ms.author: jingwang
ms.openlocfilehash: b2293c0dd74903921abb58037afd8eb5db3659d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85513270"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Copiar dados do Blob do Azure para o Banco de Dados SQL do Azure usando o Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste tutorial, você cria um pipeline de Data Factory que copia dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL do Azure. O padrão de configuração neste tutorial aplica-se a cópia de um armazenamento de dados baseado em arquivo para um armazenamento de dados relacional. Para obter uma lista de armazenamentos de dados compatíveis como origens e coletores, confira [Formatos e armazenamentos de dados compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).

Neste tutorial, execute as seguintes etapas:

> [!div class="checklist"]
> * Criar um data factory.
> * Crie serviços vinculados do Armazenamento do Azure e do Banco de Dados SQL do Azure.
> * Crie conjuntos de dados do Blob do Azure e do Banco de Dados SQL do Azure.
> * Crie um pipeline que contenha uma atividade de cópia.
> * Iniciar uma execução de pipeline.
> * Monitore as execuções de pipeline e de atividade.

Este tutorial usa o .NET SDK. Você pode usar outros mecanismos para interagir com o Azure Data Factory, veja as amostras em **Guias de início rápido**.

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* *Conta de Armazenamento do Azure*. Você usa o Armazenamento de Blobs como um armazenamento de dados de *origem*. Se você não tiver uma conta de armazenamento do Azure, confira [Criar uma conta de armazenamento para uso geral](../storage/common/storage-account-create.md).
* *Banco de dados SQL do Azure*. Você usa o banco de dados como um armazenamento de dados de *coletor*. Se você não tiver um banco de dados no Banco de Dados SQL do Azure, confira [Criar um banco de dados no Banco de Dados SQL do Azure](../azure-sql/database/single-database-create-quickstart.md).
* *Do Visual Studio*. As instruções passo a passo neste artigo usam o Visual Studio 2019.
* *[SDK do Azure para .NET](/dotnet/azure/dotnet-tools)* .
* *Aplicativo do Azure Active Directory*. Se você não tiver um aplicativo do Azure Active Directory, confira a seção [Criar um aplicativo do Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) em [Como Use o portal para criar um aplicativo do Azure AD](../active-directory/develop/howto-create-service-principal-portal.md). Copie os seguintes valores para uso em etapas posteriores: **ID do aplicativo (cliente)** , **chave de autenticação** e **ID do Diretório (locatário)** . Seguindo as instruções no mesmo artigo, atribua o aplicativo à função **Colaborador**.

### <a name="create-a-blob-and-a-sql-table"></a>Criar um blob e uma tabela SQL

Agora, prepare seu blob do Azure e o Banco de Dados SQL do Azure para o tutorial criando um blog de origem e uma tabela SQL do coletor.

#### <a name="create-a-source-blob"></a>Criar um blob de origem

Primeiro, crie um blob de origem criando um contêiner e carregando um arquivo de texto de entrada para ele:

1. Abra o Bloco de Notas. Copie o texto a seguir e salve-o localmente em um arquivo chamado *inputEmp.txt*.

    ```inputEmp.txt
    John|Doe
    Jane|Doe
    ```

2. Use uma ferramenta como o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para criar o contêiner *adfv2tutorial* e carregar o arquivo *inputEmp.txt* no contêiner.

#### <a name="create-a-sink-sql-table"></a>Criar uma tabela do SQL de coletor

Em seguida, crie uma tabela do SQL de coletor:

1. Use o script SQL a seguir para criar a tabela *dbo.emp* no seu Banco de Dados SQL do Azure.

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

2. Permita que os serviços do Azure acessem o Banco de Dados SQL. Permita acesso aos serviços do Azure no servidor, de modo que o serviço do Data Factory possa gravar dados no Banco de Dados SQL. Para verificar e ativar essa configuração, faça as seguintes etapas:

    1. Acesse o [portal do Azure](https://portal.azure.com) para gerenciar o SQL Server. Pesquise e selecione **servidores SQL**.

    2. Selecione seu servidor.

    3. No título **Segurança** do menu do SQL Server, selecione **Firewalls e redes virtuais**.

    4. Na página **Firewall e redes virtuais**, em **Permitir que os serviços e recursos do Azure acessem esse servidor**, selecione **LIGAR**.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

Usando o Visual Studio, crie um aplicativo de console C# .NET.

1. Abra o Visual Studio.
2. Na janela **Iniciar**, selecione **Criar projeto**.
3. Na janela **Criar um projeto**, escolha a versão do C# do **Aplicativo de Console (.NET Framework)** na lista de tipos de projeto. Em seguida, selecione **Avançar**.
4. Na janela **Configurar seu novo projeto**, insira o **Nome do projeto** *ADFv2Tutorial*. Para **Localização**, navegue até e/ou crie o diretório no qual salvar o projeto. Em seguida, selecione **Criar**. O novo projeto aparece no IDE do Visual Studio.

## <a name="install-nuget-packages"></a>Instalar os pacotes NuGet

Em seguida, instale os pacotes de biblioteca necessários usando o gerenciador de pacotes do NuGet.

1. Na barra de menus, selecione **Ferramentas** > **Gerenciador de Pacotes do NuGet** > **Console do Gerenciador de Pacotes**.
2. No painel **Console do Gerenciador de Pacotes**, execute os comandos a seguir para instalar os pacotes. Para obter informações sobre o pacote do NuGet do Azure Data Factory, confira [Microsoft. Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```package manager console
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -PreRelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Criar um cliente data factory

Siga estas etapas para criar um cliente de data factory.

1. Abra *Program.cs* e substitua as instruções `using` pelo código a seguir para adicionar referências a namespaces.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Adicione o código a segui ao método `Main` que define as variáveis. Substitua os 14 espaços reservados pelos seus valores.

    Para ver uma lista de regiões do Azure nas quais o Data Factory está atualmente disponível, confira [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Na lista suspensa **Produtos**, escolha **Procurar** > **Analytics** > **Data Factory**. Na lista suspensa **Regiões**, escolha as regiões de seu interesse. É exibida uma grade com o status de disponibilidade dos produtos do Data Factory para suas regiões selecionadas.

    > [!NOTE]
    > Os armazenamentos de dados, como o Armazenamento do Azure e o Banco de Dados SQL do Azure, assim como as computações, como o HDInsight, que o Data Factory usa podem estar em regiões diferentes daquelas escolhidas para o Data Factory.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "<location to create the data factory in, such as East US>";
    string dataFactoryName = "<name of data factory to create (must be globally unique)>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString =
        "Server=tcp:<your server name>.database.windows.net,1433;" +
        "Database=<your database name>;" +
        "User ID=<your username>@<your server name>;" +
        "Password=<your password>;" +
        "Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Adicione o código a seguir ao método `Main` que cria uma instância da classe `DataFactoryManagementClient`. Você usa esse objeto para criar um data factory, serviço vinculado, conjuntos de dados e pipeline. Você também pode usar esse objeto para monitorar os detalhes da execução de pipeline.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync(
        "https://management.azure.com/", cc
    ).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Criar uma data factory

Adicione o código a seguir ao método `Main` que cria um *data factory*.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};

client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings)
);

while (
    client.Factories.Get(
        resourceGroup, dataFactoryName
    ).ProvisioningState == "PendingCreation"
)
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>Criar serviços vinculados

Neste tutorial, você deverá criar dois serviços vinculados para origem e coletor, respectivamente.

### <a name="create-an-azure-storage-linked-service"></a>Criar um serviço vinculado do Armazenamento do Azure

Adicione o código a seguir ao método `Main` que cria um *serviço vinculado do Armazenamento do Azure*. Para obter informações sobre propriedades compatíveis, confira [Propriedades do serviço vinculado do Blob do Azure](connector-azure-blob-storage.md#linked-service-properties).

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey
        )
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings)
);
```

### <a name="create-an-azure-sql-database-linked-service"></a>Criar um serviço vinculado do Banco de Dados SQL do Azure

Adicione o código a seguir ao método `Main` que cria um *serviço vinculado do Banco de Dados SQL do Azure*. Para obter informações sobre propriedades compatíveis, confira [Propriedades do serviço vinculado do Banco de Dados SQL do Azure](connector-azure-sql-database.md#linked-service-properties).

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings)
);
```

## <a name="create-datasets"></a>Criar conjuntos de dados

Nesta seção, você criará dois conjuntos de dados: um para a origem, outro para o coletor.

### <a name="create-a-dataset-for-source-azure-blob"></a>Criar um conjunto de dados para o Blob do Azure de origem

Adicione o código a seguir ao método `Main` que cria um *conjunto de dados de blob do Azure*. Para obter informações sobre propriedades compatíveis, confira [Propriedades do conjunto de dados de Blob do Azure](connector-azure-blob-storage.md#dataset-properties).

Você define um conjunto de dados que representa os dados de origem no Blob do Azure. Esse conjunto de dados de Blob refere-se ao serviço vinculado do Armazenamento do Azure que você criou na etapa anterior e ele descreve:

- A localização do blob do qual copiar: `FolderPath` e `FileName`
- O formato de blob que indica como analisar o conteúdo: `TextFormat` e as respectivas configurações, como o delimitador de coluna
- A estrutura de dados, incluindo tipos de dados e nomes de coluna que, nesse exemplo, mapeiam para a tabela SQL de coletor

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement { Name = "FirstName", Type = "String" },
            new DatasetDataElement { Name = "LastName", Type = "String" }
        }
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings)
);
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Criar um conjunto de dados para o Banco de Dados SQL do Azure de coletor

Adicione o código a seguir ao método `Main` que cria um *conjunto de dados do Banco de Dados SQL do Azure*. Para obter informações sobre propriedades compatíveis, confira [Propriedades do conjunto de dados de Banco de Dados SQL do Azure](connector-azure-sql-database.md#dataset-properties).

Você define um conjunto de dados que representa os dados de coletor no Banco de Dados SQL do Azure. Esse conjunto de dados refere-se ao serviço vinculado do Banco de Dados SQL do Azure que você criou na etapa anterior. Ela também especifica a tabela SQL que contém os dados copiados.

```csharp
// Create an Azure SQL Database dataset
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

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings)
);
```

## <a name="create-a-pipeline"></a>Criar um pipeline

Adicione o código a seguir ao método `Main` que cria um *pipeline com uma atividade de cópia*. Neste tutorial, o pipeline contém uma atividade: a `CopyActivity`, que usa o conjunto de dados de Blob como origem e o conjunto de dados do SQL como coletor. Para obter informações sobre os detalhes da atividade de cópia, confira [Atividade de cópia no Azure Data Factory](copy-activity-overview.md).

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
                new DatasetReference() { ReferenceName = blobDatasetName }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference { ReferenceName = sqlDatasetName }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings)
);
```

## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline

Adicione o código a seguir para o método `Main` que *dispara uma execução de pipeline*.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Monitorar uma execução de pipeline

Agora, insira o código para verificar os estados de execução do pipeline e obter detalhes sobre a execução da atividade de cópia.

1. Adicione o código a seguir ao método `Main` para verificar continuamente os status do pipeline de execução até que ele termine de copiar os dados.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(
            resourceGroup, dataFactoryName, runResponse.RunId
        );
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Adicione o código a seguir ao método `Main` para recuperar os detalhes de execução da atividade de cópia, como o tamanho dos dados que foram lidos ou gravados.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    RunFilterParameters filterParams = new RunFilterParameters(
        DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)
    );

    ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
        resourceGroup, dataFactoryName, runResponse.RunId, filterParams
    );

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(queryResponse.Value.First().Output);
    }
    else
        Console.WriteLine(queryResponse.Value.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Executar o código

Crie o aplicativo escolhendo **Build** > **Compilar Solução**. Em seguida, inicie o aplicativo escolhendo **Depurar** > **Iniciar a Depuração** e verifique a execução do pipeline.

O console imprime o progresso de criação de um data factory, serviço vinculado, conjuntos de dados, pipeline e execução de pipeline. Em seguida, ele verifica o status da execução de pipeline. Aguarde até ver os detalhes de execução da atividade de cópia com o tamanho dos dados lidos/gravados. Em seguida, usando ferramentas como o SSMS (SQL Server Management Studio) ou o Visual Studio, você pode conectar-se ao seu Banco de Dados SQL do Azure alvo e verificar se a tabela alvo que você especificou contém os dados copiados.

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
  "usedDataIntegrationUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```

## <a name="next-steps"></a>Próximas etapas

O pipeline nessa amostra copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Você aprendeu a:

> [!div class="checklist"]
> * Criar um data factory.
> * Crie serviços vinculados do Armazenamento do Azure e do Banco de Dados SQL do Azure.
> * Crie conjuntos de dados do Blob do Azure e do Banco de Dados SQL do Azure.
> * Crie um pipeline que contenha uma atividade de cópia.
> * Iniciar uma execução de pipeline.
> * Monitore as execuções de pipeline e de atividade.

Avance para o tutorial a seguir para saber mais sobre a cópia de dados local para a nuvem:

> [!div class="nextstepaction"]
>[Copiar dados do local para a nuvem](tutorial-hybrid-copy-powershell.md)
