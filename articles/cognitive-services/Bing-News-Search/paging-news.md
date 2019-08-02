---
title: Como paginar os resultados da API de Pesquisa de Notícias do Bing
titleSuffix: Azure Cognitive Services
description: Saiba como paginar os artigos de notícias retornados pela API de Pesquisa de Notícias do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: e5f8dce1a03e44758eea737ad2da419fa67c36a2
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423737"
---
# <a name="how-to-page-through-news-search-results"></a>Como paginar os resultados da pesquisa de notícias

Quando você chama a API de Pesquisa de Notícias, o Bing retorna uma lista de resultados que são relevantes para sua consulta. Para obter o número total estimado de resultados disponíveis, acesse o campo [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news-totalmatches) do objeto de resposta.  
  
O exemplo a seguir mostra o campo `totalEstimatedMatches` que uma resposta de Notícias inclui.  

```json
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Para paginar os artigos disponíveis, use os parâmetros de consulta [contagem](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#count) e [deslocamento](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#offset).  
 

|Parâmetro  |Descrição  |
|---------|---------|
|`count`     | Especifica o número de resultados a serem retornados na resposta. O número máximo de resultados que você pode solicitar na resposta é 100. O padrão é 10. O número real entregue pode ser menor que o solicitado.        |
|`offset`     | Especifica o número de resultados para ignorar. O `offset` é baseado em zero e deve ser menor que (`totalEstimatedMatches` - `count`).          |

Por exemplo, se você quiser exibir 20 artigos por página, definiria `count` como 20 e `offset` como 0 para a primeira página de resultados. Para cada página subsequente, você deve incrementar `offset` por 20 (por exemplo, 20, 40).  
  
O exemplo a seguir solicita 20 artigos de notícias começando no deslocamento 40.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Se o valor `count` padrão funciona para sua implementação, especifique apenas o parâmetro de consulta `offset`, conforme mostrado no exemplo a seguir:  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> A paginação aplica-se somente a pesquisa de notícias (/news/search) e não a tópicos populares (/news/trendingtopics) ou novas categorias (/news).

> [!NOTE]
> O `TotalEstimatedAnswers` campo é uma estimativa do número total de resultados de pesquisa, você pode recuperar da consulta atual.  Quando você define `count` e `offset` parâmetros, o `TotalEstimatedAnswers` número pode ser alterado. 
