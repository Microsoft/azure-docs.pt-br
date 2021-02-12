---
title: Codificar um REST de transformação personalizada
description: Este tópico mostra como usar os serviços de mídia do Azure V3 para codificar uma transformação personalizada usando REST.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 2c0416e2787bc69465e18a71f6b9912f7571c24d
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956082"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Como codificar com uma transformação personalizada-REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ao codificar com os serviços de mídia do Azure, você pode começar rapidamente com uma das predefinições internas recomendadas, com base nas práticas recomendadas do setor, conforme demonstrado no tutorial de [arquivos de streaming](stream-files-tutorial-with-rest.md#create-a-transform) . Você também pode criar uma predefinição personalizada para direcionar seus requisitos específicos de cenário ou dispositivo.

## <a name="considerations"></a>Considerações

Ao criar predefinições personalizadas, as seguintes considerações se aplicam:

* Todos os valores para altura e largura em conteúdo AVC devem ser um múltiplo de 4.
* Nos serviços de mídia do Azure v3, todas as taxas de bits de codificação estão em bits por segundo. Isso é diferente das predefinições com nossas APIs v2, que usavam kilobits/segundo como a unidade. Por exemplo, se a taxa de bits em v2 tiver sido especificada como 128 (kilobits/segundo), em v3, ela seria definida como 128000 (bits/segundo).

## <a name="prerequisites"></a>Pré-requisitos 

- [Crie uma conta de Serviços de Mídia](./create-account-howto.md). <br/>Lembre-se de lembrar o nome do grupo de recursos e o nome da conta do Media Services. 
- [Configurar o Postman para chamadas à API REST de Serviços de Mídia do Azure](media-rest-apis-with-postman.md).<br/>Certifique-se de seguir a última etapa no tópico [Obter token do Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Definir uma predefinição personalizada

O exemplo a seguir define o corpo da solicitação de uma nova transformação. Definimos um conjunto de saídas que desejamos gerar quando essa transformação é usada. 

Neste exemplo, primeiro adicionamos uma camada AacAudio para a codificação de áudio e duas camadas H264Video para a codificação de vídeo. Nas camadas de vídeo, atribuímos rótulos para que possam ser usados nos nomes de arquivo de saída. Em seguida, queremos que a saída também inclua miniaturas. No exemplo abaixo, especificamos as imagens no formato PNG, geradas a 50% da resolução do vídeo de entrada e em três carimbos de data/hora-{25%, 50%, 75} do comprimento do vídeo de entrada. Por fim, especificamos o formato dos arquivos de saída-um para vídeo + áudio e outro para as miniaturas. Como temos vários H264Layers, temos que usar macros que produzem nomes exclusivos por camada. Podemos usar uma `{Label}` `{Bitrate}` macro ou, o exemplo mostra o primeiro.

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

## <a name="create-a-new-transform"></a>Criar uma nova transformação  

Neste exemplo, criamos uma **transformação** com base na predefinição personalizada que definimos anteriormente. Ao criar uma transformação, você deve primeiro usar [Get](/rest/api/media/transforms/get) para verificar se já existe uma. Se a transformação existir, reutilize-a. 

Na coleção do postmaster que você baixou, selecione **transformações e trabalhos** -> **criar ou atualizar transformação**.

O método de solicitação HTTP **PUT** é semelhante a:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Selecione a guia **corpo** e substitua o corpo pelo código JSON que você [definiu anteriormente](#define-a-custom-preset). Para que os serviços de mídia apliquem a transformação ao vídeo ou áudio especificado, você precisa enviar um trabalho sob essa transformação.

Selecione **Enviar**. 

Para que os serviços de mídia apliquem a transformação ao vídeo ou áudio especificado, você precisa enviar um trabalho sob essa transformação. Para obter um exemplo completo que mostra como enviar um trabalho em uma transformação, consulte [tutorial: transmitir arquivos de vídeo-REST](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>Próximas etapas

Ver [outras operações REST](/rest/api/media/)
