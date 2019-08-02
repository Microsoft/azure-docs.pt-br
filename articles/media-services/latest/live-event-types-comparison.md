---
title: Tipos de LiveEvent do Azure Media Services | Microsoft Docs
description: Este artigo mostra uma tabela detalhada que compara os tipos de LiveEvent.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 93f01513841d1174fea796f1615ab05df0a41af4
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150373"
---
# <a name="live-event-types-comparison"></a>Comparação de tipos de Eventos ao Vivo

Nos Serviços de Mídia do Azure, um [Evento ao vivo](https://docs.microsoft.com/rest/api/media/liveevents) pode ser um dos dois tipos: codificação ativa e passagem. 

## <a name="types-comparison"></a>Comparação de tipos 

A tabela a seguir compara os recursos dos tipos de evento ao vivo. Os tipos são definidos durante a criação usando [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None** -um codificador ao vivo do local envia um fluxo de taxa de bits múltiplas. Os fluxos ingeridos passam por evento ao vivo sem nenhum processamento adicional. 
* **LiveEventEncodingType.Standard** – um codificador ao vivo envia um fluxo de taxa de bits única para o evento ao vivo e os serviços de mídia cria vários fluxos de taxa de bits no local. Se o feed de contribuição é de 720p ou resolução mais alta, o **Default720p** predefinição codifica um conjunto de pares de resolução/taxa de bits 6 (detalhes a seguir posteriormente neste artigo).
* **LiveEventEncodingType.Premium1080p** – um codificador ao vivo envia um fluxo de taxa de bits única para o evento ao vivo e os serviços de mídia cria vários fluxos de taxa de bits no local. A predefinição Default1080p Especifica o conjunto de saída de pares de resolução/taxa de bits (detalhes a seguir posteriormente neste artigo). 

| Recurso | Evento ao vivo de passagem | Padrão ou Premium1080p de evento ao vivo |
| --- | --- | --- |
| A entrada de taxa de bits única é codificada em várias taxas de bits na nuvem |Não |Sim |
| Resolução máxima de vídeo para feed de contribuição |4K (4096 x 2160 a 60 quadros / seg) |1080p de (1920 x 1088 em 30 quadros por segundo)|
| Camadas máximas recomendadas no feed de contribuição|Até 12|Um áudio|
| Camadas máximo na saída| Igual à entrada|Até 6 (veja abaixo as predefinições de sistema)|
| Largura de banda agregada máxima de feed de contribuição|60 Mbps|N/D|
| Taxa de bits máxima para uma única camada de contribuição |20 Mbps|20 Mbps|
| Suporte para várias faixas de áudio de idioma|Sim|Não|
| Codecs de vídeo de entrada com suporte |H.264/AVC e H.265/HEVC|H.264/AVC|
| Codecs de vídeo de saída com suporte|Igual à entrada|H.264/AVC|
| Suporte para a profundidade de bits de vídeo, entrada e saída|Até 10 bits incluindo HDR 10/HLG|8 bits|
| Codecs de áudio de entrada com suporte|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Codecs de áudio de saída com suporte|Igual à entrada|AAC-LC|
| Resolução máxima de vídeo do vídeo de saída|Igual à entrada|Standard - 720p, Premium1080p - 1080p|
| Taxa máxima de quadros de vídeo de entrada|60 quadros por segundo|Padrão ou Premium1080p - 30 quadros por segundo|
| Protocolos de entrada|RTMP, MP4 fragmentado (Smooth Streaming)|RTMP, MP4 fragmentado (Smooth Streaming)|
| Preço|Confira a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique na guia “Vídeo ao vivo”|Confira a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique na guia “Vídeo ao vivo”|
| Tempo de execução máximo| 24 horas x 365 dias, linha ao vivo | Até 24 horas|
| Capacidade de passar por meio do embedded CEA 608/708 legendas de dados|Sim|Sim|
| Suporte para inserção de imagens fixas|Não|Não|
| Suporte para sinalização de anúncios via API| Não|Não|
| Suporte para o ad sinalização por meio de mensagens na faixa de SCTE-35|Sim|Sim|
| Capacidade de recuperação de interrupções breves no feed de contribuição|Sim|Parcial|
| Suporte para GOPs de entrada não uniforme|Sim|Não – entrada deve ter GOP duração fixa|
| Suporte para entrada de taxa de quadros variável|Sim|Não – a entrada deve ser uma taxa de quadros fixa. Pequenas variações são toleradas, por exemplo, durante cenas ricas em movimento. Mas o feed de contribuição não é possível descartar a taxa de quadros (por exemplo, a 15 quadros/segundo).|
| Desligamento automático do Evento ao Vivo quando há perda do feed de entrada|Não|Após 12 horas, se não houver nenhum LiveOutput em execução|

## <a name="system-presets"></a>Predefinições do sistema

As resoluções e taxas de bits contidas na saída do codificador ao vivo são determinadas pelo [presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). Se usando um **Standard** live encoder (LiveEventEncodingType.Standard), em seguida, a *Default720p* predefinição Especifica um conjunto de pares de resolução/taxa de bits 6 descrito abaixo. Caso contrário, se usar um **Premium1080p** live encoder (LiveEventEncodingType.Premium1080p), em seguida, a *Default1080p* predefinição Especifica o conjunto de saída de pares de resolução/taxa de bits.

> [!NOTE]
> Não é possível aplicar o Default1080p predefinição a um evento ao vivo, se ele tiver sido configurado para a codificação ativa padrão, você receberá um erro. Você também receberá um erro se você tentar aplicar o Default720p predefinição a um codificador ao vivo de Premium1080p.

### <a name="output-video-streams-for-default720p"></a>Fluxos de vídeo de saída para Default720p

Se o feed de contribuição é de 720p ou resolução mais alta, o **Default720p** predefinição codificará o feed em 6 camadas a seguir. Na tabela a seguir, a taxa de bits é em kbps, MaxFPS representa a taxa de quadros permitido máximo (em quadros/segundo), perfil representa o perfil de H.264 usado.

| Bitrate | Largura | Altura | MáxFPS | Perfil |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Alto |
| 2200 |960 |540 |30 |Alto |
| 1350 |704 |396 |30 |Alto |
| 850 |512 |288 |30 |Alto |
| 550 |384 |216 |30 |Alto |
| 200 |340 |192 |30 |Alto |

> [!NOTE]
> Se você precisar personalizar a predefinição de codificação ao vivo, abra um tíquete de suporte por meio do Portal do Azure. Você deve especificar a tabela desejada da resolução e das taxas de bits. Verifique se há apenas uma camada em 720p e no máximo seis camadas. Também especifique que você está solicitando uma predefinição para um codificador dinâmico padrão.
> Os valores específicos das resoluções e taxas de bits podem ser ajustados ao longo do tempo

### <a name="output-video-streams-for-default1080p"></a>Fluxos de vídeo de saída para Default1080p

Se o feed de contribuição é de resolução de 1080p, o **Default1080p** predefinição codificará o feed em 6 camadas a seguir.

| Bitrate | Largura | Altura | MáxFPS | Perfil |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Alto |
| 3000 |1280 |720 |30 |Alto |
| 1600 |960 |540 |30 |Alto |
| 800 |640 |360 |30 |Alto |
| 400 |480 |270 |30 |Alto |
| 200 |320 |180 |30 |Alto |

> [!NOTE]
> Se você precisar personalizar a predefinição de codificação ao vivo, abra um tíquete de suporte por meio do Portal do Azure. Você deve especificar a tabela desejada da resolução e das taxas de bits. Verifique se há apenas uma camada em 1080p e no máximo de 6 camadas. Também especifique que você está solicitando uma predefinição para um codificador ao vivo de Premium1080p.
> Os valores específicos das resoluções e taxas de bits podem ser ajustados ao longo do tempo.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Stream de áudio de saída para Default720p e Default1080p

Para ambos *Default720p* e *Default1080p* predefinições de áudio é codificado como estéreo AAC-LC 128 Kbps. A taxa de amostragem segue que da faixa de áudio no feed de contribuição.

## <a name="implicit-properties-of-the-live-encoder"></a>Propriedades implícitas do codificador ao vivo

A seção anterior descreve as propriedades do codificador ao vivo que podem ser controlados explicitamente, por meio de predefinição - como o número de camadas, resoluções e taxas de bits. Esta seção explica as propriedades implícitas.

### <a name="group-of-pictures-gop-duration"></a>Grupo de duração de imagens (GOP)

O codificador ao vivo segue o [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) estrutura da contribuição feed - o que significa que as camadas de saída terá a mesma duração de GOP. Portanto, é recomendável que você configure o codificador local para produzir uma feed de contribuição com fixo duração de GOP (normalmente em 2 segundos). Isso garantirá que os fluxos HLS e MPEG DASH saídos do serviço também corrigiu durações de GOP. Pequenas variações em durações de GOP provavelmente ser aceitos pela maioria dos dispositivos.

### <a name="frame-rate"></a>Taxa de quadros

O codificador ao vivo também segue as durações dos quadros de vídeo individuais no feed de contribuição de - que significa que as camadas de saída terá quadros com os mesmos durações. Portanto, é recomendável que você configure o codificador de local para produzir um feed de contribuição que tem a taxa de quadros fixa (no máximo 30 quadros por segundo). Isso garantirá que os fluxos HLS e MPEG DASH saídos do serviço também corrigiu durações de taxas de quadro. Pequenas variações em taxas de quadros podem ser toleradas pela maioria dos dispositivos, mas não há nenhuma garantia de que o codificador ao vivo produzirá uma saída que será executada corretamente. Codificador dinâmico local deve não ser descartando (por exemplo, quadros em condições de pouca bateria) ou variar a taxa de quadros de qualquer forma.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Resolução de contribuição de feed e camadas de saída

O codificador ao vivo é configurado para evitar upconverting a feed de contribuição. Como resultado a resolução máxima das camadas de saída não excederá do feed de contribuição.

Por exemplo, se você enviar uma contribuição de feed de 720p para codificação ativa de um evento ao vivo configurado para Default1080p, a saída terá apenas 5 camadas, começando com 720p em 3Mbps, indo até 1080p 200 Kbps. Ou, se você enviar uma contribuição 360p do feed em um evento ao vivo configurado para o padrão de codificação ativa, a saída vai conter de 3 camadas (em resoluções de 288p, p 216 e 192p). No caso de degeneração, se você enviar um feed de contribuição de, digamos, 160 x 90 pixels para um codificador dinâmico padrão, a saída conterá uma camada na resolução de 160 x 90 na mesma taxa de bits que o feed de contribuição.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Taxa de bits de contribuição de feed e camadas de saída

O codificador ao vivo é configurado para honrar as configurações de taxa de bits na predefinição, independentemente da taxa de bits de feed de contribuição. Como resultado pode exceder a taxa de bits das camadas de saída do feed de contribuição. Por exemplo, se você enviar em um feed com uma resolução de 720p em 1 Mbps de contribuição, as camadas de saída permanecerá o mesmo que de [tabela](live-event-types-comparison.md#output-video-streams-for-default720p) acima.

## <a name="next-steps"></a>Próximas etapas

[Visão geral da transmissão ao vivo](live-streaming-overview.md)
