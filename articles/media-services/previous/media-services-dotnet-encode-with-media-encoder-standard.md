---
title: Codificar um ativo com o Media Encoder Standard usando o .NET | Microsoft Docs
description: Este artigo mostra como usar o .NET para codificar um ativo usando o Media Encoder Standard.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako;anilmur
ms.openlocfilehash: d3eb2affe76374eb35ac724dff0204f43b567e09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61225693"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Codificar um ativo com o Codificador de Mídia Padrão usando o .NET  

Os trabalhos de codificação são uma das operações de processamento mais comuns nos serviços de mídia. Você cria trabalhos de codificação para converter arquivos de mídia de uma codificação para outra. Ao codificar, você pode usar o codificador de mídia integrado dos serviços de mídia. Você também pode usar um codificador fornecido por um parceiro de Serviços de Mídia. Os codificadores de terceiros estão disponíveis por meio do Azure Marketplace. 

Este artigo mostra como usar o .NET para codificar seus ativos com o MES (Media Encoder Standard). O Media Encoder Standard é configurado usando um dos codificadores predefinidos descritos [aqui](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Convém sempre codificar arquivos de origem em um conjunto de MP4 de taxa de bits adaptável e, em seguida, converter o conjunto para o formato desejado usando o [Empacotamento Dinâmico](media-services-dynamic-packaging-overview.md). 

Se seu ativo de saída tiver o armazenamento criptografado, você deverá configurar a política de entrega de ativos. Para obter mais informações, consulte [Configurando a política de entrega de ativos](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> O MES produz um arquivo de saída com um nome que contém os primeiros 32 caracteres do nome do arquivo de entrada. O nome é baseado no que é especificado no arquivo de predefinição. Por exemplo, "FileName": "{Basename}_{Index}{Extension}". {Basename} é substituído pelos primeiros 32 caracteres do nome do arquivo de entrada.
> 
> 

### <a name="mes-formats"></a>Formatos de MES
[Formatos e codecs](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>Predefinições de MES
O Media Encoder Standard é configurado usando um dos codificadores predefinidos descritos [aqui](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Metadados de entrada e saída
Ao codificar um ativo (ou ativos) de entrada usando MES, você obtém um ativo de saída na conclusão bem-sucedida da tarefa de codificação. O ativo de saída contém vídeo, áudio, miniaturas, manifesto, etc., com base na predefinição de codificação utilizada.

O ativo de saída também contém um arquivo com metadados sobre o ativo de entrada. O nome do arquivo XML de metadados tem o seguinte formato: <asset_id>_metadata.xml (por exemplo, 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), em que <asset_id> é o valor de AssetId do ativo de entrada. O esquema desse XML de metadados de entrada é descrito [aqui](media-services-input-metadata-schema.md).

O ativo de saída também contém um arquivo com metadados sobre o ativo de saída. O nome do arquivo XML de metadados tem o seguinte formato: <nome_arquivo_origem>_manifest.xml (por exemplo, BigBuckBunny_manifest.xml). O esquema desse XML de metadados de saída é descrito [aqui](media-services-output-metadata-schema.md).

Se desejar examinar qualquer um dos dois arquivos de metadados, você poderá criar um localizador SAS e baixe o arquivo em seu computador local. É possível encontrar um exemplo de como criar um localizador SAS e baixar um arquivo usando as Extensões do SDK do .NET para os Serviços de Mídia.

## <a name="download-sample"></a>Baixar exemplo
É possível obter e executar uma amostra que explica como codificar com o MES [aqui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="net-sample-code"></a>Código de exemplo do .NET

O exemplo de código a seguir usa o SDK .NET dos Serviços de Mídia para executar as seguintes tarefas:

* Crie um trabalho de codificação.
* Obtenha uma referência para o Codificador de Mídia Padrão.
* Especifique para usar a predefinição [Transmissão Adaptável](media-services-autogen-bitrate-ladder-with-mes.md). 
* Adicione uma única tarefa de codificação para o trabalho. 
* Especifique o ativo de entrada a ser codificado.
* Crie um ativo de saída contendo o ativo codificado.
* Adicione um manipulador de eventos para verificar o progresso do trabalho.
* Enviar o trabalho.

#### <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto do Visual Studio

Configure seu ambiente de desenvolvimento e preencha o arquivo de configuração app.config com as informações de conexão, conforme descrito em [Desenvolvimento de Serviços de Mídia com o .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Exemplo 

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderStandardSample
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

            // Create a task with the encoding details, using a string preset.
            // In this case "Adaptive Streaming" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
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

## <a name="advanced-encoding-features-to-explore"></a>Recursos de Codificação Avançada para explorar
* [Como gerar miniaturas](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Gerar miniaturas durante a codificação](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Cortar vídeos durante a codificação](media-services-crop-video.md)
* [Personalizar as predefinições de codificação](media-services-custom-mes-presets-with-dotnet.md)
* [Sobrepor ou colocar uma imagem de marca d'água em um vídeo](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Próximas etapas
[Como gerar miniatura usando o Media Encoder Standard com o .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
[Visão geral de codificação dos Serviços de Mídia](media-services-encode-asset.md)

