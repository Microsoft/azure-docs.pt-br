---
title: Codificadores de transmissão ao vivo recomendados pelos serviços de mídia
description: Saiba mais sobre codificadores locais de transmissão ao vivo recomendados pelos Serviços de Mídia
services: media-services
keywords: codificação; codificadores; mídia
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: media-services
ms.openlocfilehash: bf282b3fbba0f22cd41c0420014c46ddb7c958eb
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955132"
---
# <a name="verified-on-premises-live-streaming-encoders"></a>Codificadores de streaming ao vivo no local verificados

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Nos Serviços de Mídia do Azure, um [Evento ao vivo](/rest/api/media/liveevents) (canal) representa um pipeline para processamento de conteúdo de streaming dinâmico. O Evento ao vivo recebe fluxos de entrada dinâmicos em uma de duas maneiras.

* Um codificador dinâmico local envia um RTMP com múltiplas taxas de bits ou fluxo Smooth Streaming (MP4 fragmentado) para o evento ao vivo que não está habilitado para executar a codificação ativa com os Serviços de Mídia. Os fluxos ingeridos passam por eventos ao vivo sem nenhum processamento adicional. Esse método é chamado **passagem**. Recomendamos que o codificador ao vivo envie fluxos de múltiplas taxas de bits em vez de um fluxo de taxa de bits única para um evento de passagem ao vivo para permitir o streaming de taxa de bits adaptável para o cliente. 

    Se você estiver usando fluxos de várias taxas de bits para o evento de passagem ao vivo, o tamanho do vídeo GOP e os fragmentos de vídeo em taxas de bits diferentes devem ser sincronizados para evitar um comportamento inesperado no lado da reprodução.

  > [!TIP]
  > O uso de um método de passagem é a maneira mais econômica de realizar uma transmissão ao vivo.
 
* Um codificador ao vivo local envia um fluxo de taxa de bits única para o evento ao vivo que está habilitado para executar a codificação ativa com os serviços de mídia em um dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). O evento ao vivo, em seguida, executa a codificação ativa do fluxo de entrada de taxa de bits única para um fluxo de vídeo de múltiplas taxas de bits (adaptável).

Este artigo aborda os codificadores de transmissão ao vivo verificados no local. A verificação é feita por meio da Autoverificação do fornecedor ou verificação do cliente. O Serviços de Mídia do Microsoft Azure não realiza testes completos ou rigorosos de cada codificador e não verifica continuamente as atualizações. Para obter instruções sobre como verificar seu codificador ao vivo local, consulte [verificar seu codificador local](become-on-premises-encoder-partner.md)

Para obter informações detalhadas sobre a codificação ativa com os Serviços de Mídia, consulte [Transmissão ao vivo com Serviços de Mídia v3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Requisitos do codificador

Os codificadores devem dar suporte a TLS 1,2 ao usar protocolos HTTPS ou RTMPS.

## <a name="live-encoders-that-output-rtmp"></a>Codificadores dinâmicos com RTMP de saída

Os Serviços de Mídias recomendam usar um dos seguintes codificadores dinâmicos que têm RTMP como saída. Os esquemas de URL com suporte são `rtmp://` ou `rtmps://`.

Ao transmitir via RTMP, verifique as configurações de firewall e/ou proxy para confirmar se as portas TCP de saída 1935 e 1936 estão abertas.<br/><br/>
Ao transmitir via RTMPS, verifique as configurações de firewall e/ou proxy para confirmar se as portas TCP de saída 2935 e 2936 estão abertas.

> [!NOTE]
> Os codificadores devem dar suporte a TLS 1,2 ao usar os protocolos RTMPS.

- Adobe Flash Media Live Encoder 3.2
- [Antix digital](http://www.antixdigital.com/) StreamZ Live (anteriormente imagine Communication SelenioFlex Live)
- [Blackmagic ATEM mini e ATEM mini PRO](https://www.blackmagicdesign.com/products/atemmini)
- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- Elemento dinâmico (versão 2.14.15 e superior)
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 e Hero 8
- Haivision KB
- Haivision Makito X HEVC
- [Restream.io](https://restream.io/)
- OBS Studio
- [Streamlabs OBS](https://streamlabs.com/)
- [Alternador Studio (iOS)](https://www.switcherstudio.com/)
- Telestream Wirecast (versão 13.0.2 ou superior devido ao requisito de TLS 1,2)
- Telestream Wirecast S (há suporte apenas para RTMP. Não há suporte a RTMPS devido à falta de TLS 1.2 +)
- Teradek Slice 756
- VMIX
- xStream

> [!WARNING]
> A lista de codificadores acima é apenas uma lista de recomendações. Os codificadores não são testados ou validados pela Microsoft continuamente, e atualizações ou alterações significativas podem ser introduzidas por fornecedores de codificadores ou projetos de software livre que podem interromper a compatibilidade. 

## <a name="live-encoders-that-output-fragmented-mp4-smooth-streaming-ingest"></a>Codificadores ao vivo que geram MP4 fragmentado (ingestão de Smooth Streaming)

Os Serviços de Mídia recomendam usar um dos seguintes codificadores dinâmicos que têm Smooth Streaming com múltiplas taxas de bits (MP4 fragmentado) como saída. Os esquemas de URL com suporte são `http://` ou `https://`.

> [!NOTE]
> Os codificadores devem dar suporte a TLS 1,2 ao usar protocolos HTTPS.

- Ateme TITAN Live
- [Antix digital](http://www.antixdigital.com/) StreamZ Live (anteriormente imagine Communication SelenioFlex Live)
- Cisco Digital Media Encoder 2200
- Elemento dinâmico (versão 2.14.15 e superior devido ao requisito de TLS 1,2)
- Envivio 4Caster C4 Gen III 
- [Ffmpeg](https://www.ffmpeg.org)
- Mídia Hero ao vivo e Hero 4K (UHD/HEVC) do Excel

> [!TIP]
>  Se você estiver transmitindo eventos ao vivo em vários idiomas (por exemplo, uma faixa de áudio em inglês e uma faixa de áudio em espanhol), poderá fazer isso com o Media Excel Live Encoder configurado para enviar o feed ao vivo para um evento ao vivo de passagem.

> [!WARNING]
> A lista de codificadores acima é apenas uma lista de recomendações. Os codificadores não são testados ou validados pela Microsoft continuamente, e o suporte ou bugs podem ser introduzidos por fornecedores de codificadores ou projetos de software livre que interrompem a compatibilidade a qualquer momento. 

## <a name="configuring-on-premises-live-encoder-settings"></a>Definir as configurações do codificador dinâmico local

Para obter informações sobre quais configurações são válidas para o seu tipo de evento ao vivo, consulte [Comparação de tipos de evento ao vivo](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Requisitos de reprodução

Para reproduzir conteúdo, os fluxos de áudio e de vídeo devem estar presentes. Não há suporte para a reprodução de fluxo somente de vídeo.

### <a name="configuration-tips"></a>Dicas de configuração

- Sempre que possível, use uma conexão de Internet com fio.
- Ao determinar os requisitos de largura de banda, dobre as taxas de bits de streaming. Embora não seja obrigatório, essa regra simples ajuda a atenuar o impacto do congestionamento de rede.
- Ao usar codificadores baseados em software, feche todos os programas desnecessários.
- A alteração da configuração do codificador após ela ter começado a efetuar push tem efeitos negativos sobre o evento. As alterações na configuração podem fazer o evento ficar instável. 
- Sempre teste e valide versões mais recentes do software de codificador para obter compatibilidade contínua com os serviços de mídia do Azure. A Microsoft não valida codificadores nesta lista, e a maioria das validações é feita pelos fornecedores de software diretamente como uma "certificação".
- Reserve bastante tempo para configurar seu evento. Para eventos em alta escala, recomendamos que você comece a configuração uma hora antes do evento.
- Use o vídeo H. 264 e a saída do codec de áudio AAC-LC.
- Fique com as resoluções e taxas de quadros com suporte para o tipo de evento ao vivo ao qual você está transmitindo (por exemplo, 60fps está rejeitado no momento.)
- Verifique se há um quadro-chave ou um alinhamento temporal GOP entre as qualidades de vídeo.
- Verifique se há um nome de fluxo exclusivo para cada qualidade de vídeo.
- Use a codificação CBR estrita recomendada para desempenho ideal de taxa de bits adaptável.

> [!IMPORTANT]
> Observe a condição física da máquina (CPU/memória/etc.), já que o carregamento de fragmentos para a nuvem envolve operações de CPU e e/s. Se você alterar as configurações no codificador, será determinado que redefina os canais/evento ao vivo para que a alteração tenha efeito.

## <a name="see-also"></a>Veja também

[Transmissão ao vivo com os Serviços de Mídia v3](live-streaming-overview.md)

## <a name="next-steps"></a>Próximas etapas

[Como verificar seu codificador](become-on-premises-encoder-partner.md)
