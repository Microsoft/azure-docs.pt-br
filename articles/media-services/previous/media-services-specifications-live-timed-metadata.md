---
title: Serviços de Mídia do Microsoft Azure - Sinalização de metadados cronometrados transmissão ao vivo | Microsoft Docs
description: Essa especificação descreve dois modos com suporte dos Serviços de Mídia para sinalização de metadados cronometrados em transmissão ao vivo. Isso inclui suporte para sinais de metadados cronometrados genéricos, bem como sinalização SCTE-35 para inserção de junção ad.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: johndeu;
ms.openlocfilehash: 10dbf7e8cf67ab721cf525d4a1e7594473592bd4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459106"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>A sinalização atingiu o tempo de metadados na transmissão ao vivo 


## <a name="1-introduction"></a>1 Introdução 
Para facilitar a inserção de anúncios ou eventos personalizados em um player de cliente, muitas vezes as emissoras fazem uso de metadados cronometrados inseridos no vídeo. Para ativar esses cenários, os Serviços de Mídia fornecem suporte para o transporte de metadados cronometrados junto com a mídia, do ponto de ingestão do canal de transmissão ao vivo para o aplicativo do cliente.
Essa especificação descreve dois modos com suporte dos Serviços de Mídia para metadados cronometrados em sinais de transmissão ao vivo:

1. [SCTE-35] sinalização que atendem às práticas recomendadas descritas por [SCTE-67]

2. Um modo de sinalização de metadados cronometrado, para mensagens que não são [SCTE-35]

### <a name="12-conformance-notation"></a>1.2 Notação de conformidade
As palavras-chave “DEVE”, “NÃO DEVE”, “OBRIGATÓRIO”, “RECOMENDADO”, “PODE” E “OPCIONAL” neste documento devem ser interpretadas conforme descritas na RFC 2119

### <a name="13-terms-used"></a>1.3 Termos usados

| Termo              | Definição                                                                                                                                                                                                                       |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tempo da apresentação | O horário em que um evento é apresentado em um espectador. O horário representa o momento na linha do tempo da mídia em que um espectador vê o evento. Por exemplo, a hora de apresentação de uma mensagem de comando splice_info() SCTE-35 é o splice_time(). |
| Horário de chegada      | O horário em que uma mensagem de evento chega. O horário é normalmente diferente do horário da apresentação do evento, pois mensagens de evento são enviadas antes do horário da apresentação do evento.                                     |
| Faixa esparsa      | Faixa de mídia que não é contínua e é sincronizada por horário com um pai ou faixa de controle.                                                                                                                                    |
| Origem            | O serviço de Streaming de Mídia do Azure                                                                                                                                                                                                |
| Coletor do canal      | O serviço de Live Streaming de Mídia do Azure                                                                                                                                                                                           |
| HLS               | Protocolo Apple HTTP Live Streaming                                                                                                                                                                                               |
| DASH              | Dynamic Adaptive Streaming Over HTTP                                                                                                                                                                                             |
| Smooth            | Protocolo Smooth Streaming                                                                                                                                                                                                        |
| MPEG2-TS          | Fluxos de Transporte de MPEG 2                                                                                                                                                                                                         |
| RTMP              | Protocolo de multimídia em tempo real                                                                                                                                                                                                    |
| uimsbf            | Inteiro sem sinal, bit mais significativo primeiro.                                                                                                                                                                                    |

-----------------------

## <a name="2-timed-metadata-ingest"></a>2 Ingestão de metadados cronometrados
## <a name="21-rtmp-ingest"></a>2.1 Ingestão RTMP
O RTMP oferece suporte a sinais de metadados cronometrados enviados como mensagens de advertência AMF inseridas dentro do fluxo RTMP. As mensagens de advertência podem ser enviadas algum tempo antes do evento real ou inserção de junção ad precisa ocorrer. Para oferecer suporte a esse cenário, o horário real da junção ou segmento é enviado dentro da mensagem de solução. Para obter mais informações, consulte [AMF0].

A tabela a seguir descreve o formato do conteúdo da mensagem AMF que os Serviços de Mídia irão ingerir.

O nome da mensagem AMF pode ser usado para diferenciar vários fluxos de eventos do mesmo tipo.  Para mensagens [SCTE-35], o nome da mensagem AMF DEVE ser "onAdCue" conforme recomendado nos [SCTE-67].  Todos os campos abaixo não DEVEM ser ignorados para que a inovação desse design não seja impedida no futuro.

### <a name="signal-syntax"></a>Sintaxe de sinal
Para o modo simples de RTMP, os Serviços de Mídia oferecem suporte a uma única mensagem de advertência AMF chamada "onAdCue" com o seguinte formato:

### <a name="simple-mode"></a>Modo simples

| Nome do campo | Tipo de campo | Obrigatório? | Descrições                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| advertência        | Cadeia de caracteres     | Obrigatório | A mensagem do evento.  Deve ser "SpliceOut" para designar uma junção de modo simples.                                              |
| ID         | Cadeia de caracteres     | Obrigatório | Um identificador exclusivo que descreve a junção ou o segmento. Identifica esta instância da mensagem                            |
| duration   | Número     | Obrigatório | A duração da junção. As unidades são frações de segundo.                                                                |
| elapsed    | Número     | Opcional | Quando o sinal está sendo repetido para dar suporte a ajuste, este campo deve ser a quantidade de horas de apresentação decorrida desde que a junção começou. As unidades são frações de segundo. Ao usar o modo simples, esse valor não deve exceder a duração original da junção.                                                  |
| time       | Número     | Obrigatório | Deverá ser o horário da junção, no horário da apresentação. As unidades são frações de segundo.                                     |

---------------------------

### <a name="scte-35-mode"></a>Modo SCTE-35

| Nome do campo | Tipo de campo | Obrigatório? | Descrições                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| advertência        | Cadeia de caracteres     | Obrigatório | A mensagem do evento.  Para mensagens [SCTE-35], isso DEVE ser o splice_info_section() binário codificado base64 (IETF RFC 4648) para que as mensagens sejam enviadas para clientes HLS, Smooth e Dash em conformidade com [SCTE-67].                                              |
| tipo       | Cadeia de caracteres     | Obrigatório | Uma URN ou URL que identifica o esquema da mensagem. Para mensagens [SCTE-35], isso DEVE ser "urn:scte:scte35:2013a:bin" para que as mensagens sejam enviadas para clientes HLS, Smooth e Dash em conformidade com [SCTE-67].  |
| ID         | Cadeia de caracteres     | Obrigatório | Um identificador exclusivo que descreve a junção ou o segmento. Identifica esta instância da mensagem.  Mensagens com semântica equivalente devem ter o mesmo valor.|
| duration   | Número     | Obrigatório | A duração do evento ou junção-segmento ad, se conhecido. Se desconhecido, o valor deve ser 0.                                                                 |
| elapsed    | Número     | Opcional | Quando o sinal [SCTE-35] ad está sendo repetido para ajustar, este campo deve ser a quantidade de horas de apresentação decorrida desde que a junção começou. As unidades são frações de segundo. No modo [SCTE-35], esse valor pode exceder a duração especificada original da junção ou segmento.                                                  |
| time       | Número     | Obrigatório | O horário da apresentação do evento ou junção ad.  A hora de apresentação e a duração DEVEM estar alinhadas com Pontos de Acesso de Fluxo (SAP) do tipo 1 ou 2, conforme definido em [ISO-14496-12] Anexo I. Para a saída HLS, a hora e a duração DEVEM estar alinhadas com limites de segmento. A hora de apresentação e a duração de mensagens de eventos diferentes dentro do mesmo fluxo de eventos NÃO DEVEM se sobrepor. As unidades são frações de segundo.

---------------------------

#### <a name="211-cancellation-and-updates"></a>2.1.1 Cancelamento e atualizações

As mensagens podem ser canceladas ou atualizadas enviando-se várias mensagens com a mesma hora e ID de apresentação. A hora e ID de apresentação identificam exclusivamente o evento e a última mensagem recebida por um horário de apresentação específico que atende às restrições de pré-inscrição é a mensagem que é tratada. O evento atualizado substitui qualquer mensagem recebida anteriormente. A restrição de pré-inscrição é de quatro segundos. Mensagens recebidas em pelo menos quatro segundos antes do horário da apresentação serão tratadas.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 Ingestão de MP4 fragmentado (Smooth Streaming)
Consulte [LIVE-FMP4] para requisitos para ingestão de fluxo ao vivo. As seções a seguir fornecem detalhes com relação à ingestão de metadados de apresentação cronometrados.  Metadados de apresentação cronometrados são ingeridos como uma faixa esparsa, que é definida na Caixa de Manifesto do Servidor Live (ver MS-SSTR) e a Caixa do Filme (‘moov’).  Cada fragmento esparso consiste em uma Caixa de Fragmentos de Filme (‘moof’) e uma Data Box de Mídia (‘mdat’), em que a caixa ‘mdat’ é a mensagem binária.

#### <a name="221-live-server-manifest-box"></a>2.2.1 Caixa de manifesto do servidor Live
A faixa esparsa DEVE ser declarada na Caixa de Manifesto do Servidor Live com uma entrada \<textstream\> e DEVE ter os seguintes atributos definidos:

| **Nome do atributo** | **Tipo de campo** | **Obrigatório?** | **Descrição**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Número         | Obrigatório      | DEVE ser “0”, o que indica uma faixa com taxa de bits variável e desconhecida.                                                                                                                                                                                                 |
| parentTrackName    | Cadeia de caracteres         | Obrigatório      | DEVE ser o nome da faixa pai, para que os códigos de tempo de faixa esparsos sejam alinhados com a escala de tempo. A faixa pai não pode ser uma faixa esparsa.                                                                                                                    |
| manifestOutput     | Boolean        | Obrigatório      | DEVE ser “true”, para indicar que a faixa esparsa será inserida no manifesto do cliente suave.                                                                                                                                                               |
| Subtype            | Cadeia de caracteres         | Obrigatório      | DEVE ser o código de quatro caracteres “DATA”.                                                                                                                                                                                                                         |
| Esquema             | Cadeia de caracteres         | Obrigatório      | DEVE ser um URN ou URL que identifica o esquema da mensagem. Para mensagens [SCTE-35], isso DEVE ser "urn:scte:scte35:2013a:bin" para que as mensagens sejam enviadas para clientes HLS, Smooth e Dash em conformidade com [SCTE-67]. |
| trackName          | Cadeia de caracteres         | Obrigatório      | DEVE ser o nome da faixa esparsa. O trackName pode ser usado para diferenciar vários fluxos de eventos com o mesmo esquema. Cada fluxo de eventos exclusivo deve ter um nome de faixa exclusivo.                                                                           |
| escala de tempo          | Número         | Opcional      | DEVE ser a escala de tempo da faixa pai.                                                                                                                                                                                                                      |

-------------------------------------

### <a name="222-movie-box"></a>2.2.2 Caixa de filme

A Caixa de Filme (‘moov’) segue o Caixa do Manifesto do Servidor Live como parte do cabeçalho do fluxo para uma faixa esparsa.

A caixa ‘moov’ DEVE conter uma caixa **TrackHeaderBox (‘tkhd’)** definida em [ISO-14496-12] com as restrições a seguir:

| **Nome do Campo** | **Tipo de campo**          | **Obrigatório?** | **Descrição**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| duration       | Inteiro sem sinal de 64 bits | Obrigatório      | DEVE ser 0, desde que a caixa da faixa tenha zero amostras e a duração total de amostras na caixa da faixa seja 0. |

-------------------------------------

A caixa ‘moov’ DEVE conter uma caixa **HandlerBox (‘hdlr’)** conforme definido em [ISO-14496-12] com as restrições a seguir:

| **Nome do Campo** | **Tipo de campo**          | **Obrigatório?** | **Descrição**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | Inteiro sem sinal de 32 bits | Obrigatório      | DEVE ser ‘meta’. |

-------------------------------------

A caixa ‘stsd’ DEVE conter uma caixa MetaDataSampleEntry com um nome de codificação conforme definido em [ISO-14496-12].  Por exemplo, para mensagens de SCTE-35, o nome de codificação DEVE ser 'scte'.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Caixa de fragmentos de filme e Data Box de Mídia

Fragmentos de faixa esparsa consistem em uma Caixa de Fragmentos de Filme (‘moof’) e uma Data Box de Mídia (‘mdat’).

A caixa MovieFragmentBox (‘moof’) DEVE conter uma caixa **TrackFragmentExtendedHeaderBox (‘uuid’)** conforme definido em [MS-SSTR] com os seguintes campos:

| **Nome do Campo**         | **Tipo de campo**          | **Obrigatório?** | **Descrição**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | Inteiro sem sinal de 64 bits | Obrigatório      | DEVE ser a hora de chegada do evento. Esse valor alinha a mensagem com a faixa pai.   |
| fragment_duration      | Inteiro sem sinal de 64 bits | Obrigatório      | DEVE ser a duração do evento. A duração pode ser zero para indicar que a duração é desconhecida. |

------------------------------------


A caixa MediaDataBox (‘mdat’) deve ter o seguinte formato:

| **Nome do Campo**          | **Tipo de campo**                   | **Obrigatório?** | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| version                 | Inteiro sem sinal de 32 bits (uimsbf) | Obrigatório      | Determina o formato dos conteúdos da caixa ‘mdat’. Versões não reconhecidas serão ignoradas. Atualmente, a única versão aceita é 1.                                                                                                                                                                                                                                                                                                                                                      |
| ID                      | Inteiro sem sinal de 32 bits (uimsbf) | Obrigatório      | Identifica esta instância da mensagem. Mensagens com semântica equivalente devem ter o mesmo valor. Ou seja, o processamento de qualquer caixa de mensagem de um evento com a mesma ID é suficiente.                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | Inteiro sem sinal de 32 bits (uimsbf) | Obrigatório      | A soma do fragment_absolute_time, especificado na TrackFragmentExtendedHeaderBox e o presentation_time_delta DEVEM ser o horário de apresentação do evento. A hora de apresentação e a duração DEVEM estar alinhadas com Pontos de Acesso de Fluxo (SAP) do tipo 1 ou 2, conforme definido em [ISO-14496-12] Anexo I. Para a saída HLS, a hora e a duração DEVEM estar alinhadas com limites de segmento. A hora de apresentação e a duração de mensagens de eventos diferentes dentro do mesmo fluxo de eventos NÃO DEVEM se sobrepor. |
| message                 | matriz de bytes                       | Obrigatório      | A mensagem do evento. Para mensagens [SCTE-35], a mensagem é a splice_info_section() binária, embora [SCTE-67] recomende outra coisa. Para mensagens [SCTE-35], isso DEVE ser a splice_info_section() para que as mensagens sejam enviadas para clientes HLS, Smooth e Dash em conformidade com [SCTE-67]. Para mensagens [SCTE-35], a splice_info_section() binária é o conteúdo da caixa ‘mdat’ e NÃO é codificado na base64.                                                            |

------------------------------


### <a name="224-cancellation-and-updates"></a>2.2.4 Cancelamento e atualizações
As mensagens podem ser canceladas ou atualizadas enviando-se várias mensagens com a mesma hora e ID de apresentação.  A hora e ID de apresentação identificam exclusivamente o evento. A última mensagem recebida por um horário de apresentação específico, que atende às restrições de pré-inscrição, é a mensagem que é tratada. A mensagem atualizada substitui qualquer mensagem recebida anteriormente.  A restrição de pré-inscrição é de quatro segundos. Mensagens recebidas em pelo menos quatro segundos antes do horário da apresentação serão tratadas. 


## <a name="3-timed-metadata-delivery"></a>3 Entrega de metadados cronometrados

Dados de fluxo de evento são opacos para Serviços de Mídia. Serviços de Mídia simplesmente passam três partes de informações entre o ponto de extremidade de ingestão e o ponto de extremidade do cliente. As propriedades a seguir são fornecidas para o cliente, em conformidade com [SCTE-67] e/ou o cliente do protocolo de streaming:

1.  Esquema – uma URN ou URL que identifica o esquema da mensagem.

2.  Horário da apresentação – o horário da apresentação do evento na linha do tempo da mídia.

3.  Duração – a duração do evento.

4.  ID – um identificador exclusivo opcional para o evento.

5.  Mensagem – os dados do evento.


## <a name="31-smooth-streaming-delivery"></a>3.1 Entrega do Smooth Streaming

Consulte a faixa esparsa que manipula detalhes em [MS-SSTR].

#### <a name="smooth-client-manifest-example"></a>Exemplo de manifesto do cliente Smooth
~~~ xml
<?xml version=”1.0” encoding=”utf-8”?>
<SmoothStreamingMedia MajorVersion=”2” MinorVersion=”0” TimeScale=”10000000” IsLive=”true” Duration=”0”
  LookAheadFragmentCount=”2” DVRWindowLength=”6000000000”>

  <StreamIndex Type=”video” Name=”video” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(video={start time})”>
    <QualityLevel Index=”0” Bitrate=”230000”
      CodecPrivateData=”250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <QualityLevel Index=”1” Bitrate=”305000”
      CodecPrivateData=”250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”audio” Name=”audio” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(audio={start time})”>
    <QualityLevel Index=”0” Bitrate=”96000” CodecPrivateData=”1000030000000000000000000000E00042C0”
      FourCC=”WMAP” AudioTag=”354” Channels=”2” SamplingRate=”44100” BitsPerSample=”16” PacketSize=”4459”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”text” Name=”scte35-event-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013a:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- <f> contains base-64 encoded splice_info_section() message 
    <c t=”600000000” d=”300000000”>
<f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia> 
~~~ 

## <a name="32--apple-hls-delivery"></a>3.2 Entrega de HLS da Apple
Metadados cronometrados para HTTP Live Streaming (HLS) da Apple podem ser inseridos na lista de reprodução de segmentos dentro de uma marca M3U personalizada.  A camada de aplicativo pode analisar a lista de reprodução de M3U e processar marcas M3U. Os Serviços de Mídia do Microsoft Azure incorporarão metadados cronometrados na marca EXT-X-CUE definida em [HLS].  A marca EXT-X-CUE está sendo atualmente usada por DynaMux para mensagens do tipo ADI3.  Para oferecer suporte a mensagens SCTE-35 e não SCTE-35, defina os atributos da marca EXT-X-CUE conforme definido abaixo:

| **Nome do atributo** | **Tipo**                      | **Obrigatório?**                             | **Descrição**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ADVERTÊNCIA                | cadeia de caracteres entre aspas                 | Obrigatório                                  | A mensagem codificada como uma cadeia de caracteres base64, conforme descrito em [IETF RFC 4648](https://tools.ietf.org/html/rfc4648). Para mensagens [SCTE-35], esta é a splice_info_section() codificada na base64.                                                                                                |
| TYPE               | cadeia de caracteres entre aspas                 | Obrigatório                                  | Uma URN ou URL que identifica o esquema da mensagem. Para mensagens [SCTE-35], o tipo tem o valor especial “scte35”.                                                                                                                                |
| ID                 | cadeia de caracteres entre aspas                 | Obrigatório                                  | Um identificador exclusivo para o evento. Se a ID não for especificada quando a mensagem for ingerida, o Azure Media Services gerará uma ID exclusiva.                                                                                                                                          |
| DURAÇÃO           | um número de ponto flutuante decimal | Obrigatório                                  | A duração do evento. Se desconhecido, o valor deve ser 0. As unidades são frações de segundos.                                                                                                                                                                                           |
| DECORRIDO            | um número de ponto flutuante decimal | Opcional, mas Necessário para janela deslizante | Quando o sinal está sendo repetido para dar suporte a uma janela de apresentação deslizante, este campo DEVE ser a quantidade de horas de apresentação decorridas desde que o evento começou. As unidades são frações de segundo. Esse valor pode exceder a duração especificada original da junção ou segmento. |
| TIME               | um número de ponto flutuante decimal | Obrigatório                                  | O horário da apresentação do evento. As unidades são frações de segundo.                                                                                                                                                                                                                    |


A camada de aplicativo de player HLS usará o TIPO para identificar o formato da mensagem, decodificar a mensagem, aplicar as conversões de tempo necessárias e processar o evento.  Os eventos são sincronizados por horário na lista de reprodução de segmento da faixa pai, de acordo com o carimbo de data/hora do evento.  Eles são inseridos antes do segmento mais próximo (marca #EXTINF).

#### <a name="hls-segment-playlist-example"></a>Exemplo de lista de reprodução de segmento HLS
~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:346
#EXT-X-TARGETDURATION:6
#EXT-X-I-FRAMES-ONLY
#EXT-X-PROGRAM-DATE-TIME:2018-12-13T15:54:19.462Z
#EXTINF:4.000000,no-desc
KeyFrames(video_track=15447164594627600,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447164634627600,format=m3u8-aapl)
#EXT-X-CUE:ID="1026",TYPE="scte35",DURATION=30.000000,TIME=1544716520.022760,CUE="/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w=="
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447165474627600,format=m3u8-aapl)
~~~

#### <a name="hls-message-handling"></a>Manipulação de mensagens HLS

Eventos são sinalizados na lista de reprodução de segmento de cada faixa de vídeo e áudio. A posição da marca EXT-X-CUE DEVE sempre ser imediatamente anterior ao primeiro segmento HLS (para junção externa ou início de segmento) ou imediatamente após o último segmento HLS (para junção interna ou no fim de segmento) aos quais seus atributos de TEMPO e DURAÇÃO fazem referência, conforme solicitado pelo [HLS].

Quando uma janela de apresentação deslizante está habilitada, a marca EXT-X-CUE DEVE ser repetida com frequência suficiente para que a junção ou segmento seja sempre totalmente descrito na lista de reprodução de segmento e o atributo DECORRIDO DEVE ser usado para indicar a quantidade de tempo em que a junção ou segmento está ativo, conforme exigido pelo [HLS].

Quando uma janela de apresentação deslizante está habilitada, as marcas EXT-X-CUE são removidas da lista de reprodução de segmento quando o tempo de mídia a que eles se referem seja revertido para fora da janela deslizante de apresentação.

## <a name="33--dash-delivery"></a>3.3 Entrega de DASH
[DASH] fornece três formas de eventos de sinal:

1.  Eventos sinalizados no MPD
2.  Eventos sinalizados em banda na Representação – usando a Caixa de Mensagem de Evento (‘emsg’)
3.  Uma combinação de 1 e 2

Eventos sinalizados no MPD são úteis para streaming VOD porque os clientes têm acesso a todos os eventos, imediatamente quando o MPD é baixado. A solução em banda é útil para a transmissão ao vivo porque os clientes não precisam fazer o download do MPD novamente. Para segmentação baseada em horário, o cliente determina a URL para o próximo segmento adicionando a duração e o carimbo de data/hora do segmento atual. Se essa solicitação falhar, o cliente presume uma descontinuidade e faz o download do MPD, caso contrário continua transmitindo sem fazer o download do MPD.

Os Serviços de Mídia do Microsoft Azure farão sinalização no MPD e em banda usando a Caixa de Mensagem de Evento.

#### <a name="mpd-signaling"></a>Sinalização MPD

Eventos serão sinalizados no MPD usando o elemento EventStream, que aparece no elemento Período.

O elemento EventStream tem os seguintes atributos:

| **Nome do atributo** | **Tipo**                | **Obrigatório?** | **Descrição**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | string                  | Obrigatório      | Identifica o esquema da mensagem. O esquema é definido como o valor do atributo Esquema na caixa de Manifesto do Servidor Live. O valor DEVE ser um URN ou URL que identifica o esquema de mensagem; por exemplo, “urn:scte:scte35:2013a:bin”.                                                                |
| value              | string                  | Opcional      | Um valor de cadeia de caracteres adicional usado pelos proprietários do esquema para personalizar a semântica da mensagem. Para diferenciar vários fluxos de eventos com o mesmo esquema, o valor DEVE ser definido como o nome do fluxo de eventos (trackName para ingestão Smooth ou nome de mensagem AMF para ingestão RTMP). |
| Escala de tempo          | Inteiro sem sinal de 32 bits | Obrigatório      | A escala de tempo, em tiques por segundo, dos campos de horas e os duração na caixa ‘emsg’.                                                                                                                                                                                                       |


Zero ou mais elementos de Evento estão contidos dentro do elemento EventStream e têm os seguintes atributos:

| **Nome do atributo**  | **Tipo**                | **Obrigatório?** | **Descrição**                                                                                                                                                                                                             |
|---------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| presentation_time   | Inteiro sem sinal de 64 bits | Opcional      | DEVE ser o horário de apresentação de mídia do evento em relação ao início do Período. A hora e a duração da apresentação DEVEM ser alinhadas com Pontos de Acesso de Fluxo (SAP) do tipo 1 ou 2, conforme definido em [ISO-14496-12] Anexo I. |
| duration            | Inteiro sem sinal de 32 bits | Opcional      | A duração do evento. Isso DEVE ser omitido se a duração for desconhecida.                                                                                                                                                 |
| ID                  | Inteiro sem sinal de 32 bits | Opcional      | Identifica esta instância da mensagem. Mensagens com semântica equivalente devem ter o mesmo valor. Se a ID não for especificada quando a mensagem for ingerida, o Azure Media Services gerará uma ID exclusiva.             |
| Valor do elemento Evento | string                  | Obrigatório      | A mensagem de evento como uma cadeia de caracteres base64, conforme descrito em [IETF RFC 4648](https://tools.ietf.org/html/rfc4648).                                                                                                                   |

#### <a name="xml-syntax-and-example-for-dash-manifest-mpd-signaling"></a>Sintaxe de XML e exemplo para sinalização de manifesto DASH (MPD)

~~~ xml
<!-- XML Syntax -->
<xs:complexType name=”EventStreamType”>
  <xs:sequence>
    <xs:element name=”Event” type=”EventType” minOccurs=”0” maxOccurs=”unbounded”/>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute ref=”xlink:href”/>
  <xs:attribute ref=”xlink:actuate” default=”onRequest”/>
  <xs:attribute name=”schemeIdUri” type=”xs:anyURI” use=”required”/>
  <xs:attribute name=”value” type=”xs:string”/>
  <xs:attribute name=”timescale” type=”xs:unsignedInt”/>
</xs:complexType>
<!-- Event -->
<xs:complexType name=”EventType”>
  <xs:sequence>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute name=”presentationTime” type=”xs:unsignedLong” default=”0”/>
  <xs:attribute name=”duration” type=”xs:unsignedLong”/>
  <xs:attribute name=”id” type=”xs:unsignedInt”/>
  <xs:anyAttribute namespace=”##other” processContents=”lax”/>
</xs:complexType><?xml version=”1.0” encoding=”utf-8”?>


<!-- Example Section in MPD -->
  <EventStream schemeIdUri="urn:scte:scte35:2013a:bin" value="scte35_track_001_000" timescale="10000000">
        <Event presentationTime="15447165200227600" duration="300000000" id="1026">/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w==</Event>
        <Event presentationTime="15447166250227600" duration="300000000" id="1027">/DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==</Event>
        <Event presentationTime="15447167300227600" duration="600000000" id="1028">/DAlAAAAAAAAAP/wFAUAAAQEf+//KjkknP4AUmXAAAAAAAAAWcEldA==</Event>
        <Event presentationTime="15447168350227600" duration="600000000" id="1029">/DAlAAAAAAAAAP/wFAUAAAQFf+//KslyqP4AUmXAAAAAAAAAvKNt0w==</Event>
        <Event presentationTime="15447169400227600" duration="300000000" id="1030">/DAlAAAAAAAAAP/wFAUAAAQGf+//K1mIvP4AKTLgAAAAAAAAt2zEbw==</Event>
        <Event presentationTime="15447170450227600" duration="600000000" id="1031">/DAlAAAAAAAAAP/wFAUAAAQHf+//K+hc/v4AUmXAAAAAAAAANNRzVw==</Event>
    </EventStream>
~~~

>[!NOTE]
>Observe que presentationTime é a hora da apresentação do evento, não a hora de chegada da mensagem.
>

### <a name="431-in-band-event-message-box-signaling"></a>4.3.1 Sinalização de caixa de mensagem de evento em banda
Um fluxo de eventos em banda requer que o MPD tenha um elemento InbandEventStream no nível do Conjunto de Adaptação.  Este elemento tem um atributo obrigatório schemeIdUri e o atributo opcional da escala de tempo, que também aparecem caixa de mensagem de evento (‘emsg’).  Caixas de mensagem de evento com identificadores de esquema que não estão definidos no MPD não devem estar presentes. Se um cliente DASH detectar uma caixa de mensagem de evento com um esquema que não esteja definido no MPD, ele deve ignorá-lo.
A caixa de mensagem de evento (‘emsg’) fornece a sinalização para eventos genéricos relacionados à hora da apresentação de mídia. Se estiver presente, qualquer caixa ‘emsg’ deve ser colocada antes de qualquer caixa ‘moof’.

### <a name="dash-event-message-box-emsg"></a>Caixa de mensagem de evento DASH ‘emsg’
~~~
Box Type: `emsg’
Container: Segment
Mandatory: No
Quantity: Zero or more
~~~

~~~ c
aligned(8) class DASHEventMessageBox extends FullBox(‘emsg’, version = 0, flags =0) 
{
    string scheme_id_uri;
    string value;
    unsigned int(32) timescale;
    unsigned int(32) presentation_time_delta;
    unsigned int(32) event_duration;
    unsigned int(32) id;
    unsigned int(8) message_data[];
}
~~~

Os campos do DASHEventMessageBox estão definidos abaixo:

| **Nome do Campo**          | **Tipo de campo**          | **Obrigatório?** | **Descrição**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | string                  | Obrigatório      | Identifica o esquema da mensagem. O esquema é definido como o valor do atributo Esquema na caixa de Manifesto do Servidor Live. O valor DEVE ser um URN ou URL que identifica o esquema da mensagem. Para mensagens [SCTE-35], isso usa o valor especial “urn:scte:scte35:2013a:bin”, embora [SCTE-67] recomende outra coisa. |
| Value                   | string                  | Obrigatório      | Um valor de cadeia de caracteres adicional usado pelos proprietários do esquema para personalizar a semântica da mensagem. Para diferenciar vários fluxos de eventos com o mesmo esquema, o valor será definido como o nome do fluxo de eventos (trackName para ingestão Smooth ou nome de mensagem AMF para ingestão RTMP).                                                                  |
| Escala de tempo               | Inteiro sem sinal de 32 bits | Obrigatório      | A escala de tempo, em tiques por segundo, dos campos de horas e os duração na caixa ‘emsg’.                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | Inteiro sem sinal de 32 bits | Obrigatório      | O intervalo de tempo de apresentação de mídia do tempo de apresentação do evento e o primeiro horário nesse segmento. A hora e a duração da apresentação DEVEM ser alinhadas com Pontos de Acesso de Fluxo (SAP) do tipo 1 ou 2, conforme definido em [ISO-14496-12] Anexo I.                                                                                            |
| event_duration          | Inteiro sem sinal de 32 bits | Obrigatório      | A duração do evento ou 0xFFFFFFFF para indicar uma duração desconhecida.                                                                                                                                                                                                                                                                                          |
| ID                      | Inteiro sem sinal de 32 bits | Obrigatório      | Identifica esta instância da mensagem. Mensagens com semântica equivalente devem ter o mesmo valor. Se a ID não for especificada quando a mensagem for ingerida, o Azure Media Services gerará uma ID exclusiva.                                                                                                                                                    |
| Message_data            | matriz de bytes              | Obrigatório      | A mensagem do evento. Para mensagens [SCTE-35], os dados da mensagem é a splice_info_section() binária, embora [SCTE-67] recomende outra coisa.                                                                                                                                                                                                                                 |

### <a name="332-dash-message-handling"></a>3.3.2 Manipulação de mensagens DASH

Eventos são sinalizados em banda, dentro da caixa ‘emsg’, para faixas de vídeo e áudio.  A sinalização ocorre para todas as solicitações de segmento para as quais o presentation_time_delta é de 15 segundos ou menos. Quando uma janela de apresentação deslizante está habilitada, as mensagens de evento são removidas do MPD quando a soma do tempo e da duração da mensagem de evento é menor que o tempo dos dados de mídia no manifesto.  Em outras palavras, as mensagens de evento são removidas do manifesto quando o tempo de mídia ao qual elas se referem foi transferida para fora da janela de apresentação deslizante.

## <a name="4-scte-35-ingest"></a>4 Ingestão SCTE-35

Mensagens [SCTE-35] são ingeridas no formato binário que usa o esquema de **“urn:scte:scte35:2013a:bin”** para ingestão Smooth o tipo **“scte35”** para ingestão RTMP. Para facilitar a conversão de intervalo [SCTE-35], que se baseia em carimbos de data/hora de apresentação de fluxo de transporte MPEG-2 (pt), um mapeamento entre PTS (pts_time + pts_adjustment da splice_time()) e a linha do tempo de mídia é fornecida pelo tempo de apresentação do evento (o campo fragment_absolute_time para ingestão Smooth e o campo de hora para ingestão RTMP). O mapeamento é necessário porque o valor de PTS de 33 bits passa aproximadamente a cada 26,5 horas.

A ingestão Smooth Streaming requer que a Data Box de Mídia (‘mdat’) deve conter o **splice_info_section()** definido na tabela 8-1 de [SCTE-35]. Para ingestão RTMP, o atributo de advertência da mensagem AMF é definido como o **splice_info_section()** codificado como base64. Quando as mensagens têm o formato descrito acima, elas são enviadas para clientes HLS, Smooth e Dash em conformidade com [SCTE-67].


## <a name="5-references"></a>5 Referências

**[SCTE-35]** ANSI/SCTE 35 2013a – Mensagem de advertência de inserção do programa digital para cabo, 2013a

**[SCTE-67]** ANSI/SCTE 67 2014 – Prática Recomendada para SCTE 35: Mensagem de advertência de inserção do programa digital para cabo

**[DASH]**  ISO/IEC 23009-1 2014 – Tecnologia da informação – Dynamic adaptive streaming sobre HTTP (DASH) – Parte 1: Formatos de descrição e segmento de apresentação de mídia, 2ª edição

**[HLS]** [“HTTP Live Streaming”, draft-pantos-http-live-streaming-14, 14 de outubro de 2014,](http://tools.ietf.org/html/draft-pantos-http-live-streaming-14)

**[MS-SSTR]** [“Protocolo Microsoft Smooth Streaming”, 15 de maio de 2014](https://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf)

**[AMF0]** [“Formato de mensagem de ação AMF0”](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)

**[LIVE-FMP4]** [Especificação de ingestão dinâmica de MP4 fragmentado dos Serviços de Mídia do Azure](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)

**[ISO-14496-12]** ISO/IEC 14496-12: Parte 12, formato de arquivo de mídia base ISO, quarta edição 2012-07-15.

**[RTMP**  [“Protocolo de mensagens em tempo real da Adobe”, 21 de dezembro de 2012](https://www.adobe.com/devnet/rtmp.html) 

------------------------------------------

## <a name="next-steps"></a>Próximas etapas
Exibir os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
