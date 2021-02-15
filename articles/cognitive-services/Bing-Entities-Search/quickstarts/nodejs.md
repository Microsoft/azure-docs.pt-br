---
title: 'Início Rápido: Enviar uma solicitação de pesquisa para a API REST usando o Node.js – Pesquisa de Entidade do Bing'
titleSuffix: Azure Cognitive Services
description: Use este início rápido para enviar uma solicitação para a API REST da Pesquisa de Entidade do Bing usando Node.js e receba uma resposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: b9311329ea4115d49f36dd7d39782bbd748a356b
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106097"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>Início Rápido: Enviar uma solicitação de pesquisa para a API REST da Pesquisa de Entidade do Bing usando o Node.js

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Use este início rápido para fazer sua primeira chamada à API de Pesquisa de Entidade do Bing e exibir a resposta JSON. Este aplicativo JavaScript simples envia uma consulta de pesquisa de notícias para a API e exibe a resposta. O código-fonte deste exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js).

Embora esse aplicativo seja escrito em JavaScript, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).

* A [Biblioteca de Solicitações JavaScript](https://github.com/request/request).

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. Crie um arquivo JavaScript no seu IDE ou editor favorito e defina o rigor e os requisitos de HTTPS.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. Crie variáveis para o ponto de extremidade de API, sua chave de assinatura e a consulta de pesquisa. É possível usar o ponto de extremidade global no código a seguir ou o ponto de extremidade do [subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal do Azure para seu recurso.

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. Acrescentar os parâmetros de mercado e de consulta a uma cadeia de caracteres chamada `query`. Codifique sua consulta como URL com `encodeURI()`.
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>Lidar e analisar a resposta

1. Defina uma função chamada `response_handler()` que usa uma chamada HTTP, `response`, como um parâmetro. 

2. Dentro dessa função, defina uma variável para conter o corpo da resposta JSON.  
    ```javascript
    let response_handler = function (response) {
        let body = '';
    };
    ```

3. Armazene o corpo da resposta quando o sinalizador `data` for chamado.
    ```javascript
    response.on('data', function (d) {
        body += d;
    });
    ```

4. Quando um sinalizador `end` é sinalizado, analise o JSON e o imprima.

    ```javascript
    response.on ('end', function () {
    let json = JSON.stringify(JSON.parse(body), null, '  ');
    console.log (json);
    });
    ```

## <a name="send-a-request"></a>Enviar uma solicitação

1. Crie uma função chamada `Search()` para enviar uma solicitação de pesquisa. Nela, siga estas etapas:

2. Nessa função, crie um objeto JSON contendo os parâmetros de solicitação. Use `Get` para o método e adicione suas informações de host e caminho. Adicione a chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`. 

3. Use `https.request()` para enviar a solicitação com o manipulador de resposta criado anteriormente e os parâmetros de pesquisa.
    
   ```javascript
   let Search = function () {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + query,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    
    let req = https.request (request_params, response_handler);
    req.end ();
   }
      ```

2. Chame a função `Search()`.

## <a name="example-json-response"></a>Resposta JSON de exemplo

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de Pesquisa de Entidade do Bing?](../overview.md )
* [Referência da API de Pesquisa de Entidade do Bing](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
