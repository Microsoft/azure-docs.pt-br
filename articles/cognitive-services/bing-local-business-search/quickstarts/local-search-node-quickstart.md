---
title: Início Rápido – Enviar uma consulta para a API da Pesquisa de Negócios Locais do Bing usando o Node.js
titleSuffix: Azure Cognitive Services
description: Comece a usar a API de pesquisa do Bing Local Business no Node.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 0c91a66539bc276fdf2e6e4056f3b67f770398b8
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423342"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Início Rápido: envie uma consulta para a API de Pesquisa do Bing Local Business usando o Node.js

Use este início rápido para começar a enviar solicitações para a API de Pesquisa do Bing Local Business, que é um Serviço Cognitivo do Azure. Embora esse aplicativo simples seja escrito em Node.js, a API é um serviço da Web RESTful compatível com qualquer linguagem de programação capaz de fazer solicitações HTTP e analisar JSON.

Este aplicativo de exemplo obtém dados de resposta local da API para a consulta de pesquisa `hotel in Bellevue`.

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).

* A [biblioteca de solicitações JavaScript](https://github.com/request/request)

Você deve ter uma [Conta da API de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com as APIs do Bing. A [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) é suficiente para esse início rápido. Use a chave de acesso fornecida pela avaliação gratuita.  Veja também [Cognitive Services Pricing - API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="code-scenario"></a>Cenário do código

O código a seguir obtém define e envia a solicitação. Ele é implementado nas etapas a seguir:

1. Declare variáveis para especificar o ponto de extremidade por host e caminho.
2. Especifique a consulta e adicione o parâmetro de consulta.
3. Crie uma função de manipulador para a resposta.
4. Defina a função Search que cria a solicitação e adiciona o cabeçalho Ocp-Apim-Subscription-Key.
5. Execute a função Search.

O código completo para esta demonstração é:

```
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

* [ Início Rápido da Pesquisa de empresa local ](local-quickstart.md)
* [Início Rápido da Pesquisa Java em empresas locais](local-search-java-quickstart.md)
* [Início Rápido do Python em Pesquisa de empresa local](local-search-python-quickstart.md)
