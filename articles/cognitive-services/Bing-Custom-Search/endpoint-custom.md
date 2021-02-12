---
title: Ponto de extremidade da Pesquisa Personalizada do Bing
titleSuffix: Azure Cognitive Services
description: Crie experiências de pesquisa personalizadas para tópicos sobre os quais você se preocupa. Os usuários veem os resultados da pesquisa adaptados ao conteúdo que eles se preocupam.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 35dd147574eb7820debba55b105deed001be80fb
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338479"
---
# <a name="custom-search"></a>Pesquisa Personalizada

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
A Pesquisa Personalizada do Bing permite que você crie experiências de pesquisa personalizada para os tópicos importantes para você. Os usuários veem os resultados da pesquisa personalizados ao conteúdo importante para eles, em vez de terem que acessar os resultados da pesquisa com conteúdo irrelevante.

## <a name="custom-search-endpoint"></a>Ponto de Extremidade da Pesquisa Personalizada do Bing
Para obter os resultados da entidade usando a API de Pesquisa Personalizada do Bing, envie uma solicitação `GET` para o ponto de extremidade a seguir. Use os cabeçalhos e parâmetros de URL para definir mais especificações.

Ponto de extremidade: retorna as sugestões de pesquisa como resultados JSON relevantes à entrada do usuário definidos por `?q=""`.
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Para obter exemplos que descrevem como configurar fontes de Pesquisa Personalizada, veja o [tutorial](./tutorials/custom-search-web-page.md). Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros etc., veja a referência [API de Pesquisa Personalizada do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference).

## <a name="custom-search-response-json"></a>JSON de Resposta da Pesquisa Personalizada
Uma solicitação de pesquisa personalizada retorna resultados como objetos JSON, veja [Objetos de resposta](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects). 

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

Para saber mais sobre como configurar uma instância da Pesquisa Personalizada, veja [Configurar sua experiência de pesquisa personalizada](./define-your-custom-view.md).

## <a name="next-steps"></a>Próximas etapas
As APIs do **Bing** dão suporte a ações de pesquisa que retornam os resultados de acordo com seu tipo. Todos os pontos de extremidade de pesquisa retornam os resultados como objetos de resposta JSON.  Todos os pontos de extremidade dão suporte a consultas que retornam um idioma e/ou um local específico por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros compatíveis com cada ponto de extremidade, confira as páginas de referência de cada tipo.
Para obter exemplos de solicitações básicas usando a API de Pesquisa Personalizada, veja [Inícios rápidos sobre a Pesquisa Personalizada](/azure/cognitive-services/bing-custom-search/)