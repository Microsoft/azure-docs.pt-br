---
title: Quais são as APIs de Pesquisa do Bing?
titleSuffix: Azure Cognitive Services
description: Use este artigo para saber mais sobre as APIs de Pesquisa do Bing e como você pode habilitar pesquisas de Internet cognitivas em seus aplicativos e serviços.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 72b4755c6f01a10851e79cf274842f1599bc2c55
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349530"
---
# <a name="what-are-the-bing-search-apis"></a>Quais são as APIs de Pesquisa do Bing?

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Com as APIs de Pesquisa do Bing, é possível criar aplicativos e serviços conectados à Web que encontram páginas da Web, imagens, notícias, locais e muito mais, sem anúncios. Enviando solicitações de pesquisa usando os SDKs ou as APIs REST de Pesquisa do Bing, você pode obter conteúdo e informações relevantes para pesquisas na Web. Use este artigo para saber mais sobre as diferentes APIs de pesquisa do Bing e como você pode integrar pesquisas cognitivas em seus aplicativos e serviços. Preços e limites de taxa podem variar entre as APIs.

## <a name="the-bing-web-search-api"></a>A API de Pesquisa na Web do Bing

A [API de Pesquisa na Web do Bing](../Bing-Web-Search/overview.md) retorna páginas da Web, imagens, vídeo, notícias e muito mais. Você pode filtrar as consultas de pesquisa enviadas a essa API para incluir ou excluir determinados tipos de conteúdo.

Considere usar a API de Pesquisa na Web do Bing em aplicativos que talvez precisem pesquisar por todos os tipos de conteúdo da Web relevante. Se seu aplicativo pesquisa por um tipo específico de conteúdo online, considere uma das APIs de pesquisa abaixo:

## <a name="content-specific-bing-search-apis"></a>APIs de Pesquisa do Bing específicas a um conteúdo

As seguintes APIs de pesquisa do Bing retornam conteúdo específico da Web como imagens, notícias, empresas locais e vídeos.

| API do Bing | Descrição |
| -- | -- |
| [Pesquisa de Entidade](../Bing-Entities-Search/overview.md) | A API de Pesquisa de Entidade do Bing retorna resultados da pesquisa contendo entidades, as quais podem ser pessoas, lugares ou coisas. Dependendo da consulta, a API retornará uma ou mais entidades que satisfaçam a consulta de pesquisa. A consulta de pesquisa pode incluir indivíduos notáveis, empresas locais, pontos de referência, destinos e muito mais. |
| [Pesquisa de Imagem](../Bing-Image-Search/overview.md) | O API de Pesquisa de Imagem do Bing permite pesquisar e localizar imagens estáticas e animadas de alta qualidade semelhantes a [Bing.com/images](https://www.Bing.com/images). Você pode refinar a pesquisa para incluir ou excluir imagens por atributo, incluindo o tamanho, cor, licença e atualização. Você também pode pesquisar imagens mais populares, carregar imagens para obter informações sobre eles e exibir visualizações em miniatura. |
| [Pesquisa de Notícias](../Bing-News-Search/search-the-web.md) | O API de Pesquisa de Notícias do Bing permite que você encontre histórias de notícias semelhantes a [Bing.com/News](https://www.Bing.com/news). A API retorna artigos de notícias de várias fontes ou domínios específicos. Você pode pesquisar em categorias para obter artigos populares, bem como as principais histórias e manchetes. |
| [Pesquisa de Vídeo](../Bing-Video-Search/overview.md) | O API de Pesquisa de Vídeo do Bing permite encontrar vídeos na Web. Obtenha vídeos populares, conteúdo relacionado e visualizações em miniatura. |
| [Pesquisa Visual](../Bing-visual-search/overview.md) | Carregue uma imagem ou use uma URL para obter informações criteriosas sobre ela, tais como pesquisas relacionadas, imagens e produtos visualmente similares. |
 [Pesquisa de Empresas Locais](../bing-local-business-search/overview.md) | A API de pesquisa de negócios local do Bing permite que seus aplicativos encontrem informações de contato e local sobre empresas locais com base em consultas de pesquisa. |

## <a name="the-bing-custom-search-api"></a>A API de Pesquisa Personalizada do Bing

Criar uma instância de pesquisa personalizada com a API de [pesquisa personalizada do Bing](../Bing-Custom-Search/overview.md) permite que você crie uma experiência de pesquisa focada apenas no conteúdo e nos tópicos sobre os quais você se preocupa. Por exemplo, depois de especificar os domínios, os sites e as páginas da Web específicas que o Bing pesquisará, Pesquisa Personalizada do Bing personalizará os resultados para esse conteúdo específico. Você pode incorporar as APIs de Pesquisa de Vídeo, de Imagem e a Sugestão Automática Personalizada do Bing para personalizar ainda mais sua experiência de pesquisa.

## <a name="additional-bing-search-apis"></a>APIs de Pesquisa do Bing adicionais

Os APIs de Pesquisa do Bing a seguir permitem melhorar sua experiência de pesquisa combinando-as com outras APIs de pesquisa do Bing.

| API | Descrição |
| -- | -- |
| [Sugestão Automática do Bing](../Bing-Autosuggest/get-suggested-search-terms.md) | Melhore a experiência de pesquisa do seu aplicativo com a API de Sugestão Automática do Bing retornando pesquisas sugeridas em tempo real.  |
| [Estatísticas do Bing](bing-web-stats.md) | As Estatísticas do Bing fornecem análise para as APIs de Pesquisa do Bing usadas pelo aplicativo. Algumas das análises disponíveis incluem o volume de chamadas, as principais cadeias de caracteres de consulta e a distribuição geográfica. |

## <a name="next-steps"></a>Próximas etapas

* [Detalhes de preço](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) da API de Pesquisa do Bing
* Os [Requisitos de exibição e uso do Bing](./use-display-requirements.md) especificam os usos aceitáveis do conteúdo e das informações obtidas por meio das APIs de pesquisa do Bing.