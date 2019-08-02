---
title: Usar a classificação para exibir as respostas ‒ Pesquisa de Entidade do Bing
titleSuffix: Azure Cognitive Services
description: Saiba como usar a classificação para exibir as respostas que a API de Pesquisa de Entidade do Bing retorna.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 110cef117683b20170649a231226c8193496edf3
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423909"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Usar a classificação para exibir os resultados da pesquisa da entidade  

Cada resposta da pesquisa de entidade inclui uma resposta [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse), que especifica como você deve exibir os resultados da pesquisa retornados pela API de Pesquisa de Entidade do Bing. A resposta de classificação agrupa os resultados no polo, principal e o conteúdo da barra lateral. O resultado de polo é o resultado mais importante ou proeminente e deve ser exibido primeiro. Se você não exibir os resultados restantes em um formato de barra lateral e principal tradicional, você deve fornecer a visibilidade de maior conteúdo principal que o conteúdo da barra lateral. 
  
Em cada grupo, a matriz de [Itens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) identifica a ordem na qual o conteúdo deve aparecer. Cada item fornece duas maneiras para identificar o resultado em uma resposta.  
 

|Campo | Descrição  |
|---------|---------|
|`answerType` e `resultIndex` | `answerType` identifica a resposta (entidade ou local) e `resultIndex` identifica um resultado dentro dessa resposta (por exemplo, uma entidade). O índice começa em 0.|
|`value`    | `value` contém uma ID que corresponde à ID de uma resposta ou um resultado dentro da resposta. A resposta ou os resultados contêm a ID, mas não ambos. |
  
Usar o `answerType` e `resultIndex` é um processo de duas etapas. Primeiro, use `answerType` para identificar a resposta que contém os resultados a serem exibidos. Em seguida, use `resultIndex` para indexar os resultados dessa resposta para obter o resultado a exibir. (O valor `answerType` é o nome do campo no objeto [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse).) Se você precisa para exibir todos os resultados da resposta juntos, a resposta de classificação de item não inclui o campo `resultIndex`.

Usar a ID requer correspondência da ID de classificação com a ID de uma resposta ou um dos seus resultados. Se um objeto de resposta inclui um campo `id`, exiba todos os resultados da resposta juntos. Por exemplo, se o objeto `Entities` inclui o campo `id`, exibir todos os artigos de entidades juntos. Se o objeto `Entities` não inclui o campo `id`, em seguida, cada entidade contém um campo `id` e a resposta de classificação mescla as entidades com os resultados de locais.  
  
## <a name="ranking-response-example"></a>Exemplo de resposta de classificação

A seguir é mostrado um exemplo de [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

Com base nessa resposta de classificação, a barra lateral exibirá os resultados de duas entidades relacionados ao Jimi Hendrix.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](tutorial-bing-entities-search-single-page-app.md)
