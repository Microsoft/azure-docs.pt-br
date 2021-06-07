---
title: Esquema de metadados de entrada dos serviços de mídia do Azure v3
description: Este artigo fornece uma visão geral do esquema de metadados de entrada dos serviços de mídia do Azure v3.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 0acb882410d103cf6f6c34bbecf2006094437b04
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102634678"
---
# <a name="input-metadata"></a>Metadados de entrada

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Um trabalho de codificação é associado um ativo (ou ativos) de entrada no qual você deseja executar algumas tarefas de codificação.  Após a conclusão de uma tarefa, um ativo de saída é produzido. O ativo de saída contém vídeo, áudio, miniaturas, manifesto e outros arquivos. 

O ativo de saída também contém um arquivo com metadados sobre o ativo de entrada. O nome do arquivo JSON de metadados tem uma ID aleatória, não use-o para identificar o ativo de entrada ao qual o ativo de saída pertence. Para identificar o ativo de entrada ao qual ele pertence, use o `Uri` campo (para obter mais informações, consulte [outros elementos filho](#other-child-elements)).  

Os serviços de mídia não examinam os ativos de entrada preventivamente para gerar metadados. Os metadados de entrada são gerados somente como um artefato quando um ativo de entrada é processado em um trabalho. Portanto, esse artefato é gravado no ativo de saída. Ferramentas diferentes são usadas para gerar metadados para ativos de entrada e ativos de saída. Portanto, os metadados de entrada têm um esquema um pouco diferente dos metadados de saída.

Este artigo aborda os elementos e tipos do esquema JSON no qual o metadados de entrada ( &lt; asset_id &gt;_metadata.js) é baseado. Para obter informações sobre o arquivo que contém metadados sobre o ativo de saída, consulte [metadados de saída](output-metadata-schema.md).  

Você pode encontrar o exemplo de esquema JSON no final deste artigo.  

## <a name="assetfile"></a>AssetFile  

Contém uma coleção de elementos AssetFile para o trabalho de codificação.  

> [!NOTE]
> Os quatro elementos filhos a seguir devem aparecer em uma sequência.

| Nome  | Descrição |
| --- | --- | 
| **VideoTracks**|Cada arquivo de ativo físico pode conter nenhuma ou mais faixas de vídeos intercaladas em um formato de contêiner apropriado. Para obter mais informações, consulte [VideoTracks](#videotracks). |
| **AudioTracks**|Cada arquivo de ativo físico pode conter nenhuma ou mais faixas de áudio intercaladas em um formato de contêiner apropriado. Para obter mais informações, consulte [AudioTracks](#audiotracks) |
| **Metadados**  |Metadados do arquivo de ativo representados como cadeias de caracteres de chave\valor. <br />Por exemplo: `<Metadata key="language" value="eng" />` |

### <a name="other-child-elements"></a>Outros elementos filho

| Nome | Descrição |
| --- | --- |
| **Nome**<br />Obrigatório |Nome do arquivo de ativo. <br /><br />Exemplo: `"Name": "Ignite-short.mp4"` |
| **Uri**<br />Obrigatório |A URL em que o ativo de entrada está localizado. Para identificar o ativo de entrada ao qual o ativo de saída pertence, use o `Uri` campo em vez de ID.|
| **Tamanho**<br />Obrigatório |Tamanho do arquivo de ativo em bytes.  <br /><br />Exemplo: `"Size": 75739259`|
| **Duration**<br />Obrigatório |Duração da reprodução de conteúdo. <br /><br />Exemplo: `"Duration": "PT1M10.304S"`. |
| **NumberOfStreams**<br />Obrigatório |Número de fluxos no arquivo de ativo.  <br /><br />Exemplo: `"NumberOfStreams": 2`|
| **FormatNames**<br />Obrigatório |Nomes de formato.  <br /><br />Exemplo: `"FormatNames": "mov,mp4,m4a,3gp,3g2,mj2"`|
| **FormatVerboseName**<br /> Obrigatório |Nomes detalhados de formato. <br /><br />Exemplo: `"FormatVerboseName": "QuickTime / MOV"` |
| **StartTime** |Hora de início do conteúdo.  <br /><br />Exemplo: `"StartTime": "PT0S"` |
| **OverallBitRate** |Taxa de bits média do arquivo de ativo em bits por segundo.  <br /><br />Exemplo: `"OverallBitRate": 8618539`|

## <a name="videotracks"></a>VideoTracks

| Nome | Descrição |
| --- | --- |
| **FourCC**<br />Obrigatório |Código FourCC do codec de vídeo relatado por ffmpeg.<br /><br />Exemplo: `"FourCC": "avc1" | "hev1" | "hvc1"` |
| **Perfil** |Perfil da faixa de vídeo. <br /><br />Exemplo: `"Profile": "Main"`|
| **Level** |Nível da faixa de vídeo. <br /><br />Exemplo: `"Level": "3.2"`|
| **PixelFormat** |Formato de pixel da faixa de vídeo. <br /><br />Exemplo: `"PixelFormat": "yuv420p"`|
| **Largura**<br />Obrigatório |Largura do vídeo codificado em pixels. <br /><br />Exemplo: `"Width": "1280"`|
| **Altura**<br />Obrigatório |Altura do vídeo codificado em pixels.<br /><br />Exemplo: `"Height": "720"` |
| **DisplayAspectRatioNumerator**<br />Obrigatório |Numerador de taxa de proporção de exibição do vídeo.<br /><br />Exemplo: `"DisplayAspectRatioNumerator": 16.0` |
| **DisplayAspectRatioDenominator**<br />Obrigatório |Denominador de taxa de proporção de exibição do vídeo. <br /><br />Exemplo: `"DisplayAspectRatioDenominator": 9.0`|
| **SampleAspectRatioNumerator** |Numerador de proporção de amostra de vídeo. <br /><br />Exemplo: `"SampleAspectRatioNumerator": 1.0`|
| **SampleAspectRatioDenominator**|Exemplo: `"SampleAspectRatioDenominator": 1.0`|
| **FrameRate**<br />Obrigatório |Medida de taxa de quadros de vídeo em formato .3f. <br /><br />Exemplo: `"FrameRate": 29.970`|
| **720p** |Taxa média de bits de vídeo em bits por segundo, calculada a partir do arquivo de ativo. Apenas a carga de fluxo elementar é contada, e a sobrecarga de empacotamento não está incluída. <br /><br />Exemplo: `"Bitrate": 8421583`|
| **HasBFrames** |Número de faixas de vídeo de quadros B. <br /><br />Exemplo: `"HasBFrames": 2`|
| **Metadados** |As cadeias de caracteres de chave/valor genéricas que podem ser usadas para armazenar uma variedade de informações. <br />Consulte o exemplo completo no final do artigo. |
| **Id**<br />Obrigatório |Índice baseado em zero da faixa de áudio ou de vídeo.<br /><br /> Essa **ID** não é necessariamente o TrackID assim como utilizado em um arquivo MP4. <br /><br />Exemplo: `"Id": 2`|
| **Codec** |Cadeia de caracteres de codec de faixa de vídeo. <br /><br />Exemplo: `"Codec": "h264 | hev1"`|
| **CodecLongName** |Nome longo de codec de faixa de áudio ou vídeo. <br /><br />Exemplo: `"CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10"`|
| **Codec** |Cadeia de caracteres de codec de faixa de vídeo. <br /><br />Exemplo: `"Codec": "h264 | hev1"`|
| **TimeBase**<br />Obrigatório |Base de tempo.<br /><br />Exemplo: `"TimeBase": "1/30000"`|
| **NumberOfFrames** |Número de quadros (presentes em faixas de vídeo). <br /><br />Exemplo: `"NumberOfFrames": 2107`|
| **StartTime** |Hora de início da faixa.<br /><br />Exemplo: `"StartTime": "PT0.033S"` |
| **Duration** |Duração da faixa. <br /><br />Exemplo: `"Duration": "PT1M10.304S"`|

## <a name="audiotracks"></a>AudioTracks

| Nome  | Descrição |
| --- | --- | 
| **SampleFormat** |Formato de exemplo. <br /><br />Exemplo: `"SampleFormat": "fltp"`|
| **ChannelLayout** |Layout do canal. <br /><br />Exemplo: `"ChannelLayout": "stereo"`|
| **Canais**<br />Obrigatório |Número (0 ou mais) de canais de áudio. <br /><br />Exemplo: `"Channels": 2`|
| **SamplingRate**<br />Obrigatório |Taxa de amostragem de áudio em amostras/s ou Hz. <br /><br />Exemplo: `"SamplingRate": 48000`|
| **720p** |Taxa média de bits de áudio em bits por segundo, calculada com base no arquivo de ativo. Apenas a carga de fluxo elementar é contada, e a sobrecarga de empacotamento não está incluída nesta contagem. <br /><br />Exemplo: `"Bitrate": 192080`|
| **Metadados** |As cadeias de caracteres de chave/valor genéricas que podem ser usadas para armazenar uma variedade de informações.  <br />Consulte o exemplo completo no final do artigo. |
| **Id**<br />Obrigatório |Índice baseado em zero da faixa de áudio ou de vídeo.<br /><br /> Essa não é necessariamente a TrackID como usada em um arquivo MP4. <br /><br />Exemplo: `"Id": 1`|
| **Codec** |Cadeia de caracteres de codec de faixa de vídeo. <br /><br />Exemplo: `"Codec": "aac"`|
| **CodecLongName** |Nome longo de codec de faixa de áudio ou vídeo. <br /><br />Exemplo: `"CodecLongName": "AAC (Advanced Audio Coding)"`|
| **TimeBase**<br />Obrigatório |Base de tempo.<br /><br />Exemplo: `"TimeBase": "1/48000"` |
| **NumberOfFrames** |Número de quadros (presentes em faixas de vídeo). <br /><br />Exemplo: `"NumberOfFrames": 3294`|
| **StartTime** |Hora de início da faixa. Para obter mais informações, consulte [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html). <br /><br />Exemplo: `"StartTime": "PT0S"` |
| **Duration** |Duração da faixa. <br /><br />Exemplo: `"Duration": "PT1M10.272S"` |

## <a name="metadata"></a>Metadados

| Nome | Descrição |
| --- | --- |
| **chave**<br />Obrigatório |A chave do par chave/valor. |
| **value**<br /> Obrigatório |O valor no par chave/valor. |

## <a name="schema-example"></a>Exemplo de esquema

```json
{
  "AssetFile": [
    {
      "VideoTracks": [
        {
          "FourCC": "avc1",
          "Profile": "Main",
          "Level": "3.2",
          "PixelFormat": "yuv420p",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "SampleAspectRatioNumerator": 1.0,
          "SampleAspectRatioNumeratorSpecified": true,
          "SampleAspectRatioDenominator": 1.0,
          "SampleAspectRatioDenominatorSpecified": true,
          "FrameRate": 29.970,
          "Bitrate": 8421583,
          "BitrateSpecified": true,
          "HasBFrames": 2,
          "HasBFramesSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Video Media Handler"
            },
            {
              "key": "encoder",
              "value": "AVC Coding"
            }
          ],
          "Id": 2,
          "Codec": "h264",
          "CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10",
          "TimeBase": "1/30000",
          "NumberOfFrames": 2107,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0.033S",
          "Duration": "PT1M10.304S"
        }
      ],
      "AudioTracks": [
        {
          "SampleFormat": "fltp",
          "ChannelLayout": "stereo",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192080,
          "BitrateSpecified": true,
          "BitsPerSampleSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Sound Media Handler"
            }
          ],
          "Id": 1,
          "Codec": "aac",
          "CodecLongName": "AAC (Advanced Audio Coding)",
          "TimeBase": "1/48000",
          "NumberOfFrames": 3294,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0S",
          "Duration": "PT1M10.272S"
        }
      ],
      "Metadata": [
        {
          "key": "major_brand",
          "value": "mp42"
        },
        {
          "key": "minor_version",
          "value": "19529854"
        },
        {
          "key": "compatible_brands",
          "value": "mp42isom"
        },
        {
          "key": "creation_time",
          "value": "2018-02-21T21:42:08.000000Z"
        }
      ],
      "Name": "Ignite-short.mp4",
      "Uri": "https://amsstorageacct.blob.core.windows.net/asset-00000000-0000-0000-000000000000/ignite.mp4",
      "Size": 75739259,
      "Duration": "PT1M10.304S",
      "NumberOfStreams": 2,
      "FormatNames": "mov,mp4,m4a,3gp,3g2,mj2",
      "FormatVerboseName": "QuickTime / MOV",
      "StartTime": "PT0S",
      "OverallBitRate": 8618539,
      "OverallBitRateSpecified": true
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

[Metadados de saída](output-metadata-schema.md)
