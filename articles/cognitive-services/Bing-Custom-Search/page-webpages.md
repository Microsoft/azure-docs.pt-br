---
title: Percorrer as páginas da Web disponíveis – Pesquisa Personalizada do Bing
titlesuffix: Azure Cognitive Services
description: Mostra como percorrer todas as páginas da Web que a Pesquisa Personalizada do Bing pode retornar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 891d0f88158b7d315b5908a7e1c0f73215b4b09e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61335357"
---
# <a name="paging-webpages"></a>Paginação de páginas da Web 

Quando você chama a API de Pesquisa Personalizada, o Bing retorna uma lista de resultados. A lista é um subconjunto do número total de resultados que podem ser relevantes para a consulta. Para obter o número total estimado de resultados disponíveis, acesse o campo [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#totalestimatedmatches) do objeto de resposta.  
  
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
  
Para paginar as páginas da Web disponíveis, use os parâmetros de consulta [contagem](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#count) e [deslocamento](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#offset).  
  
O parâmetro `count` especifica o número de resultados a serem retornados na resposta. O número máximo de resultados que você pode solicitar na resposta é 50. O padrão é 10. O número real entregue pode ser menor que o solicitado.

O parâmetro `offset` especifica o número de resultados para ignorar. O `offset` é baseado em zero e deve ser menor que (`totalEstimatedMatches` - `count`).  
  
Se você quiser exibir 15 páginas da Web por página, você definiria `count` como 15 e `offset` como 0 para a primeira página de resultados. Para cada página subsequente, você deve incrementar `offset` por 15 (por exemplo, 15, 30).  
  
O exemplo a seguir mostra um exemplo que solicita 15 imagens começando no deslocamento 45.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

Se o valor padrão `count` funcionar para a implementação, será necessário somente especificar o parâmetro de consulta `offset`.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> O `TotalEstimatedAnswers` campo é uma estimativa do número total de resultados de pesquisa, você pode recuperar da consulta atual.  Quando você define `count` e `offset` parâmetros, o `TotalEstimatedAnswers` número pode ser alterado. 

