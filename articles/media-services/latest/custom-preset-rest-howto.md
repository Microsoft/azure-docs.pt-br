---
title: Codificar transformação personalizada usando os serviços de mídia v3 REST - Azure | Microsoft Docs
description: Este tópico mostra como usar os serviços de mídia do Azure v3 para codificar uma transformação personalizada usando REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: a9de15530981e14e664df605cb3274c9e754ef0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733021"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Como codificar com uma transformação personalizada - REST

Quando estiver codificando com os serviços de mídia do Azure, você pode começar a usar rapidamente com uma das predefinições internas recomendadas, com base em práticas recomendadas do setor, como demonstrado na [arquivos de Streaming](stream-files-tutorial-with-rest.md#create-a-transform) tutorial. Você também pode criar um personalizado predefinido para seus requisitos específicos de cenário ou dispositivo de destino.

## <a name="considerations"></a>Considerações

Ao criar predefinições personalizadas, as seguintes considerações se aplicam:

* Todos os valores de altura e largura no conteúdo de AVC devem ser um múltiplo de 4.
* Serviços de mídia do Azure v3, todas as taxas de bits de codifica são em bits por segundo. Isso é diferente das predefinições com nossas APIs v2, que usado quilobits por segundo, como a unidade. Por exemplo, se a taxa de bits na versão 2 foi especificada como 128 (quilobits/segundo), na v3 ele deve ser definido como 128000 (bits/segundo).

## <a name="prerequisites"></a>Pré-requisitos 

- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md). <br/>Lembre-se de lembrar o nome do grupo de recursos e o nome da conta do Media Services. 
- [Configurar o Postman para chamadas à API REST de Serviços de Mídia do Azure](media-rest-apis-with-postman.md).<br/>Certifique-se de seguir a última etapa no tópico [Obter token do Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Definir uma predefinição personalizada

O exemplo a seguir define o corpo da solicitação de uma nova transformação. Definimos um conjunto de saídas que queremos ser gerado quando essa transformação é usada. 

Neste exemplo, vamos adicionar uma camada de AacAudio para a codificação de áudio e duas camadas do H264Video para a codificação de vídeo. Nas camadas de vídeo, vamos atribuir rótulos para que eles podem ser usados nos nomes de arquivo de saída. Em seguida, queremos que a saída para incluir também as miniaturas. No exemplo a seguir, podemos especificar imagens no formato PNG, gerados em 50% da resolução de vídeo de entrada e em três carimbos de hora - {25%, 50%, 75} do comprimento do vídeo de entrada. Por fim, podemos especificar o formato para os arquivos de saída - um para vídeo + áudio e outro para as miniaturas. Como temos vários H264Layers, precisamos usar macros que geram nomes exclusivos por camada. Podemos pode usar um `{Label}` ou `{Bitrate}` macro, o exemplo mostra o antigo.

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.AacAudio",
                            "channels": 2,
                            "samplingRate": 48000,
                            "bitrate": 128000,
                            "profile": "AacLc"
                        },
                        {
                            "@odata.type": "#Microsoft.Media.H264Video",
                            "keyFrameInterval": "PT2S",
                            "stretchMode": "AutoSize",
                            "sceneChangeDetection": false,
                            "complexity": "Balanced",
                            "layers": [
                                {
                                    "width": "1280",
                                    "height": "720",
                                    "label": "HD",
                                    "bitrate": 3400000,
                                    "maxBitrate": 3400000,
                                    "bFrames": 3,
                                    "slices": 0,
                                    "adaptiveBFrame": true,
                                    "profile": "Auto",
                                    "level": "auto",
                                    "bufferWindow": "PT5S",
                                    "referenceFrames": 3,
                                    "entropyMode": "Cabac"
                                },
                                {
                                    "width": "640",
                                    "height": "360",
                                    "label": "SD",
                                    "bitrate": 1000000,
                                    "maxBitrate": 1000000,
                                    "bFrames": 3,
                                    "slices": 0,
                                    "adaptiveBFrame": true,
                                    "profile": "Auto",
                                    "level": "auto",
                                    "bufferWindow": "PT5S",
                                    "referenceFrames": 3,
                                    "entropyMode": "Cabac"
                                }
                            ]
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "25%",
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
                            "filenamePattern": "Video-{Basename}-{Label}{Extension}",
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

## <a name="create-a-new-transform"></a>Criar uma nova transformação  

Neste exemplo, podemos criar uma **transformar** que se baseia a predefinição personalizada que definimos anteriormente. Ao criar uma transformação, primeiro você deve usar [obter](https://docs.microsoft.com/rest/api/media/transforms/get) para verificar se já existe. Se existir a transformação, reutilizá-lo. 

Na coleção do Postman que você baixou, selecione **transforma e trabalhos**->**criação ou atualização transformar**.

O método de solicitação HTTP **PUT** é semelhante a:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Selecione o **corpo** guia e substitua o corpo json com o código que você [definidos anteriormente](#define-a-custom-preset). Serviços de mídia aplicar a transformação para o áudio ou vídeo especificado, você precisa enviar um trabalho sob essa transformação.

Selecione **Enviar**. 

Serviços de mídia aplicar a transformação para o áudio ou vídeo especificado, você precisa enviar um trabalho sob essa transformação. Para obter um exemplo completo que mostra como enviar um trabalho em uma transformação, consulte [Tutorial: Stream de arquivos de vídeo - REST](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>Próximas etapas

Consulte [outras operações REST](https://docs.microsoft.com/rest/api/media/)
