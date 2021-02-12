---
title: Gerar miniaturas usando o Media Encoder Standard .NET
description: Este artigo mostra como usar o .NET para codificar um ativo e gerar miniaturas ao mesmo tempo usando Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/01/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 551574685ec9a2f9eb1ef569fbec7c9a104353ff
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955861"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-net"></a>Como gerar miniaturas usando o codificador padrão com .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Você pode usar Media Encoder Standard para gerar uma ou mais miniaturas do vídeo de entrada em formatos de arquivo de imagem [JPEG](https://en.wikipedia.org/wiki/JPEG) ou [png](https://en.wikipedia.org/wiki/Portable_Network_Graphics) .

## <a name="recommended-reading-and-practice"></a>Leitura e prática recomendadas

É recomendável que você se familiarize com transformações personalizadas lendo [como codificar com uma transformação personalizada-.net](customize-encoder-presets-how-to.md).

## <a name="transform-code-example"></a>Exemplo de código de transformação

O exemplo de código abaixo cria apenas uma miniatura.  Você deve definir os seguintes parâmetros:

- **Start** -a posição no vídeo de entrada de onde começar a gerar miniaturas. O valor pode estar no formato ISO 8601 (por exemplo, PT05S para iniciar em 5 segundos) ou uma contagem de quadros (por exemplo, 10 para iniciar no 10º quadro) ou um valor relativo para a duração do fluxo (por exemplo, 10% para iniciar com 10% da duração do fluxo). Também dá suporte a uma macro {Best}, que diz ao codificador para selecionar a melhor miniatura dos primeiros segundos do vídeo e produzir apenas uma miniatura, não importa quais são as outras configurações para Step e Range. O valor padrão é macro {Best}.
- **etapa** – os intervalos em que as miniaturas são geradas. O valor pode estar no formato ISO 8601 (por exemplo, PT05S para uma imagem a cada 5 segundos) ou uma contagem de quadros (por exemplo, 30 para uma imagem a cada 30 quadros) ou um valor relativo para a duração do fluxo (por exemplo, 10% para uma imagem a cada 10% da duração do fluxo). O valor da etapa afetará a primeira miniatura gerada, que pode não ser exatamente a especificada na hora de início da transformação predefinida. Isso ocorre devido ao codificador, que tenta selecionar a melhor miniatura entre a hora de início e a posição da etapa da hora de início como a primeira saída. Como o valor padrão é 10%, isso significa que, se o fluxo tiver duração longa, a primeira miniatura gerada poderá estar longe da especificada na hora de início. Tente selecionar um valor razoável para Step se a primeira miniatura for esperada próxima à hora de início ou defina o valor de intervalo como 1 se apenas uma miniatura for necessária na hora de início.
- **Range** -a posição relativa à transformação de hora de início predefinida no vídeo de entrada no qual parar de gerar miniaturas. O valor pode estar no formato ISO 8601 (por exemplo, PT5M30S para parar em 5 minutos e 30 segundos a partir da hora de início) ou uma contagem de quadros (por exemplo, 300 para parar no quadro 1/300 do quadro na hora de início. Se esse valor for 1, isso significa produzir apenas uma miniatura na hora de início) ou um valor relativo para a duração do fluxo (por exemplo, 50% para parar a metade da duração do fluxo da hora de início). O valor padrão é 100%, que significa parar no final do fluxo.
- **camadas** – uma coleção de camadas de imagem de saída a ser produzida pelo codificador.

```csharp

private static Transform EnsureTransformExists(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Create a new Transform Outputs array - this defines the set of outputs for the Transform
        TransformOutput[] outputs = new TransformOutput[]
        {
            // Create a new TransformOutput with a custom Standard Encoder Preset
            // This demonstrates how to create custom codec and layer output settings

          new TransformOutput(
                new StandardEncoderPreset(
                    codecs: new Codec[]
                    {
                        // Generate a set of PNG thumbnails
                        new PngImage(
                            start: "25%",
                            step: "25%",
                            range: "80%",
                            layers: new PngLayer[]{
                                new PngLayer(
                                    width: "50%",
                                    height: "50%"
                                )
                            }
                        )
                    },
                    // Specify the format for the output files for the thumbnails
                    formats: new Format[]
                    {
                        new PngFormat(
                            filenamePattern:"Thumbnail-{Basename}-{Index}{Extension}"
                        )
                    }
                ),
                onError: OnErrorType.StopProcessingJob,
                relativePriority: Priority.Normal
            )
        };

        string description = "A transform that includes thumbnails.";
        // Create the custom Transform with the outputs defined above
        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs, description);
    }

    return transform;
}
```

## <a name="next-steps"></a>Próximas etapas
[Gerar miniaturas usando REST](media-services-generate-thumbnails-rest.md)
