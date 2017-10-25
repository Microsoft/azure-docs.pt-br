---
title: SDK do .NET de Gerenciamento v1.x para Stream Analytics do Azure | Microsoft Docs
description: "Comece com o SDK .NET do Azure Stream Analytics Management. Saiba como configurar e executar trabalhos de análise. Criar um projeto, entradas, saídas e transformações."
keywords: ".NET SDK, API de análise"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 5e93de87-0c6f-4f4b-be98-08d63f832897
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/06/2017
ms.author: samacha
ms.openlocfilehash: cd8f4e0dd07be8878d6bbb1739fb84a3cc96a186
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="management-net-sdk-v1x-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>SDK do .NET de Gerenciamento v1.x: configurar e executar trabalhos de análise usando a API do Stream Analytics do Azure para .NET
Saiba como configurar e executar trabalhos de análise usando a API do Stream Analytics para .NET usando o SDK do .NET de Gerenciamento. Configurar um projeto, criar fontes de entrada e saídas, transformações e iniciar e parar trabalhos. Seus trabalhos de análise, você pode transmitir dados de armazenamento de Blob ou de um hub de eventos.

Consulte a [Documentação de referência de gerenciamento da API do Stream Analytics para .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

O Azure Stream Analytics é um serviço completamente gerenciado que oferece baixa latência, alta disponibilidade e processamento escalonável de eventos complexos por streaming de dados na nuvem. O Stream Analytics permite que os clientes configurem trabalhos de streaming para analisar fluxos de dados e realizem análise quase em tempo real.  

> [!NOTE]
> Código de exemplo neste artigo ainda usa a versão herdada (1.x) do SDK .NET de Gerenciamento do Stream Analytics do Azure. Para o código de exemplo usando a versão atualizada do SDK, consulte [Usar o SDK do .NET de Gerenciamento para Stream Analytics](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-dotnet-management-sdk).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter o seguinte:

* Instale o Visual Studio 2017 ou 2015.
* Baixe e instale o [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Crie um grupo de recursos do Azure em sua assinatura. O seguinte é um exemplo de script do PowerShell do Azure. Para obter mais informações sobre o PowerShell do Azure, consulte [Instalar e configurar o PowerShell do Azure](/powershell/azure/overview).  

        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group
        Select-AzureSubscription -SubscriptionName <subscription name>

            # If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
            #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


* Configure uma origem de entrada e o destino de saída para usar. Para obter mais instruções, consulte [Adicionar Entradas](stream-analytics-add-inputs.md) para configurar uma entrada de exemplo e [Adicionar Saídas](stream-analytics-add-outputs.md) para configurar uma saída de exemplo.

## <a name="set-up-a-project"></a>Configurar um projeto
Para criar um trabalho de análise, use a API do Stream Analytics para .NET. Primeiro, configure seu projeto.

1. Crie um aplicativo de console do Visual Studio C# .NET.
2. No Console do Gerenciador de pacotes, execute os seguintes comandos para instalar os pacotes do NuGet. O primeiro é o SDK .NET do Azure Stream Analytics Management. O segundo é o cliente do Active Directory do Azure que será usado para autenticação.
   
        Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 1.8.3
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.4
3. Adicione a seguinte seção **appSettings** ao arquivo App.config.
   
        <appSettings>
          <!--CSM Prod related values-->
          <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
          <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
          <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
          <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
          <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
        </appSettings>

    Substitua os valores por **SubscriptionId** e **ActiveDirectoryTenantId** por suas IDs de assinatura e locatário do Azure. Você pode obter esses valores executando o seguinte cmdlet do PowerShell do Azure:

        Get-AzureAccount

4. Adicione a seguinte referência em seu arquivo .csproj:

        <Reference Include="System.Configuration" />

1. Adicione as seguintes declarações **using** ao arquivo de origem (Program.cs) no projeto.
   
        using System;
        using System.Configuration;
        using System.Threading.Tasks;
        
        using Microsoft.Azure;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
2. Adicione um método auxiliar de autenticação:

   ```   
   private static async Task<string> GetAuthorizationHeader()
   {
       var context = new AuthenticationContext(
           ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
           ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

        AuthenticationResult result = await context.AcquireTokenASync(
           resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
           clientId: ConfigurationManager.AppSettings["AsaClientId"],
           redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
           promptBehavior: PromptBehavior.Always);

        if (result != null)
            return result.AccessToken;

       throw new InvalidOperationException("Failed to acquire token");
   }
   ```  

## <a name="create-a-stream-analytics-management-client"></a>Crie um cliente de gerenciamento do Stream Analytics
Um objeto **StreamAnalyticsManagementClient** permite que você gerencie o trabalho e os componentes de trabalho, como entrada, saída e transformação.

Adicione o seguinte código ao início do método **Main** :

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
    string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
    string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
    string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
        ConfigurationManager.AppSettings["SubscriptionId"],
        GetAuthorizationHeader().Result);

    // Create Stream Analytics management client
    StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

O valor da variável **resourceGroupName** deve ser igual ao nome do grupo de recursos que você criou ou escolheu nas etapas de pré-requisito.

Para automatizar o aspecto de apresentação de credencial da criação do trabalho, consulte [Autenticação de uma entidade de serviço com o Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-authenticate-service-principal.md).

As seções restantes deste artigo pressupõem que esse código esteja no início do método **Main** .

## <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics
O código a seguir cria um trabalho do Stream Analytics sob o grupo de recursos que você definiu. Você adicionará entrada, saída e transformação ao trabalho mais tarde.

    // Create a Stream Analytics job
    JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
    {
        Job = new Job()
        {
            Name = streamAnalyticsJobName,
            Location = "<LOCATION>",
            Properties = new JobProperties()
            {
                EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
                Sku = new Sku()
                {
                    Name = "Standard"
                }
            }
        }
    };

    JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## <a name="create-a-stream-analytics-input-source"></a>Criar uma fonte de entrada do Stream Analytics
O código a seguir cria uma fonte de entrada do Stream Analytics com o tipo de fonte de entrada de blob e serialização de CSV. Para criar uma fonte de entrada de hub de eventos, use **EventHubStreamInputDataSource** em vez de **BlobStreamInputDataSource**. Da mesma forma, você pode personalizar o tipo de serialização da fonte de entrada.

    // Create a Stream Analytics input source
    InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
    {
        Input = new Input()
        {
            Name = streamAnalyticsInputName,
            Properties = new StreamInputProperties()
            {
                Serialization = new CsvSerialization
                {
                    Properties = new CsvSerializationProperties
                    {
                        Encoding = "UTF8",
                        FieldDelimiter = ","
                    }
                },
                DataSource = new BlobStreamInputDataSource
                {
                    Properties = new BlobStreamInputDataSourceProperties
                    {
                        StorageAccounts = new StorageAccount[]
                        {
                            new StorageAccount()
                            {
                                AccountName = "<YOUR STORAGE ACCOUNT NAME>",
                                AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
                            }
                        },
                        Container = "samples",
                        PathPattern = ""
                    }
                }
            }
        }
    };

    InputCreateOrUpdateResponse inputCreateResponse =
        client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

As fontes de entrada de armazenamento de Blob ou de um hub de eventos estão ligadas a um trabalho específico. Para usar a mesma fonte de entrada para trabalhos diferentes, chame o método novamente e especifique um nome de trabalho diferente.

## <a name="test-a-stream-analytics-input-source"></a>Testar uma fonte de entrada do Stream Analytics
O método **TestConnection** testa se o trabalho do Stream Analytics é capaz de se conectar à fonte de entrada, bem como outros aspectos específicos para o tipo de fonte de entrada. Por exemplo, em que na fonte de entrada de blob criada em uma etapa anterior, o método verificará se o nome da conta de armazenamento e o par de chaves podem ser usados para conectar-se à conta de armazenamento, bem como verificará se o contêiner especificado existe.

    // Test input source connection
    DataSourceTestConnectionResponse inputTestResponse =
        client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## <a name="create-a-stream-analytics-output-target"></a>Criar um destino de saída do Stream Analytics
Criar um destino de saída é muito semelhante a criar uma fonte de entrada do Stream Analytics. Como as fontes de entrada, os destinos de saída são vinculados a trabalho específico. Para usar o mesmo destino de saída para diferentes trabalhos, chame o método novamente e especifique um nome de trabalho diferente.

O código a seguir cria um destino de saída (Banco de Dados SQL do Azure). Você pode personalizar o tipo de dados de destino de saída e/ou o tipo de serialização.

    // Create a Stream Analytics output target
    OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
    {
        Output = new Output()
        {
            Name = streamAnalyticsOutputName,
            Properties = new OutputProperties()
            {
                DataSource = new SqlAzureOutputDataSource()
                {
                    Properties = new SqlAzureOutputDataSourceProperties()
                    {
                        Server = "<YOUR DATABASE SERVER NAME>",
                        Database = "<YOUR DATABASE NAME>",
                        User = "<YOUR DATABASE LOGIN>",
                        Password = "<YOUR DATABASE LOGIN PASSWORD>",
                        Table = "<YOUR DATABASE TABLE NAME>"
                    }
                }
            }
        }
    };

    OutputCreateOrUpdateResponse outputCreateResponse =
        client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## <a name="test-a-stream-analytics-output-target"></a>Testar um destino de saída do Stream Analytics
Um destino de saída do Stream Analytics também tem um método **TestConnection** para conexões de teste.

    // Test output target connection
    DataSourceTestConnectionResponse outputTestResponse =
        client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## <a name="create-a-stream-analytics-transformation"></a>Criar uma transformação do Stream Analytics
O código a seguir cria uma transformação do Stream Analytics com a consulta "selecionar * da entrada" e especifica para alocar uma unidade de streaming para o trabalho do Stream Analytics. Para obter mais informações sobre o ajuste de unidades de streaming, consulte Dimensionar trabalhos do [Escalonar trabalhos de Análise de fluxo do Azure](stream-analytics-scale-jobs.md).

    // Create a Stream Analytics transformation
    TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
    {
        Transformation = new Transformation()
        {
            Name = streamAnalyticsTransformationName,
            Properties = new TransformationProperties()
            {
                StreamingUnits = 1,
                Query = "select * from Input"
            }
        }
    };

    var transformationCreateResp =
        client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

Como entrada e saída, uma transformação também está vinculada ao trabalho de Stream Analytics específico sob o qual foi criada.

## <a name="start-a-stream-analytics-job"></a>Iniciar um trabalho do Stream Analytics
Depois de criar um trabalho do Stream Analytics e suas entradas, saídas e transformação, você pode iniciar o trabalho chamando o método **Iniciar** .

O código de exemplo a seguir inicia um trabalho do Stream Analytics com uma hora de início de saída personalizada definida para 12 de dezembro de 2012, 12h12min12 UTC:

    // Start a Stream Analytics job
    JobStartParameters jobStartParameters = new JobStartParameters
    {
        OutputStartMode = OutputStartMode.CustomTime,
        OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
    };

    LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);

## <a name="stop-a-stream-analytics-job"></a>Interromper um trabalho do Stream Analytics
Você pode interromper um trabalho do Stream Analytics em execução chamando o método **Parar** .

    // Stop a Stream Analytics job
    LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## <a name="delete-a-stream-analytics-job"></a>Excluir um trabalho do Stream Analytics
O método **Excluir** excluirá o trabalho, bem como os sub-recursos subjacentes, incluindo entradas, saídas e transformação do trabalho.

    // Delete a Stream Analytics job
    LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);

## <a name="get-support"></a>Obtenha suporte
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
Você aprendeu as noções básicas do uso de um SDK do .NET para criar e executar trabalhos de análise. Para saber mais, consulte os seguintes:

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [SDK .NET do Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn889315.aspx)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
