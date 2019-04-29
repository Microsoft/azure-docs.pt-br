---
title: Gerenciar a política de caching da CDN do Azure nos Serviços de Mídia do Azure | Microsoft Docs
description: Saiba como gerenciar a política de caching da CDN do Azure nos Serviços de Mídia do Azure.
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: ''
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: 516df2f6177303987fc0354dde647c1fc26820ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61069417"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Gerenciar a política de caching da CDN do Azure nos Serviços de Mídia do Azure
Os Serviços de Mídia do Azure fornecem streaming adaptável e download progressivo com base em HTTP. Streaming baseado em HTTP é altamente escalonável, com os benefícios do armazenamento em cache em camadas proxy e CDN, bem como armazenamento em cache no lado do cliente. Pontos de extremidade de streaming fornecem recursos de streaming gerais e também a configuração de cabeçalhos de cache HTTP. Pontos de extremidade de streaming definem o Controle de Cache HTTP: cabeçalhos idade máxima e Vencimento. Saiba mais sobre os cabeçalhos de cache HTTP em [W3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Cabeçalhos de armazenamento em cache padrão
Por padrão, os pontos de extremidade de streaming aplicam cabeçalhos de cache de 3 dias para dados de streaming sob demanda (fragmentos/partes reais de mídia) e manifesto (lista de reprodução). Para transmissão ao vivo, os pontos de extremidade de streaming aplicam cabeçalhos de cache de 3 dias para solicitações de dados (fragmentos/partes reais de mídia) e cabeçalho de cache de 2 segundos para solicitações de manifesto (lista de reprodução). Quando os programa ao vivo são transformados em sob demanda (arquivamento dinâmico) os cabeçalhos de cache de streaming sob demanda são aplicados.

## <a name="azure-cdn-integration"></a>Integração da CDN com o Azure
Os Serviços de Mídia do Azure fornecem [CDN integrada](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) para pontos de extremidade de streaming. Cabeçalhos do controle de cache são aplicados da mesma forma que os pontos de extremidade de streaming para pontos de extremidade de streaming habilitados para CDN. O Azure CDN usa os valores de cache configurados para o ponto de extremidade de streaming para definir o tempo de vida dos objetos armazenados internamente em cache e também usa esse valor para definir os cabeçalhos de cache de entrega. Ao usar os pontos de extremidades de streaming habilitados para CDN, não é recomendável definir valores pequenos para o cache. A definição de valores pequenos diminui o desempenho e reduz o benefício da CDN. Não é permitido definir cabeçalhos de cache menores do que 600 segundos para pontos de extremidades de streaming habilitados para CDN.

> [!IMPORTANT]
>Os Serviços de Mídia do Azure têm integração completa com o Azure CDN. Com um único clique, você pode integrar todos os Azure CDN provedores disponíveis para o ponto de extremidade de streaming incluindo produtos standard e premium. Para saber mais, veja este [anúncio](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> Encargos de dados de streaming de ponto de extremidade para CDN somente são desabilitados se o CDN for habilitada para APIs de extremidade de streaming ou usando a seção de ponto de extremidade de streaming do portal do Azure. Integração manual ou criar diretamente um ponto de extremidade de CDN usando APIs de CDN ou a seção do portal não desabilitará os encargos de dados.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Configurando os cabeçalhos de cache com os Serviços de Mídia do Azure
Você pode usar o portal do Azure ou as APIs de Serviços de Mídia do Azure para configurar os valores de cabeçalho do cache.

1. Para configurar cabeçalhos de cache usando o portal do Azure, consulte [Como gerenciar pontos de extremidade de streaming](../media-services/previous/media-services-portal-manage-streaming-endpoints.md), seção Configurando o ponto de extremidade de streaming.
2. API REST dos Serviços de Mídia do Azure, [StreamingEndpoint](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl).
3. SDK do .NET dos Serviços de Mídia do Azure, [Propriedades de StreamingEndpointCacheControl](https://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Ordem de precedência de configuração do cache
1. O valor de cache configurado dos Serviços de Mídia do Azure substitui o valor padrão.
2. Se não houver configuração manual, os valores padrão serão aplicados.
3. Por padrão, os cabeçalhos de cache de 2 segundos são aplicados ao manifesto (lista de reprodução) do streaming dinâmico, independentemente da configuração de Mídia do Azure ou do Armazenamento do Azure, e não é possível substituir esse valor.

