---
title: Obter imagens populares com a API de Pesquisa de Imagem do Bing
titleSuffix: Azure Cognitive Services
description: Pesquise imagens populares da Web com a API de Pesquisa de Imagem do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: cf7d1baf895d44730eb913b658ee4c7fe7eb7b11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96341607"
---
# <a name="get-trending-images-from-the-web"></a>Obter imagens populares da Web

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Para obter imagens populares de hoje, envie a solicitação GET a seguir:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Atualmente, a API de Imagens Populares dá suporte apenas aos seguintes mercados:  

- en-US (inglês, Estados Unidos)  
- en-CA (inglês, Canadá)  
- en-AU (inglês, Austrália)  
- zh-CN (chinês, China)

A resposta contém um objeto [TrendingImages](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#trendingimages) que lista imagens por categoria. Use o `title` da categoria para agrupar as imagens na sua experiência de usuário. As categorias podem ser alteradas diariamente.  

```json
{
    "_type" : "TrendingImages",  
    "categories" : [{  
        "title" : "Popular people searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Smith",  
                "displayText" : "Mr. Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=smith&FORM=..."
            },  
            "image" : {  
                "id" : "C3C60AE779A054D5CD80D3CACF0F3",  
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OIP.M2532...",  
                "contentUrl" : "http:\/\/www.contoso.com.au\/assets\/Uploads\/smith-SH01.jpg",  
                "thumbnail" : {  
                    "width" : 288,  
                    "height" : 300  
                }  
            }  
        },  
        . . .  
        ]  
    },  
    . . .  
    {  
        "title" : "Popular Halloween searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Halloween costumes for adults",  
                "displayText" : "Halloween costumes for adults",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Halloween+costumes..."
            },  
            "image" : {  
                "id" : "0F3395F2983003F89DCEE711B55D7FA53E4",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP.Me429c...",  
                "contentUrl" : "http:\/\/images.domain.com\/products\/8179\/1-1\/adult-squirrel...",  
                "thumbnail" : {  
                    "width" : 336,  
                    "height" : 480  
                }  
            }  
        }]  
    }]  
}  
```  

Cada bloco contém uma imagem e opções para obter imagens relacionadas. Para obter as imagens relacionadas, você pode usar a consulta `text` para chamar a [API de Pesquisa de Imagem](./overview.md) e exibir as imagens relacionadas por conta própria. Ou, você pode usar a URL no `webSearchUrl` para levar o usuário à página de resultados da pesquisa de imagens do Bing, que contém as imagens relacionadas.

Se você chamar a API de Pesquisa de Imagem para obter as imagens relacionadas, defina o parâmetro de consulta [id](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#id) com a ID no campo `id`. Especificar a ID garante que a resposta contenha a imagem (é a primeira imagem na resposta) e suas imagens relacionadas. Além disso, defina o parâmetro de consulta [q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) para o texto no `query` campo `text` do objeto.

O exemplo a seguir mostra como usar a ID de imagem para obter imagens relacionadas do Sr. Smith na resposta anterior da API de Imagens Populares.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```