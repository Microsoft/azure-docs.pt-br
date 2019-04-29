---
title: Como filtrar os resultados da pesquisa - API de pesquisa do Bing na Web
titleSuffix: Azure Cognitive Services
description: Saiba como filtrar e exibir resultados da pesquisa da API de pesquisa da Web do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: 26c38c34543683a3fc450d3a0ae932d8bd30dc98
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61431031"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrar as respostas que a resposta da pesquisa inclui  

Quando você realiza uma consulta na Web, o Bing retorna todo o conteúdo relevante que ele encontra para a pesquisa. Por exemplo, se a consulta de pesquisa for "vela+barcos", a resposta poderá conter as seguintes respostas:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```
Você pode filtrar os tipos de conteúdo que receberá (para imagens, vídeos e notícias, por exemplo) usando o parâmetro de consulta [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#responsefilter). Se o Bing localizar conteúdo relevante para as respostas especificadas, este conteúdo será retornado. O filtro de respostas é uma lista de respostas delimitada por vírgulas. 

Para excluir tipos específicos de conteúdo da resposta, como imagens, você pode adicionar um caractere `-` ao início do valor `responseFilter`. Você pode separar os tipos excluídos com uma vírgula (`,`). Por exemplo: 

```
&responseFilter=-images,-videos
```


A seguir, mostramos como usar `responseFilter` para solicitar imagens, vídeos e notícias sobre barcos a vela. Quando você codifica a cadeia de caracteres de consulta, as vírgulas são alteradas para %2C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

O exemplo a seguir mostra a resposta à solicitação anterior. Como o Bing não encontrou resultados relevantes de vídeo e notícias a resposta não os incluiu.

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

Embora o Bing não tenha retornado os resultados de vídeo e notícias na resposta anterior, isso não significa que o conteúdo de vídeo e de notícias não exista. Isso significa apenas que a página não os inclui. No entanto, se você percorresse as [páginas](./paging-webpages.md) através de mais resultados, as páginas subsequentes provavelmente os incluiriam. Além disso, se você chamar os pontos de extremidade [API de Pesquisa de Vídeo](../bing-video-search/search-the-web.md) e [API de Pesquisa de Notícias](../bing-news-search/search-the-web.md) diretamente, a resposta provavelmente conterá resultados.

Não é recomendável usar `responseFilter` para obter os resultados de uma única API. Se desejar o conteúdo de uma única API do Bing, chame essa API diretamente. Por exemplo, para receber apenas imagens, envie uma solicitação para o ponto de extremidade da API de Pesquisa de Imagens, `https://api.cognitive.microsoft.com/bing/v7.0/images/search` ou um dos outros pontos de extremidade [Imagens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#endpoints). Chamar a única API é importante não apenas por motivos de desempenho, mas porque as APIs específicas do conteúdo oferecem resultados mais úteis. Por exemplo, você pode usar filtros que não estão disponíveis para a API de Pesquisa na Web para filtrar os resultados.  

Para obter os resultados da pesquisa de um domínio específico, inclua o operador de consulta `site:` na cadeia de caracteres da consulta.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> Dependendo da consulta, se você usar o operador de consulta `site:`, haverá a possibilidade de que a resposta contenha conteúdo adulto, independentemente da configuração da [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#safesearch). Só use `site:` se estiver ciente sobre o conteúdo do site e se seu cenário permitir a possibilidade de obtenção de conteúdo adulto.

## <a name="limiting-the-number-of-answers-in-the-response"></a>Limite o número de itens na resposta

O Bing inclui itens na resposta com base na classificação. Por exemplo, se você consultar *vela+barcos*, o Bing retornará `webpages`, `images`, `videos` e `relatedSearches`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Para limitar o número de respostas que o Bing retorna para as duas principais respostas (páginas da Web e imagens), defina o parâmetro de consulta [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) como 2.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

A resposta incluirá apenas `webPages` e `images`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

Se você adicionar o parâmetro de consulta `responseFilter` à consulta anterior e defini-lo como páginas da Web e notícias, a resposta conterá apenas páginas da Web, pois as notícias não serão classificadas.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>Promova respostas que não estão classificadas

Se as respostas mais bem classificadas retornadas pelo Bing para uma consulta fossem páginas da Web, imagens, vídeos e pesquisas relacionadas, a resposta incluiria essas respostas. Se você definir [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) como dois (2), o Bing retornará as duas principais respostas classificadas: páginas da Web e imagens. Se você quiser que o Bing inclua imagens e vídeos na resposta, especifique o parâmetro de consulta [promover](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#promote) e defina-o como imagens e vídeos.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

O exemplo a seguir é a resposta da solicitação anterior. O Bing retorna as duas principais respostas, páginas da Web e imagens e promove vídeos para a resposta.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Se você definir `promote` como notícias, a resposta não incluirá a resposta de notícias por essa não ser uma resposta de classificação &mdash;você só pode promover respostas classificadas.

As respostas que você deseja promover não contam em relação ao limite `answerCount`. Por exemplo, se as respostas classificadas forem notícias, imagens e vídeos, e você definir `answerCount` como 1 e `promote` para notícias, a resposta conterá notícias e imagens. Ou, se as respostas classificadas forem vídeos, imagens e notícias, a resposta conterá vídeos e notícias.

Você só poderá usar `promote` se especificar o parâmetro de consulta `answerCount`.
