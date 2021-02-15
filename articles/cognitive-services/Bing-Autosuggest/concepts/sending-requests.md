---
title: Enviar solicitações para a API de Sugestão Automática do Bing
titleSuffix: Azure Cognitive Services
description: A API de Sugestão Automática do Bing retorna uma lista de consultas sugeridas com base na cadeia de consulta parcial da caixa de pesquisa. Saiba mais sobre como enviar solicitações.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: scottwhi
ms.openlocfilehash: dd845c0fb877afa76b84eb5c2d86392f763eccf7
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353384"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>Enviando solicitações para a API de Sugestão Automática do Bing.

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Se o aplicativo enviar consultas para uma das APIs de Pesquisa do Bing, você poderá usar a API de Sugestão Automática do Bing para aprimorar a experiência de pesquisa dos usuários. A API de Sugestão Automática do Bing retorna uma lista de consultas sugeridas com base na cadeia de consulta parcial da caixa de pesquisa. Como os caracteres são inseridos em uma caixa de pesquisa no aplicativo, você pode exibir as sugestões em uma lista suspensa. Use este artigo para saber mais sobre como enviar solicitações para essa API. 

## <a name="bing-autosuggest-api-endpoint"></a>Ponto de extremidade da API de Sugestão Automática do Bing

A **API de Sugestão Automática do Bing** inclui um ponto de extremidade, que retorna uma lista de consultas sugeridas com base em um termo de pesquisa parcial.

Para obter as consultas sugeridas usando a API do Bing, envie uma solicitação `GET` para o ponto de extremidade a seguir. Use os cabeçalhos e parâmetros de URL para definir mais especificações.

**Ponto de extremidade:** retorna as sugestões de pesquisa como resultados JSON relevantes à entrada do usuário definidos por `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., confira a referência [API de Sugestão Automática do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference).

As APIs do **Bing** dão suporte a ações de pesquisa que retornam os resultados de acordo com seu tipo. Todos os pontos de extremidade de pesquisa retornam os resultados como objetos de resposta JSON.
Todos os pontos de extremidade dão suporte a consultas que retornam um idioma e/ou um local específico por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros compatíveis com cada ponto de extremidade, confira as páginas de referência de cada tipo.
Para obter exemplos de solicitações básicas usando a API de Sugestão Automática, confira [Inícios Rápidos da Sugestão Automática](/azure/cognitive-services/Bing-Autosuggest).

## <a name="bing-autosuggest-api-requests"></a>Solicitações da API de Sugestão Automática do Bing

> [!NOTE]
> * As solicitações à API de Sugestão Automática do Bing devem usar o protocolo HTTPS.

É recomendável que todas as solicitações sejam originadas de um servidor. A distribuição da chave como parte de um aplicativo cliente dá mais oportunidades para um terceiro mal-intencionado acessá-lo. Além disso, fazer chamadas em um servidor fornece um ponto único de upgrade para atualizações futuras.

A solicitação deve especificar o parâmetro de consulta [q](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) que contém o termo de pesquisa parcial do usuário. Embora seja opcional, a solicitação também deve especificar o parâmetro de consulta [mkt](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt), que identifica o mercado de onde você deseja obter os resultados. Para obter uma lista de parâmetros de consulta opcionais, consulte [parâmetros de consulta](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Todos os valores de parâmetro de consulta devem ser codificados em URL.

A solicitação precisa especificar o cabeçalho [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey). Embora isso seja opcional, você é incentivado a especificar também os seguintes cabeçalhos:

- [User-Agent](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [Localização de pesquisa X](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Os cabeçalhos de IP e local do cliente são importantes para retornar o conteúdo com reconhecimento de local.

Para obter uma lista de todos os cabeçalhos de solicitação e resposta, confira [Cabeçalhos](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

> [!NOTE]
> Quando você chama a API de Sugestão Automática do Bing por meio do JavaScript, os recursos de segurança interna do navegador podem impedi-lo de acessar os valores desses cabeçalhos.

Para resolver esse problema, faça a solicitação da API de Sugestão Automática do Bing por meio de um proxy CORS. A resposta de tal proxy tem um `Access-Control-Expose-Headers` cabeçalho que filtra os cabeçalhos de resposta e os torna disponíveis para o JavaScript.

É fácil instalar um proxy CORS para permitir que nosso [aplicativo de tutorial](../tutorials/autosuggest.md) acesse os cabeçalhos opcionais do cliente. Primeiro, caso ainda não tenha, [instale o Node.js](https://nodejs.org/en/download/). Em seguida, insira o comando a seguir em um prompt de comando.

```console
npm install -g cors-proxy-server
```

Em seguida, altere o ponto de extremidade da API de Sugestão Automática do Bing no arquivo HTML para:

```http
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
```

Por fim, inicie o proxy CORS com o seguinte comando:

```console
cors-proxy-server
```

Deixe a janela de comando aberta enquanto você usa o aplicativo de tutorial, já que se fechar a janela irá parar o proxy. Na seção Cabeçalhos HTTP expansíveis abaixo dos resultados da pesquisa, é possível ver o cabeçalho `X-MSEdge-ClientID` (entre outros) e verificar se é o mesmo para cada solicitação.

A solicitação deve incluir todos os parâmetros e cabeçalhos de consulta sugeridos. 

O exemplo a seguir mostra uma solicitação que retorna as cadeias de caracteres de consulta sugeridas para *navegar*.

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Se for a primeira vez que você chama qualquer uma das APIs do Bing, não inclua o cabeçalho da ID do cliente. Inclua apenas o cabeçalho da ID do cliente, se você já chamou uma API do Bing e o Bing retornou uma ID do cliente para a combinação de usuário e dispositivo.

O grupo de sugestão da web a seguir é uma resposta à solicitação acima. O grupo contém uma lista de sugestões de consulta de pesquisa, sendo que cada sugestão inclui um campo `displayText`, `query` e `url`.

O campo `displayText` contém a consulta sugerida que você usaria para preencher a lista suspensa da caixa de pesquisa. Exiba todas as sugestões incluídas na resposta e na ordem determinada.  

Se o usuário seleciona uma consulta na lista suspensa, você pode usá-la para chamar um dos [APIs de pesquisa do Bing](../../bing-web-search/bing-api-comparison.md?bc=%2fen-us%2fazure%2fbread%2ftoc.json&toc=%2fen-us%2fazure%2fcognitive-services%2fbing-autosuggest%2ftoc.json) e exibir os resultados por conta própria ou enviar o usuário para a página de resultados do Bing usando o campo `url` retornado.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>Próximas etapas

- [O que é a Sugestão Automática do Bing?](../get-suggested-search-terms.md)
- [Referência de API de Sugestão Automática do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
- [Obtendo sugestões da API de Sugestão Automática do Bing](get-suggestions.md)