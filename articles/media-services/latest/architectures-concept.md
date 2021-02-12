---
title: Arquiteturas dos Serviços de Mídia
description: Este artigo descreve as arquiteturas dos serviços de mídia.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: inhenkel
ms.openlocfilehash: ad464eb1c0b6dec694c7c40868a0f95fcfeaf6e8
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98891481"
---
# <a name="media-services-architectures"></a>Arquiteturas dos Serviços de Mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="live-streaming-digital-media"></a>Mídia digital de transmissão ao vivo

Uma solução de transmissão ao vivo permite que você capture vídeos em tempo real e transmita para os consumidores em tempo real, como entrevistas de transmissão, conferências e eventos esportivos online. Nesta solução, o vídeo é capturado por uma câmera de vídeo e enviado para um ponto de extremidade de entrada de canal. O canal recebe o fluxo de entrada ao vivo e o torna disponível para streaming por meio de um ponto de extremidade de streaming para um navegador da Web ou aplicativo móvel. O canal também fornece um ponto de extremidade de monitoramento de visualização para visualizar e validar seu fluxo antes do processamento e da entrega. O canal também pode registrar e armazenar o conteúdo ingerido a fim de ser transmitido posteriormente (vídeo sob demanda).

Essa solução é criada nos serviços gerenciados do Azure: serviços de mídia e rede de distribuição de conteúdo. Esses serviços são executados em um ambiente de alta disponibilidade, com patch e suporte, permitindo que você se concentre em sua solução em vez do ambiente em que eles são executados.

Consulte [mídia digital de transmissão ao vivo](/azure/architecture/solution-ideas/articles/digital-media-live-stream) no centro de arquitetura do Azure.

## <a name="video-on-demand-digital-media"></a>Mídia digital de vídeo por demanda

Uma solução básica de vídeo sob demanda que fornece a capacidade de transmitir conteúdo de vídeo gravado, como filmes, clipes de notícias, segmentos esportivos, vídeos de treinamento e tutoriais de atendimento ao cliente, para qualquer dispositivo de ponto de extremidade compatível com vídeo, aplicativo móvel ou navegador de desktop. Os arquivos de vídeo são carregados no armazenamento de BLOBs do Azure, codificados para um formato padrão de múltiplas taxas de bits e, em seguida, distribuídos por meio de todos os principais protocolos de streaming de taxa de bit adaptável (HLS, MPEG-DASH e Smooth) para o cliente Player de Mídia do Azure.

Essa solução é criada nos serviços gerenciados do Azure: armazenamento de BLOBs, rede de distribuição de conteúdo e Player de Mídia do Azure. Esses serviços são executados em um ambiente de alta disponibilidade, com patch e suporte, permitindo que você se concentre em sua solução em vez do ambiente em que eles são executados.

Consulte [mídia digital de vídeo por demanda](/azure/architecture/solution-ideas/articles/digital-media-video) no centro de arquitetura do Azure.

## <a name="gridwich-media-processing-system"></a>Sistema de processamento de mídia do Gridwich

O sistema Gridwich incorpora as práticas recomendadas para o processamento e a entrega de ativos de mídia no Azure. Embora o sistema Gridwich seja específico à mídia, o processamento de mensagens e a estrutura de eventos podem ser aplicados a qualquer fluxo de trabalho de processamento de eventos sem estado.

Consulte [sistema de processamento de mídia Gridwich](/azure/architecture/reference-architectures/media-services/gridwich-architecture) no centro de arquitetura do Azure.

## <a name="next-steps"></a>Próximas etapas

> [Visão geral dos Serviços de Mídia do Azure](media-services-overview.md)