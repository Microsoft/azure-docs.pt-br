---
title: "Ramificação no pipeline do Azure Data Factory | Microsoft Docs"
description: "Aprenda a controlar o fluxo de dados no Azure Data Factory por ramificação e encadeamento de atividades."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: 2d1de12b420d203980d154e08dea036deb20a533
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Ramificação e encadeamento de atividades em um pipeline de Data Factory
O Azure Data Factory é um serviço de integração de dados baseado em nuvem que permite que você crie fluxos de trabalho controlados por dados na nuvem para orquestrar e automatizar a movimentação e a transformação de dados. Usando o Azure Data Factory, você pode criar e agendar fluxos de trabalho orientados a dados (chamados de pipelines) que podem ingerir dados de repositórios de dados diferentes, processar/transformr os dados usando serviços de computação como o Hadoop do Azure HDInsight, Spark, Azure Data Lake Analytics e Azure Machine Learning e publicar os dados de saída em repositórios de dados como o SQL Data Warehouse do Azure para consumo pelos aplicativos de business intelligence (BI). 

Neste tutorial, você deve criar um pipeline de Data Factory que apresente alguns dos recursos de fluxo de controle. Esse pipeline faz uma cópia simples de um contêiner no Armazenamento de Blobs do Azure para outro contêiner na mesma conta de armazenamento. Se a atividade de cópia for bem-sucedida, você desejará enviar detalhes da operação de cópia bem-sucedida (tais como a quantidade de dados gravados) em um email de êxito. Se a atividade de cópia falhar, você desejará enviar detalhes da falha de cópia (por exemplo, a mensagem de erro) em um email de falha. Ao longo do tutorial, você verá como passar parâmetros.

Uma visão geral de alto nível do cenário: ![Visão geral](media/tutorial-control-flow/overview.png)

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um serviço vinculado do Armazenamento do Azure.
> * Criar um conjunto de dados do Blob do Azure
> * Criar um pipeline que contém uma atividade de cópia e uma atividade da Web
> * Enviar saídas de atividades para as atividades subsequentes
> * Utilizar passagem de parâmetros e variáveis do sistema
> * Iniciar uma execução de pipeline
> * Monitorar as execuções de pipeline e de atividade

Este tutorial usa o .NET SDK. Você pode usar outros mecanismos para interagir com o Azure Data Factory, consulte "Guias de início rápido" no sumário.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **Conta de Armazenamento do Azure**. Você usa o Armazenamento de Blobs como um armazenamento de dados de **origem**. Se você não tiver uma conta de Armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para conhecer as etapas para criar uma.
* **Banco de dados SQL do Azure**. Você usa o banco de dados como um armazenamento de dados de **coletor**. Se você não tiver um Banco de Dados SQL do Azure, veja o artigo [Criar um Banco de Dados SQL do Azure](../sql-database/sql-database-get-started-portal.md) para conhecer as etapas para criar um.
* **Visual Studio** 2013, 2015 ou 2017. Este artigo passo a passo usa o Visual Studio 2017.
* **Baixar e instalar o [SDK do .NET do Azure](http://azure.microsoft.com/downloads/)**.
* **Crie um aplicativo no Azure Active Directory** seguindo [estas instruções](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Anote os valores a seguir, que você usará em etapas posteriores: **ID do aplicativo**, **chave de autenticação** e **ID do locatário**. Seguindo as instruções no mesmo artigo, atribua o aplicativo à função "**Colaborador**".

### <a name="create-blob-table"></a>Criar tabela de blob

1. Inicie o Bloco de notas. Copie o texto a seguir e salve-o como um arquivo **input.txt** no disco.

    ```
    John|Doe
    Jane|Doe
    ```
2. Use ferramentas como o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/) para criar o contêiner **adfv2branch** e carregar o arquivo **input.txt** no contêiner.

## <a name="create-visual-studio-project"></a>Criar um projeto do Visual Studio

Usando o Visual Studio 2015/2017, crie um aplicativo de console C# .NET.

1. Inicie o **Visual Studio**.
2. Clique em **Arquivo**, aponte para **Novo** e clique em **Projeto**. O .NET versão 4.5.2 ou superior é necessário.
3. Selecione **Visual C#** -> **Aplicativo de Console (.NET Framework)** na lista de tipos de projeto à direita.
4. Digite **ADFv2BranchTutorial** para o Nome.
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

2. Add these static variables to the **Program class**. Replace place-holders with your own values.

    ```csharp
        // Set variables
        static string tenantID = "<tenant ID>";
        static string applicationId = "<application ID>";
        static string authenticationKey = "<Authentication key for your application>";
        static string subscriptionId = "<Azure subscription ID>";
        static string resourceGroup = "<Azure resource group name>";

        // Note that the data stores (Azure Storage, Azure SQL Database, etc.) and computes (HDInsight, etc.) used by data factory can be in other regions.
        // Currently, only East US and East US 2 regions are supported. 
        static string region = "East US";
        static string dataFactoryName = "<Data factory name>";

        // Specify the source Azure Blob information
        static string storageAccount = "<Azure Storage account name>";
        static string storageKey = "<Azure Storage account key>";
        // confirm that you have the input.txt file placed in th input folder of the adfv2branch container. 
        static string inputBlobPath = "adfv2branch/input";
        static string inputBlobName = "input.txt";
        static string outputBlobPath = "adfv2branch/output";
        static string emailReceiver = "<specify email address of the receiver>";

        static string storageLinkedServiceName = "AzureStorageLinkedService";
        static string blobSourceDatasetName = "SourceStorageDataset";
        static string blobSinkDatasetName = "SinkStorageDataset";
        static string pipelineName = "Adfv2TutorialBranchCopy";

        static string copyBlobActivity = "CopyBlobtoBlob";
        static string sendFailEmailActivity = "SendFailEmailActivity";
        static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
    
    ```

3. Adicione o código a seguir, que cria uma instância da classe **DataFactoryManagementClient**, ao método **Main**. Você usa esse objeto para criar o data factory, o serviço vinculado, os conjuntos de dados e o pipeline. Você também pode usar esse objeto para monitorar os detalhes da execução de pipeline.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Criar uma data factory
Crie uma função "CreateOrUpdateDataFactory" no arquivo Program.cs:

```csharp
static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating data factory " + dataFactoryName + "...");
    Factory resource = new Factory
    {
        Location = region
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

    Factory response;
    {
        response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
    }

    while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
    {
        System.Threading.Thread.Sleep(1000);
    }
    return response;
}
```



Adicione o código a seguir, que cria um **data factory**, ao método **Main**. 

```csharp
Factory df = CreateOrUpdateDataFactory(client);
```

## <a name="create-an-azure-storage-linked-service"></a>Criar um serviço vinculado do Armazenamento do Azure
Crie uma função "StorageLinkedServiceDefinition" no arquivo Program.cs:

```csharp
static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
    AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
    LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
    return linkedService;
}
```
Adicione o código a seguir, que cria um **serviço vinculado do Armazenamento do Azure**, ao método **Main**. Saiba mais sobre [Propriedades do serviço vinculado de Blob do Azure](connector-azure-blob-storage.md#linked-service-properties) nas propriedades e detalhes com suporte.

```csharp
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
```

## <a name="create-datasets"></a>Criar conjuntos de dados

Nesta seção, você criará dois conjuntos de dados: um para a origem e o outro para o coletor. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Criar um conjunto de dados para o Blob do Azure de origem
Adicione o código a seguir, que cria um **Conjunto de Dados do Blob do Azure**, ao método **Main**. Saiba mais sobre [Propriedades de conjunto de dados de Blob do Azure](connector-azure-blob-storage.md#dataset-properties) nas propriedades e detalhes com suporte.

Você define um conjunto de dados que representa os dados de origem no Blob do Azure. Esse conjunto de dados de Blob refere-se ao serviço vinculado do Armazenamento do Azure que você criou na etapa anterior e ele descreve:

- A localização do blob da qual copiar: **FolderPath** e **FileName**;
- Observe o uso de parâmetros para FolderPath. "sourceBlobContainer" é o nome do parâmetro e a expressão é substituída pelos valores passados na execução de pipeline. A sintaxe para definir os parâmetros é `@pipeline().parameters.<parameterName>`

Criar uma função "SourceBlobDatasetDefinition" no arquivo Program.cs

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    { 
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Criar um conjunto de dados para o Blob do Azure de coletor

Criar uma função "SourceBlobDatasetDefinition" no arquivo Program.cs

```csharp
static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    {
        FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
    return dataset;
}
```

Adicione o código a seguir, que cria o conjunto de dados de origem e também o de coletor do Blob do Azure, ao método **Main**. 

```csharp
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
```

## <a name="create-a-c-class-emailrequest"></a>Criar uma classe do C#: EmailRequest
Em seu projeto do C#, crie uma classe denominada **EmailRequest**. Isso define quais propriedades o pipeline envia no corpo da solicitação ao enviar um email. Neste tutorial, o pipeline envia as quatro propriedades do pipeline para o email:

- **Mensagem**: o corpo do email. Em caso de uma cópia bem-sucedida, esta propriedade conterá os detalhes da execução (número de dados gravados). Em caso de uma cópia com falha, esta propriedade conterá os detalhes do erro.
- **Nome do data factory**: o nome do data factory
- **Nome do pipeline**: o nome do pipeline
- **Destinatário**: parâmetro que é passado. Essa propriedade especifica o destinatário do email.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```
## <a name="create-email-workflow-endpoints"></a>Criar pontos de extremidade do fluxo de trabalho de email
Para disparar o envio de um email, você deve usar os [Aplicativos Lógicos](../logic-apps/logic-apps-what-are-logic-apps.md) para definir o fluxo de trabalho. Para obter detalhes sobre como criar um fluxo de trabalho de Aplicativos Lógicos, consulte [Como criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md). 

### <a name="success-email-workflow"></a>Fluxo de trabalho de email de êxito 
Criar um fluxo de trabalho dos Aplicativos Lógicos chamado `CopySuccessEmail`. Defina o gatilho do fluxo de trabalho como `When an HTTP request is received` e adicione uma ação de `Office 365 Outlook – Send an email`.

![Fluxo de trabalho de email de êxito](media/tutorial-control-flow/success-email-workflow.png)

Para o gatilho de solicitação, preencha o `Request Body JSON Schema` com o JSON a seguir:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```
Isso se alinha com a classe **EmailRequest** que você criou na seção anterior. 

A sua solicitação deve ter esta aparência no Designer de Aplicativo Lógico:

![Designer de Aplicativo Lógico – solicitação](media/tutorial-control-flow/logic-app-designer-request.png)

Para a ação **Enviar Email**, personalize o modo como você deseja formatar o email, utilizando as propriedades passadas no esquema JSON do corpo da solicitação. Aqui está um exemplo:

![Designer de Aplicativo Lógico – ação de enviar email](media/tutorial-control-flow/send-email-action.png)

Anote a URL da solicitação HTTP Post para o fluxo de trabalho de email de êxito:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="fail-email-workflow"></a>Fluxo de trabalho de email de falha 
Clone o **CopySuccessEmail** e criar outro fluxo de trabalho de Aplicativos Lógicos de **CopyFailEmail**. No gatilho de solicitação, o `Request Body JSON schema` é o mesmo. Basta alterar o formato do seu email como o `Subject` para ajustá-lo, tornando-o um email de falha. Aqui está um exemplo:

![Designer de Aplicativo Lógico – fluxo de trabalho de email de falha](media/tutorial-control-flow/fail-email-workflow.png)

Anote a URL da solicitação HTTP Post para o fluxo de trabalho de email de falha:

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

Agora você deve ter duas URL de fluxo de trabalho:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```
## <a name="create-a-pipeline"></a>Criar uma pipeline
Adicione o código a seguir, que cria um pipeline com uma atividade de cópia e a propriedade dependsOn. Neste tutorial, o pipeline contém uma atividade: a atividade de cópia, que usa o conjunto de dados de Blob como uma origem e outro conjunto de dados de Blob como um coletor. Após a atividade de cópia ter êxito/falhar, ele chama tarefas de email diferentes.

Nesse pipeline, você deve usar os seguintes recursos:

- parâmetros
- Atividade da Web
- Dependência de atividade
- Usando a saída de uma atividade como uma entrada para a atividade subsequente

Subdividiremos o pipeline a seguir seção por seção:

```csharp

static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
        {
            Console.WriteLine("Creating pipeline " + pipelineName + "...");
            PipelineResource resource = new PipelineResource
            {
                Parameters = new Dictionary<string, ParameterSpecification>
                {
                    { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                    { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                    { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                },
                Activities = new List<Activity>
                {
                    new CopyActivity
                    {
                        Name = copyBlobActivity,
                        Inputs = new List<DatasetReference>
                        {
                            new DatasetReference
                            {
                                ReferenceName = blobSourceDatasetName
                            }
                        },
                        Outputs = new List<DatasetReference>
                        {
                            new DatasetReference
                            {
                                ReferenceName = blobSinkDatasetName
                            }
                        },
                        Source = new BlobSource { },
                        Sink = new BlobSink { }
                    },
                    new WebActivity
                    {
                        Name = sendSuccessEmailActivity,
                        Method = WebActivityMethod.POST,
                        Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                        Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                        DependsOn = new List<ActivityDependency>
                        {
                            new ActivityDependency
                            {
                                Activity = copyBlobActivity,
                                DependencyConditions = new List<String> { "Succeeded" }
                            }
                        }
                    },
                    new WebActivity
                    {
                        Name = sendFailEmailActivity,
                        Method =WebActivityMethod.POST,
                        Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                        Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                        DependsOn = new List<ActivityDependency>
                        {
                            new ActivityDependency
                            {
                                Activity = copyBlobActivity,
                                DependencyConditions = new List<String> { "Failed" }
                            }
                        }
                    }
                }
            };
            Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
            return resource;
        }
```
Adicione o código a seguir, que cria o pipeline, ao método **Main**:

```
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
```
### <a name="parameters"></a>parâmetros
A primeira seção do nosso pipeline define parâmetros. 

- sourceBlobContainer – parâmetro no pipeline consumido pelo conjunto de dados de blob de origem.
- sinkBlobContainer – parâmetro no pipeline consumido pelo conjunto de dados de blob de coletor
- destinatário – o parâmetro no pipeline consumido pelas duas atividades da Web no endereço de email que recebe o email


```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```
### <a name="web-activity"></a>Atividade da Web
A atividade da Web permite uma chamada para qualquer ponto de extremidade REST. Para obter mais informações sobre a atividade, consulte o artigo [Atividade da Web](control-flow-web-activity.md). Esse pipeline usa uma atividade de Web para chamar o fluxo de trabalho de email de Aplicativos Lógicos. Você cria duas atividades da Web: um que chama o fluxo de trabalho **CopySuccessEmail** e outro que chama o **CopyFailWorkFlow**.

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```
Na propriedade "Url", cole os pontos de extremidade da URL de solicitação de seu fluxo de trabalho de Aplicativos Lógicos adequadamente. Na propriedade "Corpo", passe uma instância da classe "EmailRequest". A solicitação de email contém as seguintes propriedades:

- Mensagem – passagem do valor de `@{activity('CopyBlobtoBlob').output.dataWritten`. Acessa uma propriedade da atividade de cópia anterior e passa o valor de dataWritten. Em caso de falha, passe a saída de erro em vez de `@{activity('CopyBlobtoBlob').error.message`.
- Nome do Data Factory – passagem do valor de `@{pipeline().DataFactory}` Essa é uma variável de sistema, permitindo que você acesse o nome do data factory correspondente. Para obter uma lista das variáveis de sistema, consulte o artigo [Variáveis de Sistema](control-flow-system-variables.md).
- Nome do Pipeline – passagem do valor de `@{pipeline().Pipeline}`. Essa também é uma variável de sistema, permitindo que você acesse o nome do pipeline correspondente. 
- Destinatário – passagem do valor de "@pipeline(). parameters.receiver"). Acessando os parâmetros de pipeline.
 
Esse código cria uma nova Dependência de Atividade, dependendo da atividade de cópia anterior substituída por ela.

## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline
Adicione o código a seguir, que **dispara uma execução de pipeline**, ao método **Main**.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Classe Main 
O método Main final deve ter esta aparência. Compile e executar o programa para disparar uma execução de pipeline!

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
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
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Executar o código
Compile e inicie o aplicativo, então verifique a execução do pipeline.
O console imprime o progresso de criação do data factory, do serviço vinculado, dos conjuntos de dados, do pipeline e da execução de pipeline. Em seguida, ele verifica o status da execução de pipeline. Aguarde até ver os detalhes de execução da atividade de cópia com o tamanho dos dados lidos/gravados. Em seguida, use ferramentas como o Gerenciador de Armazenamento do Azure para verificar se os blobs são copiados de "inputBlobPath" para "outputBlobPath" conforme você especificou nas variáveis.

**Saída de exemplo:**

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": {
      "type": "SecureString",
      "value": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
    }
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
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
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você realizou as seguintes etapas: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um serviço vinculado do Armazenamento do Azure.
> * Criar um conjunto de dados do Blob do Azure
> * Criar um pipeline que contém uma atividade de cópia e uma atividade da Web
> * Enviar saídas de atividades para as atividades subsequentes
> * Utilizar passagem de parâmetros e variáveis do sistema
> * Iniciar uma execução de pipeline
> * Monitorar as execuções de pipeline e de atividade

Agora você pode seguir para a seção Conceitos para obter mais informações sobre o Azure Data Factory.
> [!div class="nextstepaction"]
>[Pipelines e atividades](concepts-pipelines-activities.md)