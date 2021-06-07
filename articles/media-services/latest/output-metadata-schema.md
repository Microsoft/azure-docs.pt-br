---
title: Esquema de metadados de saída dos serviços de mídia do Azure
description: Este artigo fornece uma visão geral do esquema de metadados de saída dos serviços de mídia do Azure v3.
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
ms.openlocfilehash: f26905366949c2c198e52f78bc7adb734cbb7f90
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612449"
---
# <a name="output-metadata"></a>Metadados de saída

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Um trabalho de codificação é associado um ativo (ou ativos) de entrada no qual você deseja executar algumas tarefas de codificação. Por exemplo, codificar um arquivo MP4 em conjuntos de taxa de bits adaptável MP4 H.264; criar uma miniatura; criar sobreposições. Após a conclusão de uma tarefa, um ativo de saída é produzido.  O ativo de saída contém vídeo, áudio, miniaturas e outros arquivos. O ativo de saída também contém um arquivo com metadados sobre o ativo de saída. O nome do arquivo JSON de metadados tem o seguinte formato: `<source_file_name>_manifest.json` (por exemplo, `BigBuckBunny_manifest.json` ). Você deve verificar qualquer * _metadata.jsem e consultar a cadeia de caracteres FilePath no para localizar o nome de arquivo de origem (sem truncamento).

Os serviços de mídia não examinam os ativos de entrada preventivamente para gerar metadados. Os metadados de entrada são gerados somente como um artefato quando um ativo de entrada é processado em um trabalho. Portanto, esse artefato é gravado no ativo de saída. Ferramentas diferentes são usadas para gerar metadados para ativos de entrada e ativos de saída. Portanto, os metadados de entrada têm um esquema um pouco diferente dos metadados de saída.

Este artigo aborda os elementos e tipos do esquema JSON nos quais os metadados de saída ( &lt; source_file_name &gt;_manifest.js) se baseiam. <!--For information about the file that contains metadata about the input asset, see [Input metadata](input-metadata-schema.md).  -->

Você pode encontrar o código de esquema completo e o exemplo de JSON no final deste artigo.  

## <a name="assetfile"></a>AssetFile

Coleção de entradas AssetFile do trabalho de codificação.  

| Nome | Descrição |
| --- | --- |
| **Fontes** |Coleção de arquivos de mídia de entrada/origem, que foi processada para produzir esse AssetFile.<br />Exemplo: `"Sources": [{"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"}]`|
| **VideoTracks**|Cada AssetFile físico pode conter zero ou mais faixas de vídeos intercaladas em um formato de contêiner apropriado. <br />Consulte [VideoTracks](#videotracks). |
| **AudioTracks**|Cada AssetFile físico pode conter zero ou mais faixas de áudio intercaladas em um formato de contêiner apropriado. Esta é a coleção de todas essas faixas de áudio.<br /> Para obter mais informações, consulte [AudioTracks](#audiotracks). |
| **Nome**<br />Obrigatório |O nome de arquivo de ativo de mídia. <br /><br />Exemplo: `"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"`|
| **Tamanho**<br />Obrigatório |Tamanho do arquivo de ativo em bytes. <br /><br />Exemplo: `"Size": 32414631`|
| **Duration**<br />Obrigatório |Duração da reprodução de conteúdo. Para obter mais informações, consulte o formato [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html) . <br /><br />Exemplo: `"Duration": "PT1M10.315S"`|

## <a name="videotracks"></a>VideoTracks 

Cada AssetFile físico pode conter zero ou mais faixas de vídeos intercaladas em um formato de contêiner apropriado. O elemento **VideoTracks** representa uma coleção de todas as faixas de vídeos.  

| Nome | Descrição |
| --- | --- |
| **Id**<br /> Obrigatório |Índice de base zero desta faixa de vídeo. **Observação:**  Essa **ID** não é necessariamente a TrackID como usada em um arquivo MP4. <br /><br />Exemplo: `"Id": 1`|
| **FourCC**<br />Obrigatório | Código FourCC do codec de vídeo relatado por ffmpeg.  <br /><br />Exemplo: `"FourCC": "avc1" | "hev1" | "hvc1"`|
| **Perfil** |Perfil de H264 (aplicável somente ao Codec H264)  <br /><br />Exemplo: `"Profile": "High"` |
| **Level** |Nível H264 (aplicável somente ao codec H264).  <br /><br />Exemplo: `"Level": "3.2"`|
| **Largura**<br />Obrigatório |Largura do vídeo codificado em pixels.  <br /><br />Exemplo: `"Width": "1280"`|
| **Altura**<br />Obrigatório |Altura do vídeo codificado em pixels.  <br /><br />Exemplo: `"Height": "720"`|
| **DisplayAspectRatioNumerator**<br />Obrigatório|Numerador de taxa de proporção de exibição do vídeo.  <br /><br />Exemplo: `"DisplayAspectRatioNumerator": 16.0`|
| **DisplayAspectRatioDenominator**<br />Obrigatório |Denominador de taxa de proporção de exibição do vídeo.  <br /><br />Exemplo: `"DisplayAspectRatioDenominator": 9.0`|
| **Quadros**<br />Obrigatório |Medida de taxa de quadros de vídeo em formato .3f.  <br /><br />Exemplo: `"Framerate": 29.970`|
| **720p**<br />Obrigatório |Taxa média de bits de vídeo em bits por segundo, calculada a partir do Assetfile. Conta apenas o conteúdo de transmissão elementar e não inclui a sobrecarga de empacotamento.  <br /><br />Exemplo: `"Bitrate": 3551567`|
| **TargetBitrate**<br />Obrigatório |Taxa de bits média de destino para esta faixa de vídeo, conforme solicitado por meio da predefinição de codificação, em bits por segundo. <br /><br />Exemplo: `"TargetBitrate": 3520000` |

## <a name="audiotracks"></a>AudioTracks 

Cada AssetFile físico pode conter zero ou mais faixas de áudio intercaladas em um formato de contêiner apropriado. O elemento **AudioTracks** representa uma coleção de todas essas faixas de áudio.  

| Nome  | Descrição |
| --- | --- |
| **Id**<br />Obrigatório  |Índice de base zero desta faixa de áudio. **Observação:**  Isso não é necessariamente o TrackID como usado em um arquivo MP4.  <br /><br />Exemplo: `"Id": 2`|
| **Codec**  |Cadeia de caracteres de codec de faixa de áudio.  <br /><br />Exemplo: `"Codec": "aac"`|
| **Idioma**|Exemplo: `"Language": "eng"`|
| **Canais**<br />Obrigatório|Número de canais de áudio.  <br /><br />Exemplo: `"Channels": 2`|
| **SamplingRate**<br />Obrigatório |Taxa de amostragem de áudio em amostras/s ou Hz.  <br /><br />Exemplo: `"SamplingRate": 48000`|
| **720p**<br />Obrigatório |Taxa média de bits de áudio em bits por segundo, calculada com base no AssetFile. Conta apenas o conteúdo de transmissão elementar e não inclui a sobrecarga de empacotamento.  <br /><br />Exemplo: `"Bitrate": 128041`|

## <a name="json-schema-example"></a>Exemplo de esquema JSON

```json
{
  "AssetFile": [
    {
      "Sources": [
        {
          "Name": "Ignite-short_1280x720_AACAudio_3551.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.2",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 3551567,
          "TargetBitrate": 3520000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_1280x720_AACAudio_3551.mp4",
      "Size": 32414631,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_960x540_AACAudio_2216.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.1",
          "Width": "960",
          "Height": "540",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 2216326,
          "TargetBitrate": 2210000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_960x540_AACAudio_2216.mp4",
      "Size": 20680897,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_640x360_AACAudio_1150.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.0",
          "Width": "640",
          "Height": "360",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 1150440,
          "TargetBitrate": 1150000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_640x360_AACAudio_1150.mp4",
      "Size": 11313920,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_480x270_AACAudio_722.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "2.1",
          "Width": "480",
          "Height": "270",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 722682,
          "TargetBitrate": 720000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_480x270_AACAudio_722.mp4",
      "Size": 7554708,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_320x180_AACAudio_380.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "1.3",
          "Width": "320",
          "Height": "180",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 380655,
          "TargetBitrate": 380000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }`
      ],
      "Name": "Ignite-short_320x180_AACAudio_380.mp4",
      "Size": 4548932,
      "Duration": "PT1M10.315S"
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

[Criar uma entrada de trabalho de uma URL HTTPS](job-input-from-http-how-to.md)
