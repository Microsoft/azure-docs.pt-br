---
title: Gerar miniaturas usando Media Encoder Standard REST
description: Este artigo mostra como usar REST para codificar um ativo e gerar miniaturas ao mesmo tempo usando Media Encoder Standard.
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
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: f4456a65f422c23da73f36dd74680fbb598db186
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955827"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-rest"></a>Como gerar miniaturas usando o codificador padrão com REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Você pode usar o Media Encoder Standard para gerar uma ou mais miniaturas de sua entrada de vídeo nos formatos de arquivo de imagem [JPEG](https://en.wikipedia.org/wiki/JPEG), [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) ou [BMP](https://en.wikipedia.org/wiki/BMP_file_format).

## <a name="recommended-reading-and-practice"></a>Leitura e prática recomendadas

É recomendável que você se familiarize com transformações personalizadas lendo [como codificar com um REST de transformação personalizado](custom-preset-rest-howto.md).

## <a name="thumbnail-parameters"></a>Parâmetros de miniatura

Você deve definir os seguintes parâmetros:

- **Start** -a posição no vídeo de entrada de onde começar a gerar miniaturas. O valor pode estar no formato ISO 8601 (por exemplo, PT05S para iniciar em 5 segundos) ou uma contagem de quadros (por exemplo, 10 para iniciar no 10º quadro) ou um valor relativo para a duração do fluxo (por exemplo, 10% para iniciar com 10% da duração do fluxo). Também dá suporte a uma macro {Best}, que diz ao codificador para selecionar a melhor miniatura dos primeiros segundos do vídeo e produzir apenas uma miniatura, não importa quais são as outras configurações para Step e Range. O valor padrão é macro {Best}.
- **etapa** – os intervalos em que as miniaturas são geradas. O valor pode estar no formato ISO 8601 (por exemplo, PT05S para uma imagem a cada 5 segundos) ou uma contagem de quadros (por exemplo, 30 para uma imagem a cada 30 quadros) ou um valor relativo para a duração do fluxo (por exemplo, 10% para uma imagem a cada 10% da duração do fluxo). O valor da etapa afetará a primeira miniatura gerada, que pode não ser exatamente a especificada na hora de início da transformação predefinida. Isso ocorre devido ao codificador, que tenta selecionar a melhor miniatura entre a hora de início e a posição da etapa da hora de início como a primeira saída. Como o valor padrão é 10%, isso significa que, se o fluxo tiver duração longa, a primeira miniatura gerada poderá estar longe da especificada na hora de início. Tente selecionar um valor razoável para Step se a primeira miniatura for esperada próxima à hora de início ou defina o valor de intervalo como 1 se apenas uma miniatura for necessária na hora de início.
- **Range** -a posição relativa à transformação de hora de início predefinida no vídeo de entrada no qual parar de gerar miniaturas. O valor pode estar no formato ISO 8601 (por exemplo, PT5M30S para parar em 5 minutos e 30 segundos a partir da hora de início) ou uma contagem de quadros (por exemplo, 300 para parar no quadro 1/300 do quadro na hora de início. Se esse valor for 1, isso significa produzir apenas uma miniatura na hora de início) ou um valor relativo para a duração do fluxo (por exemplo, 50% para parar a metade da duração do fluxo da hora de início). O valor padrão é 100%, que significa parar no final do fluxo.
- **camadas** – uma coleção de camadas de imagem de saída a ser produzida pelo codificador.

## <a name="example-of-a-single-png-file-preset"></a>Exemplo de uma predefinição de "único arquivo PNG"

A predefinição JSON a seguir pode ser usada para produzir um único arquivo PNG de saída dos primeiros segundos do vídeo de entrada, em que o codificador faz uma tentativa de melhor esforço na localização de um quadro "interessante". Observe que as dimensões da imagem de saída foram definidas como 100%, o que significa que correspondem às dimensões do vídeo de entrada. Observe também como a configuração de "Formato" em "Saídas" deve coincidir com o uso de "PngLayers" na seção "Codecs".  

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset for thumbnails",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "{Best}",
                            "step": "25%",
                            "range": "80%",
                            "layers": [
                                {
                                    "width": "50%",
                                    "height": "50%"
                                }
                            ]
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
                            "outputFiles": []
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngFormat",
                            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Exemplo de uma predefinição de uma “série de imagens JPEG”

A predefinição JSON a seguir pode ser usada para produzir um conjunto de 10 imagens em carimbos de data/hora de 5%, 15%,..., 95% da linha do tempo de entrada, em que o tamanho da imagem é especificado para ser um trimestre do vídeo de entrada.

### <a name="json-preset"></a>Predefinição JSON

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "25%",
          "Height": "25%"
        }
      ],
      "Start": "5%",
      "Step": "10%",
      "Range": "96%",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Exemplo de uma predefinição de “uma imagem em um carimbo de data/hora específico”

A predefinição JSON a seguir pode ser usada para produzir uma única imagem JPEG na marca de 30 segundos do vídeo de entrada. Essa predefinição espera que o vídeo de entrada tenha mais de 30 segundos de duração (caso contrário, haverá falha no trabalho).

### <a name="json-preset"></a>Predefinição JSON

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "25%",
          "Height": "25%"
        }
      ],
      "Start": "00:00:30",
      "Step": "1",
      "Range": "1",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Exemplo de uma predefinição de "miniaturas em diferentes resoluções"

A predefinição a seguir pode ser usada para gerar miniaturas com resoluções diferentes em uma tarefa. No exemplo, nas posições 5%, 15%, ..., 95% da linha do tempo de entrada, o codificador gera duas imagens – uma em 100% da resolução do vídeo de entrada e outra em 50%.

Observe o uso da macro {Resolution} no FileName. Isso faz com que o codificador use a largura e altura especificadas na seção Codificação da predefinição ao gerar o nome do arquivo das imagens de saída. Isso também ajuda você a distinguir entre as diferentes imagens facilmente.

### <a name="json-preset"></a>Predefinição JSON

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
{
  "Quality": 90,
  "Type": "JpgLayer",
  "Width": "100%",
  "Height": "100%"
},
{
  "Quality": 90,
  "Type": "JpgLayer",
  "Width": "50%",
  "Height": "50%"
}

      ],
      "Start": "5%",
      "Step": "10%",
      "Range": "96%",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
      "Format": {
"Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Exemplo de como gerar uma miniatura durante a codificação

Embora todos os exemplos acima tenham discutido como enviar uma tarefa de codificação que produz apenas imagens, também é possível combinar a codificação de vídeo ou áudio com a geração de miniaturas. A predefinição JSON a seguir informa ao codificador padrão para gerar uma miniatura durante a codificação.

### <a name="json-preset"></a>Predefinição JSON

Para obter informações sobre o esquema, consulte [este](../previous/media-services-mes-schema.md) artigo.

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "SceneChangeDetection": "true",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 4500,
          "MaxBitrate": 4500,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "ReferenceFrames": 3,
          "EntropyMode": "Cabac",
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"

        }
      ],
      "Type": "H264Video"
    },
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        }
      ],
      "Start": "{Best}",
      "Type": "JpgImage"
    },
    {
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 128,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    },
    {
      "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas
[Gerar miniaturas usando o .NET](media-services-generate-thumbnails-dotnet.md)