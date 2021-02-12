---
title: Sugerindo termos de pesquisa com a API de Sugestão Automática do Bing
titleSuffix: Azure Cognitive Services
description: Este artigo discute o conceito de sugestão de termos de consulta usando o API de Sugestão Automática do Bing e o impacto do comprimento da consulta em relevância.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: be7686c4d8a676d2a1d85516d2e4aa6abe3f3bfd
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353401"
---
# <a name="suggesting-query-terms"></a>Sugestão de termos de consulta

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Normalmente, você chamará a API de Sugestão Automática do Bing sempre que um usuário digitar um novo caractere na caixa de pesquisa do aplicativo. A integridade da cadeia de caracteres de consulta afeta a relevância dos termos de consulta sugeridos que a API retorna. Quanto mais completa a cadeia de caracteres de consulta, mais relevante é a lista de termos de consulta sugeridos. Por exemplo, as sugestões que a API poderá retornar para `s` provavelmente são menos relevantes do que as consultas retornadas para `sailing dinghies`.

## <a name="example-request"></a>Solicitação de exemplo

O exemplo a seguir mostra uma solicitação que retorna as cadeias de caracteres de consulta sugeridas para *navegar*. Lembre-se de codificar a URL do termo de consulta parcial do usuário quando você definir o parâmetro de consulta [q](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#query). Por exemplo, se o usuário inserir *sailing les*, defina `q` como `sailing+les` ou `sailing%20les`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

A resposta a seguir contém uma lista de objetos de [SearchAction](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#searchaction) que contêm os termos de consulta sugeridos.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

## <a name="using-suggested-query-terms"></a>Usando os termos de consulta sugeridos

Cada sugestão inclui um campo `displayText`, `query` e `url`. O campo `displayText` contém a consulta sugerida que você usa para preencher a lista suspensa de sua caixa de pesquisa. Exiba todas as sugestões incluídas na resposta e na ordem determinada.

O exemplo a seguir mostra uma caixa de pesquisa suspensa com os termos de consulta sugeridos pela API de Sugestão Automática do Bing.

![Lista suspensa da caixa de pesquisa com sugestão automática](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Se o usuário selecionar uma consulta sugerida na lista suspensa, você poderá usar o termo de consulta no campo `query` para chamar a [API de Pesquisa na Web do Bing](../../bing-web-search/overview.md) e exibir os resultados por conta própria. Ou você pode usar a URL no campo `url` para enviar o usuário à página de resultados da pesquisa do Bing.

## <a name="next-steps"></a>Próximas etapas

* [O que é a API de Sugestão Automática do Bing?](../get-suggested-search-terms.md)