---
title: Codificação avançada com o Fluxo de Trabalho do Media Encoder Premium | Microsoft Docs
description: Saiba como codificar com fluxo de trabalho do Media Encoder Premium. Os exemplos de código são escritos em C# e usam a SDK dos Serviços de Mídia para .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: d20fc0cee6bce1389649e6287170b1963e799ccf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463883"
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Codificação avançada com fluxo de trabalho do Media Encoder Premium
> [!NOTE]
> O processador de mídia do Media Encoder Premium Workflow analisado neste artigo não está disponível na China.
>
>

Para questões de Codificador Premium, email mepd@microsoft.com.

## <a name="overview"></a>Visão geral
Os Serviços de Mídia do Microsoft Azure estão apresentando o processador de mídia do **Fluxo de trabalho do Codificador de Mídia Premium** . Esse processador oferece recursos avançados de codificação para seus fluxos de trabalho premium sob demanda.

Os tópicos a seguir descrevem os detalhes relacionados ao **Fluxo de Trabalho do Media Encoder Premium**:

* [Formatos suportados pelo Fluxo de trabalho do Media Encoder Premium](media-services-premium-workflow-encoder-formats.md) – Discute os formatos de arquivo e codecs com suporte do **Fluxo de trabalho do Media Encoder Premium**.
* A [visão geral e a comparação dos codificadores de mídia sob demanda do Azure](media-services-encode-asset.md) comparam os recursos de codificação do **Media Encoder Premium Workflow** e do **Media Encoder Standard**.

Este artigo demonstra como codificar com o **Media Encoder Premium Workflow** usando o .NET.

As tarefas de codificação para o **Fluxo de trabalho do Media Encoder Premium** exigem um arquivo de configuração separado, chamado de arquivo de Fluxo de trabalho. Esses arquivos têm uma extensão .workflow e são criados usando a ferramenta [Designer de Fluxo de Trabalho](media-services-workflow-designer.md) .

Você também pode obter os arquivos de fluxo de trabalho padrão [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). A pasta também contém a descrição desses arquivos.

Os arquivos de fluxo de trabalho devem ser carregados para sua conta de serviços de mídia como um ativo e esse ativo deve ser passado para a tarefa de codificação.

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto do Visual Studio

Configure seu ambiente de desenvolvimento e preencha o arquivo de configuração app.config com as informações de conexão, conforme descrito em [Desenvolvimento de Serviços de Mídia com o .NET](media-services-dotnet-how-to-use.md). 

## <a name="encoding-example"></a>Exemplo de codificação

O exemplo a seguir demonstra como codificar com o **Fluxo de trabalho do Media Encoder Premium**.

As seguintes etapas são executadas:

1. Criar um ativo e carregar um arquivo de fluxo de trabalho.
2. Criar um ativo e carregar um arquivo de mídia de origem.
3. Obtenha o processador de mídia "Fluxo de trabalho do Media Encoder Premium".
4. Criar um trabalho e uma tarefa.

    Na maioria dos casos, a cadeia de caracteres de configuração para a tarefa está vazia (como no exemplo a seguir). Há alguns cenários avançados (que exigem que você defina propriedades de tempo de execução dinamicamente) em que você forneceria uma cadeia de caracteres XML para a tarefa de codificação. Exemplos desses cenários são: criação de uma sobreposição, união de mídia paralela ou sequencial, colocação de legenda.
5. Adicionar dois ativos de entrada à tarefa.

   1. 1º – o ativo de fluxo de trabalho.
   2. 2º – o ativo de vídeo.

      >[!NOTE]
      >O ativo de fluxo de trabalho deve ser adicionado à tarefa antes do ativo de mídia.
      A cadeia de caracteres de configuração para essa tarefa deve estar vazia.
   
6. Envie o trabalho de codificação.

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderPremiumWorkflowSample
{
    class Program
    {
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

        private static readonly string _supportFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _workflowFilePath =
            Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");

        private static readonly string _singleMP4InputFilePath =
            Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");

        static void Main(string[] args)
        {

            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
            var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
            IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset);

        }

        static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
        {
            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
            var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return asset;
        }

        static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Premium Workflow encoding job");
            // Get a media processor reference, and pass to it the name of the
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                processor,
                "",
                TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(workflow);
            task.InputAssets.Add(video); // we add one asset
                                         // Add an output asset to contain the results of the job.
                                         // This output is specified as AssetCreationOptions.None, which
                                         // means the output asset is not encrypted.
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();

            // If job state is Error the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                throw new Exception("\nExiting method due to job error.");
            }

            return job.OutputMediaAssets[0];
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

Para questões de Codificador Premium, email mepd@microsoft.com.

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
