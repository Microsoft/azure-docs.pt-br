---
title: 'Início Rápido: Visualização de URL do Projeto, Node.js'
titlesuffix: Azure Cognitive Services
description: Comece a usar a Visualização de URL dos Serviços Cognitivos da Microsoft no Azure.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 92124a53c871be7a83d7b276e31f000d7c674358
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698006"
---
# <a name="quickstart-url-preview-with-nodejs"></a>Início Rápido: Visualização de URL com Node.js 

O exemplo de Node a seguir cria uma visualização de Url para o site SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Pré-requisitos

Obtenha uma chave de acesso para a avaliação gratuita de [Laboratórios dos Serviços Cognitivos](https://labs.cognitive.microsoft.com/en-us/project-answer-search)

## <a name="code-scenario"></a>Cenário do código 

O código a seguir obtém os dados da Visualização de URL.
Ele é implementado nas etapas a seguir:
1. Declare variáveis para especificar o ponto de extremidade por host e caminho.
2. Especifique a URL de consulta para visualizar e adicione o parâmetro de consulta.  
3. Crie uma função de manipulador para a resposta.
4. Defina a função Search que cria a solicitação e adicione o cabeçalho *Ocp-Apim-Subscription-Key*.
5. Execute a função Search. 

O código completo para esta demonstração é:

```
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-ACCESS-KEY'; 
let host = 'api.labs.cognitive.microsoft.com';
let path = '/urlpreview/v7.0/search';

let mkt = 'en-US';
let q = 'https://swiftkey.com/';

let params = '?q=' + encodeURI(q);

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
- [Exemplo de código em C#](csharp.md)
- [Início rápido em Java](java-quickstart.md)
- [Início rápido em JavaScript](javascript.md)
- [Início rápido em Python](python-quickstart.md)
