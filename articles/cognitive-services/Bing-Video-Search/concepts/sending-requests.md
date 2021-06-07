---
title: Enviar solicitações de pesquisa para o API de Pesquisa de Vídeo do Bing
titleSuffix: Azure Cognitive Services
description: Este artigo descreve os parâmetros e atributos de solicitações enviadas à API de Pesquisa de Vídeo do Bing, bem como o objeto de resposta JSON retornado.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: aahi
ms.openlocfilehash: 78a3c3c2936bfcaf58ea9e46bd3d6a610cdfe436
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96341488"
---
# <a name="sending-search-requests-to-the-bing-video-search-api"></a>Enviar solicitações de pesquisa para a API de Pesquisa de Vídeo do Bing

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Este artigo descreve os parâmetros e atributos de solicitações enviadas à API de Pesquisa de Vídeo do Bing, bem como o objeto de resposta JSON retornado. 

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="suggest-search-terms-with-the-bing-autosuggest-api"></a>Sugerir termos de pesquisa com a API de Sugestão Automática do Bing

Se você fornecer uma caixa de pesquisa em que o usuário insere seu termo de pesquisa, use a [API de Sugestão Automática do Bing](../../bing-autosuggest/get-suggested-search-terms.md) para melhorar a experiência. A API retorna cadeias de caracteres de consulta sugeridas com base em termos de pesquisa parciais como os tipos de usuário.

Depois que o usuário insere seu termo de pesquisa, a URL o codifica antes de definir o parâmetro de consulta [q](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query). Por exemplo, se o usuário inserir *sailing dinghies* (bote à vela), defina `q` como `sailing+dinghies` ou `sailing%20dinghies`.

## <a name="sending-a-request"></a>Enviando uma solicitação

Para obter resultados da pesquisa de Vídeo, envie uma solicitação GET para o seguinte ponto de extremidade:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```
   
A solicitação deve usar o protocolo HTTPS.

É recomendável que todas as solicitações sejam originadas de um servidor. A distribuição da chave como parte de um aplicativo cliente fornece mais oportunidades para um terceiro mal-intencionado acessá-lo. Fazer chamadas de um servidor fornece um ponto único de upgrade para versões futuras da API.

  
A solicitação precisa especificar o parâmetro de consulta [q](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query), que contém o termo de pesquisa do usuário. Embora seja opcional, a solicitação também deve especificar o parâmetro de consulta [mkt](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#mkt), que identifica o mercado de onde você deseja obter os resultados. Para obter uma lista de parâmetros de consulta opcionais, como `pricing`, confira [Parâmetros de consulta](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query-parameters). Todos os valores de parâmetro de consulta devem ser codificados em URL.  
  
A solicitação precisa especificar o cabeçalho [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#subscriptionkey). Embora isso seja opcional, você é incentivado a especificar também os seguintes cabeçalhos:  
  
-   [Agente do usuário](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#clientid)  
-   [X-Search-ClientIP](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#clientip)  
-   [Localização de pesquisa X](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#location)  

Os cabeçalhos de IP e local do cliente são importantes para retornar o conteúdo com reconhecimento de local.  

Para obter uma lista de todos os cabeçalhos de solicitação e resposta, confira [Cabeçalhos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#headers).

## <a name="example-search-request"></a>Exemplo de solicitação de pesquisa

O exemplo a seguir mostra uma solicitação de pesquisa que inclui todos os cabeçalhos e parâmetros de consulta sugeridos. Se for a primeira vez que você chama qualquer uma das APIs do Bing, não inclua o cabeçalho da ID do cliente. Só inclua a ID do cliente se você já tiver chamado uma API do Bing e o Bing retornou uma ID de cliente para a combinação de usuário e dispositivo. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

## <a name="example-json-response"></a>Resposta JSON de exemplo

O exemplo a seguir mostra a resposta à solicitação anterior. O exemplo também mostra os cabeçalhos de resposta específicos do Bing.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D5694...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYWCvh...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjHZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56944...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjBZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y6...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E11E3CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        . . .
    ],
    "nextOffset" : 0,
    "pivotSuggestions" : [
        {
            "pivot" : "sailing",
            "suggestions" : []
        },
        {
            "pivot" : "dinghies",
            "suggestions" : [
                {
                    "text" : "Sailing Cruising",
                    "displayText" : "Cruising",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF754...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Sailing..."
                    }
                },
                . . .
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Próximas etapas

Experimente a API. Acesse o [Console de Teste da API de Pesquisa de Vídeo](https://dev.cognitive.microsoft.com/docs/services/56b43f3ccf5ff8098cef3809/operations/58113fe5e31dac0a1ce6b0a8). 

Para obter detalhes sobre como consumir os objetos de resposta, consulte [Pesquisando vídeos na Web](../overview.md).

Para obter detalhes sobre como obter insights sobre um vídeo, como pesquisas relacionadas, confira [Insights de vídeo](../video-insights.md).  
  
Para obter detalhes sobre vídeos que são populares nas mídias sociais, confira [Vídeos populares](../trending-videos.md).