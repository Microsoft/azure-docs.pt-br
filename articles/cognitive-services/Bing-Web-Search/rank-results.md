---
title: Como usar rankings para exibir os resultados da pesquisa - API de Pesquisa na Web do Bing
titleSuffix: Azure Cognitive Services
description: Aprenda a usar a classificação para exibir os resultados da pesquisa da API de Pesquisa na Web do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.author: scottwhi
ms.openlocfilehash: 9a49c4af474d7f0618bf0cff1a093e5cbb62fe2d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648815"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>Como usar a classificação para exibir os resultados da API de Pesquisa na Web do Bing  

A resposta de cada pesquisa inclui uma resposta [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse), que especifica como você precisa exibir os resultados da pesquisa. A resposta de classificação agrupa os resultados por conteúdo da linha principal e conteúdo da barra lateral para uma página tradicional de resultados da pesquisa. Se você não exibir os resultados em um formato de barra lateral e linha principal tradicional, precisará fornecer uma maior visibilidade ao conteúdo da linha principal do que ao conteúdo da barra lateral.  

Em cada grupo (linha principal ou barra lateral), a matriz de [Itens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) identifica a ordem na qual o conteúdo precisa ser exibido. Cada item fornece as duas maneiras a seguir para identificar o resultado em uma resposta.  

-   `answerType` e `resultIndex` – o campo `answerType` identifica a resposta (por exemplo, página da Web ou Notícias) e `resultIndex` identifica um resultado na resposta (por exemplo, um artigo de notícias). O índice é baseado em zero.  

-   `value` — O campo `value` contém uma ID que corresponde à ID de uma resposta ou um resultado dentro de resposta. A resposta ou os resultados contêm a ID, mas não ambos.  

O uso da ID é mais simples porque você só precisa fazer a correspondência da ID de classificação com a ID de uma resposta ou um de seus resultados. Se um objeto de resposta inclui um campo `id`, exiba todos os resultados da resposta juntos. Por exemplo, se o objeto `News` incluir o campo `id`, exiba todos os artigos de notícias juntos. Se o objeto `News` não incluir o campo `id`, cada artigo de notícias conterá um campo `id` e a resposta de classificação combinará os artigos de notícias com os resultados de outras respostas.  

O uso do `answerType` e do `resultIndex` é um pouco mais complicado. Use `answerType` para identificar a resposta que contém os resultados a serem exibidos. Em seguida, use `resultIndex` para indexar os resultados da resposta para obter o resultado a ser exibido. (O valor `answerType` é o nome do campo no objeto [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse).) Se você precisa para exibir todos os resultados da resposta juntos, a resposta de classificação de item não inclui o campo `resultIndex`.  

## <a name="ranking-response-example"></a>Exemplo de resposta de classificação

A seguir é mostrado um exemplo de [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse). Como a resposta da Web não inclui um campo `id`, exiba todas as páginas da Web individualmente com base na classificação (cada página da Web inclui um campo `id`). Como as imagens, os vídeos e as respostas das pesquisas relacionadas incluem o campo `id`, você exibe os resultados de cada uma dessas respostas juntos, com base na classificação.

```json
{  
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
        "totalEstimatedMatches" : 835000,
        "value" : [
            {
                "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
                "name" : "Motor Sports - Live at the race track ...",
                "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                "displayUrl" : "www.contoso.com\/usa\/eventsandracing\/motorsport",
                "snippet" : "Here you will find detailed information about racing...",
                "deepLinks" : [{
                    "name" : "Customer Racing",
                    "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                    "snippet" : "Customer racing news; General news..."
            },
            . . .  
        ]  
    }],  
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "2016 Supercar Wallpapers",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2017-03-25T11:14:00",
                "contentUrl" : "http:\/\/www.contoso.com\/wall...",
                "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "contentSize" : "373283 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "http:\/\/www.contoso.com\/lmp-...",
                "width" : 1920,
                "height" : 1080,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "insightsSourcesSummary" : {
                    "shoppingSourcesCount" : 0,
                    "recipeSourcesCount" : 0
                }
            },
            . . .  
        ]  
    },  
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches",
        "value" : [
            {
                "text" : "vintage racing teams",
                "displayText" : "vintage racing teams",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2..."
            },
            . . .  
        ]  
    },  
    "videos" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/videos...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "Why We Race",
                "description" : "A new era begins in motorsports this weekend...",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2...",
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.Vo1...",
                "datePublished" : "2014-01-25T16:31:48",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=oL...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "encodingFormat" : "mp4",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=oLAZgD...",
                "width" : 480,
                "height" : 360,
                "duration" : "PT2M42S",
                "motionThumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OM...",
                "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www.you...<\/iframe>",
                "allowHttpsEmbed" : true,
                "viewCount" : 47325,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "allowMobileEmbed" : true,
                "isSuperfresh" : false
            },
            . . .  
        ]  
    },  
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 2,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2"
                }
            },
            {
                "answerType" : "Videos",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 3,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 4,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 5,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5"
                }
            }]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}  
```  

Com base nessa resposta de classificação, a linha principal exibe os seguintes resultados da pesquisa:  

-   O primeiro resultado de página da Web
-   Todas as imagens  
-   O segundo e o terceiro resultados de página da Web  
-   Todos os vídeos  
-   O 4º, 5º e 6º resultados de página da Web  

A barra lateral exibe os seguintes resultados da pesquisa:  

-   Todas as pesquisas relacionadas  


## <a name="next-steps"></a>Próximos passos

Para obter informações sobre como promover resultados não classificados, confira [Promovendo respostas que não estão classificadas](./filter-answers.md#promoting-answers-that-are-not-ranked).

Para obter informações sobre como limitar o número de respostas classificadas na resposta, confira [Limitando o número de respostas na resposta](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

Para obter um exemplo em C# que usa a classificação para exibir os resultados, confira [Tutorial de classificação do C#](./csharp-ranking-tutorial.md).
