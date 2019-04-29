---
title: Usar o Codificador de Mídia do Azure Standard para gerar automaticamente uma escada de taxa de bits | Microsoft Docs
description: Este tópico mostra como usar o Media Encoder Standard (MES) para gerar automaticamente uma escada de taxa de bits com base na resolução de entrada e na taxa de bits. A resolução de entrada e a taxa de bits nunca serão excedidas. Por exemplo, se a entrada for 720p em 3Mbps, a saída continuará 720p na melhor das hipóteses e iniciará com taxas menores que 3Mbps.
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
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: bbaf4d490fcebb4cd741a9b83ffc5d7e85699755
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61224337"
---
#  <a name="use-azure-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Usar o Codificador de Mídia do Azure Standard para gerar automaticamente uma escada de taxa de bits  

## <a name="overview"></a>Visão geral

Este artigo mostra como usar o Media Encoder Standard (MES) para gerar automaticamente uma escada de taxa de bits (pares de resolução de taxa de bits) com base na resolução de entrada e na taxa de bits. A predefinição gerada automaticamente nunca excederá a resolução de entrada e a taxa de bits. Por exemplo, se a entrada for 720p em 720p, a saída continuará 720p na melhor das hipóteses e iniciará com taxas menores que 3 Mbps.

### <a name="encoding-for-streaming-only"></a>Codificar para Streaming Somente

Se sua intenção for codificar o vídeo de origem somente para streaming, é recomendável usar a predefinição "Streaming adaptável" ao criar uma tarefa de codificação. Ao usar a predefinição do **Streaming Adaptável**, o codificador MES protegerá, de forma inteligente, uma escada de taxa de bits. No entanto, não será possível controlar os custos de codificação, já que o serviço determina quantas camadas usar e em qual resolução. Você pode ver exemplos das camadas de saída produzidas por MES como resultado de codificação com a predefinição do **Streaming Adaptável** no final deste artigo. O Ativo de saída contém arquivos MP4 onde áudio e vídeo não são intercalados.

### <a name="encoding-for-streaming-and-progressive-download"></a>Codificação para download progressivo e streaming

Se sua intenção for codificar o vídeo de origem de streaming, bem como para produzir arquivos MP4 para fazer o download progressivo, é recomendável usar a predefinição "conteúdo adaptável várias taxas de bits MP4" ao criar uma tarefa de codificação. Ao usar a predefinição **MP4 de Taxa de Bits Múltiplas Adaptável a Conteúdo**, o codificador MES aplica a mesma lógica de codificação como acima, mas agora, o ativo de saída conterá arquivos MP4 onde áudio e vídeo são intercalados. Você pode usar um desses arquivos MP4 (por exemplo, a versão de taxa de bits mais alta) como um arquivo de download progressivo.

## <a id="encoding_with_dotnet"></a>Codificação com o SDK do .NET dos Serviços de Mídia

O exemplo de código a seguir usa o SDK .NET dos Serviços de Mídia para executar as seguintes tarefas:

- Crie um trabalho de codificação.
- Obtenha uma referência para o Codificador de Mídia Padrão.
- Adicione uma tarefa de codificação ao trabalho e especifique para usar a predefinição do **Streaming Adaptável**. 
- Crie um ativo de saída contendo o ativo codificado.
- Adicione um manipulador de eventos para verificar o progresso do trabalho.
- Enviar o trabalho.

#### <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto do Visual Studio

Configure seu ambiente de desenvolvimento e preencha o arquivo de configuração app.config com as informações de conexão, conforme descrito em [Desenvolvimento de Serviços de Mídia com o .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Exemplo

```
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace AdaptiveStreamingMESPresest
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

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using the "Adaptive Streaming" preset.
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

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            "Adaptive Streaming",
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

## <a id="output"></a>Saída

Esta seção exibe três exemplos de camadas de saída produzidas por MES como resultado de codificação com a predefinição do **Streaming Adaptável**. 

### <a name="example-1"></a>Exemplo 1
Fonte com altura "1080" e taxa de quadros "29.970" produz seis camadas de vídeo:

|Camada|Altura|Largura|Taxa de bits (Kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Exemplo 2
Fonte com altura "720" e taxa de quadros "23.970" produz cinco camadas de vídeo:

|Camada|Altura|Largura|Taxa de bits (Kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Exemplo 3
Fonte com altura "360" e taxa de quadros "29.970" produz três camadas de vídeo:

|Camada|Altura|Largura|Taxa de bits (Kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja também
[Visão geral da codificação de serviços de mídia](media-services-encode-asset.md)

