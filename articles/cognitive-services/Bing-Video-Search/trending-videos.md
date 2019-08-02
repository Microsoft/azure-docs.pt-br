---
title: Pesquisar vídeos populares na Web usando a API de Pesquisa de Vídeo do Bing
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API de Pesquisa de Vídeo do Bing para pesquisar vídeos populares na Web.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: ea6f79bf6c305ecc07b3e684ede15ad439039e7e
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500607"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Obter vídeos populares com a API de Pesquisa de Vídeo do Bing 

A API de Pesquisa de Vídeo do Bing permite que você encontre os vídeos mais populares de hoje em toda a Web e em categorias diferentes. 

## <a name="get-request"></a>Solicitação GET

Para obter vídeos populares atuais a partir da API de Pesquisa de Vídeo do Bing, envie a solicitação GET a seguir:  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>Mercados com suporte

Os seguintes mercados oferecem suporte a vídeos populares.  
 
-   en-US (inglês, Austrália)  
-   en-CA (inglês, Canadá)  
-   en-GB (inglês, Grã-Bretanha)  
-   en-ID (inglês, Indonésia)  
-   en-IE (inglês, Irlanda)  
-   en-IN (inglês, Índia)  
-   en-NZ (inglês, Nova Zelândia)  
-   en-PH (inglês, Filipinas)  
-   en-SG (inglês, Singapura)  
-   en-US (inglês, Estados Unidos)  
-   en-WW (inglês, internacional agregar código)  
-   en-ZA (inglês, África do Sul)  
-   zh-CN (chinês, China)

## <a name="example-json-response"></a>Resposta JSON de exemplo  

O exemplo a seguir mostra uma resposta de API que contém os vídeos populares, que são listados por categoria e subcategoria. A resposta também contém vídeos de faixa, que são os vídeos mais populares, e que podem vir de um ou mais categorias.  

```json
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Obter insights para vídeos](video-insights.md)