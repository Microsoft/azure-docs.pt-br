---
title: Personalizando as predefinições do Media Encoder Standard | Microsoft Docs
description: Esse tópico mostra como executar a codificação avançada ao personalizar predefinições de tarefa do Codificador de Mídia Padrão. Esse tópico mostra como usar o SDK do .NET dos Serviços de Mídia para criar um trabalho e uma tarefa de codificação. Ele também mostra como fornecer predefinições personalizadas para o trabalho de codificação.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ec95392f-d34a-4c22-a6df-5274eaac445b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2019
ms.author: juliako
ms.openlocfilehash: 39a1dd5c3d26eeb6545a96aa35f9457bd9859c21
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61247236"
---
# <a name="customizing-media-encoder-standard-presets"></a>Personalizando as predefinições do Media Encoder Standard  

## <a name="overview"></a>Visão geral

Este artigo mostra como executar a codificação avançada com o MES (Media Encoder Standard) usando uma predefinição personalizada. O artigo usa o .NET para criar uma tarefa de codificação e um trabalho que executa essa tarefa.  

Este artigo mostra a você como personalizar uma predefinição usando a predefinição a [H264 Taxas de Bits Múltiplas 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) e reduzindo o número de camadas. O artigo [Personalizando as predefinições do Media Encoder Standard](media-services-advanced-encoding-with-mes.md) demonstra as predefinições personalizadas que podem ser usadas para executar tarefas avançadas de codificação.

> [!NOTE]
> As predefinições personalizadas descritas neste artigo não podem ser usadas [serviços de mídia V3](https://docs.microsoft.com/azure/media-services/latest/) transformações ou os comandos da CLI. Consulte a [diretrizes de migração da v2 para v3](../latest/migrate-from-v2-to-v3.md) para obter mais detalhes.

## <a id="customizing_presets"></a> Personalizando uma predefinição do MES

### <a name="original-preset"></a>Predefinição original

Salve o JSON definido no artigo [H264 Taxas de Bits Múltiplas 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) em algum arquivo com a extensão .json. Por exemplo, **CustomPreset_JSON.json**.

### <a name="customized-preset"></a>Predefinição personalizada

Abra o arquivo **CustomPreset_JSON.json** e remova as primeiras três camadas de **H264Layers** para que seu arquivo tenha esta aparência.

```json 
    {  
      "Version": 1.0,  
      "Codecs": [  
        {  
          "KeyFrameInterval": "00:00:02",  
          "H264Layers": [  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 1000,  
              "MaxBitrate": 1000,  
              "BufferWindow": "00:00:05",  
              "Width": 640,  
              "Height": 360,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            },  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 650,  
              "MaxBitrate": 650,  
              "BufferWindow": "00:00:05",  
              "Width": 640,  
              "Height": 360,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            },  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 400,  
              "MaxBitrate": 400,  
              "BufferWindow": "00:00:05",  
              "Width": 320,  
              "Height": 180,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            }  
          ],  
          "Type": "H264Video"  
        },  
        {  
          "Profile": "AACLC",  
          "Channels": 2,  
          "SamplingRate": 48000,  
          "Bitrate": 128,  
          "Type": "AACAudio"  
        }  
      ],  
      "Outputs": [  
        {  
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
          "Format": {  
            "Type": "MP4Format"  
          }  
        }  
      ]  
    }  
```

## <a id="encoding_with_dotnet"></a>Codificação com o SDK do .NET dos Serviços de Mídia

O exemplo de código a seguir usa o SDK .NET dos Serviços de Mídia para executar as seguintes tarefas:

- Crie um trabalho de codificação.
- Obtenha uma referência para o Codificador de Mídia Padrão.
- Carregue a predefinição JSON personalizada criada na seção anterior. 
  
        // Load the JSON from the local file.
        string configuration = File.ReadAllText(fileName);  

- Adicione uma tarefa de codificação para o trabalho. 
- Especifique o ativo de entrada a ser codificado.
- Crie um ativo de saída contendo o ativo codificado.
- Adicione um manipulador de eventos para verificar o progresso do trabalho.
- Enviar o trabalho.
   
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

namespace CustomizeMESPresests
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

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using custom presets.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("CustomPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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
                    break;
                default:
                    break;
            }
        }

        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }

    }
}
```

## <a name="see-also"></a>Consulte também

- [Como codificar com uma transformação personalizada usando a CLI](../latest/custom-preset-cli-howto.md)
- [Codificação com os Serviços de Mídia v3](../latest/encoding-concept.md)

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
