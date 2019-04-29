---
title: SDK do .NET de Gerenciamento para Azure Stream Analytics
description: Comece com o SDK .NET do Azure Stream Analytics Management. Saiba como configurar e executar trabalhos de análise. Criar um projeto, entradas, saídas e transformações.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 4fa4a9a8d01d499dc431c8b182401226aa72bf1f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479993"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Gerenciamento de SDK do .NET: Configurar e executar trabalhos de análise usando API do Azure Stream Analytics para .NET
Saiba como configurar e executar trabalhos de análise usando a API do Stream Analytics para .NET usando o SDK do .NET de Gerenciamento. Configurar um projeto, criar fontes de entrada e saídas, transformações e iniciar e parar trabalhos. Seus trabalhos de análise, você pode transmitir dados de armazenamento de Blob ou de um hub de eventos.

Consulte a [Documentação de referência de gerenciamento da API do Stream Analytics para .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

O Azure Stream Analytics é um serviço completamente gerenciado que oferece baixa latência, alta disponibilidade e processamento escalonável de eventos complexos por streaming de dados na nuvem. O Stream Analytics permite que os clientes configurem trabalhos de streaming para analisar fluxos de dados e realizem análise quase em tempo real.  

> [!NOTE]
> Atualizamos o código de exemplo neste artigo com a versão v2.x do SDK do .NET de Gerenciamento do Stream Analytics do Azure. Para código de exemplo usando a versão herdada (1.x) do SDK, consulte [Usar o SDK do .NET de Gerenciamento v1.x para Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk-v1).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter o seguinte:

* Instale o Visual Studio 2017 ou 2015.
* Baixe e instale o [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Crie um grupo de recursos do Azure em sua assinatura. O seguinte é um exemplo de script do PowerShell do Azure. Para obter mais informações sobre o PowerShell do Azure, consulte [Instalar e configurar o PowerShell do Azure](/powershell/azure/overview).  

   ```powershell
   # Log in to your Azure account
   Add-AzureAccount
   
   # Select the Azure subscription you want to use to create the resource group
   Select-AzureSubscription -SubscriptionName <subscription name>
   
   # If Stream Analytics has not been registered to the subscription, remove the remark    symbol (#) to run the Register-AzProvider cmdlet to register the provider namespace
   #Register-AzProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
   
   # Create an Azure resource group
   New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
   ```

* Configure uma origem de entrada e o destino de saída para o trabalho a ser conectado.

## <a name="set-up-a-project"></a>Configurar um projeto
Para criar um trabalho de análise, use a API do Stream Analytics para .NET. Primeiro, configure seu projeto.

1. Crie um aplicativo de console do Visual Studio C# .NET.
2. No Console do Gerenciador de pacotes, execute os seguintes comandos para instalar os pacotes do NuGet. O primeiro é o SDK .NET do Azure Stream Analytics Management. A segunda é para autenticação de cliente do Azure.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Adicione a seguinte seção **appSettings** ao arquivo App.config.
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    Substitua os valores por **SubscriptionId** e **ActiveDirectoryTenantId** por suas IDs de assinatura e locatário do Azure. Você pode obter esses valores executando o seguinte cmdlet do PowerShell do Azure:

   ```powershell
      Get-AzureAccount
   ```

4. Adicione a seguinte referência em seu arquivo .csproj:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Adicione as seguintes declarações **using** ao arquivo de origem (Program.cs) no projeto.
   
   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Threading;
   using System.Threading.Tasks;
   
   using Microsoft.Azure.Management.StreamAnalytics;
   using Microsoft.Azure.Management.StreamAnalytics.Models;
   using Microsoft.Rest.Azure.Authentication;
   using Microsoft.Rest;
   ```

6. Adicione um método auxiliar de autenticação:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Crie um cliente de gerenciamento do Stream Analytics
Um objeto **StreamAnalyticsManagementClient** permite que você gerencie o trabalho e os componentes de trabalho, como entrada, saída e transformação.

Adicione o seguinte código ao início do método **Main** :

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamingJobName = "<YOUR STREAMING JOB NAME>";
    string inputName = "<YOUR JOB INPUT NAME>";
    string transformationName = "<YOUR JOB TRANSFORMATION NAME>";
    string outputName = "<YOUR JOB OUTPUT NAME>";
    
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
    // Get credentials
    ServiceClientCredentials credentials = GetCredentials().Result;
    
    // Create Stream Analytics management client
    StreamAnalyticsManagementClient streamAnalyticsManagementClient = new StreamAnalyticsManagementClient(credentials)
    {
        SubscriptionId = ConfigurationManager.AppSettings["SubscriptionId"]
    };
   ```

O valor da variável **resourceGroupName** deve ser igual ao nome do grupo de recursos que você criou ou escolheu nas etapas de pré-requisito.

Para automatizar o aspecto de apresentação de credencial da criação do trabalho, consulte [Autenticação de uma entidade de serviço com o Gerenciador de Recursos do Azure](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

As seções restantes deste artigo pressupõem que esse código esteja no início do método **Main** .

## <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics
O código a seguir cria um trabalho do Stream Analytics sob o grupo de recursos que você definiu. Você adicionará entrada, saída e transformação ao trabalho mais tarde.

   ```csharp
   // Create a streaming job
   StreamingJob streamingJob = new StreamingJob()
   {
       Tags = new Dictionary<string, string>()
       {
           { "Origin", ".NET SDK" },
           { "ReasonCreated", "Getting started tutorial" }
       },
       Location = "West US",
       EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Drop,
       EventsOutOfOrderMaxDelayInSeconds = 5,
       EventsLateArrivalMaxDelayInSeconds = 16,
       OutputErrorPolicy = OutputErrorPolicy.Drop,
       DataLocale = "en-US",
       CompatibilityLevel = CompatibilityLevel.OneFullStopZero,
       Sku = new Sku()
       {
           Name = SkuName.Standard
       }
   };
   StreamingJob createStreamingJobResult = streamAnalyticsManagementClient.StreamingJobs.CreateOrReplace(streamingJob, resourceGroupName, streamingJobName);
   ```

## <a name="create-a-stream-analytics-input-source"></a>Criar uma fonte de entrada do Stream Analytics
O código a seguir cria uma fonte de entrada do Stream Analytics com o tipo de fonte de entrada de blob e serialização de CSV. Para criar uma fonte de entrada de hub de eventos, use **EventHubStreamInputDataSource** em vez de **BlobStreamInputDataSource**. Da mesma forma, você pode personalizar o tipo de serialização da fonte de entrada.

   ```csharp
   // Create an input
   StorageAccount storageAccount = new StorageAccount()
   {
       AccountName = "<YOUR STORAGE ACCOUNT NAME>",
       AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
   };
   Input input = new Input()
   {
       Properties = new StreamInputProperties()
       {
           Serialization = new CsvSerialization()
           {
               FieldDelimiter = ",",
               Encoding = Encoding.UTF8
           },
           Datasource = new BlobStreamInputDataSource()
           {
               StorageAccounts = new[] { storageAccount },
               Container = "<YOUR STORAGE BLOB CONTAINER>",
               PathPattern = "{date}/{time}",
               DateFormat = "yyyy/MM/dd",
               TimeFormat = "HH",
               SourcePartitionCount = 16
           }
       }
   };
   Input createInputResult = streamAnalyticsManagementClient.Inputs.CreateOrReplace(input, resourceGroupName, streamingJobName, inputName);
   ```

As fontes de entrada de armazenamento de Blob ou de um hub de eventos estão ligadas a um trabalho específico. Para usar a mesma fonte de entrada para trabalhos diferentes, chame o método novamente e especifique um nome de trabalho diferente.

## <a name="test-a-stream-analytics-input-source"></a>Testar uma fonte de entrada do Stream Analytics
O método **TestConnection** testa se o trabalho do Stream Analytics é capaz de se conectar à fonte de entrada, bem como outros aspectos específicos para o tipo de fonte de entrada. Por exemplo, em que na fonte de entrada de blob criada em uma etapa anterior, o método verificará se o nome da conta de armazenamento e o par de chaves podem ser usados para conectar-se à conta de armazenamento, bem como verificará se o contêiner especificado existe.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Criar um destino de saída do Stream Analytics
Criar um destino de saída é muito semelhante a criar uma fonte de entrada do Stream Analytics. Como as fontes de entrada, os destinos de saída são vinculados a trabalho específico. Para usar o mesmo destino de saída para diferentes trabalhos, chame o método novamente e especifique um nome de trabalho diferente.

O código a seguir cria um destino de saída (Banco de Dados SQL do Azure). Você pode personalizar o tipo de dados de destino de saída e/ou o tipo de serialização.

   ```csharp
   // Create an output
   Output output = new Output()
   {
       Datasource = new AzureSqlDatabaseOutputDataSource()
       {
           Server = "<YOUR DATABASE SERVER NAME>",
           Database = "<YOUR DATABASE NAME>",
           User = "<YOUR DATABASE LOGIN>",
           Password = "<YOUR DATABASE LOGIN PASSWORD>",
           Table = "<YOUR DATABASE TABLE NAME>"
       }
   };
   Output createOutputResult = streamAnalyticsManagementClient.Outputs.CreateOrReplace(output, resourceGroupName, streamingJobName, outputName);
   ```

## <a name="test-a-stream-analytics-output-target"></a>Testar um destino de saída do Stream Analytics
Um destino de saída do Stream Analytics também tem um método **TestConnection** para conexões de teste.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Criar uma transformação do Stream Analytics
O código a seguir cria uma transformação do Stream Analytics com a consulta "selecionar * da entrada" e especifica para alocar uma unidade de streaming para o trabalho do Stream Analytics. Para obter mais informações sobre o ajuste de unidades de streaming, consulte Dimensionar trabalhos do [Escalonar trabalhos de Análise de fluxo do Azure](stream-analytics-scale-jobs.md).

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Como entrada e saída, uma transformação também está vinculada ao trabalho de Stream Analytics específico sob o qual foi criada.

## <a name="start-a-stream-analytics-job"></a>Iniciar um trabalho do Stream Analytics
Depois de criar um trabalho do Stream Analytics e suas entradas, saídas e transformação, você pode iniciar o trabalho chamando o método **Iniciar** .

O código de exemplo a seguir inicia um trabalho do Stream Analytics com uma hora de início de saída personalizada definida para 12 de dezembro de 2012, 12h12min12 UTC:

   ```csharp
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Interromper um trabalho do Stream Analytics
Você pode interromper um trabalho do Stream Analytics em execução chamando o método **Parar** .

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Excluir um trabalho do Stream Analytics
O método **Excluir** excluirá o trabalho, bem como os sub-recursos subjacentes, incluindo entradas, saídas e transformação do trabalho.

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Obter suporte
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

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
[azure.blob.storage]: https://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: https://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: https://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
