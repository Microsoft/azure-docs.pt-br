---
title: Empacotamento dinâmico nos Serviços de Mídia do Azure v3
description: Este artigo apresenta uma visão geral do empacotamento dinâmico nos Serviços de Mídia do Azure.
author: myoungerman
manager: femila
services: media-services
ms.service: media-services
ms.workload: media
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: c2ffda7400109e16cf1110a4e14ecbc3604a7ecf
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492300"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Empacotamento dinâmico nos Serviços de Mídia v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Os Serviços de Mídia do Microsoft Azure servem para codificar vários formatos de arquivo de origem de mídia. O recurso é fornecido por meio de diferentes protocolos de streaming, com ou sem proteção de conteúdo, para alcançar todos os principais dispositivos, como iOS e Android. Esses clientes entendem os diferentes protocolos. Por exemplo, o iOS exige que os fluxos sejam entregues no formato HLS (HTTP Live Streaming), ao passo que os dispositivos Android dão suporte a HLS e a MPEG-DASH.

Nos Serviços de Mídia, um [ponto de extremidade de streaming](stream-streaming-endpoint-concept.md) (origem) representa um serviço de origem e empacotamento dinâmico (just-in-time) que pode enviar conteúdo ativo e sob demanda diretamente para um aplicativo de player do cliente. Ele usa um dos protocolos de streaming de mídia comuns mencionados na seção a seguir. O *Empacotamento dinâmico* é um recurso que vem por padrão em todos os pontos de extremidade de streaming.

## <a name="to-prepare-your-source-files-for-delivery"></a>Preparar os arquivos de origem para entrega

Para aproveitar o empacotamento dinâmico, é preciso [codificar](encode-concept.md) o arquivo (de origem) mezanino em um conjunto de arquivos MP4 de taxa de bits (ISO Base Media 14496-12). É necessário ter um [ativo](assets-concept.md) com arquivos MP4 codificados e arquivos de configuração de streaming exigidos pelo empacotamento dinâmico dos Serviços de Mídia. Neste conjunto de arquivos MP4, você pode usar o empacotamento dinâmico para transmitir vídeo por meio dos protocolos de streaming de mídia descritos abaixo.

O empacotamento dinâmico dos Serviços de Mídia do Azure dá suporte apenas a arquivos de vídeo e áudio no formato de contêiner MP4. Os arquivos de áudio também precisam ser codificados em um contêiner MP4 ao usar codecs alternativos, como Dolby.  

> [!TIP]
> Uma maneira de obter o MP4 e transmitir os arquivos de configuração é [codificar o arquivo mezanino com os Serviços de Mídia](#encode-to-adaptive-bitrate-mp4s). 

Para fazer vídeos no ativo codificado disponível para clientes de reprodução, é necessário criar um [Localizador de Streaming](stream-streaming-locators-concept.md) e URLs de streaming. Em seguida, com base no formato especificado no manifesto do cliente de streaming (HLS, MPEG, DASH ou Smooth Streaming), você receberá o fluxo no protocolo escolhido.

Como resultado você só precisa armazenar e pagar pelos arquivos em um único formato de armazenamento, e os Serviços de Mídia vão criar e fornecer a resposta apropriada com base nas solicitações de um cliente.

Se você planeja proteger seu conteúdo usando a criptografia dinâmica dos Serviços de Mídia, confira os [Protocolos de streaming e tipos de criptografia](drm-content-protection-concept.md#streaming-protocols-and-encryption-types).

### <a name="hls-protocol"></a>Protocolo HLS

O cliente de streaming pode especificar os seguintes formatos de HLS:

|Protocolo|Exemplo|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|

> [!NOTE]
> As diretrizes anteriores da Apple recomendavam que o fallback para redes de baixa largura de banda era para fornecer um fluxo somente de áudio.  No momento, o codificador dos Serviços de Mídia gera automaticamente uma faixa somente de áudio.  As diretrizes da Apple agora estabelecem que a faixa somente de áudio *não* deve ser incluída, principalmente para a distribuição da Apple TV.  Para impedir que o player utilize como padrão uma faixa somente de áudio, sugerimos que você use a marca "audio-only=false" na URL que remove a representação somente de áudio em HLS ou que você simplesmente use HLS-V3. Por exemplo, `http://host/locator/asset.ism/manifest(format=m3u8-aapl,audio-only=false)`.

### <a name="mpeg-dash-protocol"></a>Protocolo MPEG-DASH

O cliente de streaming pode especificar os seguintes formatos de MPEG-DASH:

|Protocolo|Exemplo|
|---|---|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |

### <a name="smooth-streaming-protocol"></a>Protocolo Smooth Streaming

O cliente de streaming pode especificar os seguintes formatos de Smooth Streaming:

|Protocolo|Observações/exemplos| 
|---|---|
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|
|Smooth Streaming 2.0 (manifesto herdado)|Por padrão, o formato de manifesto do Smooth Streaming contém a marca de repetição (r-tag). No entanto, alguns players não dão suporte à `r-tag`. Os clientes com esses players podem usar um formato que desabilita a r-tag:<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

> [!NOTE]
> O Smooth Streaming requer que o áudio e o vídeo estejam presentes em sua transmissão.

## <a name="on-demand-streaming-workflow"></a>Fluxo de trabalho do streaming sob demanda

Os procedimentos a seguir mostram um fluxo de trabalho de streaming comum dos Serviços de Mídia, em que o empacotamento dinâmico é usado juntamente com o Media Encoder Standard, nos Serviços de Mídia do Microsoft Azure.

1. [Carregue um arquivo de entrada](job-input-from-http-how-to.md), por exemplo, um MP4, um QuickTime/MOV ou outro formato de arquivo com suporte. Esse arquivo também é denominado arquivo de mezanino ou de origem. Para obter a lista de formatos com suporte, confira [Formatos compatíveis com o Encoder Standard](encode-media-encoder-standard-formats-reference.md).
1. [Codifique](#encode-to-adaptive-bitrate-mp4s) seu arquivo de mezanino em um conjunto de taxa de bits adaptável H.264/AAC MP4.

    Se já tiver arquivos codificados e quiser apenas copiar e transmitir os arquivos, use: APIs [CopyVideo](/rest/api/media/transforms/createorupdate#copyvideo) e [CopyAudio](/rest/api/media/transforms/createorupdate#copyaudio). Um novo arquivo MP4 com um manifesto de streaming (arquivo .ism) será criado como resultado.
1. Publicar o ativo de saída que contém o conjunto MP4 de taxa de bits adaptável. Você publica criando um [localizador de streaming](stream-streaming-locators-concept.md).
1. Criar URLs que segmentam diferentes formatos (HLS, MPEG-DASH e Smooth Streaming). O *ponto de extremidade de streaming* cuidaria de servir o manifesto correto e solicitações para todos esses formatos diferentes.
    
O diagrama a seguir mostra o streaming sob demanda com o fluxo de trabalho do empacotamento dinâmico.

![Diagrama de um fluxo de trabalho para streaming sob demanda com empacotamento dinâmico](./media/encode-dynamic-packaging-concept/media-services-dynamic-packaging.svg)

O caminho de download está presente na imagem acima apenas para mostrar que é possível baixar um arquivo MP4 diretamente por meio do *ponto de extremidade de streaming* (origem) (você especifica a [política de streaming](stream-streaming-policy-concept.md) para download no localizador de streaming).<br/>O empacotador dinâmico não está alterando o arquivo. Opcionalmente, você pode usar as APIs do Armazenamento de Blobs do Azure para acessar um MP4 diretamente para download progressivo se deseja ignorar os recursos do *ponto de extremidade de streaming* (origem). 

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Codificar para MP4s de taxa de bits adaptável

Os artigos a seguir mostram exemplos de [como codificar um vídeo com os Serviços de Mídia](encode-concept.md):

* [Codificar de uma URL HTTPS usando predefinições internas](job-input-from-http-how-to.md).
* [Codificar um arquivo local usando predefinições internas](job-input-from-local-file-how-to.md).
* [Criar uma predefinição personalizada para segmentar um cenário específico ou requisitos de dispositivo](transform-custom-presets-how-to.md).

Confira a lista de [codecs e formatos](encode-media-encoder-standard-formats-reference.md) do Encoder Standard.

## <a name="live-streaming-workflow"></a>Fluxo de trabalho de streaming ao vivo

Um evento ao vivo pode ser definido como uma *passagem* (um codificador dinâmico local envia um fluxo de taxa de bits múltipla) ou uma *Codificação Ativa* (um codificador dinâmico local envia um fluxo de taxa de bits única). 

Aqui está um fluxo de trabalho comum para transmissão ao vivo com *empacotamento dinâmico*:

1. Crie um [evento ao vivo](live-event-outputs-concept.md).
1. Obtenha a URL de ingestão e configure seu codificador local para usar a URL no envio do feed de contribuição.
1. Obtenha a URL de visualização e use-a para verificar se a entrada do codificador está sendo recebida.
1. Crie um novo ativo.
1. Crie uma saída dinâmica e use o nome do ativo que você criou.<br />A saída dinâmica arquiva o fluxo no ativo.
1. Crie um localizador de streaming com os tipos internos da política de streaming.<br />Se você pretende criptografar seu conteúdo, reveja a [Visão geral da proteção de conteúdo](drm-content-protection-concept.md).
1. Liste os caminhos no localizador de streaming para retornar as URLs a serem usadas.
1. Obtenha o nome do host para o ponto de extremidade de streaming do qual você deseja transmitir.
1. Criar URLs que segmentam diferentes formatos (HLS, MPEG-DASH e Smooth Streaming). O *ponto de extremidade de streaming* serve o manifesto correto e solicita os diferentes formatos.

Este diagrama mostra o fluxo de trabalho para transmissão ao vivo com *empacotamento dinâmico*:

![Diagrama de um fluxo de trabalho para codificação de passagem com empacotamento dinâmico](./media/live-streaming/pass-through.svg)

Para obter informações sobre transmissão ao vivo nos Serviços de Mídia v3, confira [Visão geral de transmissão ao vivo](stream-live-streaming-concept.md).

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Codecs de vídeo compatíveis com o Empacotamento Dinâmico

O empacotamento dinâmico dá suporte a arquivos de vídeo no formato de arquivo de contêiner MP4 e contêm vídeo codificado com [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC ou AVC1) ou [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 ou hvc1).

> [!NOTE]
> As resoluções de até 4K e taxa de quadros de até 60 quadros por segundo foram testadas com o *empacotamento dinâmico*.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Codecs de áudio suportados por embalagem dinâmica

O empacotamento dinâmico também dá suporte a arquivos de áudio armazenados no formato de contêiner de arquivo MP4 contendo o fluxo de áudio codificado em um dos seguintes codecs:

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 ou HE-AAC v2). 
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (AC-3 ou E-AC3 avançado).  O áudio codificado deve ser armazenado no formato de contêiner MP4 para funcionar com o Empacotamento dinâmico.
* Dolby Atmos

   O streaming de conteúdo Dolby Atmos tem suporte para padrões como o protocolo MPEG-DASH com formato CSF (Common Streaming Format) ou MP4 fragmentado CMAF (Common Media Application Format) e via HTTP Live Streaming (HLS) com CMAF.
* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   Os codecs DTS com suporte pelos formatos de pacote DASH-CSF, DASH-CMAF, HLS-M2TS e HLS-CMAF são:  

    * DTS Digital Surround (dtsc)
    * DTS-HD High Resolution e DTS-HD Master Audio  (dtsh)
    * DTS Express (dtse)
    * DTS-HD Lossless (no core) (dtsl)

O empacotamento dinâmico é compatível com múltiplas faixas de áudio com DASH ou HLS (versão 4 ou posterior) para ativos de streaming que têm várias faixas de áudio com vários codecs e idiomas.

Para todos os codecs de áudio acima, o áudio codificado deve ser armazenado no formato de contêiner MP4 para funcionar com o Empacotamento dinâmico. O serviço não dá suporte a formatos de arquivo de fluxo elementares brutos no armazenamento de blobs (por exemplo, o seguinte não teria suporte: .dts, .ac3.) 

Somente arquivos com a extensão .mp4 de .mp4a têm suporte para empacotamento de áudio. 

### <a name="limitations"></a>Limitações

#### <a name="ios-limitation-on-aac-51-audio"></a>Limitação de iOS em áudio AAC 5.1

Dispositivos iOS da Apple não são compatíveis com o codec de áudio AAC 5.1. O áudio multicanal precisa ser codificado usando codecs Dolby Digital ou Dolby Digital Plus.

Para obter informações detalhadas, confira [especificação de criação de HLS para dispositivos Apple](https://developer.apple.com/documentation/http_live_streaming/hls_authoring_specification_for_apple_devices).

> [!NOTE]
> Os Serviços de Mídia não dão suporte à codificação de Dolby Digital, Dolby Digital Plus ou Dolby Digital Plus com formatos de áudio multicanal Dolby Atmos.

#### <a name="dolby-digital-audio"></a>Áudio Dolby Digital

Atualmente, o empacotamento dinâmico dos Serviços de Mídia não é compatível com arquivos que contenham áudio [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3), pois isso é considerado um codec herdado pela Dolby.

## <a name="manifests"></a>Manifestos

No *empacotamento dinâmico* dos Serviços de Mídia, os manifestos do cliente de streaming para HLS, MPEG-DASH e Smooth Streaming são gerados dinamicamente com base no seletor de formato na URL.  

Um arquivo de manifesto inclui o streaming de metadados, como: tipo da trilha (áudio, vídeo ou texto), nome da trilha, hora de início e término, taxa de bits (qualidades), idiomas da trilha, janela de apresentação (janela deslizante de duração fixa) e codec de vídeo (FourCC). Também instrui o player a recuperar o próximo fragmento, fornecendo informações sobre os próximos fragmentos de vídeo executáveis que estão disponíveis e sua localização. Os fragmentos (ou segmentos) são os "pedaços" reais de um conteúdo de vídeo.

### <a name="examples"></a>Exemplos

#### <a name="hls"></a>HLS

Aqui está um exemplo de um arquivo de manifesto HLS, também chamado de uma lista de reprodução principal HLS: 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

#### <a name="mpeg-dash"></a>MPEG-DASH

Aqui está um exemplo de um arquivo de manifesto MPEG-DASH, também chamado MPEG-DASH Media Presentation Description (MPD):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
#### <a name="smooth-streaming"></a>Smooth Streaming

Aqui está um exemplo de um arquivo de manifesto de Smooth Streaming:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

### <a name="naming-of-tracks-in-the-manifest"></a>Nomenclatura de faixas no manifesto

Se um nome de faixa de áudio estiver especificado no arquivo .ism, os Serviços de Mídia adicionarão um elemento `Label` dentro de um `AdaptationSet` para especificar as informações textuais da faixa de áudio específica. Um exemplo do manifesto DASH de saída:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

O player pode usar o elemento `Label` a ser exibido em sua interface do usuário.

### <a name="signaling-audio-description-tracks"></a>Sinalizar faixas de descrição de áudio

Você pode adicionar uma faixa de narração ao vídeo para ajudar clientes com deficiência visual a seguir a gravação do vídeo ouvindo a narração. Você precisa anotar uma faixa de áudio como descrição de áudio no manifesto. Para fazer isso, adicione os parâmetros “accessibility” e “role” ao arquivo .ism. Você tem a responsabilidade de definir esses parâmetros corretamente para sinalizar faixas de áudio como descrição de áudio. Por exemplo, adicione `<param name="accessibility" value="description" />` e `<param name="role" value="alternate"` ao arquivo.ism para uma faixa de áudio específica. 

Para obter mais informações, confira o exemplo [Como sinalizar uma faixa de áudio descritiva](signal-descriptive-audio-howto.md).

#### <a name="smooth-streaming-manifest"></a>Manifesto Smooth Streaming

Se você estivesse executando um fluxo de Smooth Streaming, o manifesto transportaria valores nos atributos `Accessibility` e `Role` para essa faixa de áudio. Por exemplo, `Role="alternate" Accessibility="description"` seria adicionado no elemento `StreamIndex` para indicar que é uma descrição de áudio.

#### <a name="dash-manifest"></a>Manifesto DASH

Para o manifesto DASH, os dois elementos a seguir seriam adicionados para sinalizar a descrição de áudio:

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>Playlist HLS

Para HLS v7 e superior `(format=m3u8-cmaf)`, sua playlist transportaria `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"` quando a faixa de descrição de áudio fosse sinalizada.

#### <a name="example"></a>Exemplo

Para obter mais informações, confira [Como sinalizar faixas de descrição de áudio](signal-descriptive-audio-howto.md).

## <a name="dynamic-manifest"></a>Manifesto dinâmico

Para controlar o número de faixas, formatos, taxas de bits e janelas de tempo de apresentação que são enviadas aos players, você pode usar a filtragem dinâmica com o empacotador dinâmico dos Serviços de Mídia. Para saber mais, confira [Pré-filtragem de manifestos com o empacotador dinâmico](filters-dynamic-manifest-concept.md).

## <a name="dynamic-encryption"></a>Criptografia dinâmica

Você pode usar *criptografia dinâmica* para criptografar dinamicamente seu conteúdo ao vivo ou sob demanda com o AES-128 ou qualquer um dos três sistemas principais de gerenciamento de direitos digitais (DRM): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os Serviços de Mídia também fornecem um serviço para entrega de chaves AES e licenças DRM a clientes autorizados. Para saber mais, confira [criptografia dinâmica](drm-content-protection-concept.md).

> [!NOTE]
> O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="more-information"></a>Mais informações

Confira a [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="need-help"></a>Precisa de ajuda?

Para abrir um tíquete de suporte, navegue até [Nova solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

[Carregar, codificar e transmitir vídeos](stream-files-tutorial-with-api.md)
