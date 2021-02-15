---
title: Início Rápido – enviar uma consulta à API usando o Node.js – Pesquisa de Negócios Locais do Bing
titleSuffix: Azure Cognitive Services
description: Use este início rápido para começar a enviar solicitações para a API de Pesquisa do Bing Local Business, que é um Serviço Cognitivo do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 03e1b159dbdc26d53af290c4370581788562ff3b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499621"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Início Rápido: envie uma consulta para a API de Pesquisa do Bing Local Business usando o Node.js

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisaram ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Use este início rápido para aprender a enviar solicitações para a API de Pesquisa de Negócios Locais do Bing, que é um Serviço Cognitivo do Azure. Embora esse aplicativo simples seja escrito em Node.js, a API é um serviço Web RESTful compatível com qualquer linguagem de programação capaz de fazer solicitações HTTP e analisar JSON.

Este aplicativo de exemplo obtém dados de resposta local da API para uma consulta de pesquisa.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão mais recente do [Node.js](https://nodejs.org/en/download/).
* A [Biblioteca de Solicitações JavaScript](https://github.com/request/request).
* Depois de obter a assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Criar um recurso de Pesquisa do Bing"  target="_blank">crie um recurso de Pesquisa do Bing <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.


## <a name="code-scenario"></a>Cenário do código

O seguinte código define e envia a solicitação, que é implementada nas seguintes etapas:

1. Declare variáveis para especificar o ponto de extremidade por host e caminho.
2. Especifique a consulta e adicione o parâmetro de consulta.
3. Crie uma função de manipulador para a resposta.
4. Defina a função Search que cria a solicitação e adiciona o cabeçalho `Ocp-Apim-Subscription-Key`.
5. Execute a função Search.


```javascript
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>Próximas etapas

* [Início rápido do C# de Pesquisa de negócios local](local-quickstart.md)
* [Início Rápido da Pesquisa Java em empresas locais](local-search-java-quickstart.md)
* [Início Rápido do Python em Pesquisa de empresa local](local-search-python-quickstart.md)