---
title: Personalizar e sugerir consultas de pesquisa de imagem - API de pesquisa de imagem do Bing
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre como personalizar as consultas de pesquisa enviadas à API de Pesquisa de Imagem do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: d833b017004365e9dad7241e360f42ff41a55883
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542752"
---
# <a name="customize-and-suggest-image-search-queries"></a>Personalizar e sugerir consultas de pesquisa de imagem

Use este artigo para aprender a personalizar as consultas e sugerir os termos de pesquisa para enviar para a API de pesquisa de imagem do Bing.

## <a name="suggest-search-terms"></a>Sugerir os termos de pesquisa

Se o aplicativo tiver uma caixa de pesquisa em que os termos de pesquisa são inseridos, use a [API de Sugestão Automática do Bing](../../bing-autosuggest/get-suggested-search-terms.md) para melhorar a experiência. A API pode exibir os termos de pesquisa sugeridos em tempo real. A API retorna cadeias de consulta sugeridas com base em termos de pesquisa parciais e nos serviços cognitivos do Azure.

## <a name="pivot-the-query"></a>Dinamizar a consulta

Se o Bing conseguir segmentar a consulta de pesquisa original, o objeto [Imagens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) contém `pivotSuggestions`. Dinamizar sugestões pode ser exibido como termos de pesquisa opcional para o usuário. Por exemplo, se a consulta original foi *Microsoft Surface*, o Bing pode segmentar a consulta entre *Microsoft* e *Surface* e fornecer pivôs sugeridos para cada um. Essas sugestões podem ser exibidas como termos de pesquisa opcionais para o usuário.

O seguinte exemplo mostra as sugestões de pivô para o *Microsoft Surface*:  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

O campo `pivotSuggestions` contém a lista de segmentos (dinâmicas) em que a consulta original foi dividida. Para cada tipo de dinâmica, a resposta contém uma lista de objetos de [Consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj) que contem consultas sugeridas. O `text` campo contém a consulta sugerida. O campo `displayText` contém a consulta sugerida, e o campo contém o termo que substitui a dinâmica na consulta original. Por exemplo, data de lançamento do Surface.

Se a cadeia de caracteres de consulta dinâmica é o que o usuário está procurando, use o `text` e `thumbnail` cadeias de caracteres de consulta de campos a serem exibidas na dinamização. Tornar o texto e a miniatura clicável usando a `webSearchUrl` URL ou a `searchLink` URL. Use `webSearchUrl` para enviar o usuário para os resultados da pesquisa do Bing. Se você fornecer sua própria página de resultados, use `searchLink`.

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>Expanda a consulta

Se o Bing conseguir expandir a consulta para restringir a pesquisa original, o objeto [Imagens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) contém o campo `queryExpansions`. Por exemplo, se a consulta foi *Microsoft Surface*, as consultas expandidas podem ser:
- Microsoft Surface **Pro 3**.
- Microsoft Surface **RT**.
- Microsoft Surface **Phone**.
- Microsoft Surface **Hub**.

O exemplo a seguir mostra as consultas expandidas do *Microsoft Surface*.

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

O campo `queryExpansions` contém uma lista de objetos de [Consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj). O campo `text` contém a consulta sugerida. O campo `displayText` contém ao termo de expansão. Se a cadeia de caracteres de consulta expandida é o que o usuário está procurando, use os campos `text` e `thumbnail` para exibir as cadeias de caracteres de consulta expandida. Tornar o texto e a miniatura clicável usando a `webSearchUrl` URL ou a `searchLink` URL. Use `webSearchUrl` para enviar o usuário para os resultados da pesquisa do Bing. Se você fornecer sua própria página de resultados, use `searchLink`.

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>Solicitações de limitação

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Próximas etapas

Se você ainda não experimentou a API de Pesquisa de Imagem do Bing, experimente seguir um [início rápido](../quickstarts/csharp.md). Se estiver procurando algo mais complexo, experimente seguir o tutorial para a criação de um [aplicativo Web de página única](../tutorial-bing-image-search-single-page-app.md).
