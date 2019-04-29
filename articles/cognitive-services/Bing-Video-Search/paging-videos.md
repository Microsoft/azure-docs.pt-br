---
title: Como percorrer os vídeos disponíveis – Pesquisa de Vídeo do Bing
titlesuffix: Azure Cognitive Services
description: Saiba como percorrer todos os vídeos retornados pela API de Pesquisa de Vídeo do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 0af36fa68b2d801eed52e6f081b040fb56929c91
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60613641"
---
# <a name="paging-through-video-search-results"></a>Paginação de resultados de pesquisa de vídeo

A API de Pesquisa de Vídeo do Bing retorna um subconjunto de todos os resultados da pesquisa encontrados para a consulta. Ao percorrer esses resultados com chamadas subsequentes à API, você poderá obtê-los e exibi-los no aplicativo.

> [!NOTE]
> Paginação aplica-se somente a pesquisa de vídeos (vídeos/pesquisa) e não a insights de vídeo (vídeos/detalhes) ou tendências vídeos (vídeos/tendência).

## <a name="total-estimated-matches"></a>Total de correspondências estimadas

Para obter o número estimado de resultados da pesquisa encontrados, use o campo [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) na resposta JSON.   
  
```json  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
## <a name="paging-through-videos"></a>Paginação de vídeos

Para percorrer os vídeos disponíveis, use os parâmetros de consulta [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) e [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) ao enviar a solicitação.  
  

|Parâmetro  |DESCRIÇÃO  |
|---------|---------|
|`count`     | Especifica o número de resultados a serem retornados na resposta. O número máximo de resultados que você pode solicitar na resposta é 100. O padrão é 10. O número real entregue pode ser menor que o solicitado.        |
|`offset`     | Especifica o número de resultados para ignorar. O `offset` é baseado em zero e deve ser menor que (`totalEstimatedMatches` - `count`).          |

Por exemplo, se você quiser exibir 20 artigos por página, definiria `count` como 20 e `offset` como 0 para a primeira página de resultados. Para cada página subsequente, você deve incrementar `offset` por 20 (por exemplo, 20, 40).  
  
O exemplo a seguir solicita 20 vídeos, começando no deslocamento 40.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Se você usar o valor padrão em [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count), precisa apenas especificar o parâmetro de consulta `offset`, como mostra o exemplo a seguir.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Se você percorresse 35 vídeos por vez, teria o parâmetro `offset` definido como 0 na primeira solicitação de consulta e incrementaria `offset` em 35 em cada solicitação subsequente. No entanto, alguns resultados na próxima resposta podem conter resultados duplicados de vídeos da resposta anterior. Por exemplo, os primeiros dois vídeos em uma resposta podem ser os mesmos dois vídeos da resposta anterior.

Para eliminar resultados duplicados, use o campo [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) do objeto `Videos`.

Por exemplo, se você quiser paginar 30 vídeos por vez, defina `count` como 30 e `offset` como 0 na primeira solicitação. Na próxima solicitação, você definiria o parâmetro de consulta `offset` com o valor `nextOffset`.

> [!NOTE]
> O `TotalEstimatedAnswers` campo é uma estimativa do número total de resultados de pesquisa, você pode recuperar da consulta atual.  Quando você define `count` e `offset` parâmetros, o `TotalEstimatedAnswers` número pode ser alterado. 
  
## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Obter insights para vídeos](video-insights.md)
