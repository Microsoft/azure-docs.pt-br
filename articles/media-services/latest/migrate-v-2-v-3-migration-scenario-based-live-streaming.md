---
title: Diretrizes de migração de streaming ao vivo dos serviços de mídia
description: Este artigo fornece orientação baseada em cenários de transmissão ao vivo que ajudará você a migrar por minuto dos serviços de mídia do Azure v2 para v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 322a599d5ad02ddcf19c61261d968d098b6c9871
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563119"
---
# <a name="live-streaming-scenario-based-migration-guidance"></a>Diretrizes de migração baseadas em cenários de transmissão ao vivo

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-4.svg)

O portal do Azure agora dá suporte à configuração e ao gerenciamento de eventos ao vivo.  Você é incentivado a experimentá-lo ao testar sua migração de v2 para v3.

## <a name="test-the-v3-live-event-workflow"></a>Testar o fluxo de trabalho de evento ao vivo v3

> [!NOTE]
> Os canais e programas criados com v2 (que são mapeados para eventos ao vivo e saídas ao vivo em v3) são gerenciados com a API v3. A orientação é alternar para eventos dinâmicos v3 e saídas ao vivo em um momento conveniente quando você pode parar o canal v2 existente. Atualmente, não há suporte para migrar diretamente um canal dinâmico 24x7 em execução contínua para os novos eventos ao vivo v3. Portanto, o tempo de inatividade de manutenção precisa ser coordenado com a melhor conveniência para seu público e seu negócio.

Teste a nova maneira de fornecer eventos ao vivo com os serviços de mídia antes de mover seu conteúdo de v2 para v3. Aqui estão os recursos v3 com os quais trabalhar e considerar a migração.

- Crie um novo [evento ao vivo](live-events-outputs-concept.md#live-events) V3 para codificação. Você pode habilitar as [predefinições de codificação 1080p e 720p](live-event-types-comparison.md#system-presets).
- Usar a entidade de [saída em tempo real](live-events-outputs-concept.md#live-outputs) em vez de programas
- Criar [localizadores de streaming](streaming-locators-concept.md).
- Considere sua necessidade de transmissão ao vivo [HLS e Dash](dynamic-packaging-overview.md) .
- Se você precisar de início rápido de eventos ao vivo, explore os novos recursos do [modo de espera](live-events-outputs-concept.md#standby-mode) .
- Se você quiser transcrever seu evento ao vivo enquanto ele estiver acontecendo, explore o novo recurso de [transcrição ao vivo](live-transcription.md) .
- Crie eventos ao vivo 24x7x365 na v3 se precisar de uma duração de streaming maior.
- Use a [grade de eventos](monitoring/monitor-events-portal-how-to.md) para monitorar seus eventos ao vivo.

Veja os conceitos de eventos ao vivo, tutoriais e guias de instruções abaixo para obter etapas específicas.

## <a name="live-events-concepts-tutorials-and-how-to-guides"></a>Conceitos, tutoriais e guias de eventos ao vivo

### <a name="concepts"></a>Conceitos

- [Transmissão ao vivo com os Serviços de Mídia do Azure v3](live-streaming-overview.md)
- [Eventos e saídas ao vivo nos Serviços de Mídia](live-events-outputs-concept.md)
- [Codificadores de streaming ao vivo no local verificados](recommended-on-premises-live-encoders.md)
- [Usar mudança de tempo e saídas ao vivo para criar reprodução de vídeo sob demanda](live-event-cloud-dvr.md)
- [Transcrição ao vivo (visualização)](live-transcription.md)
- [Comparação de tipos de eventos ao vivo](live-event-types-comparison.md)
- [Estados e cobrança de eventos ao vivo](live-event-states-billing.md)
- [Configurações de baixa latência de evento ao vivo](live-event-latency.md)
- [Códigos de erro de eventos dinâmicos dos serviços de mídia](live-event-error-codes.md)

### <a name="tutorials-and-quickstarts"></a>Tutoriais e guias de início rápido

- [Tutorial: Transmissão ao vivo com os Serviços de Mídia](stream-live-tutorial-with-api.md)
- [Criar uma transmissão ao vivo dos Serviços de Mídia do Azure com o OBS](live-events-obs-quickstart.md)
- [Início Rápido: Carregar, codificar e transmitir o conteúdo com o portal](manage-assets-quickstart.md)
- [Início rápido: criar uma transmissão ao vivo dos serviços de mídia do Azure com Wirecast](live-events-wirecast-quickstart.md)

## <a name="samples"></a>Exemplos

Você também pode [comparar o código V2 e v3 nos exemplos de código](migrate-v-2-v-3-migration-samples.md).
