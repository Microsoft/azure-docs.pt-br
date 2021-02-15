---
title: Pesquisar notícias com a API de Pesquisa de Notícias do Bing
titleSuffix: Azure Cognitive Services
description: Saiba como enviar consultas de pesquisa de notícias gerais, tópicos populares e manchetes.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: ecf01720126a9bf5da4aabb08653e62b42265d36
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351343"
---
# <a name="search-for-news-with-the-bing-news-search-api"></a>Pesquisar notícias com a API de Pesquisa de Notícias do Bing

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A API de Pesquisa de Imagem do Bing facilita a integração das funcionalidades de pesquisa cognitiva de notícias do Bing aos seus aplicativos.

Embora a API de Pesquisa de Notícias do Bing encontre e retorne principalmente artigos de notícias relevantes, ela fornece vários recursos para a recuperação inteligente e focalizada de notícias na Web.

## <a name="suggest-and-use-search-terms"></a>Sugerir e usar termos de pesquisa

Se você fornecer uma caixa de pesquisa em que o usuário insere seu termo de pesquisa, use a [API de Sugestão Automática do Bing](../../bing-autosuggest/get-suggested-search-terms.md) para melhorar a experiência. A API retorna cadeias de caracteres de consulta sugeridas com base em termos de pesquisa parciais como os tipos de usuário.

Depois que o usuário insere seu termo de pesquisa, a URL codifica-o antes de definir o parâmetro de consulta [q](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query). Por exemplo, se o usuário inserir *sailing dinghies* (bote à vela), defina `q` como `sailing+dinghies` ou `sailing%20dinghies`.

## <a name="get-general-news"></a>Obter notícias gerais

Para obter artigos de notícias gerais relacionados ao termo de pesquisa do usuário da Web, envie a solicitação GET a seguir:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Se for a primeira vez que você chama qualquer uma das APIs do Bing, não inclua o cabeçalho da ID do cliente. Só inclua a ID do cliente se você já tiver chamado uma API do Bing e o Bing retornou uma ID de cliente para a combinação de usuário e dispositivo.

Para obter notícias de um domínio específico, use o operador de consulta [site:](/previous-versions/bing/search/ff795613(v=msdn.10)).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

O exemplo JSON a seguir mostra a resposta à solicitação anterior. Como parte dos [requisitos de uso e exibição](../../bing-web-search/use-display-requirements.md) para APIs de pesquisa do Bing, você precisará exibir cada artigo de notícias na ordem fornecida na resposta. Se o artigo tiver artigos clusterizados, você deverá indicar se existem artigos relacionados e exibi-los mediante solicitação.

```json
{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v5\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99A72...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },

    ...

    {
        "name" : "Fabrikam Sailing Club to host Mirror Dinghy...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.36...",
                "width" : 448,
                "height" : 300
            }
        },
        "description" : "The sailing club that trained Olympian Ben...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-04T11:02:00",
        "category" : "Sports"
    }]
}
```

A resposta [notícias](/rest/api/cognitiveservices-bingsearch/bing-news-api-v5-reference#news) lista os artigos de notícias que o Bing considerou relevantes para a consulta. O campo `totalEstimatedMatches` contém uma estimativa do número de artigos disponíveis para exibição. Para obter informações sobre a paginação pelos artigos, veja [Paginação em notícias](../../bing-web-search/paging-search-results.md).

Cada [artigo de notícias](/rest/api/cognitiveservices-bingsearch/bing-news-api-v5-reference#newsarticle) na lista inclui o nome, descrição e URL do artigo para o artigo no site do host. Se o artigo contiver uma imagem, o objeto incluirá uma miniatura da imagem. Use `name` e `url` para criar um hiperlink que leva o usuário para o artigo de notícias no site do host. Se o artigo incluir uma imagem, também torne possível clicar nela usando `url`. Lembre-se de usar `provider` para atribuir o artigo.

Se o Bing puder determinar a categoria de artigo de notícias, o artigo incluirá o campo `category`.

## <a name="get-todays-top-news"></a>Obter as principais notícias de hoje

Para obter artigos de notícias de hoje, você pode enviar a mesma solicitação de notícias geral como antes, deixando o parâmetro `q` não definido.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

A resposta para obter as principais notícias é quase a mesma que para obter notícias gerais. No entanto, a resposta `news` não inclui o campo `totalEstimatedMatches` porque não há um número definido de resultados. O número de artigos de notícias principais pode variar conforme o ciclo de notícias. Lembre-se de usar o campo `provider` para atribuir o artigo.

## <a name="get-news-by-category"></a>Obter notícias por categoria

Para obter artigos de notícias divididos em categorias, como principais artigos de esportes ou entretenimento, envie a solicitação GET para o Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?category=sports&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Use o parâmetro de consulta [category](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) para especificar a categoria de artigos a obter. Para obter uma lista de categorias de notícias possíveis que você pode especificar, veja [Categorias por mercado](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news-categories-by-market).

A resposta para obter notícias para cada categoria é quase a mesma que para obter notícias gerais. No entanto, os artigos são todos da categoria especificada.

## <a name="get-headline-news"></a>Obter as manchetes

Para solicitar artigos de notícias de manchete e solicitar artigos de todas as categorias de notícias, envie a seguinte solicitação GET ao Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Não inclua o parâmetro de consulta [categoria](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category).

A resposta para obter notícias de manchete é igual à resposta para obter as principais notícias de hoje. Se o artigo for um artigo de manchete, seu campo `headline` será definido como **true**.

Por padrão, a resposta inclui até 12 artigos de machete. Para alterar o número de artigos de manchete a retornar, especifique o parâmetro de consulta [headlineCount](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#headlinecount). A resposta também inclui até quatro artigos não de manchete para cada categoria de notícias.

A resposta conta clusters como um artigo. Como um cluster pode ter vários artigos, a resposta pode incluir mais de 12 artigos de título de manchete e mais de quatro artigos não de manchete em cada categoria.

## <a name="get-trending-news"></a>Obter as notícias populares

Para obter tópicos de notícias em alta nas redes sociais, envie a seguinte solicitação GET ao Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
X-MSAPI-UserState: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!NOTE]
> Tópicos em Alta disponíveis apenas nos mercados en-US e zh-CN.

O JSON a seguir mostra a resposta à solicitação anterior. Cada artigo de notícias mais popular inclui uma imagem relacionada, sinalizador de últimas notícias e uma URL para os resultados da pesquisa do Bing para o artigo. Use a URL no campo `webSearchUrl` para levar o usuário para a página de resultados da pesquisa do Bing. Ou use o texto da consulta para chamar a [API de Pesquisa Web](../../bing-web-search/overview.md) para exibir os resultados por conta própria.

```json
{
    "_type" : "TrendingTopics",
    "value" : [{
        "name" : "Canada pot measure",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_hHD...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso Images"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Canada marijuana"
        }
    },
    {
        "name" : "Down on Vegas move",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_Bfbmg8h...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Marcus Appel Las Vegas"
        }
    },

    ...

    ]
}
```

## <a name="getting-related-news"></a>Como obter notícias relacionadas

Se houver outros artigos relacionados a um artigo de notícias, o artigo de notícias poderá incluir o campo [clusteredArticles](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle-clusteredarticles). O exemplo a seguir mostra um artigo com artigos em cluster.

```json
    {
        "name" : "Playoffs 2017: Betting lines, point spreads...",
        "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D115...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.D7B1...",
                "width" : 700,
                "height" : 393
            }
        },
        "description" : "April 14, 2017 3:37pm EDT April 14, 2017 3:34pm...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T19:43:00",
        "category" : "Sports",
        "clusteredArticles" : [{
            "name" : "Playoffs 2017: Betting odds, favorites to win...",
            "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D1159E...",
            "description" : "April 14, 2017 3:30pm EDT April 14, 2017 3:27pm...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }],
            "datePublished" : "2017-04-14T19:37:00",
            "category" : "Sports"
        }]
    },
```

## <a name="throttling-requests"></a>Solicitações de limitação

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como paginar os resultados da API de Pesquisa de Notícias do Bing](../../bing-web-search/paging-search-results.md)