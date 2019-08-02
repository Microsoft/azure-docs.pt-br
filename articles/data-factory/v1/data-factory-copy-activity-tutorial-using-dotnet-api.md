---
title: 'Tutorial: Criar um pipeline com a Atividade de Cópia usando a API do .NET | Microsoft Docs'
description: Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando a REST .NET.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 58fc4007-b46d-4c8e-a279-cb9e479b3e2b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e2f4f214523d9d42761323ec02ca6dae4c20bba6
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839421"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>Tutorial: Criar um pipeline com a Atividade de Cópia usando a API do .NET
> [!div class="op_single_selector"]
> * [Visão geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API do .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte o [tutorial de atividade de cópia](../quickstart-create-data-factory-dot-net.md). 

Neste artigo, você aprenderá a usar a [API do .NET](https://portal.azure.com) para criar um data factory com um pipeline que copia dados de um armazenamento de blobs do Azure para um Banco de Dados SQL do Azure. Se você ainda está se familiarizando com o Azure Data Factory, leia o artigo [Introdução ao Azure Data Factory](data-factory-introduction.md) antes de fazer este tutorial.   

Neste tutorial, você criará um pipeline com uma atividade: Atividade de Cópia. A atividade de cópia copia dados de um armazenamento de dados com suporte para um armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens e coletores, confira [Armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). A atividade é habilitada por um serviço globalmente disponível que pode copiar dados entre vários repositórios de dados de forma segura, confiável e escalonável. Para saber mais sobre a atividade de cópia, confira [Atividades de movimentação de dados](data-factory-data-movement-activities.md).

Um pipeline pode ter mais de uma atividade. E você pode encadear duas atividades (executar uma atividade após a outra) definindo o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Para saber mais, confira [Várias atividades em um pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

> [!NOTE] 
> Confira [Referência da API do .NET do Data Factory](/dotnet/api/index?view=azuremgmtdatafactories-4.12.1) para obter uma documentação abrangente sobre a API do .NET do Data Factory.
> 
> O pipeline de dados neste tutorial copia os dados de um armazenamento de dados de origem para um armazenamento de dados de destino. Para obter um tutorial sobre como transformar dados usando o Azure Data Factory, confira [Tutorial: Criar um pipeline para transformar dados usando um cluster Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Examine [Visão geral e pré-requisitos do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter uma visão geral do tutorial e concluir as etapas de **pré-requisitos** .
* Visual Studio 2012 ou 2013 ou 2015.
* Baixar e instalar o [SDK .NET do Azure](https://azure.microsoft.com/downloads/)
* PowerShell do Azure. Siga as instruções no artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-Az-ps) para instalar a última versão do Azure PowerShell no computador. Você pode usar o Azure PowerShell para criar um aplicativo do Azure Active Directory.

### <a name="create-an-application-in-azure-active-directory"></a>Criar um aplicativo no Azure Active Directory
Crie um aplicativo do Azure Active Directory, crie uma entidade de serviço para o aplicativo e atribua-a à função **Colaborador de Data Factory** .

1. Inicie o **PowerShell**.
2. Execute o comando a seguir e insira o nome de usuário e a senha que você usa para entrar no portal do Azure.

    ```powershell
    Connect-AzAccount
    ```
3. Execute o comando a seguir para exibir todas as assinaturas dessa conta.

    ```powershell
    Get-AzSubscription
    ```
4. Execute o comando a seguir para selecionar a assinatura com a qual deseja trabalhar. Substitua **&lt;NameOfAzureSubscription**&gt; pelo nome da sua assinatura do Azure.

    ```powershell
    Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
    ```

   > [!IMPORTANT]
   > Anote **SubscriptionId** e **TenantId** da saída desse comando.

5. Crie um grupo de recursos do Azure denominado **ADFTutorialResourceGroup** executando o comando a seguir no PowerShell.

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    Se o grupo de recursos já existe, especifique se deseja atualizá-lo (S) ou mantê-lo como (N).

    Se você utilizar um grupo de recursos diferente, precisará usar o nome do seu grupo de recursos no lugar de ADFTutorialResourceGroup neste tutorial.
6. Criar um aplicativo do Azure Active Directory.

    ```powershell
    $azureAdApplication = New-AzADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"
    ```

    Se você receber o erro a seguir, especifique uma URL diferente e execute o comando novamente.
    
    ```powershell
    Another object with the same value for property identifierUris already exists.
    ```
7. Crie a entidade de serviço do AD.

    ```powershell
    New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
8. Adicione a entidade de serviço à função de **Colaborador do Data Factory** .

    ```powershell
    New-AzRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
9. Obtenha a ID do aplicativo.

    ```powershell
    $azureAdApplication 
    ```
    Anote a ID do aplicativo (IDaplicativo) na saída.

Você deve ter quatro valores após estas etapas:

* ID do locatário
* ID da assinatura
* ID do aplicativo
* Senha (especificada no primeiro comando)

## <a name="walkthrough"></a>Passo a passo
1. Usando o Visual Studio 2012/2013/2015, crie um aplicativo de console C# .NET.
   1. Inicie o **Visual Studio** 2012/2013/2015.
   2. Clique em **Arquivo**, aponte para **Novo** e clique em **Projeto**.
   3. Expanda **Modelos** e selecione **Visual C#** . Neste passo a passo, você usa C#, mas você pode usar qualquer linguagem .NET.
   4. Selecione **Aplicativo de console** na lista de tipos de projetos à direita.
   5. Digite **DataFactoryAPITestApp** como o Nome.
   6. Selecione **C:\ADFGetStarted** como o Local.
   7. Clique em **OK** para criar o projeto.
2. Clique em **Ferramentas**, aponte para **Gerenciador de Pacotes NuGet** e clique em **Console do Gerenciador de Pacotes**.
3. No **Console do Gerenciador de Pacotes**, realize as seguintes etapas:
   1. Execute o seguinte comando para instalar o pacote de Data Factory: `Install-Package Microsoft.Azure.Management.DataFactories`
   2. Execute o seguinte comando para instalar o pacote do Azure Active Directory (use a API do Active Directory no código): `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. Adicione a seção **appSetttings** a seguir ao arquivo **App.config**. Essas configurações são usadas pelo método auxiliar: **GetAuthorizationHeader**.

    Substitua os valores de **&lt;ID do aplicativo&gt;** , **&lt;Senha&gt;** , **&lt;ID da assinatura&gt;** e **&lt;ID do locatário&gt;** pelos seus próprios valores.

    ```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

            <add key="ApplicationId" value="your application ID" />
            <add key="Password" value="Password you used while creating the AAD application" />
            <add key="SubscriptionId" value= "Subscription ID" />
            <add key="ActiveDirectoryTenantId" value="Tenant ID" />
        </appSettings>
    </configuration>
    ```

5. Adicione as seguintes declarações **using** ao arquivo de origem (Program.cs) no projeto.

    ```csharp
    using System.Configuration;
    using System.Collections.ObjectModel;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure;
    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;

    ```

6. Adicione o seguinte código que cria uma instância de classe **DataPipelineManagementClient** ao método **Main**. Você usa esse objeto para criar um data factory, um serviço vinculado, conjunto de dados de entrada e de saída e um pipeline. Você também usa esse objeto para monitorar as partes de um conjunto de dados em tempo de execução.

    ```csharp
    // create data factory management client
    string resourceGroupName = "ADFTutorialResourceGroup";
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > Substitua o valor de **resourceGroupName** pelo nome do seu grupo de recursos do Azure.
   >
   > O nome de atualização do data factory (NomedataFactory) deve ser exclusivo. O nome de data factory deve ser exclusivo. Veja o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.

7. Adicione o seguinte código que cria um **data factory** no método **Main**.

    ```csharp
    // create a data factory
    Console.WriteLine("Creating a data factory");
    client.DataFactories.CreateOrUpdate(resourceGroupName,
        new DataFactoryCreateOrUpdateParameters()
        {
            DataFactory = new DataFactory()
            {
                Name = dataFactoryName,
                Location = "westus",
                Properties = new DataFactoryProperties()
            }
        }
    );
    ```

    Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline em um data factory pode ter uma ou mais atividades. Por exemplo, uma Atividade de Cópia para copiar dados de um armazenamento de dados de origem para um de destino e uma atividade do Hive do HDInsight para executar um script do Hive para transformar os dados de entrada em dados de saída do produto. Vamos começar com a criação do data factory nesta etapa.
8. Adicione o seguinte código, que cria um **serviço vinculado do Armazenamento do Azure** para o método **Main**.

   > [!IMPORTANT]
   > Substitua **storageaccountname** e **accountkey** pelo nome e pela chave da sua conta de Armazenamento do Azure.

    ```csharp
    // create a linked service for input data store: Azure Storage
    Console.WriteLine("Creating Azure Storage linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureStorageLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                )
            }
        }
    );
    ```

    Os serviços vinculados são criados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Neste tutorial, você não usa serviços de computação, como o Azure HDInsight ou o Azure Data Lake Analytics. Você usa dois armazenamentos de dados do tipo Armazenamento do Azure (origem) e o banco de dados SQL do Azure (destino). 

    Portanto, você criará dois serviços vinculados chamados AzureStorageLinkedService e AzureSqlLinkedService dos tipos: AzureStorage e AzureSqlDatabase.  

    O AzureStorageLinkedService vincula sua conta do armazenamento do Azure ao data factory. Essa conta de armazenamento é aquela na qual você criou um contêiner e carregou os dados como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
9. Adicione o seguinte código que cria um **serviço vinculado SQL do Azure** no método **Main**.

   > [!IMPORTANT]
   > Substitua **servername**, **databasename**, **username** e **password** pelos nomes de seu servidor SQL do Azure, banco de dados, usuário e senha.

    ```csharp
    // create a linked service for output data store: Azure SQL Database
    Console.WriteLine("Creating Azure SQL Database linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureSqlLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                )
            }
        }
    );
    ```

    O AzureSqlLinkedService vincula seu banco de dados SQL do Azure ao data factory. Os dados copiados do armazenamento de blobs são armazenados no banco de dados. Você criou a tabela emp no banco de dados como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
10. Adicione o seguinte código que cria **conjuntos de dados de entrada e de saída** para o método **Main**.

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "InputDataset";
    string Dataset_Destination = "OutputDataset";

    Console.WriteLine("Creating input dataset of type: Azure Blob");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                Structure = new List<DataElement>()
                {
                    new DataElement() { Name = "FirstName", Type = "String" },
                    new DataElement() { Name = "LastName", Type = "String" }
                },
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/",
                    FileName = "emp.txt"
                },
                External = true,
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },

                Policy = new Policy()
                {
                    Validation = new ValidationPolicy()
                    {
                        MinimumRows = 1
                    }
                }
            }
        }
    });

    Console.WriteLine("Creating output dataset of type: Azure SQL");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new DatasetCreateOrUpdateParameters()
        {
            Dataset = new Dataset()
            {
                Name = Dataset_Destination,
                Properties = new DatasetProperties()
                {
                    Structure = new List<DataElement>()
                    {
                        new DataElement() { Name = "FirstName", Type = "String" },
                        new DataElement() { Name = "LastName", Type = "String" }
                    },
                    LinkedServiceName = "AzureSqlLinkedService",
                    TypeProperties = new AzureSqlTableDataset()
                    {
                        TableName = "emp"
                    },
                    Availability = new Availability()
                    {
                        Frequency = SchedulePeriod.Hour,
                        Interval = 1,
                    },
                }
            }
        });
    ```
    
    Na etapa anterior, você criou serviços vinculados para vincular a conta do Armazenamento do Azure e um banco de dados SQL do Azure ao data factory. Nesta etapa, você define dois conjuntos de dados, chamados InputDataset e OutputDataset, que representam os dados de entrada e saída armazenados nos repositórios de dados referidos por AzureStorageLinkedService e AzureSqlLinkedService, respectivamente.

    O serviço vinculado do Armazenamento do Azure especifica a cadeia de conexão que o serviço Data Factory usa no tempo de execução para se conectar à sua conta do Armazenamento do Azure. E o conjunto de dados de blob de entrada (InputDataset) especifica o contêiner e a pasta que contém os dados.  

    Da mesma forma, o serviço vinculado do Banco de Dados SQL especifica a cadeia de conexão que o serviço Data Factory usa no tempo de execução para se conectar ao seu Banco de Dados SQL do Azure. Além disso, o conjunto de dados da tabela SQL de saída (OutputDataset) especifica a tabela no banco de dados na qual os dados do armazenamento de blobs são copiados.

    Nesta etapa, você criará um conjunto de dados denominado InputDataset que aponta para um arquivo de blob (emp.txt) na pasta raiz de um contêiner de blob (adftutorial) no Armazenamento do Azure representado pelo serviço vinculado AzureStorageLinkedService. Se você não especificar um valor para fileName (ou ignorá-lo), os dados de todos os blobs na pasta de entrada serão copiados no destino. Neste tutorial, você deve especificar um valor para o fileName.    

    Nesta etapa, você cria um conjunto de dados de saída denominado **OutputDataset**. Esse conjunto de dados aponta para uma tabela SQL no banco de dados SQL do Azure representada por **AzureSqlLinkedService**.
11. Adicione o código a seguir, que **cria e ativa um pipeline** no método **Main**. Nesta etapa, você cria um pipeline com uma **atividade de cópia** que usa **InputDataset** como entrada e **OutputDataset** como saída.

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2017, 5, 11, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = new DateTime(2017, 5, 12, 0, 0, 0, 0, DateTimeKind.Utc);
    string PipelineName = "ADFTutorialPipeline";

    client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new PipelineCreateOrUpdateParameters()
        {
            Pipeline = new Pipeline()
            {
                Name = PipelineName,
                Properties = new PipelineProperties()
                {
                    Description = "Demo Pipeline for data transfer between blobs",

                    // Initial value for pipeline's active period. With this, you won't need to set slice status
                    Start = PipelineActivePeriodStartTime,
                    End = PipelineActivePeriodEndTime,

                    Activities = new List<Activity>()
                    {
                        new Activity()
                        {
                            Name = "BlobToAzureSql",
                            Inputs = new List<ActivityInput>()
                            {
                                new ActivityInput() {
                                    Name = Dataset_Source
                                }
                            },
                            Outputs = new List<ActivityOutput>()
                            {
                                new ActivityOutput()
                                {
                                    Name = Dataset_Destination
                                }
                            },
                            TypeProperties = new CopyActivity()
                            {
                                Source = new BlobSource(),
                                Sink = new BlobSink()
                                {
                                    WriteBatchSize = 10000,
                                    WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                }
                            }
                        }
                    }
                }
            }
        });
    ```

    Observe os seguintes pontos:
   
    - Na seção de atividades, há apenas uma atividade cujo **tipo** é definido como **Copy**. Para saber mais sobre a atividade de cópia, confira [Atividades de movimentação de dados](data-factory-data-movement-activities.md). Nas soluções de Data Factory, você também pode usar [atividades de transformação de dados](data-factory-data-transformation-activities.md).
    - A entrada da atividade é definida como **InputDataset** e a saída da atividade é definida como **OutputDataset**. 
    - Na seção **typeProperties**, **BlobSource** é especificado como o tipo de origem e **SqlSink** é especificado como o tipo de coletor. Para ver a lista completa dos armazenamentos de dados com suporte pela atividade de cópia, como origens e coletores, confira [Armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Para aprender a usar um armazenamento de dados com suporte específico como fonte/coletor, clique no link na tabela.  
   
    Atualmente, o conjunto de dados de saída é o que conduz o agendamento. Neste tutorial, o conjunto de dados de saída está configurado para produzir uma fatia uma vez a cada hora. O pipeline tem uma hora de início e uma hora de término com um dia de diferença, o que significa 24 horas. Portanto, 24 fatias de conjunto de dados de saída são produzidas pelo pipeline.
12. Adicione o seguinte código ao método **Principal** para obter o status de uma fatia de dados do conjunto de dados de saída. Há apenas uma fatia esperada neste exemplo.

    ```csharp
    // Pulling status within a timeout threshold
    DateTime start = DateTime.Now;
    bool done = false;

    while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
    {
        Console.WriteLine("Pulling the slice status");        
        // wait before the next status check
        Thread.Sleep(1000 * 12);

        var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
            new DataSliceListParameters()
            {
                DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
            });

        foreach (DataSlice slice in datalistResponse.DataSlices)
        {
            if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
            {
                Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                done = true;
                break;
            }
            else
            {
                Console.WriteLine("Slice status is: {0}", slice.State);
            }
        }
    }
    ```

13. Adicione o seguinte código para obter detalhes de execução de uma fatia de dados ao método **Principal** .

    ```csharp
    Console.WriteLine("Getting run details of a data slice");

    // give it a few minutes for the output slice to be ready
    Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
    Console.ReadKey();

    var datasliceRunListResponse = client.DataSliceRuns.List(
            resourceGroupName,
            dataFactoryName,
            Dataset_Destination,
            new DataSliceRunListParameters()
            {
                DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
            }
        );

    foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
    {
        Console.WriteLine("Status: \t\t{0}", run.Status);
        Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
        Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
        Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
        Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
        Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
        Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
    }

    Console.WriteLine("\nPress any key to exit.");
    Console.ReadKey();
    ```

14. Adicione o método auxiliar a seguir usado pelo método **Main** na classe **Program**.

    > [!NOTE] 
    > Ao copiar e colar o código a seguir, certifique-se de que o código copiado esteja no mesmo nível do método principal.

    ```csharp
    public static async Task<string> GetAuthorizationHeader()
    {
        AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        ClientCredential credential = new ClientCredential(
            ConfigurationManager.AppSettings["ApplicationId"],
            ConfigurationManager.AppSettings["Password"]);
        AuthenticationResult result = await context.AcquireTokenAsync(
            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
            clientCredential: credential);

        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```

15. No Gerenciador de Soluções, expanda o projeto (DataFactoryAPITestApp), clique com botão direito em **Referências**e clique em **Adicionar Referência**. Marque a caixa de seleção do assembly **System. Configuration**. E clique em **OK**.
16. Compile o aplicativo de console. Clique no menu **Compilar** e clique em **Solução de Compilação**.
17. Confirme se há pelo menos um arquivo no contêiner **adftutorial** no seu armazenamento de BLOBs do Azure. Caso contrário, crie o arquivo **Emp.txt** no bloco de notas com o seguinte conteúdo e carregue-o no contêiner adftutorial.

    ```
    John, Doe
    Jane, Doe
    ```
18. Execute o exemplo, clicando em **Depurar** -> **Iniciar Depuração** no menu. Quando você vir **Obter detalhes de execução de uma fatia de dados**, aguarde alguns minutos e pressione **ENTER**.
19. Use o Portal do Azure para verificar se a data factory **APITutorialFactory** foi criada com os seguintes artefatos:
    * Serviço vinculado: **LinkedService_AzureStorage**
    * Conjunto de dados: **InputDataset** e **OutputDataset**.
    * Pipeline: **PipelineBlobSample**
20. Verifique se os dois registros de funcionários são criados na tabela **emp** do banco de dados SQL do Azure especificado.

## <a name="next-steps"></a>Próximas etapas
Confira [Referência da API do .NET do Data Factory](/dotnet/api/index?view=azuremgmtdatafactories-4.12.1) para obter uma documentação abrangente sobre a API do .NET do Data Factory.

Neste tutorial, você usou o armazenamento de blobs do Azure como um armazenamento de dados de origem e um banco de dados SQL do Azure como um armazenamento de dados de destino em uma operação de cópia. A tabela a seguir fornece uma lista de armazenamentos de dados com suporte como origens ou destinos na atividade de cópia: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Para saber mais sobre como copiar dados de/para um armazenamento de dados, clique no link para o armazenamento de dados na tabela.

