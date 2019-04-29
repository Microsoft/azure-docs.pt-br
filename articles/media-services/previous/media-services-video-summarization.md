---
title: Usar o Azure Media Video Thumbnails para Criar um Resumo de vídeo | Microsoft Docs
description: O resumo de vídeo pode ajudá-lo a criar resumos de vídeos de longa duração com a seleção automática de snippets interessantes do vídeo de origem. Isso será útil quando você desejar fornecer uma visão geral rápida do que esperar de um vídeo de longa duração.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: milanga;juliako;
ms.openlocfilehash: 0fcacf68f4b41ed8945a6a40d7da125aef499947
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60825516"
---
# <a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>Usar as Miniaturas de Vídeo de Mídia do Azure para criar um resumo de vídeo  
## <a name="overview"></a>Visão geral
O MP (processador de mídia) das **Miniaturas de Vídeo de Mídia do Azure** permite criar o resumo de um vídeo que é grande utilidade para clientes que desejam apenas visualizar um resumo de um vídeo de longa duração. Por exemplo, os clientes talvez queiram ver um breve “vídeo resumido” ao focalizar uma miniatura. Ao ajustar os parâmetros das **Miniaturas de Vídeo de Mídia do Azure** por meio de uma predefinição de configuração, é possível usar a avançada tecnologia de detecção de captura e concatenação do MP para gerar de forma algorítmica um subclipe descritivo.  

No momento, o MP da **Miniatura de Vídeo de Mídia do Azure** está em Preview.

Este artigo fornece detalhes sobre a **Miniatura de Vídeo de Mídia do Azure** e mostra como usá-la com o SDK dos Serviços de Mídia para .NET.

## <a name="limitations"></a>Limitações

Em alguns casos, se o vídeo não for composto de cenas diferentes, a saída será apenas uma única captura.

## <a name="video-summary-example"></a>Exemplo de resumo de vídeo
Apresentamos abaixo alguns exemplos do que o processador de mídia das Miniaturas de Vídeo de Mídia do Azure é capaz de fazer:

### <a name="original-video"></a>Vídeo original
[Vídeo original](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

### <a name="video-thumbnail-result"></a>Resultado da miniatura de vídeo
[Resultado da miniatura de vídeo](https://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="task-configuration-preset"></a>Configuração de tarefa (predefinição)
Ao criar uma tarefa de miniatura de vídeo com as **Miniaturas de Vídeo de Mídia do Azure**, é necessário especificar uma predefinição de configuração. O exemplo de miniatura acima foi criado com a seguinte configuração básica do JSON:

```json
    {
        "version":"1.0"
    }
```

No momento, é possível alterar os seguintes parâmetros:

| Param | DESCRIÇÃO |
| --- | --- |
| outputAudio |Especifica se o vídeo resultante conterá áudio. <br/>Valores permitidos são: Verdadeiro ou falso. Padrão: True. |
| fadeInFadeOut |Especifica se as transições de esmaecimento serão usadas entre as miniaturas de movimento separadas.  <br/>Valores permitidos são: Verdadeiro ou falso.  Padrão: True. |
| maxMotionThumbnailDurationInSecs |Inteiro que especifica qual será a duração de todo o vídeo resultante.  O padrão depende da duração do vídeo original. |

A tabela a seguir descreve a duração padrão, quando **maxMotionThumbnailInSecs** não é usado.

|  |  |  |
| --- | --- | --- |
| Duração do vídeo |d < 3 min |3 min < d < 15 min |
| Duração da miniatura |15 s (2 a 3 cenas) |30 s (3 a 5 cenas) |

O JSON a seguir define os parâmetros disponíveis.

```json
    {
        "version": "1.0",
        "options": {
            "outputAudio": "true",
            "maxMotionThumbnailDurationInSecs": "10",
            "fadeInFadeOut": "true"
        }
    }
```

## <a name="net-sample-code"></a>Código de exemplo do .NET

O programa a seguir mostra como:

1. Criar um ativo e carregar um arquivo de mídia nesse ativo.
2. Criar um trabalho com uma miniatura de vídeo baseada em um arquivo de configuração que contém a predefinição de JSON a seguir: 
    
    ```json
            {                
                "version": "1.0",
                "options": {
                    "outputAudio": "true",
                    "maxMotionThumbnailDurationInSecs": "30",
                    "fadeInFadeOut": "false"
                }
            }
    ```

3. Baixar os arquivos de saída. 

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

    namespace VideoSummarization
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


                // Run the thumbnail job.
                var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
                                            @"C:\supportFiles\VideoThumbnail\config.json");

                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
            }

            static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Video Thumbnail Input Asset",
                    AssetCreationOptions.None);

                // Declare a new job.
                IJob job = _context.Jobs.Create("My Video Thumbnail Job");

                // Get a reference to Azure Media Video Thumbnails.
                string MediaProcessorName = "Azure Media Video Thumbnails";

                var processor = GetLatestMediaProcessorByName(MediaProcessorName);

                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);

                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
                    processor,
                    configuration,
                    TaskOptions.None);

                // Specify the input asset.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);

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

### <a name="video-thumbnail-output"></a>Saída da miniatura de vídeo
[Saída da miniatura de vídeo](https://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Links relacionados
[Visão geral do Azure Media Services Analytics](media-services-analytics-overview.md)

[Demonstrações do Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

