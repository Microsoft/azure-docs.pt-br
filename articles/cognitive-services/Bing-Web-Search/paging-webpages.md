---
title: Como percorrer os resultados da pesquisa - API de pesquisa da Web do Bing
titleSuffix: Azure Cognitive Services
description: Aprenda a percorrer os resultados da pesquisa a partir da API da Pesquisa na web do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: aahi
ms.openlocfilehash: 68b99e7f0763f46c705a1ff2ba3a0e90216429c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648849"
---
# <a name="how-to-page-through-results-from-the-bing-web-search-api"></a>Como percorrer os resultados da API da Pesquisa na Web do Bing

Quando você chama a API de Pesquisa na Web, o Bing retorna uma lista de resultados. A lista é um subconjunto do número total de resultados que podem ser relevantes para a consulta. Para obter o número total estimado de resultados disponíveis, acesse o campo [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) do objeto de resposta.  

O exemplo a seguir mostra o campo `totalEstimatedMatches` que uma resposta da Web inclui.  

```
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

Para paginar as páginas da Web disponíveis, use os parâmetros de consulta [contagem](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#count) e [deslocamento](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#offset).  

O parâmetro `count` especifica o número de resultados a serem retornados na resposta. O número máximo de resultados que você pode solicitar na resposta é 50. O padrão é 10. O número real entregue pode ser menor que o solicitado.

O parâmetro `offset` especifica o número de resultados para ignorar. O `offset` é baseado em zero e deve ser menor que (`totalEstimatedMatches` - `count`).  

Se você quiser exibir 15 páginas da Web por página, você definiria `count` como 15 e `offset` como 0 para a primeira página de resultados. Para cada página subsequente, você deve incrementar `offset` por 15 (por exemplo, 15, 30).  

O exemplo a seguir solicita 15 páginas da Web começando no deslocamento 45.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Se o valor `count` padrão funcionar para sua implementação, você precisa apenas especificar o parâmetro `offset` de consulta.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

A API de Pesquisa na Web retorna resultados que incluem páginas da Web e podem incluir imagens, vídeos e notícias. Quando você pagina os resultados da pesquisa, você está paginando a resposta da [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) e não as outras respostas, como imagens ou notícias. Por exemplo, se você definir `count` como 50, você receberá 50 resultados de página da Web, mas a resposta pode incluir resultados para outras respostas também. Por exemplo, a resposta pode incluir 15 imagens e 4 artigos de notícias. Também é possível que os resultados incluam notícias na primeira página, mas não na segunda página, ou vice-versa.   

Se você especificar o parâmetro de consulta `responseFilter` e não incluir páginas da Web na lista de filtro, não use os parâmetros `count` e `offset`. 

> [!NOTE]
> O `TotalEstimatedAnswers` campo é uma estimativa do número total de resultados de pesquisa, você pode recuperar da consulta atual.  Quando você define `count` e `offset` parâmetros, o `TotalEstimatedAnswers` número pode ser alterado. 
