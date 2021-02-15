---
title: Tipos de LiveEvent dos serviços de mídia do Azure
description: Nos serviços de mídia do Azure, um evento ao vivo pode ser definido como uma *codificação ativa* ou de *passagem* . Este artigo mostra uma tabela detalhada que compara os tipos de eventos ao vivo.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 3126cbf4f9d87e9baa7267e309210b751a47069d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897791"
---
# <a name="live-event-types-comparison"></a>Comparação de tipos de Eventos ao Vivo

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Nos serviços de mídia do Azure, um  [evento ao vivo](/rest/api/media/liveevents) pode ser definido como uma *passagem* (um codificador ao vivo local envia um fluxo de taxa de bits múltipla) ou *codificação ativa* (um codificador ao vivo local envia um fluxo de taxa de bits única). 

Este artigo compara os recursos dos tipos de evento ao vivo.

## <a name="types-comparison"></a>Comparação de tipos 

A tabela a seguir compara os recursos dos tipos de evento ao vivo. Os tipos são definidos durante a criação usando [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType. None** -um codificador ao vivo local envia um fluxo de taxas de bits múltiplas. Os fluxos ingeridos passam pelo evento ao vivo sem nenhum processamento adicional. Também conhecido como evento de passagem ao vivo.
* **LiveEventEncodingType. Standard** -um codificador ao vivo local envia um fluxo de taxa de bits única para o evento ao vivo e os serviços de mídia criam fluxos de taxas de bits múltiplas. Se o feed de contribuição for de 720p ou de resolução superior, a predefinição de **default720p** codificará um conjunto de 6 pares de resolução/taxa de bits (os detalhes são seguidos posteriormente no artigo).
* **LiveEventEncodingType. Premium1080p** -um codificador ao vivo local envia um fluxo de taxa de bits única para o evento ao vivo e os serviços de mídia criam fluxos de taxas de bits múltiplas. A predefinição de default1080p especifica o conjunto de saída de pares de resolução/taxa de bits (os detalhes são seguidos posteriormente no artigo). 

| Recurso | Evento ao vivo de passagem | Evento ao vivo Standard ou Premium1080p |
| --- | --- | --- |
| A entrada de taxa de bits única é codificada em várias taxas de bits na nuvem |Não |Sim |
| Resolução máxima de vídeo para feed de contribuição |4K (4096 x 2160 a 60 quadros / seg) |1080p de (1920 x 1088 em 30 quadros por segundo)|
| Camadas máximas recomendadas no feed de contribuição|Até 12|Um áudio|
| Camadas máximo na saída| Igual à entrada|Até 6 (consulte predefinições de sistema abaixo)|
| Largura de banda agregada máxima de feed de contribuição|60 Mbps|N/D|
| Taxa de bits máxima para uma única camada de contribuição |20 Mbps|20 Mbps|
| Suporte para várias faixas de áudio de idioma|Sim|Não|
| Codecs de vídeo de entrada com suporte |H.264/AVC e H.265/HEVC|H.264/AVC|
| Codecs de vídeo de saída com suporte|Igual à entrada|H.264/AVC|
| Suporte para a profundidade de bits de vídeo, entrada e saída|Até 10 bits incluindo HDR 10/HLG|8 bits|
| Codecs de áudio de entrada com suporte|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Codecs de áudio de saída com suporte|Igual à entrada|AAC-LC|
| Resolução máxima de vídeo do vídeo de saída|Igual à entrada|Standard-720p, Premium1080p-1080p|
| Taxa máxima de quadros do vídeo de entrada|60 quadros/segundo|Standard ou Premium1080p-30 quadros/segundo|
| Protocolos de entrada|RTMP, MP4 fragmentado (Smooth Streaming)|RTMP, MP4 fragmentado (Smooth Streaming)|
| Preço|Confira a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique na guia “Vídeo ao vivo”|Confira a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique na guia “Vídeo ao vivo”|
| Tempo de execução máximo| 24 horas x 365 dias, linha ao vivo | 24 horas x 365 dias, linear ao vivo (versão prévia)|
| Capacidade de passar por meio do embedded CEA 608/708 legendas de dados|Sim|Sim|
| Capacidade de ativar a transcrição dinâmica|Sim|Sim|
| Suporte para inserção de imagens fixas|Não|Não|
| Suporte para sinalização de anúncios via API| Não|Não|
| Suporte para o ad sinalização por meio de mensagens na faixa de SCTE-35|Sim|Sim|
| Capacidade de recuperação de interrupções breves no feed de contribuição|Sim|Parcial|
| Suporte para GOPs de entrada não uniforme|Sim|Não – entrada deve ter GOP duração fixa|
| Suporte para entrada de taxa de quadros variável|Sim|Não – a entrada deve ser uma taxa de quadros fixa. Pequenas variações são toleradas, por exemplo, durante cenas ricas em movimento. Mas o feed de contribuição não pode descartar a taxa de quadros (por exemplo, para 15 quadros/segundo).|
| Desligamento automático do Evento ao Vivo quando há perda do feed de entrada|Não|Após 12 horas, se não houver nenhum LiveOutput em execução|

## <a name="system-presets"></a>Predefinições do sistema

As resoluções e taxas de bits contidas na saída do codificador ao vivo são determinadas pelo [predefinidor](/rest/api/media/liveevents/create#liveeventencoding). Se você estiver usando um codificador ativo **padrão** (LiveEventEncodingType. Standard), a predefinição *default720p* especificará um conjunto de 6 pares de resolução/taxa de bits descritos abaixo. Caso contrário, se estiver usando um codificador **Premium1080p** Live (LiveEventEncodingType. Premium1080p), a predefinição de *default1080p* especificará o conjunto de saída de pares de resolução/taxa de bits.

> [!NOTE]
> Você não poderá aplicar a predefinição de default1080p a um evento ao vivo se ela tiver sido configurada para codificação ativa padrão – você receberá um erro. Você também receberá um erro se tentar aplicar a predefinição default720p a um codificador ao vivo Premium1080p.

### <a name="output-video-streams-for-default720p"></a>Fluxos de vídeo de saída para default720p

Se o feed de contribuição for de 720p ou de resolução superior, a predefinição **default720p** codificará o feed nas 6 camadas a seguir. Na tabela a seguir, a taxa de bits é em Kbps, MaxFPS representa a taxa máxima permitida de quadros (em quadros/segundo), o perfil representa o perfil H. 264 usado.

| Bitrate | Largura | Altura | MáxFPS | Perfil |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Alta |
| 2200 |960 |540 |30 |Alta |
| 1350 |704 |396 |30 |Alta |
| 850 |512 |288 |30 |Alta |
| 550 |384 |216 |30 |Alta |
| 200 |340 |192 |30 |Alta |

> [!NOTE]
> Se você precisar personalizar a predefinição de codificação ativa, abra um tíquete de suporte por meio do portal do Azure. Você deve especificar a tabela desejada de taxa de bits e resolução de vídeo. Não há suporte para a personalização da taxa de bits de codificação de áudio. Verifique se há apenas uma camada em 720p e no máximo seis camadas. Além disso, especifique que você está solicitando uma predefinição.

### <a name="output-video-streams-for-default1080p"></a>Fluxos de vídeo de saída para default1080p

Se o feed de contribuição for de 1080p resolução, a predefinição de **default1080p** codificará o feed nas 6 camadas a seguir.

| Bitrate | Largura | Altura | MáxFPS | Perfil |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Alta |
| 3000 |1280 |720 |30 |Alta |
| 1600 |960 |540 |30 |Alta |
| 800 |640 |360 |30 |Alta |
| 400 |480 |270 |30 |Alta |
| 200 |320 |180 |30 |Alta |

> [!NOTE]
> Se você precisar personalizar a predefinição de codificação ativa, abra um tíquete de suporte por meio do portal do Azure. Você deve especificar a tabela desejada da resolução e das taxas de bits. Verifique se há apenas uma camada em 1080p e no máximo 6 camadas. Além disso, especifique que você está solicitando uma predefinição para um codificador ao vivo Premium1080p. Os valores específicos das taxas de bits e resoluções podem ser ajustados ao longo do tempo.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Fluxo de áudio de saída para default720p e default1080p

Para as predefinições *default720p* e *default1080p* , o áudio é codificado para estéreo AAC-LC em 128 kbps. A taxa de amostragem segue a faixa de áudio no feed de contribuição.

## <a name="implicit-properties-of-the-live-encoder"></a>Propriedades implícitas do codificador ao vivo

A seção anterior descreve as propriedades do codificador ao vivo que podem ser controladas explicitamente, por meio da predefinição, como o número de camadas, resoluções e taxas de bits. Esta seção esclarece as propriedades implícitas.

### <a name="group-of-pictures-gop-duration"></a>Duração de GOP (grupo de imagens)

O codificador ao vivo segue a estrutura [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) do feed de contribuição, o que significa que as camadas de saída terão a mesma duração de GOP. Portanto, é recomendável que você configure o codificador local para produzir um feed de contribuição com duração fixa GOP (normalmente 2 segundos). Isso garantirá que os fluxos de saída HLS e MPEG DASH do serviço também tenham corrigido GOP durações. Pequenas variações em durações de GOP provavelmente serão toleradas pela maioria dos dispositivos.

### <a name="frame-rate"></a>Taxa de quadros

O codificador ao vivo também segue as durações dos quadros de vídeo individuais no feed de contribuição, o que significa que as camadas de saída terão quadros com as mesmas durações. Portanto, é recomendável que você configure o codificador local para produzir um feed de contribuição com taxa de quadros fixa (no máximo 30 quadros/segundo). Isso garantirá que os fluxos de saída HLS e MPEG DASH do serviço também tenham durações de taxas de quadros fixas. Pequenas variações nas taxas de quadros podem ser toleradas pela maioria dos dispositivos, mas não há nenhuma garantia de que o codificador ao vivo produzirá uma saída que será reproduzida corretamente. Seu codificador ao vivo local não deve estar descartando quadros (por exemplo, em condições de bateria fraca) ou variando a taxa de quadros de qualquer forma.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Resolução das camadas de feed de contribuição e saída

O codificador ao vivo está configurado para evitar a conversão do feed de contribuição. Como resultado, a resolução máxima das camadas de saída não vai exceder a do feed de contribuição.

Por exemplo, se você enviar um feed de contribuição em 720p para um evento ao vivo configurado para a codificação ativa do default1080p, a saída terá apenas 5 camadas, começando com 720p em 3Mbps, indo até 1080p a 200 Kbps. Ou, se você enviar um feed de contribuição em 360p para um evento ao vivo configurado para codificação ativa padrão, a saída conterá três camadas (em resoluções de 288p, 216p e 192P). No caso de degeneração, se você enviar um feed de contribuição de, digamos, 160x90 pixels para um codificador ao vivo padrão, a saída conterá uma camada na resolução de 160x90 na mesma taxa de bits que a do feed de contribuição.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Taxa de bits das camadas de feed de contribuição e saída

O codificador ao vivo é configurado para honrar as configurações de taxa de bits na predefinição, independentemente da taxa de bits do feed de contribuição. Como resultado, a taxa de bits das camadas de saída pode exceder a do feed de contribuição. Por exemplo, se você enviar em um feed de contribuição em uma resolução de 720p a 1 Mbps, as camadas de saída permanecerão as mesmas da [tabela](live-event-types-comparison.md#output-video-streams-for-default720p) acima.

## <a name="next-steps"></a>Próximas etapas

[Visão geral da transmissão ao vivo](live-streaming-overview.md)
