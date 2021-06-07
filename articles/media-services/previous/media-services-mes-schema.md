---
title: Esquema Media Encoder Standard | Microsoft Docs
description: Este artigo descreve alguns dos elementos e tipos do esquema XML nos quais as predefinições do Media Encoder Standard se baseiam.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: f82e0c3f76dba05c3404b11e07c7130119ce0b9d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015651"
---
# <a name="media-encoder-standard-schema"></a>Esquema do Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Este artigo descreve alguns dos elementos e tipos do esquema XML nos quais as [predefinições do Media Encoder Standard](media-services-mes-presets-overview.md) se baseiam. O artigo fornece uma explicação sobre os elementos e seus valores válidos.  

## <a name="preset-root-element"></a><a name="Preset"></a> Predefinição (elemento raiz)
Define uma predefinição de codificação.  

### <a name="elements"></a>Elementos

| Nome | Type | Descrição |
| --- | --- | --- |
| **Codificação** |[Codificação](media-services-mes-schema.md#Encoding) |Elemento raiz, indica que as fontes de entrada devem ser codificadas. |
| **Saídas** |[Saídas](media-services-mes-schema.md#Output) |Coleção dos arquivos de saída desejados. |
| **StretchMode**<br/>minOccurs="0"<br/>default="AutoSize|xs:string|Controlar o tamanho do quadro de vídeo de saída, o preenchimento, o pixel ou a taxa de proporção de exibição. **StretchMode** poderia ser um dos seguintes valores: **None**, **AutoSize** (padrão) ou **AutoFit**.<br/><br/>**None**: segue estritamente a resolução de saída (por exemplo, **Largura** e **Altura** na predefinição) sem considerar a taxa de proporção de pixel ou a taxa de proporção de exibição do vídeo de entrada. Recomendado em cenários como [corte](media-services-crop-video.md), em que o vídeo de saída tem uma taxa de proporção diferente em comparação à entrada. <br/><br/>**AutoSize**: a resolução de saída será ajustada dentro do intervalo (Largura * Altura) especificado pela predefinição. No entanto, o codificador produz um vídeo de saída que tem a taxa de proporção de pixel quadrada (1:1). Portanto, a Largura ou a Altura da saída poderia ser substituída para coincidir com a taxa de proporção de exibição da entrada, sem preenchimento. Por exemplo, se a entrada for de 1920x1080 e a predefinição de codificação pedir 1280x1280, o valor da Altura na predefinição será substituído, e a saída será em 1280x720, o que mantém a taxa de proporção da entrada de 16:9. <br/><br/>**AutoFit**: se for necessário, preencha o vídeo de saída (com pillarbox ou letterbox) para respeitar a resolução de saída desejada, garantindo também que a região ativa do vídeo na saída tenha a mesma taxa de proporção que a entrada. Por exemplo, suponha que a entrada seja de 1920x1080 e a predefinição de codificação pede 1280x1280. O vídeo de saída será em 1280x1280, mas conterá um retângulo interno de 1280x720 de "vídeo ativo" com a taxa de proporção de 16:9 e regiões letterbox de 280 pixels de altura nas partes superior e inferior. Outro exemplo, se a entrada for 1440x1080 e a predefinição de codificação pedir 1280x720, a saída será em 1280x720, que contém um retângulo interno de 960x720 na taxa de proporção de 4:3 e regiões pillarbox de 160 pixels de largura nos lados esquerdo e direito. 

### <a name="attributes"></a>Atributos

| Nome | Type | Descrição |
| --- | --- | --- |
| **Versão**<br/><br/> Obrigatório |**xs: decimal** |A versão predefinida. As seguintes restrições se aplicam: xs:fractionDigits value="1" e xs:minInclusive value="1" Por exemplo, **version="1.0"**. |

## <a name="encoding"></a><a name="Encoding"></a> Mecanismo
Contém uma sequência dos elementos a seguir:  

### <a name="elements"></a>Elementos

| Nome | Type | Descrição |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Configurações de codificação de vídeo H.264. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Configurações de codificação de áudio AAC. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Configurações de imagem Bmp. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Configurações de imagem Png. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Configurações de imagem Jpg. |

## <a name="h264video"></a><a name="H264Video"></a> H264Video
### <a name="elements"></a>Elementos

| Nome | Type | Descrição |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs="0" |**xs:boolean** |Atualmente, há suporte apenas para a codificação em uma passo. |
| **KeyFrameInterval**<br/><br/> minOccurs="0"<br/><br/> **default="00:00:02"** |**xs:time** |Determina o espaçamento fixo entre quadros IDR em unidades de segundos. Também referido como a duração de GOP. Consulte **SceneChangeDetection** para controlar se o codificador pode desviar-se desse valor. |
| **SceneChangeDetection**<br/><br/> minOccurs="0"<br/><br/> default=”false” |**xs: boolean** |Se definido como true, codificador tenta detectar alteração de cena no vídeo e insere um quadro IDR. |
| **Complexidade**<br/><br/> minOccurs="0"<br/><br/> default="Balanced" |**xs:string** |Controla a compensação entre a qualidade de vídeo e a velocidade de codificação. Pode ser um dos seguintes valores: **velocidade**, **Equilibrado** ou **Qualidade**<br/><br/> Padrão: **Balanced** |
| **Syncmode**<br/><br/> minOccurs="0" | |O recurso será exposto em uma versão futura. |
| **H264Layers**<br/><br/> minOccurs="0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Coleção de camadas de vídeo de saída. |

### <a name="attributes"></a>Atributos

| Nome | Type | Descrição |
| --- | --- | --- |
| **Condição** |**xs:string** | Quando a entrada não tiver vídeo, talvez você queira forçar o codificador a inserir uma faixa de vídeo monocromática. Para fazer isso, use Condition = "InsertBlackIfNoVideoBottomLayerOnly" (para inserir um vídeo somente na taxa de bits mais baixa) ou Condition = "InsertBlackIfNoVideo" (para inserir um vídeo em todas as taxas de bits de saída). Para saber mais, confira [este artigo](media-services-advanced-encoding-with-mes.md#no_video).|

## <a name="h264layers"></a><a name="H264Layers"></a> H264Layers

Por padrão, se você enviar uma entrada para o codificador que contém apenas áudio e sem vídeo, o ativo de saída conterá apenas arquivos com dados de áudio. Alguns reprodutores podem não ser capazes de lidar com tais fluxos de saída. É possível utilizar a configuração de atributo **InsertBlackIfNoVideo** do H264Video para forçar o codificador a adicionar uma faixa de vídeo para a saída nesse cenário. Para saber mais, confira [este artigo](media-services-advanced-encoding-with-mes.md#no_video).
              
### <a name="elements"></a>Elementos

| Nome | Type | Descrição |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[H264Layer](media-services-mes-schema.md#H264Layer) |Uma coleção de camadas de H264. |

## <a name="h264layer"></a><a name="H264Layer"></a> H264Layer
> [!NOTE]
> Limites de vídeo se baseiam nos valores descritos na tabela [Níveis H264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels).  
> 
> 

### <a name="elements"></a>Elementos

| Nome | Type | Descrição |
| --- | --- | --- |
| **Perfil**<br/><br/> minOccurs="0"<br/><br/> default=”Auto” |**xs: string** |Pode ser de um dos seguintes valores **xs: string**: **Auto**, **Baseline**, **Main**, **High**. |
| **Level**<br/><br/> minOccurs="0"<br/><br/> default=”Auto” |**xs: string** | |
| **720p**<br/><br/> minOccurs="0" |**xs:int** |A taxa de bits usada para esta camada de vídeo, especificada em kbps. |
| **MaxBitrate**<br/><br/> minOccurs="0" |**xs: int** |A taxa de bits máxima usada para esta camada de vídeo, especificada em kbps. |
| **BufferWindow**<br/><br/> minOccurs="0"<br/><br/> default="00:00:05" |**xs: time** |Comprimento do buffer de vídeo. |
| **Largura**<br/><br/> minOccurs="0" |**xs: int** |Largura do quadro de vídeo de saída, em pixels.<br/><br/> No momento, você deve especificar ambos Width e Height. Width e Height devem ser números pares. |
| **Altura**<br/><br/> minOccurs="0" |**xs:int** |Altura do quadro de vídeo de saída, em pixels.<br/><br/> No momento, você deve especificar ambos Width e Height. Width e Height devem ser números pares.|
| **BFrames**<br/><br/> minOccurs="0" |**xs: int** |Número de quadros B entre quadros de referência. |
| **ReferenceFrames**<br/><br/> minOccurs="0"<br/><br/> default=”3” |**xs:int** |Número de quadros de referência em um GOP. |
| **EntropyMode**<br/><br/> minOccurs="0"<br/><br/> default=”Cabac” |**xs: string** |Poderia ser um dos seguintes valores: **Cabac** e **Cavlc**. |
| **FrameRate**<br/><br/> minOccurs="0" |número racional |Determina a taxa de quadros de vídeo de saída. Use o padrão de "0/1" para permitir que o codificador use a mesma taxa de quadros que o vídeo de entrada. Os valores permitidos devem ser taxas de quadros de vídeo comuns. No entanto, qualquer valor racional é permitido. Por exemplo, 1/1 seria 1 fps e é válido.<br/><br/> -12/1 (12 fps)<br/><br/> -15/1 (15 fps)<br/><br/> -24/1 (24 fps)<br/><br/> -24.000/1.001 (23.976 fps)<br/><br/> - 25/1 (25 fps)<br/><br/>  - 30/1 (30 fps)<br/><br/> - 30.000/1.001 (29,97 fps) <br/> <br/>**Observação** se estiver criando uma predefinição personalizada para codificação de múltiplas taxas de bits, todas as camadas da predefinição **deverão** usar o mesmo valor de FrameRate.|
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**xs: boolean** |Cópia do Codificador de Mídia do Azure |
| **Fatias**<br/><br/> minOccurs="0"<br/><br/> default="0" |**xs:int** |Determina em quantas fatias um quadro é dividido. É recomendável usar o padrão. |

## <a name="aacaudio"></a><a name="AACAudio"></a> AACAudio
 Contém uma sequência dos elementos e grupos a seguir.  

 Para saber mais sobre AAC, veja [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Elementos

| Nome | Type | Descrição |
| --- | --- | --- |
| **Perfil**<br/><br/> minOccurs="0 "<br/><br/> default="AACLC" |**xs: string** |Poderia ser um dos seguintes valores: **AACLC**, **HEAACV1** ou **HEAACV2**. |

### <a name="attributes"></a>Atributos

| Nome | Type | Descrição |
| --- | --- | --- |
| **Condição** |**xs: string** |Para forçar o codificador a produzir um ativo que contenha uma faixa de áudio silenciosa quando a entrada não tiver áudio, especifique o valor de "InsertSilenceIfNoAudio".<br/><br/> Por padrão, se você enviar uma entrada para o codificador que contenha apenas vídeo e sem áudio, o ativo de saída conterá os arquivos que contêm apenas dados de vídeo. Alguns reprodutores podem não ser capazes de lidar com tais fluxos de saída. Você pode usar essa configuração para forçar o codificador a adicionar uma faixa de áudio silenciosa à saída nesse cenário. |

### <a name="groups"></a>Grupos

| Referência | Descrição |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |Veja a descrição de [AudioGroup](media-services-mes-schema.md#AudioGroup) para saber o número apropriado de canais, a taxa de amostragem e a taxa de bits que podem ser definidas para cada perfil. |

## <a name="audiogroup"></a><a name="AudioGroup"></a> MyAudio
Para obter detalhes sobre quais valores são válidos para cada perfil, veja a tabela "Detalhes de codec de áudio" a seguir.  

### <a name="elements"></a>Elementos

| Nome | Type | Descrição |
| --- | --- | --- |
| **Canais**<br/><br/> minOccurs="0" |**xs: int** |O número de canais de áudio codificados. A seguir estão as opções válidas: 1, 2, 5, 6, 8.<br/><br/> Padrão: 2. |
| **SamplingRate**<br/><br/> minOccurs="0" |**xs: int** |A taxa de amostragem de áudio, especificada em Hz. |
| **720p**<br/><br/> minOccurs="0" |**xs: int** |A taxa de bits usada durante a codificação de áudio, especificada em kbps. |

### <a name="audio-codec-details"></a>Detalhes do codec de áudio

Codec de áudio|Detalhes  
-----------------|---  
**AACLC** |1:<br/><br/> - 11025: 8 &lt;= taxa de bits &lt; 16<br/><br/> - 12000: 8 &lt;= taxa de bits &lt; 16<br/><br/> - 16000: 8 &lt;= taxa de bits &lt;32<br/><br/>- 22050: 24 &lt;= taxa de bits &lt; 32<br/><br/> - 24000: 24 &lt;= taxa de bits &lt; 32<br/><br/> - 32000: 32 &lt;= taxa de bits &lt;= 192<br/><br/> - 44100: 56 &lt;= taxa de bits &lt;= 288<br/><br/> - 48000: 56 &lt;= taxa de bits &lt;= 288<br/><br/> - 88200 : 128 &lt;= taxa de bits &lt;= 288<br/><br/> - 96000 : 128 &lt;= taxa de bits &lt;= 288<br/><br/> 2:<br/><br/> - 11025: 16 &lt;= taxa de bits &lt; 24<br/><br/> - 12000: 16 &lt;= taxa de bits &lt; 24<br/><br/> - 16000: 16 &lt;= taxa de bits &lt; 40<br/><br/> - 22050: 32 &lt;= taxa de bits &lt; 40<br/><br/> - 24000 : 32 &lt;= taxa de bits &lt; 40<br/><br/> - 32000:  40 &lt;= taxa de bits &lt;= 384<br/><br/> - 44100: 96 &lt;= taxa de bits &lt;= 576<br/><br/> - 48000 : 96 &lt;= taxa de bits &lt;= 576<br/><br/> - 88200: 256 &lt;= taxa de bits &lt;= 576<br/><br/> - 96000: 256 &lt;= taxa de bits &lt;= 576<br/><br/> 5/6:<br/><br/> - 32000: 160 &lt;= taxa de bits &lt;= 896<br/><br/> - 44100: 240 &lt;= taxa de bits &lt;= 1024<br/><br/> - 48000: 240 &lt;= taxa de bits &lt;= 1024<br/><br/> - 88200: 640 &lt;= taxa de bits &lt;= 1024<br/><br/> - 96000: 640 &lt;= taxa de bits &lt;= 1024<br/><br/> 8:<br/><br/> - 32000 : 224 &lt;= taxa de bits &lt;= 1024<br/><br/> - 44100 : 384 &lt;= taxa de bits &lt;= 1024<br/><br/> - 48000: 384 &lt;= taxa de bits &lt;= 1024<br/><br/> - 88200: 896 &lt;= taxa de bits &lt;= 1024<br/><br/> - 96000: 896 &lt;= taxa de bits &lt;= 1024  
**HEAACV1** |1:<br/><br/> - 22050: taxa de bits = 8<br/><br/> - 24000: 8 &lt;= taxa de bits &lt;= 10<br/><br/> - 32000: 12 &lt;= taxa de bits &lt;= 64<br/><br/> - 44100: 20 &lt;= taxa de bits &lt;= 64<br/><br/> - 48000: 20 &lt;= taxa de bits &lt;= 64<br/><br/> - 88200: taxa de bits = 64<br/><br/> 2:<br/><br/> - 32000: 16 &lt;= taxa de bits &lt;= 128<br/><br/> - 44100: 16 &lt;= taxa de bits &lt;= 128<br/><br/> - 48000: 16 &lt;= taxa de bits &lt;= 128<br/><br/> - 88200 : 96 &lt;= taxa de bits &lt;= 128<br/><br/> - 96000: 96 &lt;= taxa de bits &lt;= 128<br/><br/> 5/6:<br/><br/> - 32000 : 64 &lt;= taxa de bits &lt;= 320<br/><br/> - 44100: 64 &lt;= taxa de bits &lt;= 320<br/><br/> - 48000: 64 &lt;= taxa de bits &lt;= 320<br/><br/> - 88200 : 256 &lt;= taxa de bits &lt;= 320<br/><br/> - 96000: 256 &lt;= taxa de bits &lt;= 320<br/><br/> 8:<br/><br/> - 32000: 96 &lt;= taxa de bits &lt;= 448<br/><br/> - 44100: 96 &lt;= taxa de bits &lt;= 448<br/><br/> - 48000: 96 &lt;= taxa de bits &lt;= 448<br/><br/> - 88200: 384 &lt;= taxa de bits &lt;= 448<br/><br/> - 96000: 384 &lt;= taxa de bits &lt;= 448  
**HEAACV2** |2:<br/><br/> - 22050: 8 &lt;= taxa de bits &lt;= 10<br/><br/> - 24000: 8 &lt;= taxa de bits &lt;= 10<br/><br/> - 32000: 12 &lt;= taxa de bits &lt;= 64<br/><br/> - 44100: 20 &lt;= taxa de bits &lt;= 64<br/><br/> - 48000: 20 &lt;= taxa de bits &lt;= 64<br/><br/> - 88200: 64 &lt;= taxa de bits &lt;= 64  
  
## <a name="clip"></a><a name="Clip"></a> Clipe
### <a name="attributes"></a>Atributos

| Nome | Type | Descrição |
| --- | --- | --- |
| **StartTime** |**xs:duration** |Especifica a hora de início de uma apresentação. O valor da StartTime precisa corresponder aos carimbos de hora absolutos do vídeo de entrada. Por exemplo, se o primeiro quadro de vídeo de entrada tem um carimbo de data/hora de 12:00:10.000, então, a StartTime deve ser pelo menos 12:00:10.000 ou maior. |
| **Duration** |**xs:duration** |Especifica a duração de uma apresentação (por exemplo, a aparência de uma sobreposição no vídeo). |

## <a name="output"></a><a name="Output"></a> Output
### <a name="attributes"></a>Atributos

| Nome | Type | Descrição |
| --- | --- | --- |
| **FileName** |**xs:string** |O nome do arquivo de saída.<br/><br/> Você pode usar as macros descritas na tabela a seguir para compilar os nomes dos arquivos de saída. Por exemplo:<br/><br/> **"Outputs": [      {       "FileName": "{Basename}*{Resolution}*{Bitrate}.mp4",       "Format": {         "Type": "MP4Format"       }     }   ]** |

### <a name="macros"></a>Macros

| Macro | Descrição |
| --- | --- |
| **{Basename}** |Se você estiver fazendo a codificação VoD, o {Basename} é composto pelos primeiros 32 caracteres da propriedade AssetFile.Name do arquivo primário no ativo de entrada.<br/><br/> Se o ativo de entrada for um arquivo em tempo real, o {Basename} será derivado dos atributos do trackName no manifesto do servidor. Se você estiver enviando um trabalho de subclipe usando TopBitrate como em “<VideoStream\>TopBitrate</VideoStream\>” e o arquivo de saída contiver vídeo, o {Basename} será composto pelos primeiros 32 caracteres do trackName da camada de vídeo com a taxa de bits mais alta.<br/><br/> Se, em vez disso, você estiver enviando um trabalho de subclipe usando todas as taxas de bits de entrada, como em "<VideoStream\>*</VideoStream\>" e o arquivo de saída contiver vídeo, o {Basename} será composto pelos primeiros 32 caracteres do trackName da camada de vídeo correspondente. |
| **Codec** |Mapeia "H264" para vídeo e "AAC" para áudio. |
| **720p** |A taxa de bits de vídeo de destino se o arquivo de saída contiver áudio e vídeo, ou então taxa de bits de áudio de destino se o arquivo de saída contiver somente áudio. O valor usado é a taxa de bits em kbps. |
| **Canaliza** |Contagem de canais de áudio se o arquivo contiver áudio. |
| **Das** |Largura do vídeo em pixels no arquivo de saída, se o arquivo contiver vídeo. |
| **Tamanho** |Altura do vídeo em pixels no arquivo de saída, se o arquivo contiver vídeo. |
| **Extensão** |Herda a propriedade "Type" para o arquivo de saída. O nome do arquivo de saída tem uma extensão que é uma entre: "mp4", "ts", "jpg", "png" ou "bmp". |
| **Index** |Obrigatório para a miniatura. Deve estar presente apenas uma vez. |

## <a name="video-complex-type-inherits-from-codec"></a><a name="Video"></a> Video (o tipo complexo herda do Codec)
### <a name="attributes"></a>Atributos

| Nome | Type | Descrição |
| --- | --- | --- |
| **Iniciar** |**xs:string** | |
| **Step** |**xs:string** | |
| **Intervalo** |**xs:string** | |
| **PreserveResolutionAfterRotation** |**xs:boolean** |Para obter explicações detalhadas, consulte a seção a seguir: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="preserveresolutionafterrotation"></a><a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
É recomendável usar o sinalizador **PreserveResolutionAfterRotation** em combinação com valores de resolução expressos em termos percentuais (Width="100%" , Height="100%").  

Por padrão, as configurações de resolução de codificação (Width, Height) em predefinições do MES (Media Encoder Standard) são destinadas a vídeos com 0 grau de rotação. Por exemplo, se o vídeo de entrada tiver as dimensões 1280x720 com zero grau de rotação, as predefinições padrão asseguram que a saída tem a mesma resolução.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Se o vídeo de entrada tiver sido capturado com rotação diferente de zero (por exemplo, um smartphone ou tablet segurado verticalmente), o MES aplicará por padrão as configurações de resolução de codificação (Width, Height) ao vídeo de entrada e, em seguida, compensar a rotação. Por exemplo, veja a figura a seguir. A predefinição usa Largura = "100%", Altura = "100%", que o MES interpreta como uma exigência de que a saída tenha 1280 pixels de largura e 720 pixels de altura. Após girar o vídeo, ele reduz a imagem para que ela caiba na janela, levando a áreas verticais vazias à esquerda e à direita.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Como alternativa, você pode fazer uso do sinalizador **PreserveResolutionAfterRotation** e defini-lo como "true" (o padrão é "false"). Então, se a predefinição tiver Largura = "100%", Altura = "100%" e PreserveResolutionAfterRotation definido como "true", um vídeo de entrada que tem 1280 pixels de largura e 720 pixels de altura com 90 graus de rotação produzirá uma saída com zero grau de rotação, mas 720 pixels de largura e 1280 pixels de altura. Confira a seguinte figura:  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="formatgroup-group"></a><a name="FormatGroup"></a> FormatGroup (grupo)
### <a name="elements"></a>Elementos

| Nome | Type | Descrição |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="bmplayer"></a><a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>Elemento

| Nome | Type | Descrição |
| --- | --- | --- |
| **Largura**<br/><br/> minOccurs="0" |**xs:int** | |
| **Altura**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Atributos

| Nome | Type | Descrição |
| --- | --- | --- |
| **Condição** |**xs:string** | |

## <a name="pnglayer"></a><a name="PngLayer"></a> PngLayer
### <a name="element"></a>Elemento

| Nome | Type | Descrição |
| --- | --- | --- |
| **Largura**<br/><br/> minOccurs="0" |**xs:int** | |
| **Altura**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Atributos

| Nome | Type | Descrição |
| --- | --- | --- |
| **Condição** |**xs:string** | |

## <a name="jpglayer"></a><a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>Elemento

| Nome | Type | Descrição |
| --- | --- | --- |
| **Largura**<br/><br/> minOccurs="0" |**xs:int** | |
| **Altura**<br/><br/> minOccurs="0" |**xs:int** | |
| **Qualidade**<br/><br/> minOccurs="0" |**xs:int** |Valores válidos: 1 (pior) – 100 (melhor) |

### <a name="attributes"></a>Atributos

| Nome | Type | Descrição |
| --- | --- | --- |
| **Condição** |**xs:string** | |

## <a name="pnglayers"></a><a name="PngLayers"></a> PngLayers
### <a name="elements"></a>Elementos

| Nome | Type | Descrição |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="bmplayers"></a><a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>Elementos

| Nome | Type | Descrição |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="jpglayers"></a><a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>Elementos

| Nome | Type | Descrição |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="bmpimage-complex-type-inherits-from-video"></a><a name="BmpImage"></a> BmpImage (o tipo complexo herda do Vídeo)
### <a name="elements"></a>Elementos

| Nome | Type | Descrição |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Camadas de Png |

## <a name="jpgimage-complex-type-inherits-from-video"></a><a name="JpgImage"></a> JpgImage (o tipo complexo herda do Vídeo)
### <a name="elements"></a>Elementos

| Nome | Type | Descrição |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Camadas de Png |

## <a name="pngimage-complex-type-inherits-from-video"></a><a name="PngImage"></a> PngImage (o tipo complexo herda do Vídeo)
### <a name="elements"></a>Elementos

| Nome | Type | Descrição |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Camadas de Png |

## <a name="examples"></a>Exemplos
Consulte exemplos de predefinições XML que são criadas com base neste esquema, veja [Predefinições de tarefa para MES (Media Encoder Standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

