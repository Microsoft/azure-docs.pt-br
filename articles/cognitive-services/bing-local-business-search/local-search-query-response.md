---
title: Enviando e usando respostas e consultas de API – pesquisa de negócios local do Bing
titleSuffix: Azure Cognitive Services
description: Use este artigo para aprender como enviar e usar consultas de pesquisa com a API da Pesquisa de empresa local do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rosh
ms.openlocfilehash: 70a33774ac82312660d887fb86f7e2a482c30a0c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96487160"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Envio e uso de consultas e respostas da API da Pesquisa de empresa local do Bing

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Você pode obter resultados locais da API de Pesquisa de Empresa Local do Bing enviando uma consulta de pesquisa para seu terminal e incluindo o `Ocp-Apim-Subscription-Key`cabeçalho, que é obrigatório. Juntamente com os cabeçalhos [disponíveis](local-search-reference.md#headers) e [parâmetros](local-search-reference.md#query-parameters), as pesquisas podem ser personalizadas especificando [limites geográficos](specify-geographic-search.md) para a área a ser pesquisada e as [categorias](local-search-query-response.md) de locais retornados.

## <a name="creating-a-request"></a>Criando uma solicitação

Para enviar uma solicitação à API de Pesquisa de Empresa Local do Bing, anexe um termo de pesquisa ao parâmetro `q=`antes de adicioná-lo ao ponto de extremidade da API e incluir o cabeçalho`Ocp-Apim-Subscription-Key`. Por exemplo:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

A sintaxe completa da URL da solicitação é mostrada abaixo. Consulte as iniciações rápidas da API da [Pesquisa de empresa local do Bing](quickstarts/local-quickstart.md) e o conteúdo de referência para [cabeçalhos](local-search-reference.md#headers) e [parâmetros](local-search-reference.md#query-parameters) para obter mais informações sobre o envio de solicitações. 

Para obter informações sobre categorias de pesquisa locais, consulte [Categorias de pesquisa para a API da Pesquisa de empresa local do Bing](local-categories.md).

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Usar as respostas

As respostas JSON da API da Pesquisa de empresa local do Bing contêm um objeto `SearchResponse`. A API retornará resultados de pesquisa relevantes no campo `places`. Se nenhum resultado for encontrado, o campo `places` não será incluído na resposta.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

```
{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": "restaurant in Bellevue"
   },
   "places": {
      "totalEstimatedMatches": 10,
. . . 
```

### <a name="search-result-attributes"></a>Atributos de resultado de pesquisa

Os resultados do JSON retornados pela API incluem os seguintes atributos:

* _type
* address
* entityPresentationInfo
* área geográfica
* id
* name
* routeablePoint
* telefone
* url

Para obter informações gerais sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência da [API de Pesquisa Local do Bing v7](local-search-reference.md).

> [!NOTE]
> Você, ou um terceiro em seu nome, não pode usar, reter, armazenar, armazenar em cache, compartilhar ou distribuir nenhum dado da API de pesquisa local com o objetivo de testar, desenvolver, treinar, distribuir ou disponibilizar qualquer serviço que não seja da Microsoft. ou recurso. 


## <a name="example-json-response"></a>Resposta JSON de exemplo

A resposta JSON a seguir inclui os resultados de pesquisa especificados pela consulta `?q=restaurant+in+Bellevue`.

```json
Vary: Accept-Encoding
BingAPIs-TraceId: 5376FFEB65294E24BB9F91AD70545826
BingAPIs-SessionId: 06ED7CEC80F746AA892EDAAC97CB0CB4
X-MSEdge-ClientID: 112C391E72C0624204153594738C63DE
X-MSAPI-UserState: aeab
BingAPIs-Market: en-US
X-Search-ResponseInfo: InternalResponseTime=659,MSDatacenter=CO4
X-MSEdge-Ref: Ref A: 5376FFEB65294E24BB9F91AD70545826 Ref B: BY3EDGE0306 Ref C: 2018-10-16T16:26:15Z
apim-request-id: fe54f585-7c54-4bf5-8b92-b9bede2b710a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Cache-Control: max-age=0, private
Date: Tue, 16 Oct 2018 16:26:15 GMT
P3P: CP="NON UNI COM NAV STA LOC CURa DEVa PSAa PSDa OUR IND"
Content-Length: 978
Content-Type: application/json; charset=utf-8
Expires: Tue, 16 Oct 2018 16:25:15 GMT

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "restaurant Bellevue"
  },
  "places": {
    "totalEstimatedMatches": 50,
    "value": [{
      "_type": "LocalBusiness",
      "id": "https:\/\/cognitivegblppe.azure-api.net\/api\/v7\/#Places.0",
      "name": "Facing East Taiwanese Restaurant",
      "url": "http:\/\/litadesign.wix.com\/facingeastrestaurant",
      "entityPresentationInfo": {
        "entityScenario": "ListItem",
        "entityTypeHints": ["Place", "LocalBusiness", "Restaurant"]
      },
      "geo": {
        "latitude": 47.6199188232422,
        "longitude": -122.202796936035
      },
      "routablePoint": {
        "latitude": 47.6199188232422,
        "longitude": -122.201713562012
      },
      "address": {
        "streetAddress": "1075 Bellevue Way NE Ste B2",
        "addressLocality": "Bellevue",
        "addressRegion": "WA",
        "postalCode": "98004",
        "addressCountry": "US",
        "neighborhood": "Bellevue",
        "text": "1075 Bellevue Way NE Ste B2, Bellevue, WA 98004"
      },
      "telephone": "(425) 688-2986"
    }],
    "searchAction": {
      "location": [{
        "name": "Bellevue, Washington"
      }],
      "query": "restaurant"
    }
  }
}
 
```

## <a name="throttling-requests"></a>Solicitações de limitação

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="next-steps"></a>Próximas etapas
- [ Início Rápido da Pesquisa de empresa local ](quickstarts/local-quickstart.md)
- [Início Rápido da Pesquisa Java em empresas locais](quickstarts/local-search-java-quickstart.md)
- [Início rápido de negócios o nó de pesquisa local](quickstarts/local-search-node-quickstart.md)
- [Início Rápido do Python em Pesquisa de empresa local](quickstarts/local-search-python-quickstart.md)