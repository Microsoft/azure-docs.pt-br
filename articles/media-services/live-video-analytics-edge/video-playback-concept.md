---
title: Reprodução de vídeo-Azure
description: Espaço reservado
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 17b1f93c18dfb013916c4c0d3756fb97a73e2675
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87042970"
---
# <a name="video-playback"></a>Reprodução de vídeo 

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida 

* [Visão geral da Análise de Vídeo ao vivo no IoT Edge](overview.md)
* [Análise de Vídeo ao vivo na terminologia do IoT Edge](terminology.md)
* [Conceito de grafo de mídia](media-graph-concept.md)

## <a name="overview"></a>Visão geral  

Você pode usar [grafos de mídia](media-graph-concept.md) para gravar vídeo em um [ativo](terminology.md#asset)dos serviços de mídia do Azure. Neste documento, você pode aprender sobre as etapas que precisa executar para reproduzir um ativo usando os recursos de streaming existentes dos serviços de mídia do Azure.

## <a name="streaming-endpoint"></a>ponto de extremidade de streaming 

Você pode usar os serviços de mídia do Azure para [transmitir](terminology.md#streaming) o ativo para players de vídeo usando protocolos de streaming de mídia baseados em http padrão do setor, como http Live streaming (hls) e MPEG-Dash. Essa conversão de mídia de conteúdo gravado em formatos de streaming é tratada por um [ponto de extremidade de streaming](../latest/streaming-endpoint-concept.md), que é um recurso que você precisa provisionar em sua conta de serviço de mídia do Azure.

## <a name="streaming-policy"></a>Política de streaming 

Os serviços de mídia do Azure oferecem diferentes métodos para proteger seus fluxos de vídeo, conforme discutido no artigo [proteger o conteúdo com criptografia dinâmica dos serviços de mídia](../latest/content-protection-overview.md) . Em um alto nível, as opções de proteção de conteúdo são:

* **Streaming no-Clear** – onde nenhuma criptografia é aplicada durante o streaming.
* **Use criptografia AES (AES-128)** – e implemente um método para entregar as chaves para descriptografar o vídeo somente para visualizadores autenticados.
* **Use sistemas de Rights Management digital (DRM)** – para controlar o uso, a modificação e a entrega de vídeo para dispositivos que impõem essas políticas.

Para obter a proteção de conteúdo, você pode definir e criar uma [política de streaming](../latest/streaming-policy-concept.md) em sua conta de serviço de mídia e usá-la para transmitir todos os ativos (supondo que todos os fluxos tenham os mesmos requisitos de segurança). Você também pode usar qualquer uma das políticas predefinidas (como Predefined_ClearStreamingOnly).

## <a name="streaming-locator"></a>Localizador de streaming  

Depois que um ponto de extremidade de streaming for iniciado em sua conta de serviço de mídia e a política de streaming definida, você poderá passar para transmitir mídia gravada de um ativo por meio de protocolos HLS ou DASH. Os Web players e os aplicativos móveis precisam de uma URL apontando para o fluxo HLS ou DASH. Você pode criar essa URL usando o [localizador de streaming](../latest/streaming-locators-concept.md). Conforme discutido neste artigo e mostrado no exemplo [criar um localizador de streaming e URLs de Build](../latest/create-streaming-locator-build-url.md) , a URL de streaming é composta do ponto de extremidade de streaming, da política de streaming e do localizador de streaming.

## <a name="content-recorded-using-file-sink"></a>Conteúdo registrado usando o coletor de arquivos  

Conforme descrito no [coletor de arquivos de gráficos de mídia](media-graph-concept.md#file-sink), você pode usar grafos de mídia para gravar vídeos no sistema de arquivos local do dispositivo de borda usando um coletor de arquivos em seu grafo de mídia. O coletor de arquivos gera arquivos [MP4](https://developer.mozilla.org/docs/Web/Media/Formats/Containers#MP4) , e você pode usar o elemento de [ &lt; vídeo &gt; ](https://developer.mozilla.org/docs/Web/HTML/Element/video) HTML5 para reproduzir esse conteúdo. 

## <a name="next-steps"></a>Próximas etapas

[Azure IoT Edge](../../iot-edge/index.yml)
<!--
## Next steps

[Playback recording](playback-recording-how-to.md)
-->
