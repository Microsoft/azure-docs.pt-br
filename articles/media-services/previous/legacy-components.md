---
title: Componentes herdados dos serviços de mídia do Azure | Microsoft Docs
description: Este tópico aborda os componentes herdados dos serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 3487e735e48cb5067515762d6276429ca81e43fe
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008732"
---
# <a name="azure-media-services-legacy-components"></a>Componentes herdados dos serviços de mídia do Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Com o tempo, aprimoramos os componentes do serviço de mídia e desativamos os componentes herdados. Este artigo ajuda você a migrar seu aplicativo de um componente herdado para um componente atual.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Planos de aposentadoria de componentes herdados e diretrizes de migração

Os processadores de mídia do *Windows Azure Media Encoder* (WAME) e *do Azure Media Encoder* (ame) foram preteridos.

* [Migrar do codificador de mídia do Windows Azure para Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrar do Azure Media Encoder para Media Encoder Standard](migrate-azure-media-encoder.md)

Os seguintes Análise de Mídia processadores de mídia foram preteridos ou em breve para serem preteridos:

  
 
| **Nome do processador de mídia** | **Data de aposentadoria** | **Observações adicionais** |
| --- | --- | ---|
| Azure Media Indexer 2 | 1º de janeiro de 2020 | Esse processador de mídia será substituído pelo [modo básico AudioAnalyzerPreset do Media Services V3](../latest/analyzing-video-audio-files-concept.md). Para obter mais informações, consulte [migrar do Azure Media indexer 2 para os serviços de mídia do Azure Video indexer](migrate-indexer-v1-v2.md). |
| Indexador de Mídia do Azure | 1º de março de 2023 | Esse processador de mídia será substituído pelo [modo básico AudioAnalyzerPreset do Media Services V3](../latest/analyzing-video-audio-files-concept.md). Para obter mais informações, consulte [migrar do Azure Media indexer 2 para os serviços de mídia do Azure Video indexer](migrate-indexer-v1-v2.md). |
| Detecção de movimento | 1º de junho, 2020|Não há planos de substituição no momento. |
| Resumo de vídeo |1º de junho, 2020|Não há planos de substituição no momento.|
| Reconhecimento óptico de caracteres em vídeo | 1º de junho, 2020 |Este processador de mídia foi substituído pelos [serviços de mídia do Azure Video indexer](../video-indexer/index.yml). Além disso, considere o uso da [API dos serviços de mídia do Azure v3](../latest/analyzing-video-audio-files-concept.md). <br/>Consulte [comparar as predefinições e video indexer dos serviços de mídia do Azure v3](../video-indexer/compare-video-indexer-with-media-services-presets.md). |
| Detector Facial | 1º de junho, 2020 | Este processador de mídia foi substituído pelos [serviços de mídia do Azure Video indexer](../video-indexer/index.yml). Além disso, considere o uso da [API dos serviços de mídia do Azure v3](../latest/analyzing-video-audio-files-concept.md). <br/>Consulte [comparar as predefinições e video indexer dos serviços de mídia do Azure v3](../video-indexer/compare-video-indexer-with-media-services-presets.md). |
| Content Moderator | 1º de junho, 2020 |Este processador de mídia foi substituído pelos [serviços de mídia do Azure Video indexer](../video-indexer/index.yml). Além disso, considere o uso da [API dos serviços de mídia do Azure v3](../latest/analyzing-video-audio-files-concept.md). <br/>Consulte [comparar as predefinições e video indexer dos serviços de mídia do Azure v3](../video-indexer/compare-video-indexer-with-media-services-presets.md). |

## <a name="next-steps"></a>Próximas etapas

[Diretrizes de migração dos Serviços de Mídia v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)
