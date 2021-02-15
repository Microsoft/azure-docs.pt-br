---
title: O que é a API de Pesquisa de Notícias do Bing?
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API de Pesquisa de Notícias do Bing para pesquisar na Web por manchetes atuais em várias categorias, incluindo manchetes e tópicos populares.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: c6fad3a006d2f3da74638e0680d6ba65f736ab7b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351224"
---
# <a name="what-is-the-bing-news-search-api"></a>O que é a API de Pesquisa de Notícias do Bing?

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisaram ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A API de Pesquisa de Notícias do Bing facilita a integração das funcionalidades de pesquisa cognitiva de notícias do Bing aos seus aplicativos. A API fornece uma experiência semelhante ao [Bing Notícias](https://www.bing.com/news), permitindo que você envie consultas de pesquisa e receba artigos de notícias relevantes.

Lembre-se de que a API de Pesquisa de Notícias do Bing fornece apenas resultados da pesquisa de notícias. Use a [API de Pesquisa na Web do Bing](../bing-web-search/overview.md), a [API de Pesquisa de Vídeo do Bing](../bing-video-search/overview.md) e a [API de Pesquisa de Imagem do Bing](../bing-image-search/overview.md) para outros tipos de conteúdo da Web.

## <a name="bing-news-search-api-features"></a>Recursos da API de Pesquisa de Notícias do Bing

Embora a API de Pesquisa de Notícias do Bing encontre e retorne principalmente artigos de notícias relevantes, ela fornece vários recursos para a recuperação inteligente e focalizada de notícias na Web.

|Recurso  |Descrição  |
|---------|---------|
|[Sugerindo e usando termos de pesquisa](concepts/search-for-news.md#suggest-and-use-search-terms)     | Aprimore sua experiência de pesquisa usando a [API de Sugestão Automática do Bing](../bing-autosuggest/get-suggested-search-terms.md) para exibir os termos de pesquisa sugeridos à medida que eles são digitados.         |
|[Obter notícias gerais](concepts/search-for-news.md#get-general-news)     | Encontre notícias enviando uma consulta de pesquisa para a API de Pesquisa de Notícias do Bing e obtendo uma lista de artigos de notícias relevantes.           |
|[Principais notícias de hoje](concepts/search-for-news.md#get-todays-top-news)      | Obtenha as principais reportagens do dia em todas as categorias.       |
|[Notícias por categoria](concepts/search-for-news.md)     | Pesquise notícias em categorias específicas.        | 
|[Manchetes](concepts/search-for-news.md)     | Pesquise as principais manchetes em todas as categorias.         |

## <a name="workflow"></a>Fluxo de trabalho

A API de Pesquisa de Notícias do Bing é um serviço Web RESTful, facilitando a chamada em qualquer linguagem de programação que possa fazer solicitações HTTP e analisar JSON. É possível usar o serviço usando a API REST ou o SDK.

1. Crie uma [Conta da API dos Serviços Cognitivos](../cognitive-services-apis-create-account.md) com acesso às APIs de Pesquisa do Bing. Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/cognitive-services/) gratuitamente.
2. Envie uma solicitação para a API, com uma consulta de pesquisa válida.
3. Processe a resposta da API analisando a mensagem JSON retornada.

## <a name="next-steps"></a>Próximas etapas

Primeiro, experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) para a API de Pesquisa de Notícias do Bing. Essa demonstração explica como você pode personalizar rapidamente uma consulta de pesquisa e encontrar notícias na Web.

Para começar rapidamente com sua primeira solicitação de API, experimente um início rápido para a [API REST](./csharp.md) ou um dos [SDKs](./quickstarts/client-libraries.md?pivots=programming-language-csharp).

## <a name="see-also"></a>Confira também

* A seção de referência da [API de Pesquisa de Notícias do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) contém definições e informações sobre os pontos de extremidade, os cabeçalhos, as respostas de API e os parâmetros de consulta que podem ser usados para solicitar resultados da pesquisa baseada em imagem.
* Os [Requisitos de exibição e uso do Bing](../bing-web-search/use-display-requirements.md) especificam os usos aceitáveis do conteúdo e das informações obtidas por meio das APIs de pesquisa do Bing.
* Visite a [página do hub da API de Pesquisa do Bing](../bing-web-search/overview.md) para explorar as outras APIs disponíveis.