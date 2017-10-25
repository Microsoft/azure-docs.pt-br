---
title: Criar pipelines de dados usando o SDK .NET | Microsoft Docs
description: Aprenda como criar, monitorar e gerenciar as data factories do Azure programaticamente usando o SDK da Data Factory.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: b0a357be-3040-4789-831e-0d0a32a0bda5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: d51039881673189697f1826d474546f24ffd5479
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-monitor-and-manage-azure-data-factories-using-azure-data-factory-net-sdk"></a>Criar, monitorar e gerenciar data factories do Azure usando o SDK do .NET do Azure Data Factory
## <a name="overview"></a>Visão geral
Você pode criar, monitorar e gerenciar as Data Factory do Azure programaticamente usando o SDK do .NET da Data Factory. Este artigo contém uma explicação passo a passo que você pode seguir para criar um aplicativo de console .NET de exemplo que cria e monitora uma Data Factory. 

> [!NOTE]
> Este artigo não abrange toda a API .NET de Data Factory. Consulte [Referência da API do .NET do Data Factory](/dotnet/api/index?view=azuremgmtdatafactories-4.12.1) para obter uma documentação abrangente sobre a API do .NET do de Data Factory. 

## <a name="prerequisites"></a>Pré-requisitos
* Visual Studio 2012 ou 2013 ou 2015.
* Baixe e instale o [Azure .NET SDK](http://azure.microsoft.com/downloads/).
* PowerShell do Azure. Siga as instruções no artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para instalar a última versão do Azure PowerShell no computador. Você pode usar o Azure PowerShell para criar um aplicativo do Azure Active Directory.

### <a name="create-an-application-in-azure-active-directory"></a>Criar um aplicativo no Azure Active Directory
Crie um aplicativo do Azure Active Directory, crie uma entidade de serviço para o aplicativo e atribua-a à função **Colaborador de Data Factory** .

1. Inicie o **PowerShell**.
2. Execute o comando a seguir e insira o nome de usuário e a senha que você usa para entrar no portal do Azure.

    ```PowerShell
    Login-AzureRmAccount
    ```
3. Execute o comando a seguir para exibir todas as assinaturas dessa conta.

    ```PowerShell
    Get-AzureRmSubscription
    ```
4. Execute o comando a seguir para selecionar a assinatura com a qual deseja trabalhar. Substitua **&lt;NameOfAzureSubscription**&gt; pelo nome da sua assinatura do Azure.

    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    ```

   > [!IMPORTANT]
   > Anote **SubscriptionId** e **TenantId** da saída desse comando.

5. Crie um grupo de recursos do Azure denominado **ADFTutorialResourceGroup** executando o comando a seguir no PowerShell.

    ```PowerShell
    New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    Se o grupo de recursos já existe, especifique se deseja atualizá-lo (S) ou mantê-lo como (N).

    Se você utilizar um grupo de recursos diferente, precisará usar o nome do seu grupo de recursos no lugar de ADFTutorialResourceGroup neste tutorial.
6. Criar um aplicativo do Azure Active Directory.

    ```PowerShell
    $azureAdApplication = New-AzureRmADApplication -DisplayName "ADFDotNetWalkthroughApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfdotnetwalkthroughapp.org/example" -Password "Pass@word1"
    ```

    Se você receber o erro a seguir, especifique uma URL diferente e execute o comando novamente.
    
    ```PowerShell
    Another object with the same value for property identifierUris already exists.
    ```
7. Crie a entidade de serviço do AD.

    ```PowerShell
    New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
8. Adicione a entidade de serviço à função de **Colaborador do Data Factory** .

    ```PowerShell
    New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
9. Obtenha a ID do aplicativo.

    ```PowerShell
    $azureAdApplication 
    ```
    Anote a ID do aplicativo (IDaplicativo) na saída.

Você deve ter quatro valores após estas etapas:

* ID do locatário
* ID da assinatura
* ID do aplicativo
* Senha (especificada no primeiro comando)

## <a name="walkthrough"></a>Passo a passo
No passo a passo, você cria um data factory com um pipeline que contém uma atividade de cópia. A atividade de cópia copia dados de uma pasta no seu armazenamento de blobs do Azure para outra pasta no mesmo armazenamento de blobs. 

A atividade de cópia realiza a movimentação de dados no Azure Data Factory. A atividade é habilitada por um serviço globalmente disponível que pode copiar dados entre vários repositórios de dados de forma segura, confiável e escalonável. Veja o artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia.

1. Usando o Visual Studio 2012/2013/2015, crie um aplicativo de console C# .NET.
   1. Inicie o **Visual Studio** 2012/2013/2015.
   2. Clique em **Arquivo**, aponte para **Novo** e clique em **Projeto**.
   3. Expanda **Modelos** e selecione **Visual C#**. Neste passo a passo, você usa C#, mas você pode usar qualquer linguagem .NET.
   4. Selecione **Aplicativo de console** na lista de tipos de projetos à direita.
   5. Digite **DataFactoryAPITestApp** como o Nome.
   6. Selecione **C:\ADFGetStarted** como o Local.
   7. Clique em **OK** para criar o projeto.
2. Clique em **Ferramentas**, aponte para **Gerenciador de Pacotes NuGet** e clique em **Console do Gerenciador de Pacotes**.
3. No **Console do Gerenciador de Pacotes**, realize as seguintes etapas:
   1. Execute o seguinte comando para instalar o pacote de Data Factory: `Install-Package Microsoft.Azure.Management.DataFactories`
   2. Execute o seguinte comando para instalar o pacote do Azure Active Directory (use a API do Active Directory no código): `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. Substitua o conteúdo do arquivo **App.config** no projeto pelo seguinte conteúdo: 
    
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
5. No arquivo App.Config, atualize os valores para **&lt;ID do aplicativo&gt;**, **&lt;Senha&gt;**, **&lt;ID da assinatura&gt;** e **&lt;ID do locatário&gt;** com os seus próprios valores.
6. Adicione as seguintes instruções **using** ao arquivo **Program.cs** no projeto.

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

    //IMPORTANT: specify the name of Azure resource group here
    string resourceGroupName = "ADFTutorialResourceGroup";

    //IMPORTANT: the name of the data factory must be globally unique.
    // Therefore, update this value. For example:APITutorialFactory05122017
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > Substitua o valor de **resourceGroupName** pelo nome do seu grupo de recursos do Azure. Você pode criar um grupo de recursos usando o cmdlet [New-AzureResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) .
   >
   > O nome de atualização do data factory (NomedataFactory) deve ser exclusivo. O nome de data factory deve ser exclusivo. Consulte o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos de Data Factory.
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
9. Adicione o seguinte código que cria **conjuntos de dados de entrada e de saída** para o método **Main**.

    O **FolderPath** para o blob de entrada é definido como **adftutorial/** em que **adftutoria**l é o nome do contêiner no seu armazenamento de blobs. Se esse contêiner não existir em seu armazenamento de blob do Azure, crie um contêiner com o nome **adftutorial** e carregue um arquivo de texto no contêiner.

    O FolderPath para o blob de saída é definido como: **adftutorial/apifactoryoutput /{Slice}**, em que a **Slice** é dinamicamente calculada com base no valor de **SliceStart** (data e hora de início de cada fatia).

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "DatasetBlobSource";
    string Dataset_Destination = "DatasetBlobDestination";
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
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
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Destination,
            Properties = new DatasetProperties()
            {
    
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                    PartitionedBy = new Collection<Partition>()
                    {
                        new Partition()
                        {
                            Name = "Slice",
                            Value = new DateTimePartitionValue()
                            {
                                Date = "SliceStart",
                                Format = "yyyyMMdd-HH"
                            }
                        }
                    }
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
10. Adicione o código a seguir, que **cria e ativa um pipeline** no método **Main**. Essa pipeline tem uma **CopyActivity** que usa **BlobSource** como fonte e **BlobSink** como coletor.

    A atividade de cópia realiza a movimentação de dados no Azure Data Factory. A atividade é habilitada por um serviço globalmente disponível que pode copiar dados entre vários repositórios de dados de forma segura, confiável e escalonável. Veja o artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia.

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
    string PipelineName = "PipelineBlobSample";
    
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
                        Name = "BlobToBlob",
                        Inputs = new List<ActivityInput>()
                        {
                            new ActivityInput()
                {
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
    
                },
            }
        }
    });
    ```
12. Adicione o seguinte código ao método **Principal** para obter o status de uma fatia de dados do conjunto de dados de saída. Apenas uma fatia é esperada neste exemplo.

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
13. **(opcional)** Adicione o seguinte código para obter detalhes de execução de uma fatia de dados ao método **Principal**.

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
        });
    
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
14. Adicione o método auxiliar a seguir usado pelo método **Main** na classe **Program**. Esse método exibe uma caixa de diálogo que permite fornecer o **nome de usuário** e a **senha** que você usa para fazer logon no portal do Azure.

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

15. No Gerenciador de Soluções, expanda o projeto: **DataFactoryAPITestApp**, clique com botão direito do mouse em **Referências** e clique em **Adicionar Referência**. Selecione caixa de seleção para o assembly `System.Configuration` e clique em **OK**.
15. Compile o aplicativo de console. Clique no menu **Compilar** e clique em **Solução de Compilação**.
16. Confirme se há pelo menos um arquivo no contêiner adftutorial no seu armazenamento de BLOBs do Azure. Caso contrário, crie o arquivo de Emp.txt no bloco de notas com o seguinte conteúdo e carregue-o no contêiner adftutorial.

    ```
    John, Doe
    Jane, Doe
    ```
17. Execute o exemplo, clicando em **Depurar** -> **Iniciar Depuração** no menu. Quando você vir **Obter detalhes de execução de uma fatia de dados**, aguarde alguns minutos e pressione **ENTER**.
18. Use o Portal do Azure para verificar se a data factory **APITutorialFactory** foi criada com os seguintes artefatos:
    * Serviço vinculado: **AzureStorageLinkedService**
    * Conjunto de dados: **DatasetBlobSource** e **DatasetBlobDestination**.
    * Pipeline: **PipelineBlobSample**
19. Verifique se um arquivo de saída foi criado na pasta **apifactoryoutput** no contêiner **adftutorial**.

## <a name="get-a-list-of-failed-data-slices"></a>Obter uma lista de fatias de dados com falha 

```csharp
// Parse the resource path
var ResourceGroupName = "ADFTutorialResourceGroup";
var DataFactoryName = "DataFactoryAPITestApp";

var parameters = new ActivityWindowsByDataFactoryListParameters(ResourceGroupName, DataFactoryName);
parameters.WindowState = "Failed";
var response = dataFactoryManagementClient.ActivityWindows.List(parameters);
do
{
    foreach (var activityWindow in response.ActivityWindowListResponseValue.ActivityWindows)
    {
        var row = string.Join(
            "\t",
            activityWindow.WindowStart.ToString(),
            activityWindow.WindowEnd.ToString(),
            activityWindow.RunStart.ToString(),
            activityWindow.RunEnd.ToString(),
            activityWindow.DataFactoryName,
            activityWindow.PipelineName,
            activityWindow.ActivityName,
            string.Join(",", activityWindow.OutputDatasets));
        Console.WriteLine(row);
    }

    if (response.NextLink != null)
    {
        response = dataFactoryManagementClient.ActivityWindows.ListNext(response.NextLink, parameters);
    }
    else
    {
        response = null;
    }
}
while (response != null);
```

## <a name="next-steps"></a>Próximas etapas
Consulte o exemplo a seguir para criar um pipeline usando o SDK do .NET que copia dados de um Armazenamento de Blobs do Azure para um banco de dados SQL do Azure: 

- [Criar um pipeline para copiar dados do Armazenamento de Blobs para o Banco de Dados SQL](data-factory-copy-activity-tutorial-using-dotnet-api.md)
