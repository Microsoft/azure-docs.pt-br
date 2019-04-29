---
title: API REST (V4) - Node. js – QnA Maker
titleSuffix: Azure Cognitive Services
description: Obtenha exemplos de código baseados e informações com base em REST do Node.js para ajudá-lo a começar a usar a API de Tradução de Texto da Microsoft em Serviços Cognitivos da Microsoft no Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: ba4af5d3b6612614dee54db7fdb1287ff7136321
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60913491"
---
# <a name="how-to-use-the-qna-maker-rest-api-with-nodejs"></a>Como usar a API REST de QnA Maker com Node.js 
<a name="HOLTop"></a>

Este artigo mostra como usar a [API de QnA Maker da Microsoft](../Overview/overview.md) com Node.js para fazer o seguinte.

- [Criar uma nova base de dados de conhecimento.](#Create)
- [Atualizar uma base de dados de conhecimento existente.](#Update)
- [Obter o status de uma solicitação para criar ou atualizar uma base de dados de conhecimento.](#Status)
- [Publicar em uma base de dados de conhecimento existente.](#Publish)
- [Substituir o conteúdo de uma base de dados de conhecimento existente.](#Replace)
- [Baixar o conteúdo de uma base de dados de conhecimento.](#GetQnA)
- [Obter respostas a uma pergunta usando uma base de dados de conhecimento.](#GetAnswers)
- [Obter informações sobre uma base de dados de conhecimento.](#GetKB)
- [Obter informações sobre todas as bases de dados de conhecimento pertencentes ao usuário especificado.](#GetKBsByUser)
- [Excluir uma base de dados de conhecimento.](#Delete)
- [Obter as chaves do ponto de extremidade atual.](#GetKeys)
- [Gerar novamente as chaves do ponto de extremidade atual.](#PutKeys)
- [Obter o conjunto atual de alterações de palavras que não consideram maiúsculas e minúsculas.](#GetAlterations)
- [Substituir o conjunto atual de alterações de palavras que não consideram maiúsculas e minúsculas.](#PutAlterations)

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

É necessário ter o [Node.js 6](https://nodejs.org/en/download/) para executar esse código.

É necessário ter uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com **API de QnA Maker da Microsoft**. Você precisará de uma chave de assinatura paga do seu [painel do Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

<a name="Create"></a>

## <a name="create-knowledge-base"></a>Criar base de dados de conhecimento

O código a seguir cria uma nova base de dados conhecimento, usando o método [Criar](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff).

1. Crie um novo projeto Node.js no IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/knowledgebases/create';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the POST request.
let post = function (path, content, callback) {
    let request_params = {
        method : 'POST',
        hostname : host,
        path : path,
        headers : {
            'Content-Type' : 'application/json',
            'Content-Length' : Buffer.byteLength(content),
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.write (content);
    req.end ();
}

// callback is the function to call when we have the entire response from the GET request.
let get = function (path, callback) {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases/create POST method.
let create_kb = function (path, req, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the POST request.
    post (path, req, function (response) {
// Extract the data we want from the POST response and pass it to the callback function.
        callback ({ operation : response.headers.location, response : response.body });
    });
}

// callback is the function to call when we have the response from the GET request to check the status.
let check_status = function (path, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the GET request.
    get (path, function (response) {
// Extract the data we want from the GET response and pass it to the callback function.
        callback ({ wait : response.headers['retry-after'], response : response.body });
    });
}

let req = {
  "name": "QnA Maker FAQ",
  "qnaList": [
    {
      "id": 0,
      "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
      "source": "Custom Editorial",
      "questions": [
        "How do I programmatically update my Knowledge Base?"
      ],
      "metadata": [
        {
          "name": "category",
          "value": "api"
        }
      ]
    }
  ],
  "urls": [
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "files": []
};

var path = service + method;
// Convert the request to a string.
let content = JSON.stringify(req);
create_kb (path, content, function (result) {
// Write out the response from the /knowledgebases/create method.
    console.log (pretty_print(result.response));
// Loop until the operation is complete.
    let loop = function () {
        path = service + result.operation;
// Check the status of the operation.
        check_status (path, function (status) {
// Write out the status.
            console.log (pretty_print(status.response));
// Convert the status into an object and get the value of the operationState field.
            var state = (JSON.parse(status.response)).operationState;
// If the operation isn't complete, wait and query again.
            if (state == 'Running' || state == 'NotStarted') {
                console.log ('Waiting ' + status.wait + ' seconds...');
                setTimeout(loop, status.wait * 1000);
            }
        });
    }
// Begin the loop.
    loop();
});
```

**Criar a resposta da base de dados de conhecimento**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Running",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:46Z",
  "resourceLocation": "/knowledgebases/b0288f33-27b9-4258-a304-8b9f63427dad",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
```

[Voltar ao início](#HOLTop)

<a name="Update"></a>

## <a name="update-knowledge-base"></a>Atualizar a base de dados de conhecimento

O código a seguir atualiza uma base de dados de conhecimento existente, usando o método [Atualizar](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600).

1. Crie um novo projeto Node.js no IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

// NOTE: Replace this with a valid knowledge base ID.
let kb = 'ENTER ID HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/knowledgebases/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the PATCH request.
let patch = function (path, content, callback) {
    let request_params = {
        method : 'PATCH',
        hostname : host,
        path : path,
        headers : {
            'Content-Type' : 'application/json',
            'Content-Length' : Buffer.byteLength(content),
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.write (content);
    req.end ();
}

// callback is the function to call when we have the entire response from the GET request.
let get = function (path, callback) {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases PATCH method.
let update_kb = function (path, req, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the PATCH request.
    patch (path, req, function (response) {
// Extract the data we want from the PATCH response and pass it to the callback function.
        callback ({ operation : response.headers.location, response : response.body });
    });
}

// callback is the function to call when we have the response from the GET request to check the status.
let check_status = function (path, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the GET request.
    get (path, function (response) {
// Extract the data we want from the GET response and pass it to the callback function.
        callback ({ wait : response.headers['retry-after'], response : response.body });
    });
}

let req = {
  'add': {
    'qnaList': [
      {
        'id': 1,
        'answer': 'You can change the default message if you use the QnAMakerDialog. See this for details: https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle',
        'source': 'Custom Editorial',
        'questions': [
          'How can I change the default message from QnA Maker?'
        ],
        'metadata': []
      }
    ],
    'urls': [
      'https://docs.microsoft.com/azure/cognitive-services/Emotion/FAQ'
    ]
  },
  'update' : {
    'name' : 'New KB Name'
  },
  'delete': {
    'ids': [
      0
    ]
  }
};

var path = service + method + kb;
// Convert the request to a string.
let content = JSON.stringify(req);
update_kb (path, content, function (result) {
    console.log (pretty_print(result.response));
// Loop until the operation is complete.
    let loop = function () {
        path = service + result.operation;
// Check the status of the operation.
        check_status (path, function (status) {
// Write out the status.
            console.log (pretty_print(status.response));
// Convert the status into an object and get the value of the operationState field.
            var state = (JSON.parse(status.response)).operationState;
// If the operation isn't complete, wait and query again.
            if (state == 'Running' || state == 'NotStarted') {
                console.log ('Waiting ' + status.wait + ' seconds...');
                setTimeout(loop, status.wait * 1000);
            }
        });
    }
// Begin the loop.
    loop();
});
```

**Atualizar resposta da base de dados de conhecimento**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:48Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:50Z",
  "resourceLocation": "/knowledgebases/140a46f3-b248-4f1b-9349-614bfd6e5563",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
Press any key to continue.
```

[Voltar ao início](#HOLTop)

<a name="Status"></a>

## <a name="get-request-status"></a>Obter status da solicitação

É possível chamar o método [Operação](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails) para verificar o status de uma solicitação para criar ou atualizar uma base de dados de conhecimento. Para ver como esse método é utilizado, confira o código de exemplo do método [Create](#Create) ou [Update](#Update).

[Voltar ao início](#HOLTop)

<a name="Publish"></a>

## <a name="publish-knowledge-base"></a>Publicar base de dados de conhecimento

O código a seguir publica uma base de dados de conhecimento existente, usando o método [Publicar](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe).

1. Crie um novo projeto Node.js no IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

// NOTE: Replace this with a valid knowledge base ID.
let kb = 'ENTER ID HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/knowledgebases/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the POST request.
let post = function (path, content, callback) {
    let request_params = {
        method : 'POST',
        hostname : host,
        path : path,
        headers : {
            'Content-Type' : 'application/json',
            'Content-Length' : Buffer.byteLength(content),
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.write (content);
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases POST method.
let publish_kb = function (path, req, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the POST request.
    post (path, req, function (response) {
// Extract the data we want from the POST response and pass it to the callback function.
        if (response.status == '204') {
            let result = {'result':'Success'};
            callback (JSON.stringify(result));
        }
        else {
            callback (response.body);
        }
    });
}

var path = service + method + kb;
publish_kb (path, '', function (result) {
    console.log (pretty_print(result));
});
```

**Publicar resposta da base de dados de conhecimento**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "result": "Success."
}
```

[Voltar ao início](#HOLTop)

<a name="Replace"></a>

## <a name="replace-knowledge-base"></a>Substituir base de dados de conhecimento

O código a seguir substitui o conteúdo da base de dados de conhecimento especificada, usando o método [Substituir](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_publish).

1. Crie um novo projeto Node.js no IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

// NOTE: Replace this with a valid knowledge base ID.
let kb = 'ENTER ID HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/knowledgebases/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the PUT request.
let put = function (path, content, callback) {
    let request_params = {
        method : 'PUT',
        hostname : host,
        path : path,
        headers : {
            'Content-Type' : 'application/json',
            'Content-Length' : Buffer.byteLength(content),
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.write (content);
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases PUT method.
let replace_kb = function (path, req, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the PUT request.
    put (path, req, function (response) {
// Extract the data we want from the PUT response and pass it to the callback function.
        if (response.status == '204') {
            let result = {'result':'Success'};
            callback (JSON.stringify(result));
        }
        else {
            callback (response.body);
        }
    });
}

let req = {
  'qnaList': [
    {
      'id': 0,
      'answer': 'You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600',
      'source': 'Custom Editorial',
      'questions': [
        'How do I programmatically update my Knowledge Base?'
      ],
      'metadata': [
        {
          'name': 'category',
          'value': 'api'
        }
      ]
    }
  ]
};

var path = service + method + kb;
// Convert the request to a string.
let content = JSON.stringify(req);
replace_kb (path, content, function (result) {
    console.log (pretty_print(result));
});
```

**Substituir resposta da base de dados de conhecimento**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
    "result": "Success."
}
```

[Voltar ao início](#HOLTop)

<a name="GetQnA"></a>

## <a name="download-the-contents-of-a-knowledge-base"></a>Baixar o conteúdo de uma base de dados de conhecimento

O código a seguir baixa o conteúdo da base de dados de conhecimento especificada, usando o método [Baixar base de dados de conhecimento](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_download).

1. Crie um novo projeto Node.js no IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

// NOTE: Replace this with a valid knowledge base ID.
let kb = 'ENTER ID HERE';

// NOTE: Replace this with "test" or "prod".
let env = "test";

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = `/knowledgebases/${kb}/${env}/qna/`;

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the GET request.
let get = function (path, callback) {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases GET method.
let get_qna = function (path, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the GET request.
    get (path, function (response) {
// Extract the data we want from the GET response and pass it to the callback function.
        callback ({ operation : response.headers.location, response : response.body });
    });
}

var path = service + method;
get_qna (path, function (result) {
    console.log (pretty_print(result.response));
});
```

**Baixar resposta da base de dados de conhecimento**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "qnaDocuments": [
    {
      "id": 1,
      "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
      "source": "Custom Editorial",
      "questions": [
        "How do I programmatically update my Knowledge Base?"
      ],
      "metadata": [
        {
          "name": "category",
          "value": "api"
        }
      ]
    },
    {
      "id": 2,
      "answer": "QnA Maker provides an FAQ data source that you can query from your bot or application. Although developers will find this useful, content owners will especially benefit from this tool. QnA Maker is a completely no-code way of managing the content that powers your bot or application.",
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "questions": [
        "Who is the target audience for the QnA Maker tool?"
      ],
      "metadata": []
    },
...
  ]
}
```

[Voltar ao início](#HOLTop)

<a name="GetAnswers"></a>

## <a name="get-answers-to-a-question-using-a-knowledge-base"></a>Obter respostas a uma pergunta usando uma base de dados de conhecimento

O código a seguir obtém respostas para uma pergunta usando a base de dados de conhecimento especificada, usando o método **Gerar respostas**.

1. Crie um novo projeto Node.js no IDE favorito.
1. Adicione o código fornecido abaixo.
1. Substitua o valor `host` pelo nome do site da sua assinatura do QnA Maker. Para obter mais informações, consulte [Criar um serviço do QnA Maker](../How-To/set-up-qnamaker-service-azure.md).
1. Substitua o valor `endpoint_key` por uma chave de ponto de extremidade válida para a sua assinatura. Observe que não será igual à sua chave de assinatura. É possível obter as chaves de ponto de extremidade usando o método [Obter chaves do ponto de extremidade](#GetKeys).
1. Substitua o valor `kb` pela ID da base de dados de conhecimento que você quer consultar. Observe que essa base de dados de conhecimento já deve ter sido publicada usando o método [Publicar](#Publish).
1. Execute o programa.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// NOTE: Replace this with a valid host name.
let host = "ENTER HOST HERE";

// NOTE: Replace this with a valid endpoint key.
// This is not your subscription key.
// To get your endpoint keys, call the GET /endpointkeys method.
let endpoint_key = "ENTER KEY HERE";

// NOTE: Replace this with a valid knowledge base ID.
// Make sure you have published the knowledge base with the
// POST /knowledgebases/{knowledge base ID} method.
let kb = "ENTER KB ID HERE";

let method = "/qnamaker/knowledgebases/" + kb + "/generateAnswer";

let question = {
    'question': 'Is the QnA Maker Service free?',
    'top': 3
};

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the POST request.
let post = function (path, content, callback) {
    let request_params = {
        method : 'POST',
        hostname : host,
        path : path,
        headers : {
            'Content-Type' : 'application/json',
            'Content-Length' : Buffer.byteLength(content),
            'Authorization' : 'EndpointKey ' + endpoint_key,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.write (content);
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases POST method.
let get_answers = function (path, req, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the POST request.
    post (path, req, function (response) {
        callback (response.body);
    });
}

// Convert the request to a string.
let content = JSON.stringify(question);
get_answers (method, content, function (result) {
// Write out the response from the /knowledgebases/create method.
    console.log (pretty_print(result));
});
```

**Resposta de obter respostas**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "answers": [
    {
      "questions": [
        "Can I use BitLocker with the Volume Shadow Copy Service?"
      ],
      "answer": "Yes. However, shadow copies made prior to enabling BitLocker will be automatically deleted when BitLocker is enabled on software-encrypted drives. If you are using a hardware encrypted drive, the shadow copies are retained.",
      "score": 17.3,
      "id": 62,
      "source": "https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-frequently-asked-questions",
      "metadata": []
    },
...
  ]
}
```

[Voltar ao início](#HOLTop)

<a name="GetKB"></a>

## <a name="get-information-about-a-knowledge-base"></a>Obter informações sobre uma base de dados de conhecimento

O código a seguir obtém informações sobre a base de dados de conhecimento especificada, usando o método [Obter detalhes da base de dados de conhecimento](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_getknowledgebasedetails).

1. Crie um novo projeto Node.js no IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

// NOTE: Replace this with a valid knowledge base ID.
let kb = 'ENTER ID HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/knowledgebases/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the GET request.
let get = function (path, callback) {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases GET method.
let get_kb = function (path, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the GET request.
    get (path, function (response) {
// Extract the data we want from the GET response and pass it to the callback function.
        callback ({ operation : response.headers.location, response : response.body });
    });
}

var path = service + method + kb;
get_kb (path, function (result) {
    console.log (pretty_print(result.response));
});
```

**Obter resposta dos detalhes da base de dados de conhecimento**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
  "hostName": "https://qna-docs.azurewebsites.net",
  "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
  "lastChangedTimestamp": "2018-04-12T22:58:01Z",
  "name": "QnA Maker FAQ",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "urls": [
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

[Voltar ao início](#HOLTop)

<a name="GetKBsByUser"></a>

## <a name="get-all-knowledge-bases-for-a-user"></a>Obter todas as bases de dados de conhecimento para um usuário

O código a seguir obtém informações sobre todas as bases de dados de conhecimento para um usuário especificado, usando o método [Obter bases de dados de conhecimento para usuário](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_getknowledgebasesforuser).

1. Crie um novo projeto Node.js no IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/knowledgebases/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the GET request.
let get = function (path, callback) {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases GET method.
let get_kbs = function (path, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the GET request.
    get (path, function (response) {
// Extract the data we want from the GET response and pass it to the callback function.
        callback ({ operation : response.headers.location, response : response.body });
    });
}

var path = service + method;
get_kbs (path, function (result) {
    console.log (pretty_print(result.response));
});
```

**Obter bases de dados de conhecimento para resposta do usuário**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "knowledgebases": [
    {
      "id": "081c32a7-bd05-4982-9d74-07ac736df0ac",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T11:51:58Z",
      "lastChangedTimestamp": "2018-04-12T11:51:58Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [],
      "sources": []
    },
    {
      "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
      "lastChangedTimestamp": "2018-04-12T22:58:01Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [
        "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
        "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
      ],
      "sources": [
        "Custom Editorial"
      ]
    },
...
  ]
}
Press any key to continue.
```

[Voltar ao início](#HOLTop)

<a name="Delete"></a>

## <a name="delete-a-knowledge-base"></a>Excluir uma base de dados de conhecimento

O código a seguir exclui a base de dados de conhecimento especificada, usando o método [Excluir base de dados de conhecimento](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_delete).

1. Crie um novo projeto Node.js no IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

// NOTE: Replace this with a valid knowledge base ID.
let kb = 'ENTER ID HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/knowledgebases/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the DELETE request.
let http_delete = function (path, content, callback) {
    let request_params = {
        method : 'DELETE',
        hostname : host,
        path : path,
        headers : {
            'Content-Type' : 'application/json',
            'Content-Length' : Buffer.byteLength(content),
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.write (content);
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases DELETE method.
let delete_kb = function (path, req, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the DELETE request.
    http_delete (path, req, function (response) {
// Extract the data we want from the DELETE response and pass it to the callback function.
        if (response.status == '204') {
            let result = {'result':'Success'};
            callback (JSON.stringify(result));
        }
        else {
            callback (response.body);
        }
    });
}

var path = service + method + kb;
delete_kb (path, '', function (result) {
    console.log (pretty_print(result));
});
```

**Resposta de excluir da base de dados de conhecimento**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "result": "Success."
}
```

[Voltar ao início](#HOLTop)

<a name="GetKeys"></a>

## <a name="get-endpoint-keys"></a>Obter chaves de ponto de extremidade

O código a seguir obtém as chaves do ponto de extremidade atual, usando o método [Obter chaves de ponto de extremidade](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/endpointkeys_getendpointkeys).

1. Crie um novo projeto Node.js no IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/endpointkeys/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the GET request.
let get = function (path, callback) {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases GET method.
let get_keys = function (path, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the GET request.
    get (path, function (response) {
// Extract the data we want from the GET response and pass it to the callback function.
        callback ({ operation : response.headers.location, response : response.body });
    });
}

var path = service + method;
get_keys (path, function (result) {
    console.log (pretty_print(result.response));
});
```

**Resposta de obter chaves de ponto de extremidade**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Voltar ao início](#HOLTop)

<a name="PutKeys"></a>

## <a name="refresh-endpoint-keys"></a>Atualizar chaves do ponto de extremidade

O código a seguir gera novamente as chaves do ponto de extremidade atual, usando o método [Atualizar chaves do ponto de extremidade](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/endpointkeys_refreshendpointkeys).

1. Crie um novo projeto Node.js no IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

// NOTE: Replace this with "PrimaryKey" or "SecondaryKey."
let key_type = "PrimaryKey";

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/endpointkeys/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the PATCH request.
let patch = function (path, content, callback) {
    let request_params = {
        method : 'PATCH',
        hostname : host,
        path : path,
        headers : {
            'Content-Type' : 'application/json',
            'Content-Length' : Buffer.byteLength(content),
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.write (content);
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases PATCH method.
let refresh_keys = function (path, req, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the PATCH request.
    patch (path, req, function (response) {
// Extract the data we want from the PATCH response and pass it to the callback function.
        if (response.status == '204') {
            let result = {'result':'Success'};
            callback (JSON.stringify(result));
        }
        else {
            callback (response.body);
        }
    });
}

let req = {
  'wordAlterations': [
    {
      'alterations': [
        'botframework',
        'bot frame work'
      ]
    }
  ]
};

var path = service + method + key_type;
// Convert the request to a string.
let content = JSON.stringify(req);
refresh_keys (path, content, function (result) {
    console.log (pretty_print(result));
});
```

**Resposta de atualizar chaves do ponto de extremidade**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Voltar ao início](#HOLTop)

<a name="GetAlterations"></a>

## <a name="get-word-alterations"></a>Obter alterações de palavras

O código a seguir obtém as alterações de palavras atuais, usando o método [Baixar alterações](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc).

1. Crie um novo projeto Node.js no IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/alterations/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the GET request.
let get = function (path, callback) {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases GET method.
let get_alterations = function (path, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the GET request.
    get (path, function (response) {
// Extract the data we want from the GET response and pass it to the callback function.
        callback ({ operation : response.headers.location, response : response.body });
    });
}

var path = service + method;
get_alterations (path, function (result) {
    console.log (pretty_print(result.response));
});
```

**Resposta de obter alterações de palavras**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "wordAlterations": [
    {
      "alterations": [
        "botframework",
        "bot frame work"
      ]
    }
  ]
}
```

[Voltar ao início](#HOLTop)

<a name="PutAlterations"></a>

## <a name="replace-word-alterations"></a>Substituir alterações de palavras

O código a seguir substitui as alterações de palavras atuais, usando o método [Substituir alterações](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd).

1. Crie um novo projeto Node.js no IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/alterations/';

let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// callback is the function to call when we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
// Call the callback function with the status code, headers, and body of the response.
        callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls the specified callback function when we have the entire response.
let get_response_handler = function (callback) {
// Return a function that takes an HTTP response, and is closed over the specified callback.
// This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler (callback, response);
    }
}

// callback is the function to call when we have the entire response from the PUT request.
let put = function (path, content, callback) {
    let request_params = {
        method : 'PUT',
        hostname : host,
        path : path,
        headers : {
            'Content-Type' : 'application/json',
            'Content-Length' : Buffer.byteLength(content),
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

// Pass the callback function to the response handler.
    let req = https.request (request_params, get_response_handler (callback));
    req.write (content);
    req.end ();
}

// callback is the function to call when we have the response from the /knowledgebases PUT method.
let put_alterations = function (path, req, callback) {
    console.log ('Calling ' + host + path + '.');
// Send the PUT request.
    put (path, req, function (response) {
// Extract the data we want from the PUT response and pass it to the callback function.
        if (response.status == '204') {
            let result = {'result':'Success'};
            callback (JSON.stringify(result));
        }
        else {
            callback (response.body);
        }
    });
}

let req = {
  'wordAlterations': [
    {
      'alterations': [
        'botframework',
        'bot frame work'
      ]
    }
  ]
};

var path = service + method;
// Convert the request to a string.
let content = JSON.stringify(req);
put_alterations (path, content, function (result) {
    console.log (pretty_print(result));
});
```

**Resposta de substituir alterações de palavras**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "result": "Success."
}
```

[Voltar ao início](#HOLTop)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)

## <a name="see-also"></a>Consulte também 

[Visão geral do QnA Maker](../Overview/overview.md)
