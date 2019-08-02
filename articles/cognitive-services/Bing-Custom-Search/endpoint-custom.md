---
title: Ponto de extremidade da Pesquisa Personalizada do Bing
titleSuffix: Azure Cognitive Services
description: Resumo do ponto de extremidade da API de Pesquisa Personalizada do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: e3b544d8d655b653383cc999de233e2163433d85
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405064"
---
# <a name="custom-search"></a>Pesquisa Personalizada
A Pesquisa Personalizada do Bing permite que você crie experiências de pesquisa personalizada para os tópicos importantes para você. Os usuários veem os resultados da pesquisa ajustados ao conteúdo importante para eles em vez de obter a página por meio dos resultados da com conteúdo irrelevante.

## <a name="custom-search-endpoint"></a>Ponto de Extremidade da Pesquisa Personalizada do Bing
Para obter os resultados da entidade usando a API de Pesquisa Personalizada do Bing, envie uma solicitação `GET` para o ponto de extremidade a seguir. Use os cabeçalhos e parâmetros de URL para definir mais especificações.

Ponto de extremidade: Retorna as sugestões de pesquisa como resultados JSON que são relevantes à entrada do usuário definida por `?q=""`.
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Para obter exemplos que descrevem como configurar fontes de Pesquisa Personalizada, veja o [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page). Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros etc., veja a referência [API de Pesquisa Personalizada do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference).

## <a name="custom-search-response-json"></a>JSON de Resposta da Pesquisa Personalizada
Uma solicitação de pesquisa personalizada retorna resultados como objetos JSON, veja [Objetos de resposta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Sugestão Automática Personalizada
A API de sugestão automática personalizada permite que você envie um termo de consulta de pesquisa parcial com o Bing e obtenha uma lista de consultas sugeridas que podem ser configuradas. Com a Sugestão Automática Personalizada, você adiciona as sugestões retornadas pela API e opcionalmente especifique se as sugestões geradas pelo Bing devem ser incluídas.

## <a name="custom-autosuggest-endpoint"></a>Ponto de Extremidade da Sugestão Automática
Para solicitar as sugestões de consulta personalizada, envie uma solicitação GET para:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Para obter informações sobre como definir sugestões personalizadas, veja [Definir sugestões da pesquisa personalizada](define-custom-suggestions.md).

## <a name="custom-image-search"></a>Pesquisa de Imagem Personalizada
A API de Pesquisa de Imagem Personalizada permite que você envie uma consulta de pesquisa para o Bing e obtenha uma lista de imagens relevantes da sua instância da Pesquisa Personalizada.

## <a name="custom-image-search-endpoint"></a>Ponto de Extremidade de Pesquisa de Imagem Personalizada
Para solicitar imagens de sua instância da Pesquisa Personalizada, envie uma solicitação GET para a URL a seguir:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Para saber mais sobre como configurar uma instância da Pesquisa Personalizada, veja [Configurar sua experiência de pesquisa personalizada](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view).

## <a name="next-steps"></a>Próximas etapas
As APIs do **Bing** dão suporte a ações de pesquisa que retornam os resultados de acordo com seu tipo. Todos os pontos de extremidade de pesquisa retornam os resultados como objetos de resposta JSON.  Todos os pontos de extremidade dão suporte a consultas que retornam um idioma e/ou um local específico por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros com suporte para cada ponto de extremidade, consulte as páginas de referência para cada tipo.
Para obter exemplos de solicitações básicas usando a API de Pesquisa Personalizada, veja [Inícios rápidos sobre a Pesquisa Personalizada](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)
