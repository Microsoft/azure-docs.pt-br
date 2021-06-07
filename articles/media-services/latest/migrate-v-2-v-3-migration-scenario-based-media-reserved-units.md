---
title: Diretrizes de migração de MRUs (unidades reservadas de mídia)
description: Este artigo fornece diretrizes baseadas em cenário MRU que ajudarão você a migrar dos serviços de mídia do Azure v2 para v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 00e1fa623fc625a568f6580998443758c3916be8
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563065"
---
# <a name="media-reserved-units-mrus-scenario-based-migration-guidance"></a>Diretrizes de migração baseadas em cenário de MRUs (unidades reservadas de mídia)

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-4.svg)

Este artigo fornece diretrizes baseadas em cenário MRU que ajudarão você a migrar dos serviços de mídia do Azure v2 para v3.

- Para novas contas v3 criadas no portal do Azure ou com a versão 2020-05-01 da API v3, você não precisa mais definir unidades reservadas de mídia (MRUs). O sistema agora será automaticamente dimensionado e reduzido com base na carga.
- Se você tiver uma conta V3 ou v2 que foi criada antes da versão 2020-05-01 da API, ainda poderá controlar a simultaneidade e o desempenho de seus trabalhos usando unidades reservadas de mídia. Para obter mais informações, consulte Scaling Media Processing (Colocação em escala do processamento de mídia). Você pode gerenciar o MRUs usando a CLI 2,0 para os serviços de mídia V3 ou usando o portal do Azure.  
- Se você não vir a opção de gerenciar MRUs no portal do Azure, você está executando uma conta que foi criada com a API 2020-05-01 ou posterior.
- Se você estiver familiarizado com a configuração de seu tipo MRU para S3, o desempenho será melhorado ou permanecerá o mesmo.
- Se você for um cliente v2 existente, precisará criar uma nova conta v2 para dar suporte ao aplicativo existente antes da conclusão da migração. 
- O indexador v1 ou outros processadores de mídia que ainda não estão totalmente preteridos talvez precisem ser habilitados novamente. 

Para obter mais informações sobre MRUs, consulte [unidades reservadas de mídia](concept-media-reserved-units.md) e [como dimensionar unidades reservadas de mídia](media-reserved-units-cli-how-to.md).

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>Conceitos de MRU, tutoriais e guias de instruções

### <a name="concepts"></a>Conceitos

[Unidades reservadas de mídia](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>Guias de como fazer

[Como dimensionar unidades reservadas de mídia](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>Exemplos

Você também pode [comparar o código V2 e v3 nos exemplos de código](migrate-v-2-v-3-migration-samples.md).
