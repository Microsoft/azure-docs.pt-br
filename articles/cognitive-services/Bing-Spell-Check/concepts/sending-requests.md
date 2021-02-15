---
title: Enviar solicitações para a API de Verificação Ortográfica do Bing
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre os modos de Verificação Ortográfica do Bing, as configurações e outras informações relacionadas à API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: 761cc3908b677b129e85be442b7a593a38a367f9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341556"
---
# <a name="sending-requests-to-the-bing-spell-check-api"></a>Enviar solicitações para a API de Verificação Ortográfica do Bing

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Para verificar se uma cadeia de caracteres de texto possui erros de gramática e ortografia, você poderia enviar uma solicitação GET para o ponto de extremidade a seguir:  

```
https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
```  
  
A solicitação deve usar o protocolo HTTPS.

É recomendável que todas as solicitações sejam originadas de um servidor. A distribuição da chave como parte de um aplicativo cliente fornece mais oportunidades para um terceiro mal-intencionado acessá-lo. Um servidor também fornece um ponto único de atualização para versões futuras da API.

A solicitação deve especificar o parâmetro de consulta [texto](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#text), que contém a cadeia de caracteres de texto para verificação. Embora seja opcional, a solicitação também deve especificar o parâmetro de consulta [mkt](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#mkt), que identifica o mercado de onde você deseja que venham os resultados. Para obter uma lista de parâmetros de consulta opcionais, como `mode`, confira [Parâmetros de consulta](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#query-parameters). Todos os valores de parâmetro de consulta devem ser codificados em URL.  
  
A solicitação precisa especificar o cabeçalho [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#subscriptionkey). Embora isso seja opcional, você é incentivado a especificar também os cabeçalhos a seguir. Estes cabeçalhos ajudam a API de Verificação Ortográfica do Bing a obter resultados mais precisos:  
  
-   [User-Agent](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientid)  
-   [X-Search-ClientIP](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientip)  
-   [Localização de pesquisa X](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#location)  

Para obter uma lista de todos os cabeçalhos de solicitação e resposta, confira [Cabeçalhos](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#headers).

Ao chamar a API de Verificação Ortográfica do Bing usando o JavaScript, os recursos de segurança internos do navegador podem impedir que você acesse os valores desses cabeçalhos.

Para resolver esse problema, é possível fazer a solicitação da API de Verificação Ortográfica do Bing por meio de um proxy CORS. A resposta de tal proxy tem um `Access-Control-Expose-Headers` cabeçalho que filtra os cabeçalhos de resposta e os torna disponíveis para o JavaScript.

É fácil instalar um proxy CORS para permitir que o [aplicativo de tutorial](../tutorials/spellcheck.md) acesse os cabeçalhos opcionais do cliente. Primeiro, caso ainda não tenha, [instale o Node.js](https://nodejs.org/en/download/). Em seguida, insira o comando a seguir em um prompt de comando.

```console
npm install -g cors-proxy-server
```

Em seguida, altere o ponto de extremidade da API Verificação Ortográfica do Bing no arquivo HTML para: \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/`

Por fim, inicie o proxy CORS com o seguinte comando:

```console
cors-proxy-server
```

Deixe a janela de comando aberta enquanto você usa o aplicativo de tutorial, já que se fechar a janela irá parar o proxy. Na seção Cabeçalhos HTTP expansível abaixo dos resultados da pesquisa, é possível ver o cabeçalho `X-MSEdge-ClientID` (entre outros) e verificar se é o mesmo para cada solicitação.

## <a name="example-api-request"></a>Solicitação de API de exemplo

O exemplo a seguir mostra uma solicitação que inclui todos os cabeçalhos e parâmetros de consulta sugeridos. Se for a primeira vez que você chama qualquer uma das APIs do Bing, não inclua o cabeçalho da ID do cliente. Só inclua a ID do cliente se você já tiver chamado uma API do Bing e o Bing retornou uma ID de cliente para a combinação de usuário e dispositivo. 
  
```http
GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

O exemplo a seguir mostra a resposta à solicitação anterior. O exemplo também mostra os cabeçalhos de resposta específicos do Bing.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  

## <a name="next-steps"></a>Próximas etapas

- [O que é API de Verificação Ortográfica do Bing?](../overview.md)
- [Referência de API de Verificação Ortográfica do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)