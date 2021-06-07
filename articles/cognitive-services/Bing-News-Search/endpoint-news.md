---
title: Ponto de extremidade da Pesquisa de Notícias do Bing
titleSuffix: Azure Cognitive Services
description: Este artigo fornece um resumo dos pontos de extremidade da API de pesquisa de notícias; Notícias, principais notícias e notícias de tendências.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 9c095a46b3a0526b23645c9cbb5e99eb8eda9067
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94366393"
---
# <a name="bing-news-search-api-endpoints"></a>Pontos de extremidade da API de Pesquisa de Notícias do Bing

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A **API de Pesquisa de Notícias** retorna artigos de notícias, páginas da Web, imagens, vídeos e [entidades](../bing-entities-search/overview.md). As entidades contêm informações de resumo sobre uma pessoa, lugar ou tópico.

## <a name="endpoints"></a>Pontos de extremidade

Para obter resultados da pesquisa de notícias usando a API de Pesquisa de Notícias do Bing, envie uma solicitação `GET` para um dos seguintes pontos de extremidade. Os cabeçalhos e parâmetros de URL definem mais especificações.

### <a name="news-items-by-search-query"></a>Itens de notícias por consulta de pesquisa

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Retorna itens de notícias com base em uma consulta de pesquisa. Se a consulta de pesquisa estiver vazia, a API retornará os principais artigos de notícias de categorias diferentes. Envie uma consulta codificando seu termo de pesquisa em URL e acrescentando-o ao parâmetro `q=""`. Para disponibilidade, consulte [países/regiões e mercados com suporte](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Principais itens de notícia por categoria

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Retorna os principais itens de notícias por categoria. Você pode solicitar especificamente os principais artigos de negócios, esportes ou entretenimento usando `category=business`, `category=sports`, ou `category=entertainment`.  O parâmetro `category` só pode ser usado com a URL `/news`. Há alguns requisitos formais para especificar as categorias; consulte `category` na documentação [parâmetro de consulta](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters). Envie uma consulta codificando seu termo de pesquisa em URL e acrescentando-o ao parâmetro `q=""`. Para disponibilidade, consulte [países/regiões e mercados com suporte](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Tópicos de notícias populares 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Retorna tópicos de notícias que são populares atualmente em redes sociais. Quando a opção `/trendingtopics` estiver incluída, a pesquisa do Bing ignora vários outros parâmetros, como `freshness` e `?q=""`. Para disponibilidade, consulte [países/regiões e mercados com suporte](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Próximas etapas

Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência [API de Pesquisa de Notícias do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).

Para obter informações completas sobre os parâmetros compatíveis com cada ponto de extremidade, confira as páginas de referência de cada tipo.
Para obter exemplos de solicitações básicas usando a API de Pesquisa de Notícias, consulte [Início rápido sobre pesquisa de notícias do Bing](/azure/cognitive-services/bing-news-search).