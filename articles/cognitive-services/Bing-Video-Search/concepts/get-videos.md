---
title: Enviar solicitações de pesquisa para a API de Pesquisa de Vídeo do Bing
titleSuffix: Azure Cognitive Services
description: Saiba como enviar consultas de pesquisa para a API de Pesquisa de Vídeo do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: f1d433dd7d919aa5d11f2f59b74a5e3f2dca0b59
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500297"
---
# <a name="search-for-videos-with-the-bing-video-search-api"></a>Pesquisar vídeos com a API de Pesquisa de Vídeo do Bing

A API de Pesquisa de Vídeo do Bing facilita a integração das funcionalidades de pesquisa cognitiva de notícias do Bing aos seus aplicativos. Embora a API encontre e retorne principalmente vídeos relevantes da Web, ela fornece vários recursos para a recuperação inteligente e focalizada de vídeos na Web.

## <a name="getting-videos"></a>Como obter vídeos

Para obter vídeos relacionados ao termo de pesquisa do usuário da Web, envie a solicitação GET a seguir:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Todas as solicitações devem ser feitas a partir de um servidor.

Se for a primeira vez que você chama qualquer uma das APIs do Bing, não inclua o cabeçalho da ID do cliente. Só inclua a ID do cliente se você já tiver chamado uma API do Bing e o Bing retornou uma ID de cliente para a combinação de usuário e dispositivo.

Para obter vídeos de um domínio específico, use o operador de consulta [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

A resposta contém uma resposta de [Vídeos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) contendo uma lista de vídeos que o Bing considerou serem relevantes para a consulta. Cada objeto [Vídeo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) na lista inclui a URL do vídeo, sua duração, suas dimensões e seu formato de codificação, entre outros atributos. O objeto de vídeo também inclui a URL de uma miniatura do vídeo e as dimensões da miniatura.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYW...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : 
            {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D569...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y62...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : 
            {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E113CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        ...
    ],
    "queryExpansions" : [...],
    "nextOffsetAddCount" : 0,
    "pivotSuggestions" : [...]
}
```

## <a name="video-thumbnails"></a>Miniaturas de vídeo

Você pode exibir todas as miniaturas de vídeo, ou um subconjunto delas, retornadas pela API de Pesquisa de Vídeo do Bing. Se você exibir um subconjunto, forneça ao usuário a opção para exibir os vídeos restantes. Como parte dos [requisitos de uso e exibição](../UseAndDisplayRequirements.md) da API do Bing, você precisa exibir os vídeos na ordem fornecida na resposta. Para obter informações sobre o redimensionamento da miniatura, consulte [Redimensionando e cortando miniaturas](../../bing-web-search/resize-and-crop-thumbnails.md). 

À medida que o usuário passa o mouse sobre a miniatura, é possível usar [motionThumbnailUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-motionthumbnailurl) para reproduzir uma versão em miniatura do vídeo. Atribua a miniatura de movimento ao exibi-la.

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Quando uma miniatura é clicada, há três opções para exibir o vídeo:

- Use [hostPageUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-hostpageurl) para exibir o vídeo no site do host (por exemplo, no YouTube)
- Use [webSearchUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-websearchurl) para exibi o vídeo no navegador de vídeo do Bing
- Use [embdedHtml](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-embedhtml) para inserir o vídeo em sua própria experiência 

Use o editor e o criador para o atribuir o vídeo durante a reprodução.

Para obter detalhes sobre como usar [videoId](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-videoid) para obter informações sobre o vídeo, veja [Insights de vídeo](../video-insights.md).

## <a name="filtering-videos"></a>Como filtrar vídeos

Por padrão, a API de pesquisa de vídeo retorna todos os vídeos relevantes para a consulta. Se você só quiser vídeos gratuitos ou vídeos de menos de cinco minutos de duração, use os seguintes parâmetros de consulta de filtro:

- [preços](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#pricing)&mdash;Filtrar vídeos por preço (por exemplo, vídeos gratuitos ou pelos quais você precise pagar)
- [resolução](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#resolution)&mdash;Filtrar vídeos por resolução (por exemplo, vídeos com uma resolução de 720p ou mais alta)
- [videoLength](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videolength)&mdash;Filtrar vídeos por duração do vídeo (por exemplo, vídeos de menos de cinco minutos de duração)
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#freshness)&mdash;Filtrar vídeos por idade (por exemplo, vídeos descobertos pelo Bing na última semana)

Para obter os vídeos de um domínio específico, inclua o operador de consulta [site:](https://msdn.microsoft.com/library/ff795613.aspx) na cadeia de caracteres da consulta.

> [!NOTE]
> Dependendo da consulta, se você usar o operador de consulta `site:`, haverá a possibilidade de que a resposta contenha conteúdo para adulto, independentemente da configuração [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#safesearch). Só use `site:` se estiver ciente sobre o conteúdo do site e se seu cenário permitir a possibilidade de obtenção de conteúdo adulto.

O exemplo a seguir mostra como obter vídeos gratuitos de ContosoSailing.com que tenham uma resolução de 720p ou melhor e que o Bing tenha descoberto no mês passado.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&pricing=free&freshness=month&resolution=720p&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="expanding-the-query"></a>Expandindo a consulta

Se o Bing conseguir expandir a consulta para restringir a pesquisa original, o objeto [Vídeos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) conterá o campo `queryExpansions`. Por exemplo, se a consulta foi *Limpeza de esgoto*, as consultas expandidas podem ser: **Ferramentas** de limpeza de esgoto, limpeza de esgotos **na superfície**, **máquina** de limpeza de esgoto e limpeza de esgoto **fácil**.

O exemplo a seguir mostra as consultas expandidas para *Limpeza de Sarjetas*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC5...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 4,
    "queryExpansions" : [
        {
            "text" : "Gutter Cleaning Tools",
            "displayText" : "Tools",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FB....",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Gutter..."
            }
        },
        ...
    ]
    "pivotSuggestions" : [...],
}
```

O campo `queryExpansions` contém uma lista de objetos de [Consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj). O campo `text` contém a consulta expandida e o campo `displayText` contém o termo de expansão. É possível usar os campos texto e miniatura para exibir as cadeias de caracteres da consulta expandida para o usuário caso seja isso que eles realmente estejam procurando. Crie um texto clicável para a miniatura usando a URL `webSearchUrl` ou `searchLink`. Use `webSearchUrl` para enviar o usuário para os resultados de pesquisa do Bing ou `searchLink` se você fornecer sua própria página de resultados.

## <a name="pivoting-the-query"></a>Dinamizando a consulta

Se o Bing conseguir segmentar a consulta de pesquisa original, o objeto [Vídeos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) contém o campo `pivotSuggestions`. Por exemplo, se a consulta original for *Limpeza de Sarjetas*, o Bing poderá segmentar a consulta em *Limpeza* e *Sarjetas*.

O seguinte exemplo mostra as sugestões de pivô para *Limpeza de Sarjetas*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 0,
    "queryExpansions" : [...],
    "pivotSuggestions" : [
        {
            "pivot" : "cleaning",
            "suggestions" : [
                {
                    "text" : "Gutter Repair",
                    "displayText" : "Repair",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/videos...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=Gutter..."
                    }
                },
                ...
            ]
        },
        {
            "pivot" : "gutters",
            "suggestions" : [
                {
                    "text" : "Window Cleaning",
                    "displayText" : "Window",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC59...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=Window..."
                    }
                },
                ...
            ]
        }
    ]
}
```

Para cada tipo de dinâmica, a resposta contém uma lista de objetos de [Consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj) que contem consultas sugeridas. O campo `text` contém a consulta sugerida, e o campo `displayText` contém o termo que substitui a dinâmica na consulta original. Por exemplo, Limpeza de Janela.

É possível usar os campos `text` e `thumbnail` para exibir as cadeias de caracteres da consulta expandida para o usuário caso seja isso que eles realmente estejam procurando. Crie um texto clicável para a miniatura usando a URL `webSearchUrl` ou `searchLink`. Use `webSearchUrl` para enviar o usuário para os resultados de pesquisa do Bing ou `searchLink` se você fornecer sua própria página de resultados.

## <a name="throttling-requests"></a>Solicitações de limitação

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]
