---
title: Criar o Azure Data Factory usando o SDK do .NET
description: Crie um Azure Data Factory para copiar dados de um local em um Armazenamento de Blobs do Azure para outro local.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/24/2019
ms.author: jingwang
ms.openlocfilehash: 24cba4b02bb046a16db04635a1bf5ef4f6b619a6
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234515"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-net-sdk"></a>Início Rápido: Criar um data factory e um pipeline usando o SDK do .NET

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](quickstart-create-data-factory-dot-net.md)

Este guia de início rápido descreve como usar o SDK do .NET para criar um Azure Data Factory. O pipeline que você criar nesse data factory **copia** dados de uma pasta para outra em um Armazenamento de Blobs do Azure. Para obter um tutorial sobre como **transformar** dados usando o Azure Data Factory, confira [Tutorial: Transformar dados usando o Spark](tutorial-transform-data-spark-portal.md).

> [!NOTE]
> Este artigo não fornece uma introdução detalhada do serviço Data Factory. Para obter uma introdução do serviço do Azure Data Factory, consulte [Introdução ao Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="visual-studio"></a>Visual Studio

As instruções passo a passo neste artigo usam o Visual Studio 2019. Os procedimentos para Visual Studio 2013, 2015 ou 2017 são levemente diferentes.

### <a name="azure-net-sdk"></a>SDK do Azure .NET

Baixe e instale o [SDK .NET do Azure](https://azure.microsoft.com/downloads/) no seu computador.

## <a name="create-an-application-in-azure-active-directory"></a>Criar um aplicativo no Azure Active Directory

Das seções em *Como usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que possa acessar recursos*, siga as instruções para executar estas tarefas:

1. Em [Criar um aplicativo no Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application), crie um aplicativo que represente o aplicativo .NET que você está criando neste tutorial. Para a URL de logon, você pode fornecer uma URL fictícia, como mostrado no artigo (`https://contoso.org/exampleapp`).
2. Em [Obter valores para entrar](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in), obtenha a **ID do aplicativo** e a **ID do locatário** e anote esses valores que você usará posteriormente neste tutorial. 
3. Em [Certificados e segredos](../active-directory/develop/howto-create-service-principal-portal.md#certificates-and-secrets), obtenha a **chave de autenticação** e anote esse valor que você usa posteriormente neste tutorial.
4. Em [Atribuir o aplicativo a uma função](../active-directory/develop/howto-create-service-principal-portal.md#assign-the-application-to-a-role), atribua o aplicativo à função **Colaborador** no nível da assinatura para que o aplicativo possa criar os data factories na assinatura.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

Em seguida, crie um aplicativo de console do .NET em C# no Visual Studio:

1. Inicie o **Visual Studio**.
2. Na janela Iniciar, selecione **Criar um novo projeto** > **Aplicativo de Console (.NET Framework)** . O .NET versão 4.5.2 ou superior é necessário.
3. Em **Nome do projeto**, insira **ADFv2QuickStart**.
4. Selecione **Criar** para criar o cluster.

## <a name="install-nuget-packages"></a>Instalar os pacotes NuGet

1. Selecione **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.
2. No painel **Console do Gerenciador de Pacotes**, execute os comandos a seguir para instalar os pacotes. Para obter mais informações, confira o [pacote do NuGet Microsoft.Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
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
    ```

2. Adicione o código a seguir, que define as variáveis, ao método **Main**. Substitua os espaços reservados pelos seus próprios valores. Para obter uma lista de regiões do Azure no qual o Data Factory está disponível no momento, selecione as regiões que relevantes para você na página a seguir e, em seguida, expanda **Análise** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure entre outros) e serviços de computação (HDInsight entre outros) usados pelo data factory podem estar em outras regiões.

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string dataFactoryName = 
       "<specify the name of data factory to create. It must be globally unique.>";
   string storageAccount = "<your storage account name to copy data>";
   string storageKey = "<your storage account key>";
   // specify the container and input folder from which all files 
   // need to be copied to the output folder. 
   string inputBlobPath =
       "<path to existing blob(s) to copy data from, e.g. containername/inputdir>";
   //specify the contains and output folder where the files are copied
   string outputBlobPath =
       "<the blob path to copy data to, e.g. containername/outputdir>";

   // name of the Azure Storage linked service, blob dataset, and the pipeline
   string storageLinkedServiceName = "AzureStorageLinkedService";
   string blobDatasetName = "BlobDataset";
   string pipelineName = "Adfv2QuickStartPipeline";
   ```

3. Adicione o código a seguir, que cria uma instância da classe **DataFactoryManagementClient**, ao método **Main**. Você usa esse objeto para criar um data factory, um serviço vinculado, conjuntos de dados e um pipeline. Você também pode usar esse objeto para monitorar os detalhes da execução de pipeline.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
       "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) {
       SubscriptionId = subscriptionId };
   ```

## <a name="create-a-data-factory"></a>Criar uma data factory

Adicione o código a seguir, que cria um **data factory**, ao método **Main**. 

```csharp
// Create a data factory
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState ==
       "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-a-linked-service"></a>Criar um serviço vinculado

Adicione o código a seguir, que cria um **serviço vinculado do Armazenamento do Azure**, ao método **Main**.

Os serviços vinculados são criados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Neste Início Rápido, você só precisa criar um serviço vinculado do Armazenamento do Azure para a origem da cópia e o repositório de coletor. Esse serviço chama-se "AzureStorageLinkedService" no exemplo.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(
    storageLinkedService, client.SerializationSettings));
```

## <a name="create-a-dataset"></a>Criar um conjunto de dados

Adicione o código a seguir, que cria um **Conjunto de Dados do Blob do Azure**, ao método **Main**.

Você define um conjunto de dados que representa os dados a copiar de uma origem para um coletor. Neste exemplo, esse conjunto de dados de Blob faz referência ao serviço vinculado do Armazenamento do Azure que você criou na etapa anterior. O conjunto de dados usa um parâmetro cujo valor é definido em uma atividade que consome o conjunto de dados. O parâmetro é usado para construir o "folderPath" que aponta para o local em que os dados residem/são armazenados.

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }
        }
    }
);
client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Criar um pipeline

Adicione o código a seguir, que cria um **pipeline com uma atividade de cópia**, ao método **Main**.

Neste exemplo, esse pipeline contém uma atividade e leva dois parâmetros: caminho do blob de entrada e caminho do blob de saída. Os valores para esses parâmetros são definidos quando o pipeline é disparado/executado. A atividade de cópia refere-se ao mesmo conjunto de dados de blob criado na etapa anterior como entrada e saída. Quando o conjunto de dados é usado como um conjunto de dados de entrada, o caminho de entrada é especificado. E quando o conjunto de dados é usado como um conjunto de dados de saída, o caminho de saída é especificado. 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline

Adicione o código a seguir, que **dispara uma execução de pipeline**, ao método **Main**.

Esse código também define os valores dos parâmetros **inputPath** e **outputPath** especificados no pipeline com os valores reais dos caminhos de blob de origem e de coletor.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName, parameters: parameters
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Monitorar uma execução de pipeline

1. Adicione o código a seguir ao método **Main** para verificar continuamente o status até que ele termine de copiar os dados.

   ```csharp
   // Monitor the pipeline run
   Console.WriteLine("Checking pipeline run status...");
   PipelineRun pipelineRun;
   while (true)
   {
       pipelineRun = client.PipelineRuns.Get(
           resourceGroup, dataFactoryName, runResponse.RunId);
       Console.WriteLine("Status: " + pipelineRun.Status);
       if (pipelineRun.Status == "InProgress")
           System.Threading.Thread.Sleep(15000);
       else
           break;
   }
   ```

2. Adicione o código a seguir ao método **Main** para recuperar os detalhes de execução da atividade de cópia, como o tamanho dos dados que são lidos ou gravados.

   ```csharp
   // Check the copy activity run details
   Console.WriteLine("Checking copy activity run details...");

   RunFilterParameters filterParams = new RunFilterParameters(
       DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10));
   ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
       resourceGroup, dataFactoryName, runResponse.RunId, filterParams);
   if (pipelineRun.Status == "Succeeded")
       Console.WriteLine(queryResponse.Value.First().Output);
   else
       Console.WriteLine(queryResponse.Value.First().Error);
   Console.WriteLine("\nPress any key to exit...");
   Console.ReadKey();
   ```

## <a name="run-the-code"></a>Executar o código

Compile e inicie o aplicativo, então verifique a execução do pipeline.

O console imprime o progresso de criação do data factory, do serviço vinculado, dos conjuntos de dados, do pipeline e da execução de pipeline. Em seguida, ele verifica o status da execução de pipeline. Aguarde até ver os detalhes da execução da atividade de cópia com o tamanho dos dados de leitura/gravação. Em seguida, use ferramentas como o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para verificar se os blobs são copiados de "inputBlobPath" para "outputBlobPath" conforme você especificou nas variáveis.

### <a name="sample-output"></a>Saída de exemplo

```json
Creating data factory SPv2Factory0907...
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
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@{dataset().path}",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "AzureStorageLinkedService",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "path": {
        "type": "String"
      }
    }
  }
}
Creating pipeline Adfv2QuickStartPipeline...
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
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.inputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.outputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "name": "CopyFromBlobToBlob"
      }
    ],
    "parameters": {
      "inputPath": {
        "type": "String"
      },
      "outputPath": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 2,
    "billedDuration": 23
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Verificar a saída

O pipeline cria automaticamente a pasta de saída no contêiner de blob **adftutorial**. Em seguida, ele copia o arquivo **emp.txt** da pasta de entrada para a pasta de saída. 

1. No portal do Azure, na página do contêiner **adftutorial** que você interrompeu na seção [Adicionar uma pasta de entrada e um arquivo ao contêiner de blobs](#add-an-input-folder-and-file-for-the-blob-container) acima, selecione **Atualizar** para exibir a pasta de saída. 
2. Na lista de pastas, selecione **saída**.
3. Confirme que **emp.txt** tenha sido copiado para a pasta de saída. 

## <a name="clean-up-resources"></a>Limpar recursos

Para excluir programaticamente o data factory, adicione as linhas de código a seguir ao programa: 

```csharp
Console.WriteLine("Deleting the data factory");
client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>Próximas etapas

O pipeline nessa amostra copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Percorra os [tutoriais](tutorial-copy-data-dot-net.md) para saber mais sobre o uso do Data Factory em mais cenários. 