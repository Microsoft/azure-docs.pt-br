---
title: Detectar movimentos com o Azure Media Analytics | Microsoft Docs
description: O MP (processador de mídia) Azure Media Motion Detector permite a identificação eficiente de seções de interesse em um vídeo longo e rotineiro.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/19/2019
ms.author: milanga;juliako;
ms.openlocfilehash: e0b083cba575f4d1c0eb19afb76fca29431ae75e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463524"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Detectar movimentos com o Azure Media Analytics
## <a name="overview"></a>Visão geral
O MP (processador de mídia) **Azure Media Motion Detector** permite a identificação eficiente de seções de interesse em um vídeo longo e rotineiro. A detecção de movimento pode ser usada em sequências de imagens estáticas para identificar seções do vídeo onde ocorrem movimentos. Ela gera um arquivo JSON contendo metadados com carimbos de hora e a região delimitadora onde o evento ocorreu.

Essa tecnologia, destinada à segurança de feeds de vídeo, é capaz de categorizar o movimento em eventos relevantes e falsos positivos, como mudanças de iluminação e sombras. Isso permite a geração de alertas de segurança por meio de feeds da câmera, sem gerar incontáveis eventos irrelevantes, além de permitir a extração de momentos de interesse dos vídeos de vigilância longos.

No momento, o MP **Azure Media Motion Detector** está em versão de Visualização.

Este artigo fornece detalhes sobre o **Azure Media Motion Detector** e mostra como usá-lo com o SDK dos Serviços de Mídia para .NET

## <a name="motion-detector-input-files"></a>Arquivos de entrada do Motion Detector
Arquivos de vídeo. Atualmente, há suporte para os formatos a seguir: MP4, MOV e WMV.

## <a name="task-configuration-preset"></a>Configuração de tarefa (predefinição)
Quando você criar uma tarefa com o **Azure Media Motion Detector**, deverá especificar uma predefinição de configuração. 

### <a name="parameters"></a>parâmetros
Você pode usar os seguintes parâmetros:

| NOME | Opções | DESCRIÇÃO | Padrão |
| --- | --- | --- | --- |
| sensitivityLevel |Cadeia de caracteres: 'low', 'medium', 'high' |Define o nível de sensibilidade ao qual os movimentos são relatados. Ajuste para ajustar o número de falsos positivos. |'medium' |
| frameSamplingValue |Número inteiro positivo |Define a frequência na qual o algoritmo é executado. 1 é igual a cada quadro, 2 significa cada segundo quadro, e assim por diante. |1 |
| detectLightChange |Booliano: 'true', 'false' |Define se as mudanças leves são relatadas nos resultados |'False' |
| mergeTimeThreshold |Xs-time: Hh:mm:ss<br/>Exemplo: 00:00:03 |Especifica a janela de tempo entre eventos de movimento, em que 2 eventos são combinados e relatados como 1. |00:00:00 |
| detectionZones |Uma matriz de zonas de detecção:<br/>- A Zona de Detecção é uma matriz de três ou mais pontos<br/>- Ponto é uma coordenada x e y de 0 a 1. |Descreve a lista de zonas de detecção em forma de polígono a ser usada.<br/>Os resultados são informados com as zonas como uma ID, com o primeiro sendo 'id': 0 |Zona única que abrange todo o quadro. |

### <a name="json-example"></a>Exemplo de JSON

```json
    {
      "version": "1.0",
      "options": {
        "sensitivityLevel": "medium",
        "frameSamplingValue": 1,
        "detectLightChange": "False",
        "mergeTimeThreshold":
        "00:00:02",
        "detectionZones": [
          [
            {"x": 0, "y": 0},
            {"x": 0.5, "y": 0},
            {"x": 0, "y": 1}
           ],
          [
            {"x": 0.3, "y": 0.3},
            {"x": 0.55, "y": 0.3},
            {"x": 0.8, "y": 0.3},
            {"x": 0.8, "y": 0.55},
            {"x": 0.8, "y": 0.8},
            {"x": 0.55, "y": 0.8},
            {"x": 0.3, "y": 0.8},
            {"x": 0.3, "y": 0.55}
          ]
        ]
      }
    }
```

## <a name="motion-detector-output-files"></a>Arquivos de saída do Motion Detector
Um trabalho de detecção de movimento retorna um arquivo JSON no recurso de saída, que descreve os alertas de movimento e suas categorias dentro do vídeo. O arquivo contém informações sobre o tempo e a duração do movimento detectado no vídeo.

A API do Motion Detector fornecerá indicadores quando houver objetos em movimento em um vídeo fixo em segundo plano (por exemplo, um vídeo de vigilância). O Motion Detector é treinado para reduzir alarmes falsos, como mudanças de iluminação e de sombra. As limitações atuais dos algoritmos incluem vídeos de visão noturna, objetos semitransparentes e objetos pequenos.

### <a id="output_elements"></a>Elementos do arquivo JSON de saída
> [!NOTE]
> Na versão mais recente, o formato JSON de saída foi alterado e pode representar uma alteração significativa para alguns clientes.
> 
> 

A tabela a seguir descreve os elementos do arquivo JSON de saída:

| Elemento | DESCRIÇÃO |
| --- | --- |
| Version |Refere-se à versão da API de Vídeo. A versão atual é 2. |
| Timescale |"Tiques" por segundo do vídeo. |
| Offset |A diferença de horário para carimbos de data/hora em "tiques." Na versão 1.0 das APIs de Vídeo, sempre será 0. Em cenários futuro para os quais oferecemos suporte, esse valor poderá ser alterado. |
| Framerate |Quadros por segundo do vídeo. |
| Width, Height |Refere-se à largura e à altura do vídeo em pixels. |
| Start |O carimbo de hora inicial em "tiques". |
| Duration |A duração do evento, em "tiques". |
| Interval |O intervalo de cada entrada no evento, em "tiques". |
| Events |Cada fragmento de evento contém o movimento detectado dentro dessa duração. |
| Type |Na versão atual, essa opção sempre será “2” para movimentos genéricos. Esse rótulo dá a flexibilidade às APIs de Vídeo para categorizar o movimento em futuras versões. |
| RegionID |Conforme explicado acima, isso sempre será 0 nesta versão. Esse rótulo oferece à API de Vídeo a flexibilidade de encontrar o movimento em várias regiões em versões futuras. |
| Regions |Refere-se à área no vídeo onde você se preocupa com movimento. <br/><br/>-"id" representa a área de região – nesta versão há apenas uma, ID 0. <br/>-"type" representa a forma da região em que você se preocupa com o movimento. Atualmente, "retângulo" e "polígono" têm suporte.<br/> Se você tiver especificado "retângulo", a região terá dimensões em X, Y, largura e altura. As coordenadas X e Y representam as coordenadas XY do lado superior esquerdo da região em uma escala normalizada de 0,0 a 1,0. A largura e a altura representam o tamanho da região em uma escala normalizada de 0,0 a 1,0. Na versão atual, X, Y, largura e altura são sempre fixos em 0, 0 e 1, 1. <br/>Se você tiver especificado "polígono", a região terá dimensões em pontos. <br/> |
| Fragments |Os metadados são agrupados em segmentos diferentes, chamados fragmentos. Cada fragmento contém um início, uma duração, um número de intervalo e evento(s). Um fragmento sem eventos significa que nenhum movimento foi detectado durante essa hora de início e duração. |
| Colchetes [] |Cada colchete representa um intervalo no evento. Colchetes vazios para esse intervalo significam que nenhum movimento foi detectado. |
| locations |Essa nova entrada em eventos lista o local onde ocorreu o movimento. Isso é mais específico do que as zonas de detecção. |

O seguinte exemplo JSON mostra a saída:

```json
    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
```

## <a name="limitations"></a>Limitações
* Os formatos de vídeo de entrada com suporte incluem MP4, MOV e WMV.
* A detecção de movimento é otimizada para vídeos estáticos em segundo plano. O algoritmo se concentra na redução de alarmes falsos, como mudanças de iluminação e sombras.
* Talvez algum movimento não seja detectado devido a desafios técnicos; por exemplo, vídeos de visão noturna, objetos semitransparentes e objetos pequenos.

## <a name="net-sample-code"></a>Código de exemplo do .NET

O programa a seguir mostra como:

1. Criar um ativo e carregar um arquivo de mídia nesse ativo.
2. Crie um trabalho com uma tarefa de detecção de movimento em vídeo baseada em um arquivo de configuração que contém a predefinição de JSON a seguir: 
   
    ```json
            {
            "Version": "1.0",
            "Options": {
                "SensitivityLevel": "medium",
                "FrameSamplingValue": 1,
                "DetectLightChange": "False",
                "MergeTimeThreshold":
                "00:00:02",
                "DetectionZones": [
                [
                    {"x": 0, "y": 0},
                    {"x": 0.5, "y": 0},
                    {"x": 0, "y": 1}
                ],
                [
                    {"x": 0.3, "y": 0.3},
                    {"x": 0.55, "y": 0.3},
                    {"x": 0.8, "y": 0.3},
                    {"x": 0.8, "y": 0.55},
                    {"x": 0.8, "y": 0.8},
                    {"x": 0.55, "y": 0.8},
                    {"x": 0.3, "y": 0.8},
                    {"x": 0.3, "y": 0.55}
                ]
                ]
            }
            }
    ```

3. Baixe os arquivos JSON de saída. 

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

namespace VideoMotionDetection
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

            // Run the VideoMotionDetection job.
            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\VideoMotionDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
        }

        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Video Motion Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Video Motion Detection Job");

            // Get a reference to Azure Media Motion Detector.
            string MediaProcessorName = "Azure Media Motion Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Video Motion Detection Output Asset", AssetCreationOptions.None);

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
[Blog do Detector de movimento dos Serviços de Mídia do Azure](https://azure.microsoft.com/blog/motion-detector-update/)

[Visão geral do Azure Media Services Analytics](media-services-analytics-overview.md)

[Demonstrações do Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

