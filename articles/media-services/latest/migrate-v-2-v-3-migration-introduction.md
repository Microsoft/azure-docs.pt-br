---
title: Migrar da introdução dos serviços de mídia v2 para v3
description: Este artigo é uma introdução à migração dos serviços de mídia v2 para v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 3514a7c809e939ea2c45afa5ab60539232b8781f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953284"
---
# <a name="migrate-from-media-services-v2-to-v3-introduction"></a>Migrar da introdução dos serviços de mídia v2 para v3

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

O guia de migração dos serviços de mídia ajuda você a migrar de APIs do Media Services v2 para APIs v3 com base em uma migração que aproveita os novos recursos e funções que agora estão disponíveis. Você deve usar o melhor julgamento e determinar o que melhor se adapta ao seu cenário.

## <a name="how-to-use-this-guide"></a>Como usar este guia

### <a name="navigating"></a>Navegação

Em todo o guia, você verá o gráfico a seguir.

![etapas de migração](./media/migration-guide/steps.svg)<br/>

A etapa em que você está será indicada por uma alteração de cor no número da etapa, desta forma:

![etapas de migração 2](./media/migration-guide/steps-2.svg)<br/>

No final de cada página, você verá links para o restante dos documentos de migração que podem ser lidos abaixo do título **próximas etapas** .

### <a name="guidance"></a>Diretrizes

As diretrizes fornecidas aqui são *gerais*. Ele inclui conteúdo para melhorar a conscientização do que agora está disponível na v3, bem como o que mudou nos fluxos de trabalho dos serviços de mídia.

Para obter diretrizes mais detalhadas, incluindo capturas de tela e elementos gráficos conceituais, há links para conceitos, tutoriais, guias de início rápido, exemplos e referências de API em cada tópico baseado em cenário. Também listamos exemplos que ajudam a comparar a API V2 com a API v3.

## <a name="migration-guidance-overview"></a>Visão geral das diretrizes de migração

Há quatro etapas gerais a serem seguidas durante a migração:

## <a name="step-1-benefits"></a>Benefícios da etapa 1

<hr color="#5ea0ef" size="10">

[Entenda os benefícios](migrate-v-2-v-3-migration-benefits.md) da migração para a API do Media Services V3.

## <a name="step-2-differences"></a>Diferenças da etapa 2

<hr color="#5ea0ef" size="10">

Entenda as diferenças entre a API do Media Services V2 e a API v3.

- [Acesso à API](migrate-v-2-v-3-differences-api-access.md)
- [Lacunas de recursos](migrate-v-2-v-3-differences-feature-gaps.md)
- [Alterações de terminologia e entidade](migrate-v-2-v-3-differences-terminology.md)

## <a name="step-3-sdk-setup"></a>Etapa 3 configuração do SDK

<hr color="#5ea0ef" size="10">

Entenda as diferenças do SDK e [configure para migrar para a API REST do V3 ou o SDK do cliente](migrate-v-2-v-3-migration-setup.md).

## <a name="step-4-scenario-based-guidance"></a>Diretrizes baseadas no cenário da etapa 4

<hr color="#5ea0ef" size="10">

Seu aplicativo dos serviços de mídia V2 pode ser exclusivo. Portanto, fornecemos diretrizes baseadas em cenário com base em como você *pode ter* usado os serviços de mídia no passado e as etapas para cada recurso do serviço, como:

- [Codificação](migrate-v-2-v-3-migration-scenario-based-encoding.md)
- [Transmissão ao vivo](migrate-v-2-v-3-migration-scenario-based-live-streaming.md)
- [Empacotamento e entrega](migrate-v-2-v-3-migration-scenario-based-publishing.md)
- [Proteção de conteúdo](migrate-v-2-v-3-migration-scenario-based-content-protection.md)
- [MRU (unidades reservadas para mídia)](migrate-v-2-v-3-migration-scenario-based-media-reserved-units.md)

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
