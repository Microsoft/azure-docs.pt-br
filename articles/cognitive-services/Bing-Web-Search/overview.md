---
title: O que é a API de Pesquisa na Web do Bing?
titleSuffix: Azure Cognitive Services
description: A API de Pesquisa na Web do Bing é um serviço RESTful que fornece respostas instantâneas às consultas de pesquisa na Web. Configure os resultados para incluir páginas da Web, imagens, vídeos, notícias, entre outros. Os resultados são fornecidos como JSON e são baseados na relevância de pesquisa e em suas assinaturas de pesquisa na Web do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: overview
ms.date: 03/31/2020
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 7643486962df0516cc61857a7e31cf34bc41c732
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350629"
---
# <a name="what-is-the-bing-web-search-api"></a>O que é a API de Pesquisa na Web do Bing?

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A API de Pesquisa na Web do Bing é um serviço RESTful que fornece respostas instantâneas às consultas de usuário. Os resultados da pesquisa são facilmente definidos para incluir as páginas da Web, imagens, vídeos, notícias, traduções e muito mais. A Pesquisa na Web do Bing fornece os resultados como JSON baseado na relevância de pesquisa e em suas assinaturas de Pesquisa na Web do Bing.

Essa API é ideal para aplicativos que precisam de acesso a todo o conteúdo relevante para a consulta de pesquisa do usuário. Se você estiver criando um aplicativo que exige apenas um tipo específico de resultados, considere usar a [API de Pesquisa de Imagem do Bing](../Bing-Image-Search/overview.md), [API de Pesquisa de Vídeo do Bing](../bing-video-search/overview.md), ou [API de Pesquisa de Notícias do Bing](../Bing-News-Search/search-the-web.md). Ver [API de Serviços Cognitivos](../index.yml) para obter uma lista completa das APIs de Pesquisa do Bing.

Quer ver como funciona? Experimente a [demonstração da API de Pesquisa na Web do Bing ](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/).

## <a name="features"></a>Recursos  

A Pesquisa na Web do Bing só não fornece acesso às respostas instantâneas. Ela também fornece recursos adicionais e funcionalidades que permitem que você personalize os resultados da pesquisa para seus usuários.

| Recurso | Descrição |
|---------|-------------|
| [Sugerir os termos de pesquisa em tempo real](../bing-autosuggest/get-suggested-search-terms.md) | Aprimore sua experiência com aplicativos usando a API de Sugestão Automática do Bing para exibir os termos de pesquisa sugeridos à medida que eles são digitados. |
| [Filtrar e restringir os resultados por tipo de conteúdo](filter-answers.md) | Personalize e refine os resultados da pesquisa com filtros e parâmetros de consulta para páginas da Web, imagens, vídeos, pesquisa segura e muito mais. |
| [Realce de ocorrências para caracteres unicode](hit-highlighting.md) | Identifique e remova caracteres unicode indesejados nos resultados da pesquisa antes de exibi-los para os usuários com realce de ocorrências. |
| [Localizar resultados da pesquisa por país, região e/ou mercado](./language-support.md) | A Pesquisa na Web do Bing dá suporte a mais de três dúzias de países ou regiões. Use esse recurso para refinar os resultados da pesquisa para um país/região ou mercado específico. |
| [Analisar métricas de pesquisa com Estatísticas do Bing](bing-web-stats.md) | As Estatísticas do Bing são uma assinatura paga que fornece análise sobre o volume de chamadas, as principais cadeias de caracteres de consulta, distribuição geográfica e muito mais. |

## <a name="workflow"></a>Fluxo de trabalho

A API de Pesquisa na Web do Bing é fácil de ser chamada a partir de qualquer linguagem de programação que pode fazer solicitações HTTP e analisar as respostas JSON. O serviço pode ser acessado com a [API REST](quickstarts/python.md) ou as [bibliotecas de clientes da Pesquisa na Web do Bing](./quickstarts/client-libraries.md).

1. [Crie um recurso do Azure](../cognitive-services-apis-create-account.md) para as APIs de Pesquisa do Bing. Caso não tenha uma assinatura do Azure, é possível criar uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/).  
2. Envie uma [solicitação à API de Pesquisa na Web do Bing](quickstarts/python.md).
3. Analise a resposta JSON.

## <a name="next-steps"></a>Próximas etapas

* Use nosso [início rápido do Python](./quickstarts/client-libraries.md?pivots=programming-language-python) para fazer sua primeira chamada à API de Pesquisa na Web do Bing.  
* [Compilar um aplicativo Web de página única](tutorial-bing-web-search-single-page-app.md).
* Revisar a documentação de [referência da API de Pesquisa na Web v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).  
* Saiba mais sobre [usar e exibir requisitos](./use-display-requirements.md) para Pesquisa na Web do Bing.