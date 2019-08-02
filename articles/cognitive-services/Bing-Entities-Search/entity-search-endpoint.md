---
title: O ponto de extremidade da API de Pesquisa de Entidade do Bing
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre o ponto de extremidade da API de Pesquisa de Entidade do Bing e envie solicitações para ele.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9d08091d0ea6869d13e294e60454f85a84f672ad
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424044"
---
# <a name="bing-entity-search-api-endpoint"></a>Ponto de extremidade da API de Pesquisa de Entidade do Bing


A API de Pesquisa de Entidade do Bing possui um ponto de extremidade que retorna entidades da Web com base em uma consulta. Esses resultados de pesquisa são retornados em JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Obter os resultados da entidade do ponto de extremidade

Para obter os resultados da entidade usando a **API do Bing**, envie uma solicitação `GET` para o ponto de extremidade a seguir. Use [cabeçalhos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) e [parâmetros de consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) para personalizar sua solicitação de pesquisa. As solicitações de pesquisa podem ser enviadas usando o parâmetro `?q=`.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [O que é a API de Pesquisa de Entidade do Bing?](overview.md)

## <a name="see-also"></a>Consulte também 

Para obter mais informações sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros e outros, confira o artigo de referência da [API de Pesquisa de Entidade do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
