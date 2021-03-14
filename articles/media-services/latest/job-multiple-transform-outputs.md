---
title: Criar um trabalho com várias saídas de transformação
description: Este tópico demonstra como criar um trabalho dos serviços de mídia do Azure com várias saídas de transformação.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: fc3ab64a35c24df2af9d90a5ad348f840610d188
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102635834"
---
# <a name="create-a-job-with-multiple-transform-outputs"></a>Criar um trabalho com várias saídas de transformação

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este tópico mostra como criar uma transformação com duas saídas de transformação. A primeira chama a entrada a ser codificada para streaming de taxa de bits adaptável com uma predefinição [AdaptiveStreaming](encoding-concept.md#builtinstandardencoderpreset) interna. A segunda chamada para o sinal de áudio no vídeo de entrada a ser processada com o [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md#built-in-presets). Depois que a transformação for criada, você poderá enviar um trabalho que processará seu vídeo de forma adequada. Como neste exemplo estamos especificando duas saídas de transformação, devemos especificar duas saídas de trabalho. Você pode optar por direcionar as saídas de trabalho para o mesmo ativo (como mostrado abaixo) ou pode fazer com que os resultados sejam gravados em ativos separados.

> [!TIP]
> Antes de começar a desenvolver, examine o [desenvolvimento com as APIs dos serviços de mídia v3](media-services-apis-overview.md) (inclui informações sobre como acessar APIs, convenções de nomenclatura, etc.)

## <a name="create-a-transform"></a>Criar uma transformação

O código a seguir mostra como criar uma transformação que produz duas saídas.

```csharp
private static async Task<Transform> GetOrCreateTransformAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = await client.Transforms.GetAsync(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // You need to specify what you want it to produce as an output
        TransformOutput[] output = new TransformOutput[]
        {
            new TransformOutput
            {
                Preset = new BuiltInStandardEncoderPreset()
                {
                    // This sample uses the built-in encoding preset for Adaptive Bitrate Streaming.
                    PresetName = EncoderNamedPreset.AdaptiveStreaming
                }
            },
            // Create an analyzer preset with video insights.
            new TransformOutput(new AudioAnalyzerPreset("en-US"))
        };

        // Create the Transform with the output defined above
        transform = await client.Transforms.CreateOrUpdateAsync(resourceGroupName, accountName, transformName, output);
    }

    return transform;
}
```

## <a name="submit-a-job"></a>Enviar um trabalho

Crie um trabalho com uma entrada de URL HTTPS e com duas saídas de trabalho.

```csharp
private static async Task<Job> SubmitJobAsync(IAzureMediaServicesClient client,
    string resourceGroup,
    string accountName,
    string transformName)
{
    // Output from the encoding Job must be written to an Asset, so let's create one
    string outputAssetName1 = $"output-" + Guid.NewGuid().ToString("N");
    Asset outputAsset = await client.Assets.CreateOrUpdateAsync(resourceGroup, accountName, outputAssetName1, new Asset());

    // This example shows how to encode from any HTTPs source URL - a new feature of the v3 API.  
    // Change the URL to any accessible HTTPs URL or SAS URL from Azure.
    JobInputHttp jobInput =
        new JobInputHttp(files: new[] { "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4" });

    JobOutput[] jobOutputs =
    {
        // Since we are specifying two Transform Outputs, two Job Outputs are needed.
        // In this example, the first Job Output is for the results from adaptive bitrate encoding,
        // and the second is for the results from audio analysis. In this example, both are written to the
        // same output Asset. Or, you can specify different Assets.
        
        new JobOutputAsset(outputAsset.Name),
        new JobOutputAsset(outputAsset.Name)

    };

    // In this example, we are using a unique job name.
    //
    // If you already have a job with the desired name, use the Jobs.Get method
    // to get the existing job. In Media Services v3, Get methods on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).
    Job job;
    try
    {
        string jobName = $"job-" + Guid.NewGuid().ToString("N");
        job = await client.Jobs.CreateAsync(
                    resourceGroup,
                    accountName,
                    transformName,
                    jobName,
                    new Job
                    {
                        Input = jobInput,
                        Outputs = jobOutputs,
                    });
    }
    catch (Exception exception)
    {
        if (exception.GetBaseException() is ApiErrorException apiException)
        {
            Console.Error.WriteLine(
                $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
        }
        throw exception;
    }

    return job;
}
```
## <a name="job-error-codes"></a>Códigos de erro do trabalho

Confira [Códigos de erro](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Próximas etapas

[Exemplos de serviços de mídia do Azure v3 usando .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/) 
