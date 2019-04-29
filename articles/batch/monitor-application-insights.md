---
title: Monitorar Lote com o Azure Application Insights | Microsoft Docs
description: Aprenda como instrumentar um aplicativo .NET do Lote do Azure usando a biblioteca do Azure Application Insights.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: .NET
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: lahugh
ms.openlocfilehash: 42ea8398fa1a8b1fbc42108d1165dc17da2c34d7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618520"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Monitorar e depurar um aplicativo .NET do Lote do Azure com o Application Insights

O [Application Insights](../azure-monitor/app/app-insights-overview.md) fornece uma maneira elegante e avançada para os desenvolvedores monitorarem e depurarem aplicativos implantados nos serviços do Azure. Use o Application Insights para monitorar exceções e contadores de desempenho, bem como instrumentar o código com rastreamento e métricas personalizadas. A integração do Application Insights com o aplicativo Lote do Azure permite que você obtenha insights aprofundados sobre os comportamentos e investigue os problemas quase em tempo real.

Este artigo mostra como adicionar e configurar a biblioteca do Application Insights na solução .NET do Lote do Azure e instrumentar o código do aplicativo. Adicionalmente, mostra maneiras de monitorar o aplicativo por meio do Portal do Azure e criar painéis personalizados. Para suporte do Application Insights em outros idiomas, consulte a [documentação de integrações, plataformas e idiomas](../azure-monitor/app/platforms.md).

Uma solução C# de exemplo com código para acompanhar este artigo está disponível no [GitHub](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights). Este exemplo adiciona o código de instrumentação do Application Insights ao exemplo do [TopNWords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords). Se você não estiver familiarizado com esse exemplo, tente primeiro compilar e executar o TopNWords. Isso ajudará a compreender um fluxo de trabalho básico do Lote do processamento de um conjunto de blobs de entrada em paralelo em vários nós de computação. 

> [!TIP]
> Como alternativa, configure sua solução em lote para exibir dados do Application Insights, como os contadores de desempenho da VM no Batch Explorer. [O Batch Explorer](https://github.com/Azure/BatchExplorer) é uma ferramenta cliente autônoma, rica e exclusiva para ajudar a criar, depurar e monitorar aplicativos em lote do Azure. Baixe um [pacote de instalação](https://azure.github.io/BatchExplorer/) para Mac, Linux ou Windows. Confira o [repositório de insights em lote](https://github.com/Azure/batch-insights) para conhecer etapas rápidas para habilitar dados do Application Insights no Batch Explorer. 
>

## <a name="prerequisites"></a>Pré-requisitos
* [Visual Studio 2017](https://www.visualstudio.com/vs)

* [Conta do Lote e conta de armazenamento vinculada](batch-account-create-portal.md)

* [Recurso do Application Insights](../azure-monitor/app/create-new-resource.md )
  
   * Use o Portal do Azure para criar um *recurso* do Application Insights. Selecione a opção *Geral* **Tipo de Aplicativo**.

   * Copie a [chave de instrumentação](../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) do portal. Essa chave será necessária mais adiante neste artigo.
  
  > [!NOTE]
  > Você pode ser [cobrado](https://azure.microsoft.com/pricing/details/application-insights/) pelos dados armazenados no Application Insights. Isso inclui os dados de diagnóstico e monitoramento discutidos neste artigo.
  > 

## <a name="add-application-insights-to-your-project"></a>Adicione o Application Insights ao seu projeto

O pacote do NuGet **Microsoft.ApplicationInsights.WindowsServer** e suas dependências são necessários para o projeto. Adicione ou restaure-os no projeto do aplicativo. Para instalar o pacote, use comando `Install-Package` ou o Gerenciador de Pacotes do NuGet.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Consulte o Application Insights do aplicativo .NET usando o namespace **Microsoft.ApplicationInsights**.

## <a name="instrument-your-code"></a>Instrumentalize seu código

Para instrumentar o código, a solução precisa criar um [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient) do Application Insights. No exemplo, o TelemetryClient carrega a configuração do arquivo [ApplicationInsights.config](../azure-monitor/app/configuration-with-applicationinsights-config.md). Certifique-se de atualizar o ApplicationInsights.config nos projetos a seguir com a chave de instrumentação do Application Insights Microsoft.Azure.Batch.Samples.TelemetryStartTask and TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Adicione também a chave de instrumentação no arquivo TopNWords.cs.

O exemplo em TopNWords.cs usa as seguintes [chamadas de instrumentação](../azure-monitor/app/api-custom-events-metrics.md) da API do Application Insights:
* `TrackMetric()` - Rastreia quanto tempo, em média, um nó de computação demora para baixar o arquivo de texto necessário.
* `TrackTrace()` - Adiciona chamadas de depuração ao código.
* `TrackEvent()` - Rastreia eventos interessantes para capturar.

Este exemplo exclui propositalmente o tratamento de exceções. Em vez disso, o Application Insights informa automaticamente as exceções não tratadas, o que melhora significativamente a experiência de depuração. 

O seguinte snippet ilustra como usar esses métodos.

```csharp
public void CountWords(string blobName, int numTopN, string storageAccountName, string storageAccountKey)
{
    // simulate exception for some set of tasks
    Random rand = new Random();
    if (rand.Next(0, 10) % 10 == 0)
    {
        blobName += ".badUrl";
    }

    // log the url we are downloading the file from
    insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Download file from: {1}", this.taskId, blobName), SeverityLevel.Verbose));

    // open the cloud blob that contains the book
    var storageCred = new StorageCredentials(storageAccountName, storageAccountKey);
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), storageCred);
    using (Stream memoryStream = new MemoryStream())
    {
        // calculate blob download time
        DateTime start = DateTime.Now;
        blob.DownloadToStream(memoryStream);
        TimeSpan downloadTime = DateTime.Now.Subtract(start);

        // track how long the blob takes to download on this node
        // this will help debug timing issues or identify poorly performing nodes
        insightsClient.TrackMetric("Blob download in seconds", downloadTime.TotalSeconds, this.CommonProperties);

        memoryStream.Position = 0; //Reset the stream
        var sr = new StreamReader(memoryStream);
        var myStr = sr.ReadToEnd();
        string[] words = myStr.Split(' ');

        // log how many words were found in the text file
        insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Found {1} words", this.taskId, words.Length), SeverityLevel.Verbose));
        var topNWords =
            words.
                Where(word => word.Length > 0).
                GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
                OrderByDescending(x => x.Value).
                Take(numTopN).
                ToList();
        foreach (var pair in topNWords)
        {
            Console.WriteLine("{0} {1}", pair.Key, pair.Value);
        }

        // emit an event to track the completion of the task
        insightsClient.TrackEvent("Done counting words");
    }
}
```

### <a name="azure-batch-telemetry-initializer-helper"></a>Auxiliar do inicializador de telemetria do Lote do Azure
Ao reportar a telemetria para um determinado servidor e instância, o Application Insights usa o nome da VM e a função VM do Azure para os valores padrão. No contexto do Lote do Azure, o exemplo mostra como usar o nome do pool e o nome do nó de computação. Use um [inicializador de telemetria](../azure-monitor/app/api-filtering-sampling.md#add-properties) para substituir os valores padrão. 

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using System;
using System.Threading;

namespace Microsoft.Azure.Batch.Samples.TelemetryInitializer
{
    public class AzureBatchNodeTelemetryInitializer : ITelemetryInitializer
    {
        // Azure Batch environment variables
        private const string PoolIdEnvironmentVariable = "AZ_BATCH_POOL_ID";
        private const string NodeIdEnvironmentVariable = "AZ_BATCH_NODE_ID";

        private string roleInstanceName;
        private string roleName;

        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                // override the role name with the Azure Batch Pool name
                string name = LazyInitializer.EnsureInitialized(ref this.roleName, this.GetPoolName);
                telemetry.Context.Cloud.RoleName = name;
            }

            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // override the role instance with the Azure Batch Compute Node name
                string name = LazyInitializer.EnsureInitialized(ref this.roleInstanceName, this.GetNodeName);
                telemetry.Context.Cloud.RoleInstance = name;
            }
        }

        private string GetPoolName()
        {
            return Environment.GetEnvironmentVariable(PoolIdEnvironmentVariable) ?? string.Empty;
        }

        private string GetNodeName()
        {
            return Environment.GetEnvironmentVariable(NodeIdEnvironmentVariable) ?? string.Empty;
        }
    }
}
```

Para habilitar o inicializador de telemetria, o arquivo ApplicationInsights.config no projeto TopNWordsSample inclui o seguinte:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Atualizar o trabalho e as tarefas para incluir binários do Application Insights

Para que o Application Insights seja executado corretamente nos nós de computação, verifique se os binários estão posicionados corretamente. Adicione os binários necessários à coleção de arquivos de recursos da tarefa para que eles sejam baixados no momento em que a tarefa for executada. Os snippets a seguir são semelhantes ao código no Job.cs.

Primeiro, crie uma lista estática de arquivos do Application Insights para upload.

```csharp
private static readonly List<string> AIFilesToUpload = new List<string>()
{
    // Application Insights config and assemblies
    "ApplicationInsights.config",
    "Microsoft.ApplicationInsights.dll",
    "Microsoft.AI.Agent.Intercept.dll",
    "Microsoft.AI.DependencyCollector.dll",
    "Microsoft.AI.PerfCounterCollector.dll",
    "Microsoft.AI.ServerTelemetryChannel.dll",
    "Microsoft.AI.WindowsServer.dll",
    
    // custom telemetry initializer assemblies
    "Microsoft.Azure.Batch.Samples.TelemetryInitializer.dll",
 };
...
```

Em seguida, crie os arquivos temporários usados pela tarefa.
```csharp
...
// create file staging objects that represent the executable and its dependent assembly to run as the task.
// These files are copied to every node before the corresponding task is scheduled to run on that node.
FileToStage topNWordExe = new FileToStage(TopNWordsExeName, stagingStorageAccount);
FileToStage storageDll = new FileToStage(StorageClientDllName, stagingStorageAccount);

// Upload Application Insights assemblies
List<FileToStage> aiStagedFiles = new List<FileToStage>();
foreach (string aiFile in AIFilesToUpload)
{
    aiStagedFiles.Add(new FileToStage(aiFile, stagingStorageAccount));
}
...
```

O método `FileToStage` é uma função auxiliar no exemplo de código que permite carregar facilmente um arquivo do disco local em um blob de armazenamento do Azure. Cada arquivo será posteriormente baixado para um nó de computação e referenciado por uma tarefa.

Por fim, adicione as tarefas ao trabalho e inclua os binários necessários do Application Insights.
```csharp
...
// initialize a collection to hold the tasks that will be submitted in their entirety
List<CloudTask> tasksToRun = new List<CloudTask>(topNWordsConfiguration.NumberOfTasks);
for (int i = 1; i <= topNWordsConfiguration.NumberOfTasks; i++)
{
    CloudTask task = new CloudTask("task_no_" + i, String.Format("{0} --Task {1} {2} {3} {4}",
        TopNWordsExeName,
        string.Format("https://{0}.blob.core.windows.net/{1}",
            accountSettings.StorageAccountName,
            documents[i]),
        topNWordsConfiguration.TopWordCount,
        accountSettings.StorageAccountName,
        accountSettings.StorageAccountKey));

    //This is the list of files to stage to a container -- for each job, one container is created and 
    //files all resolve to Azure Blobs by their name (so two tasks with the same named file will create just 1 blob in
    //the container).
    task.FilesToStage = new List<IFileStagingProvider>
                        {
                            // required application binaries
                            topNWordExe,
                            storageDll,
                        };
    foreach (FileToStage stagedFile in aiStagedFiles)
   {
        task.FilesToStage.Add(stagedFile);
   }    
    task.RunElevated = false;
    tasksToRun.Add(task);
}
```

## <a name="view-data-in-the-azure-portal"></a>Exibir dados no Portal do Azure

Agora que você configurou o trabalho e as tarefas para usar o Application Insights, execute o exemplo de trabalho no pool. Navegue até o Portal do Azure e abra o recurso do Application Insights provisionado. Depois que o pool for provisionado, você deverá começar a ver o fluxo de dados e a obtenção de registros. O restante deste artigo aborda apenas alguns recursos do Application Insights, mas fique à vontade para explorar o conjunto completo de recursos.

### <a name="view-live-stream-data"></a>Exibir dados do Live Stream

Para exibir os logs de rastreamento no recurso do Applications Insights, clique em **Live Stream**. A captura de tela a seguir mostra como exibir dados dinâmicos provenientes dos nós de computação no pool, por exemplo, o uso da CPU por nó de computação.

![Dados do nó de computação do Live Stream](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Exibir logs de rastreamento

Para exibir os logs de rastreamento no recurso do Applications Insights, clique em **Pesquisar**. Essa visualização mostra uma lista de dados de diagnóstico capturados pelo Application Insights, incluindo rastreamentos, eventos e exceções. 

A captura de tela a seguir mostra como um único rastreamento de uma tarefa é registrado em log e, posteriormente, consultado para fins de depuração.

![Imagem de logs de rastreamento](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Exibir exceções sem tratamento

As capturas de tela a seguir mostram como o Application Insights registra exceções lançadas do aplicativo. Nesse caso, alguns segundos depois que o aplicativo lançar a exceção será possível detalhar uma exceção específica e diagnosticar o problema.

![Exceções sem tratamento](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Medir o tempo de download de blob

Métricas personalizadas também são uma ferramenta valiosa no portal. Por exemplo, é possível exibir o tempo médio que cada nó de computação demorou para baixar o arquivo de texto necessário que estava processando.

Para criar um gráfico de exemplo:
1. No recurso do Application Insights, clique em **Metrics Explorer** > **Adicionar gráfico**.
2. Clique em **Editar** no gráfico que foi adicionado.
2. Atualize os detalhes do gráfico conforme a seguir:
   * Defina **Tipo de gráfico** para **Grade**.
   * Defina **Agregação** para **Média**.
   * Defina **Agrupar por** para **NodeId**.
   * Em **Métrica**, selecione **Personalizar** > **Baixar o blob em segundos**.
   * Ajuste a **Paleta de cores** à sua escolha. 

![Tempo de download de Blob por nó](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Monitorar os nós de computação continuamente

Você deve ter observado que todas as métricas, incluindo contadores de desempenho, são registradas apenas quando as tarefas estão em execução. Esse comportamento é útil porque limita a quantidade de dados que o Application Insights registra. No entanto, há casos em que você sempre gostaria de monitorar os nós de computação. Por exemplo, eles podem estar executando um trabalho em segundo plano que não está agendado por meio do serviço do Lote. Nesse caso, configure um processo de monitoramento para executar a vida útil do nó de computação. 

Uma maneira de obter esse comportamento é gerar um processo que carregue a biblioteca do Application Insights e seja executado em segundo plano. No exemplo, a tarefa inicial carrega os binários no computador e mantém um processo em execução indefinidamente. Configure o arquivo de configuração do Application Insights para que esse processo emita dados adicionais nos quais você está interessado, como contadores de desempenho.

```csharp
...
 // Batch start task telemetry runner
private const string BatchStartTaskFolderName = "StartTask";
private const string BatchStartTaskTelemetryRunnerName = "Microsoft.Azure.Batch.Samples.TelemetryStartTask.exe";
private const string BatchStartTaskTelemetryRunnerAIConfig = "ApplicationInsights.config";
...
CloudPool pool = client.PoolOperations.CreatePool(
    topNWordsConfiguration.PoolId,
    targetDedicated: topNWordsConfiguration.PoolNodeCount,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
...

// Create a start task which will run a dummy exe in background that simply emits performance
// counter data as defined in the relevant ApplicationInsights.config.
// Note that the waitForSuccess on the start task was not set so the Compute Node will be
// available immediately after this command is run.
pool.StartTask = new StartTask()
{
    CommandLine = string.Format("cmd /c {0}", BatchStartTaskTelemetryRunnerName),
    ResourceFiles = resourceFiles
};
...
```

> [!TIP]
> Para aumentar a capacidade de gerenciamento da solução, é possível agrupar o assembly em um [pacote de aplicativos](./batch-application-packages.md). Em seguida, para implantar o pacote de aplicativos automaticamente nos pools, adicione uma referência de pacote de aplicativos à configuração do pool.
>

## <a name="throttle-and-sample-data"></a>Limitação e dados de exemplo 

Devido à natureza em grande escala dos aplicativos do Lote do Azure executando em produção, convém limitar a quantidade de dados coletados pelo Application Insights para gerenciar os custos. Consulte [Amostragem em Application Insights](../azure-monitor/app/sampling.md) e obtenha alguns mecanismos para fazer isso.


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [Application Insights](../azure-monitor/app/app-insights-overview.md).

* Para suporte do Application Insights em outros idiomas, consulte a [documentação de integrações, plataformas e idiomas](../azure-monitor/app/platforms.md).


