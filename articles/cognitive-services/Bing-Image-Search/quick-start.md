---
title: Início rápido da API de Pesquisa de Imagens | Microsoft Docs
description: Mostra como começar a usar a API de Pesquisa de Imagem do Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: BE2B2F8C-20B5-4E0B-AEC8-EAD505BA4C85
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 03/04/2019
ms.author: scottwhi
ms.openlocfilehash: 18d5e17c4d44d396ca0044fbaf023cb4242555b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60916658"
---
# <a name="your-first-images-search-query"></a>Sua primeira consulta de pesquisa de imagens

Antes que você possa fazer a primeira chamada, será necessário obter uma chave de assinatura dos Serviços Cognitivos da Pesquisa do Bing. Para obter uma chave, confira [Experimentar os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api).  Veja também [Cognitive Services Pricing - API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Para obter resultados de pesquisa de imagens, você envia uma solicitação GET ao ponto de extremidade a seguir:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search
```
  
A solicitação deve usar o protocolo HTTPS.

É recomendável que todas as solicitações sejam originadas de um servidor. A distribuição da chave como parte de um aplicativo cliente fornece mais oportunidades para um terceiro mal-intencionado acessá-lo. Além disso, fazer chamadas em um servidor fornece um ponto único de atualização para versões futuras da API.

A solicitação precisa especificar o parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query), que contém o termo de pesquisa do usuário. Embora seja opcional, a solicitação também deve especificar o parâmetro de consulta [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#mkt), que identifica o mercado de onde você deseja obter os resultados. Para obter uma lista de parâmetros de consulta opcionais, como `freshness` e `size`, confira [Parâmetros de consulta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query-parameters). Todos os valores de parâmetro de consulta precisam ser codificados em URL.  
  
A solicitação precisa especificar o cabeçalho [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#subscriptionkey). Embora isso seja opcional, você é incentivado a especificar também os seguintes cabeçalhos:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#location)  

Os cabeçalhos de IP e local do cliente são importantes para retornar o conteúdo com reconhecimento de local.  

Para obter uma lista de todos os cabeçalhos de solicitação e resposta, confira [Cabeçalhos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#headers).

## <a name="the-request"></a>A solicitação

A seguir, uma solicitação de pesquisa que inclui todos os parâmetros e cabeçalhos de consulta sugeridos. Se é a primeira vez que você chama qualquer uma das APIs do Bing, não inclua o cabeçalho da ID do cliente. Só inclua a ID do cliente se você já tiver chamado uma API do Bing e o Bing retornou uma ID de cliente para a combinação de usuário e dispositivo. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

O exemplo a seguir mostra a resposta à solicitação anterior.

```json
{
    "_type" : "Images",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "totalEstimatedMatches" : 838,
    "value" : [
        {
            "name" : "File:Rich Passage Minto Sailing Dinghy.jpg - Wikipedia",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
            "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
            "datePublished" : "2011-10-29T11:26:00",
            "contentUrl" : "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
            "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
            "contentSize" : "79239 B",
            "encodingFormat" : "jpeg",
            "hostPageDisplayUrl" : "en.wikipedia.org\/wiki\/File:Rich_Passage...",
            "width" : 526,
            "height" : 688,
            "thumbnail" : {
                "width" : 229,
                "height" : 300
            },
            "imageInsightsToken" : "ccid_GNarK7ma*mid_CCF85447ADA6...",
            "insightsSourcesSummary" : {
                "shoppingSourcesCount" : 0,
                "recipeSourcesCount" : 0
            },
            "imageId" : "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
            "accentColor" : "376094"
        },
        . . .
    ],
    "queryExpansions" : [
        {
            "text" : "Small Sailing Dinghies",
            "displayText" : "Small",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8B4...",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=..."
            }
        },
        . . .
    ],
    "nextOffset" : 10,
    "pivotSuggestions" : [
        {
            "pivot" : "sailing",
            "suggestions" : []
        },
        {
            "pivot" : "dinghies",
            "suggestions" : [
                {
                    "text" : "Sailing Tender",
                    "displayText" : "Tender",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=..."
                    }
                },
                . . .
            ]
        }
    ],
    "displayShoppingSourcesBadges" : false,
    "displayRecipeSourcesBadges" : true
}
```

## <a name="next-steps"></a>Próximas etapas

Experimente a API. Vá para [Console de teste de API de Pesquisa de Imagem](https://dev.cognitive.microsoft.com/docs/services/8336afba49a84475ba401758c0dbf749/operations/571fab09dbe2d933e891028f). 

Para obter detalhes sobre como consumir os objetos de resposta, consulte [Pesquisa na Web](./search-the-web.md).

Para obter detalhes sobre como obter informações sobre uma imagem, como páginas da Web que incluem a imagem ou pessoas que foram reconhecidas na imagem, consulte [Informações da Imagem](./image-insights.md).  
  
Para obter detalhes de imagens que são populares nas redes sociais, consulte [Imagens Populares](./trending-images.md).  
