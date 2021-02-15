---
title: Configurar codificadores locais ao usar os Serviços de Mídia do Azure para criar fluxos de múltiplas taxas de bits | Microsoft Docs
description: Este tópico lista os codificadores ativos locais que podem ser usados para capturar os eventos ao vivo e enviar uma transmissão ao vivo de taxa de bits única para os canais do AMS (que são habilitados para a codificação ativa) para mais processamento. O tópico contém links para tutoriais que mostram como configurar os codificadores listados.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0ec6f046-0841-4673-9057-883bdbc30d5c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 77f2d433b32d180c7b3a819af96a50b721c2087f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89263426"
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

