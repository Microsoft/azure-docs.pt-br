---
title: Diretrizes de migração de proteção de conteúdo
description: Este artigo fornece diretrizes baseadas em cenário de proteção de conteúdo que ajudarão você a migrar por minuto dos serviços de mídia do Azure v2 para v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 9c0040c0ad34b019eaa90bbd1571377ad3539578
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940119"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Diretrizes de migração baseadas em cenários de proteção de conteúdo

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-4.svg)

Este artigo fornece diretrizes baseadas em cenário de proteção de conteúdo que ajudarão você a migrar por minuto dos serviços de mídia do Azure v2 para v3.

## <a name="protect-content-in-v3-api"></a>Proteger o conteúdo na API v3

Use o suporte para recursos de [várias chaves](design-multi-drm-system-with-access-control.md) na nova API v3.

Consulte os conceitos, tutoriais e guias de proteção de conteúdo abaixo para obter etapas específicas.

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Conceitos, tutoriais e guias de proteção de conteúdo

### <a name="concepts"></a>Conceitos

- [Proteger seu conteúdo com a criptografia dinâmica dos serviços de mídia](content-protection-overview.md)
- [Criação de um sistema de proteção de conteúdo de DRM múltiplo com controle de acesso](design-multi-drm-system-with-access-control.md)
- [Modelo de licença dos serviços de mídia v3 com PlayReady](playready-license-template-overview.md)
- [Visão geral do modelo de licença do Media Services V3 com Widevine](widevine-license-template-overview.md)
- [Requisitos e configuração de licença do Apple FairPlay](fairplay-license-overview.md)
- [Políticas de streaming](streaming-policy-concept.md)
- [Políticas de chave de conteúdo](content-key-policy-concept.md)

### <a name="tutorials"></a>Tutoriais

[Início Rápido: Usar o portal para criptografar o conteúdo](encrypt-content-quickstart.md)

### <a name="how-to-guides"></a>Guias de como fazer

- [Obter uma chave de assinatura da política existente](get-content-key-policy-dotnet-howto.md)
- [Streaming FairPlay offline para iOS com os serviços de mídia v3](offline-fairplay-for-ios.md)
- [Streaming Widevine offline para Android com os serviços de mídia v3](offline-widevine-for-android.md)
- [Streaming do PlayReady offline para Windows 10 com os serviços de mídia v3](offline-plaready-streaming-for-windows-10.md)

## <a name="samples"></a>Exemplos

Você também pode [comparar o código V2 e v3 nos exemplos de código](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]