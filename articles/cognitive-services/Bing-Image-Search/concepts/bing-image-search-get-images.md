---
title: Obter imagens da Web - API de Pesquisa de Imagem do Bing
titleSuffix: Azure Cognitive Services
description: Use a API de Pesquisa de Imagem do Bing para pesquisar e obter imagens relevantes da Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 309bbca762149f8804742d9ef02d4c3e8dfcdc6b
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542761"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Obter imagens da Web com a API de Pesquisa de Imagem do Bing

Ao usar a API REST de Pesquisa de Imagem do Bing, você pode obter imagens da Web relacionadas ao seu termo de pesquisa, enviando a seguinte solicitação GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Use o [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) parâmetro de consulta para o termo de pesquisa codificado de url. Por exemplo, se você inserir *sailing dinghies* (bote à vela), defina `q` como `sailing+dinghies` ou `sailing%20dinghies`.

> [!IMPORTANT]
> * Todas as solicitações precisam ser feitas por meio de um servidor, e não de um cliente.
> * Se essa é a primeira vez que você chama uma das APIs de Pesquisa do Bing, não inclua o cabeçalho da ID do cliente. Só inclua a ID do cliente se já tiver chamado uma API do Bing que retornou uma ID do cliente para a combinação de usuário e dispositivo.

## <a name="get-images-from-a-specific-web-domain"></a>Obter imagens de um domínio da Web específico

Para obter imagens de um domínio específico, use o operador de consulta [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> As respostas a consultas usando o operador `site:` podem incluir conteúdo para adultos, independentemente da configuração [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch). Só use `site:` se estiver ciente do conteúdo do domínio.

## <a name="filter-images"></a>Filtrar imagens

 Por padrão, a API de Pesquisa de Imagem retorna todas as imagens que são relevantes para a consulta. Caso deseje filtrar as imagens retornadas pelo Bing (por exemplo, para retornar somente as imagens com uma tela de fundo transparente ou um tamanho específico), use os seguintes parâmetros de consulta:

* [aspecto](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#aspect)— Filtrar imagens por taxa de proporção (por exemplo, imagens de tela grande ou padrão).
* [cor](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#color)— Filtrar imagens por cor dominante ou preto e branco.
* [atualização](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#freshness)— Filtrar imagens por idade (por exemplo, imagens descobertas pelo Bing na última semana).
* [altura](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#height), [largura](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#width)— Filtrar imagens por largura e altura.
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagecontent)— Filtrar imagens por conteúdo (por exemplo, imagens que mostram apenas o rosto de uma pessoa).
* [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype)— Filtrar imagens por tipo (por exemplo, clip-art, GIFs animados ou planos de fundo transparentes).
* [licença](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license)— Filtrar imagens pelo tipo de licença associado ao site.
* [tamanho](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#size)— Filtrar imagens por tamanho, como imagens pequenas de até 200x200 pixels.

Para obter imagens de um domínio específico, use o operador de consulta [site:](https://msdn.microsoft.com/library/ff795613.aspx).

O exemplo a seguir mostra como obter imagens pequenas de ContosoSailing.com que o Bing descobriu na última semana.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Formato de resposta da Pesquisa de Imagem do Bing

A mensagem de resposta do Bing contém uma resposta [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) que contém uma lista de imagens que os Serviços Cognitivos consideraram relevantes para a consulta. Cada objeto [Image](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) da lista inclui as seguintes informações sobre a imagem: a URL, seu tamanho, suas dimensões, seu formato de codificação, uma URL para uma miniatura da imagem e as dimensões da miniatura.

> [!NOTE]
> * As imagens precisam ser exibidas na ordem fornecida na resposta.
> * Porque os formatos de URL e os parâmetros estão sujeitos a alterações sem aviso prévio, use todas as URLs como-está. Você não deveria receber dependências no formato de URL ou parâmetros, exceto onde observado.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

Quando você chamar a API de Pesquisa de Imagem do Bing, o Bing retorna uma lista de resultados. A lista é um subconjunto do número total de resultados que são relevantes para a consulta. O campo `totalEstimatedMatches` da resposta contém uma estimativa do número de imagens disponíveis para exibição. Para obter detalhes sobre como percorrer as imagens restantes, consulte [Paginação de imagens](../paging-images.md).

## <a name="next-steps"></a>Próximas etapas

Se você ainda não experimentou a API de Pesquisa de Imagem do Bing, experimente seguir um [início rápido](../quickstarts/csharp.md). Se estiver procurando algo mais complexo, experimente seguir o tutorial para a criação de um [aplicativo Web de página única](../tutorial-bing-image-search-single-page-app.md).
