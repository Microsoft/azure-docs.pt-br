---
title: Indexando os Arquivos de Mídia com a Visualização do Azure Media Indexer 2 | Microsoft Docs
description: O Azure Media Indexer permite que você torne o conteúdo de seus arquivos de mídia pesquisável e gere uma transcrição de texto completo para legendas codificadas e palavras-chave. Este tópico mostra como usar a Visualização do Indexador de Mídia 2 .
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/19/2019
ms.author: adsolank;juliako;
ms.openlocfilehash: 304ecda320e1fdd9573bc961fde74efe03400aa3
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764176"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Indexando arquivos de mídia com a Preview do Azure Media Indexer 2
## <a name="overview"></a>Visão geral
O MP (processador de mídia) da **Preview do Indexador de Mídia do Azure 2** permite tornar conteúdo e arquivos de mídia pesquisáveis, bem como gerar faixas de legendagem oculta. Comparada à versão anterior do [Indexador de Mídia do Azure](media-services-index-content.md), a **Preview do Indexador de Mídia do Azure 2** executa uma indexação mais rápida e oferece maior suporte a idiomas. Os idiomas com suporte incluem o inglês, espanhol, francês, alemão, italiano, chinês (mandarim, simplificado), português, árabe, russo e japonês.

No momento, o MP da **Preview do Indexador de Mídia do Azure 2** está em Preview.

Este artigo mostra como criar trabalhos de indexação com a **Visualização do Azure Media Indexer 2**.

> [!NOTE]
> As seguintes considerações se aplicam:
> 
> Não há suporte para o Indexador 2 no Azure China e no Azure Governamental.
> 
> Quanto a indexação de conteúdo, certifique-se de usar os arquivos de mídia com fala muito clara (sem música em segundo plano, ruído, efeitos ou assovio no microfone). Alguns exemplos de conteúdo apropriado são: reuniões, palestras e apresentações registradas. O seguinte conteúdo pode não ser adequado para indexação: filmes, programas de TV, tudo com áudio misto e efeitos de som, com conteúdo mal gravado com ruídos de fundo (assovio).
> 
> 

Este artigo fornece detalhes sobre a **Visualização do Azure Media Indexer 2** e mostra como usá-la com o SDK dos Serviços de Mídia para .NET

## <a name="input-and-output-files"></a>Arquivos de entrada e saída
### <a name="input-files"></a>Arquivos de entrada
Arquivos de áudio ou vídeo

### <a name="output-files"></a>Arquivos de saída
Um trabalho de indexação pode gerar arquivos de legenda oculta nos seguintes formatos:  

* **SAMI**
* **TTML**
* **WebVTT**

Arquivos de CC (Legenda Oculta) nesses formatos podem ser usados para tornar os arquivos de áudio e vídeo acessíveis para pessoas com deficiência auditiva.

## <a name="task-configuration-preset"></a>Configuração de tarefa (predefinição)
Ao criar uma tarefa de indexação com a **Preview do Azure Media Indexer 2**, é necessário especificar uma predefinição de configuração.

O JSON a seguir define os parâmetros disponíveis.

```json
    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }
```

## <a name="supported-languages"></a>Idiomas com suporte
A Preview do Azure Media Indexer 2 dá suporte à conversão de fala em texto para os seguintes idiomas (ao especificar o nome do idioma na configuração de tarefa, use o código de 4 caracteres entre colchetes, como mostrado abaixo):

* Inglês [EnUs]
* Espanhol [EsEs]
* Chinês (mandarim, simplificado) [ZhCn]
* Francês [FrFr]
* Alemão [DeDe]
* Italiano [ItIt]
* Português [PtBr]
* Árabe (Egípcio) [ArEg]
* Japonês [JaJp]
* Russo [RuRu]
* Inglês britânico [EnGb]
* Espanhol (México) [México EsMx] 

## <a name="supported-file-types"></a>Tipos de arquivo com suporte

Para obter informações sobre os tipos de arquivos com suporte, consulte a seção [codecs/formatos com suporte](media-services-media-encoder-standard-formats.md#input-containerfile-formats).

## <a name="net-sample-code"></a>Código de exemplo do .NET

O programa a seguir mostra como:

1. Criar um ativo e carregar um arquivo de mídia nesse ativo.
2. Crie um trabalho com uma tarefa de indexação baseada em um arquivo de configuração que contenha a predefinição de JSON a seguir:

    ```json
            {
            "version":"1.0",
            "Features":
                [
                {
                "Options": {
                        "Formats":["WebVtt","ttml"],
                        "Language":"enUs",
                        "Type":"RecoOptions"
                },
                "Type":"SpReco"
                }]
            }
    ```
    
3. Baixe os arquivos de saída. 
   
#### <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto do Visual Studio

Configure seu ambiente de desenvolvimento e preencha o arquivo de configuração app.config com as informações de conexão, conforme descrito em [Desenvolvimento de Serviços de Mídia com o .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Exemplo

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;
using System.Threading.Tasks;

namespace IndexContent
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Run indexing job.
            var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                        @"C:\supportFiles\Indexer\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
        }

        static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Indexing Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Indexing Job");

            // Get a reference to Azure Media Indexer 2 Preview.
            string MediaProcessorName = "Azure Media Indexer 2 Preview";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Indexing Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset to be indexed.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

            progressJobTask.Wait();

            // If job state is Error, the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                Console.WriteLine(string.Format("Error: {0}. {1}",
                                                error.Code,
                                                error.Message));
                return null;
            }

            return job.OutputMediaAssets[0];
        }

        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
        {
            IAsset asset = _context.Assets.Create(assetName, options);

            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);

            return asset;
        }

        static void DownloadAsset(IAsset asset, string outputDirectory)
        {
            foreach (IAssetFile file in asset.AssetFiles)
            {
                file.Download(Path.Combine(outputDirectory, file.Name));
            }
        }

        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors
                .Where(p => p.Name == mediaProcessorName)
                .ToList()
                .OrderBy(p => new Version(p.Version))
                .LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor",
                                                           mediaProcessorName));

            return processor;
        }

        static private void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine();
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:
                    // Cast sender as a job.
                    IJob job = (IJob)sender;
                    // Display or log error details as needed.
                    // LogJobStop(job.Id);
                    break;
                default:
                    break;
            }
        }
    }
}
```

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Links relacionados
[Visão geral do Azure Media Services Analytics](media-services-analytics-overview.md)

[Demonstrações do Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

