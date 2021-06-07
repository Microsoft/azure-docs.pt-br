---
title: Enviando solicitações de pesquisa para a API de Pesquisa de Entidade do Bing
titleSuffix: Azure cognitive Services
description: A API de Pesquisa de Entidade do Bing envia uma consulta de pesquisa para o Bing e obtém os resultados que incluem entidades e locais.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: ad6d89fec9f2c94129e19c09ee3e1e76d5bb6e44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96353265"
---
# <a name="sending-search-requests-to-the-bing-entity-search-api"></a>Enviando solicitações de pesquisa para a API de Pesquisa de Entidade do Bing

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A API de Pesquisa de Entidade do Bing envia uma consulta de pesquisa para o Bing e obtém os resultados que incluem entidades e locais. Os resultados de local incluem restaurantes, hotel ou outras empresas locais. Para locais, a consulta pode especificar o nome da empresa local ou ela pode solicitar uma lista (por exemplo, restaurantes próximos a mim). Os resultados de entidade incluem pessoas, lugares ou coisas. Neste contexto, o local é atrações turísticas, estados, países, regiões etc.

## <a name="the-endpoint"></a>O ponto de extremidade

Para obter resultados de pesquisa de entidade e local, envie uma solicitação GET para o ponto de extremidade a seguir:  

```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

As solicitações precisam usar o protocolo HTTPS.

É recomendável que todas as solicitações sejam originadas de um servidor. A distribuição da chave como parte de um aplicativo cliente fornece mais oportunidades para um terceiro mal-intencionado acessá-lo. Além disso, fazer chamadas em um servidor fornece um ponto único de upgrade para versões futuras da API.

## <a name="specifying-query-parameters-and-headers"></a>Especificar cabeçalhos e parâmetros de consulta

A solicitação precisa especificar o parâmetro de consulta [q](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query), que contém o termo de pesquisa do usuário. A solicitação também deve especificar o parâmetro de consulta [mkt](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#mkt), que identifica o mercado de onde você deseja que venham os resultados. Para uma lista de parâmetros de consulta opcionais, consulte [Parâmetros de Consulta](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters). A URL codifica todos os parâmetros de consulta.  
  
A solicitação precisa especificar o cabeçalho [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#subscriptionkey). Embora isso seja opcional, você é incentivado a especificar também os seguintes cabeçalhos:  
  
-   [User-Agent](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-ClientIP](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#clientip)  
-   [X-Search-Location](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#location)  

Os cabeçalhos de IP e local do cliente são importantes para retornar o conteúdo com reconhecimento de local.  

Para obter uma lista de todos os cabeçalhos de solicitação e resposta, confira [Cabeçalhos](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers).

## <a name="the-request"></a>A solicitação

O exemplo a seguir mostra uma solicitação de entidades que inclui todos os cabeçalhos e parâmetros de consulta sugeridos. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Se for a primeira vez que você chama qualquer uma das APIs do Bing, não inclua o cabeçalho da ID do cliente. Só inclua a ID do cliente se você já tiver chamado uma API do Bing e o Bing retornou uma ID de cliente para a combinação de usuário e dispositivo.

## <a name="the-response"></a>A resposta

O exemplo a seguir mostra a resposta à solicitação anterior. O exemplo também mostra os cabeçalhos de resposta específicos do Bing. Para obter informações sobre o objeto de resposta, consulte [SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#searchresponse).

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "mount rainier"
    },
    "entities" : {
        "queryScenario" : "DominantEntity",
        "value" : [{
            "contractualRules" : [{
                "_type" : "ContractualRules\/LicenseAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "license" : {
                    "name" : "CC-BY-SA",
                    "url" : "http:\/\/creativecommons.org\/licenses\/by-sa\/3.0\/"
                },
                "licenseNotice" : "Text under CC-BY-SA license"
            },
            {
                "_type" : "ContractualRules\/LinkAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "text" : "en.wikipedia.org",
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            },
            {
                "_type" : "ContractualRules\/MediaAttribution",
                "targetPropertyName" : "image",
                "mustBeCloseToContent" : true,
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            }],
            "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Mount%20Rainier...",
            "name" : "Mount Rainier",
            "image" : {
                "name" : "Mount Rainier",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A21890c0e1f...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
                }],
                "hostPageUrl" : "http:\/\/upload.wikimedia.org\/wikipedia...",
                "width" : 110,
                "height" : 110
            },
            "description" : "Mount Rainier, Mount Tacoma, or Mount Tahoma is the highest...",
            "entityPresentationInfo" : {
                "entityScenario" : "DominantEntity",
                "entityTypeHints" : ["Attraction"],
                "entityTypeDisplayHint" : "Mountain"
            },
            "bingId" : "9ae3e6ca-81ea-6fa1-ffa0-42e1d78906"
        }]
    }
}
```


## <a name="next-steps"></a>Próximas etapas

* [Pesquisando entidades com a API de Entidade do Bing](search-for-entities.md)
* [Requisitos de uso e exibição da API do Bing](../../bing-web-search/use-display-requirements.md)