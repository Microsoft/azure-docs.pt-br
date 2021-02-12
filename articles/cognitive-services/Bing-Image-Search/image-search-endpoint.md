---
title: Pontos de extremidade da API de Pesquisa de Imagem do Bing
titleSuffix: Azure Cognitive Services
description: A API de Pesquisa de Imagem inclui três pontos de extremidade. O ponto de extremidade 1 retorna imagens da Web. O ponto de extremidade 2 retorna ImageInsights. O ponto de extremidade 3 retorna imagens populares.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 67fec77136f5d593279be2846e63c51b60e16bb4
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593511"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Pontos de extremidade da API de Pesquisa de Imagem do Bing

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020** , todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A **API de Pesquisa de Imagem**  inclui três pontos de extremidade.  O ponto de extremidade 1 retorna imagens da Web com base em uma consulta. O ponto de extremidade 2 retorna [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse).  O ponto de extremidade 3 retorna imagens populares.

## <a name="endpoints"></a>Pontos de extremidade

Para obter resultados de imagem usando a API do Bing, envie uma solicitação para um dos pontos de extremidade a seguir. Use os cabeçalhos e parâmetros de URL para definir mais especificações.

**Ponto de extremidade 1:** retorna imagens que são relevantes para a consulta de pesquisa do usuário definida pelo `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Ponto de extremidade 2:** retorna insights sobre uma imagem, usando um `GET` ou `POST`.
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Uma solicitação GET retorna informações sobre uma imagem, como páginas da Web que incluem a imagem. Inclua o parâmetro [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) com uma solicitação `GET`.

Ou, você pode incluir uma imagem binária no corpo de uma solicitação `POST` e definir o parâmetro [módulos](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) para `RecognizedEntities`. Isso retornará um [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) para usar como um parâmetro em uma solicitação `GET` subsequente, que retorna informações sobre as pessoas na imagem.  Defina `modules` para `All` para obter todas as informações, exceto `RecognizedEntities` nos resultados de `POST` sem fazer outra chamada usando `insightsToken`.


**Ponto de extremidade 3:** retorna imagens que são populares com base nas solicitações de pesquisa feitas por outros usuários. As imagens são separadas em categorias diferentes, por exemplo, com base em pessoas ou eventos notáveis.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Para obter uma lista de mercados que dão suporte a imagens populares, consulte [Imagens Populares](./trending-images.md).

Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência [API de Pesquisa de Imagem do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).
## <a name="response-json"></a>JSON de resposta
A resposta a uma solicitação de pesquisa de imagem inclui resultados como objetos JSON. Para obter exemplos de como analisar os resultados, confira o [tutorial](./tutorial-bing-image-search-single-page-app.md) e o [código-fonte](./tutorial-bing-image-search-single-page-app.md).

## <a name="next-steps"></a>Próximas etapas
As APIs do **Bing** dão suporte a ações de pesquisa que retornam os resultados de acordo com seu tipo. Todos os pontos de extremidade de pesquisa retornam os resultados como objetos de resposta JSON.  Todos os pontos de extremidade dão suporte a consultas que retornam um idioma e/ou um local específico por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros compatíveis com cada ponto de extremidade, confira as páginas de referência de cada tipo.
Para obter exemplos de solicitações básicas usando a API de Pesquisa de Imagem, consulte [Inícios rápidos de Pesquisa de Imagem](./overview.md).