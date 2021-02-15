---
title: Diretrizes de migração de empacotamento e entrega
description: Este artigo fornece diretrizes baseadas em cenário para empacotamento e entrega que ajudarão você a migrar dos serviços de mídia do Azure v2 para v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 90dfc2fd6f6258ec5bfdea38423489c72ee06e03
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928188"
---
# <a name="packaging-and-delivery-scenario-based-migration-guidance"></a>Diretrizes de migração baseadas em cenário de empacotamento e entrega

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-4.svg)

Este artigo fornece diretrizes baseadas em cenário para empacotamento e entrega que ajudarão você a migrar dos serviços de mídia do Azure v2 para v3.

Alterações importantes na forma como o conteúdo é publicado na API v3. O novo modelo de publicação é simplificado e usa menos entidades para criar um localizador de streaming. A API foi reduzida para apenas duas entidades versus as quatro entidades exigidas anteriormente. As políticas de chave de conteúdo e os localizadores de streaming agora substituem a necessidade de `ContentKeyAuthoriationPolicy` , `AssetDeliveyPolicy` , `ContentKey` e `AccessPolicy` .

## <a name="packaging-and-delivery-in-v3"></a>Empacotamento e entrega em v3

1. Criar [políticas de chave de conteúdo](content-key-policy-concept.md).
1. Criar [localizadores de streaming](streaming-locators-concept.md).
1. Obter os [caminhos de streaming](create-streaming-locator-build-url.md) 
    1. Configure [-o para](dynamic-packaging-overview.md#mpeg-dash-protocol) um [HLS](dynamic-packaging-overview.md#hls-protocol) ou um player.

Consulte a publicação de conceitos, tutoriais e guias de instruções abaixo para obter etapas específicas.

## <a name="publishing-concepts-tutorials-and-how-to-guides"></a>Publicando conceitos, tutoriais e guias de instruções

### <a name="concepts"></a>Conceitos

- [Empacotamento dinâmico nos Serviços de Mídia v3](dynamic-packaging-overview.md)
- [Filtros](filters-concept.md)
- [Filtrar seus manifestos usando o Dynamic Packager](filters-dynamic-manifest-overview.md)
- [Pontos de extremidade de streaming (origem) nos serviços de mídia do Azure](streaming-endpoint-concept.md)
- [Transmitir conteúdo com integração CDN](scale-streaming-cdn.md)
- [Localizadores de streaming](streaming-locators-concept.md)

### <a name="how-to-guides"></a>Guias de como fazer

- [Gerenciar pontos de extremidade de streaming com os serviços de mídia v3](manage-streaming-endpoints-howto.md)
- [Exemplo de CLI: publicar um ativo](cli-publish-asset.md)
- [Criar um localizador de streaming e criar URLs](create-streaming-locator-build-url.md)
- [Baixar os resultados de um trabalho](download-results-howto.md)
- [Sinalizar faixas de áudio descritivas](signal-descriptive-audio-howto.md)
- [Player de Mídia do Azure configuração completa](https://docs.microsoft.com/azure/media-services/azure-media-player/azure-media-player-full-setup)
- [Como usar o Video.js Player com os serviços de mídia do Azure](how-to-video-js-player.md)
- [Como usar o Shaka Player com os serviços de mídia do Azure](how-to-shaka-player.md)

## <a name="samples"></a>Exemplos

Você também pode [comparar o código V2 e v3 nos exemplos de código](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
