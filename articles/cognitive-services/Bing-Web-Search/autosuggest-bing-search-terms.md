---
title: Termos de pesquisa de sugestão automática - API de Pesquisa na Web do Bing
titleSuffix: Azure Cognitive Services
description: Emparelhe a API de Pesquisa na Web do Bing com a API de Sugestão Automática do Bing para fornecer aos usuários uma experiência de pesquisa aprimorada.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 1a3f479fbbe68c8880cd7fefb3b57c77d4cfbbfe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96349564"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Termos de pesquisa de Sugestão Automática do Bing no aplicativo

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Se você fornecer uma caixa de pesquisa em que o usuário insere seu termo de pesquisa, use a [API de Sugestão Automática do Bing](../bing-autosuggest/get-suggested-search-terms.md) para melhorar a experiência. A API retorna cadeias de caracteres de consulta sugeridas com base em termos de pesquisa parciais como os tipos de usuário.

Depois que o usuário insere um termo de pesquisa, ele deve ser codificado como URL antes que o parâmetro de consulta [q](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) seja definido. Por exemplo, se o usuário inserir *sailing dinghies* (bote à vela), defina `q` como `sailing+dinghies` ou `sailing%20dinghies`.

Se o termo de consulta contiver um erro de ortografia, a resposta da pesquisa incluirá um objeto [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext). O objeto mostra a ortografia original e a ortografia corrigida usada pelo Bing para a pesquisa.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

Você pode usar essas informações para, ao exibir os resultados da pesquisa, informar o usuário de que você modificou a cadeia de caracteres de consulta fornecida por ele.

![Exemplo de experiência do usuário do contexto de consulta](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Próximas etapas  

* Revise o exemplo [Respostas da API de Pesquisa na Web do Bing](search-responses.md).  

## <a name="see-also"></a>Confira também  

* [Referência da API de Pesquisa na Web do Bing](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)