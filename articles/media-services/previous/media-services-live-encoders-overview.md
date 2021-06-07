---
title: Configurar codificadores locais ao usar os Serviços de Mídia do Azure para criar fluxos de múltiplas taxas de bits | Microsoft Docs
description: Este tópico lista os codificadores ativos locais que podem ser usados para capturar os eventos ao vivo e enviar uma transmissão ao vivo de taxa de bits única para os canais do AMS (que são habilitados para a codificação ativa) para mais processamento. O tópico contém links para tutoriais que mostram como configurar os codificadores listados.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 0ec6f046-0841-4673-9057-883bdbc30d5c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d83c81e1a8a7d8d689e18cc8ba46fbb137959481
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014852"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Como configurar os codificadores locais ao usar os Serviços de Mídia do Azure para criar fluxos de múltiplas taxas de bits

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Este tópico lista os codificadores ativos locais que podem ser usados para capturar os eventos ao vivo e enviar uma transmissão ao vivo de taxa de bits única para os canais do AMS (que são habilitados para a codificação ativa) para mais processamento. O tópico também contém links para os tutoriais que mostram como configurar os codificadores listados.

> [!NOTE]
> Ao transmitir via RTMP, verifique as configurações de firewall e/ou proxy para confirmar se as portas TCP de saída 1935 e 1936 estão abertas.

## <a name="haivision-kb-encoder"></a>Codificador Haivision KB
Para saber mais sobre como configurar o [Codificador Haivision KB](https://www.haivision.com/products/kb-series/) para enviar uma transmissão ao vivo de taxa de bits única para um Canal do AMS, veja [Configurando o Codificador Haivision KB](media-services-configure-kb-live-encoder.md).

## <a name="telestream-wirecast"></a>Telestream Wirecast
Para obter informações sobre como configurar o codificador [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) para enviar uma transmissão ao vivo de taxa de bits única para um Canal do AMS, veja [Configurando o Wirecast](media-services-configure-wirecast-live-encoder.md).

## <a name="elemental-live"></a>Elemental Live
Para obter mais informações, consulte [Elemental Live](https://www.elemental.com/products/aws-elemental-appliances-software/#elemental-live).

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Próximas etapas

[Transmissão ao vivo usando os Serviços de Mídia do Azure para criar fluxos de múltiplas taxas de bits](media-services-manage-live-encoder-enabled-channels.md).

