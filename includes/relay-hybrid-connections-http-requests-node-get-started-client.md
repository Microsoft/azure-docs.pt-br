---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 3c18efa7eb520b765c9bb3c2aff00104f971f5a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553924"
---
### <a name="create-a-nodejs-application"></a>Criar um aplicativo do Node.js

Se você tiver desabilitado a opção "Exige Autorização do Cliente" ao criar a Retransmissão, poderá enviar solicitações para a URL de Conexões Híbridas com qualquer navegador. Para acessar os pontos de extremidade protegidos, você precisa criar e passar um token no cabeçalho `ServiceBusAuthorization`, que é mostrado aqui.

Para começar, crie um novo arquivo JavaScript chamado `sender.js`.

### <a name="add-the-relay-npm-package"></a>Adicionar o pacote NPM de retransmissão

Execute `npm install hyco-https` em um prompt de comando do Node na pasta do projeto. Este pacote também importa o pacote `https` normal. Para o lado do cliente, a principal diferença é que o pacote fornece funções para construir URIs de Retransmissão e tokens.

### <a name="write-some-code-to-send-messages"></a>Escrever código para enviar mensagens

1. Adicione a `constants` a seguir à parte superior do arquivo `sender.js`.
   
    ```js
    const https = require('hyco-https');
    ```

2. Adicione as seguintes constantes ao arquivo `sender.js` para obter os detalhes de conexão híbrida. Substitua os espaços reservados entre colchetes pelos valores obtidos quando você criou a conexão híbrida.
   
   1. `const ns` - o namespace de retransmissão. Use o nome totalmente qualificado do namespace, por exemplo, `{namespace}.servicebus.windows.net`.
   2. `const path` - o nome da conexão híbrida.
   3. `const keyrule` - o nome da chave SAS.
   4. `const key` - o valor da chave SAS.

3. Adicione o seguinte código ao arquivo `sender.js`. Você perceberá que o código é muito diferente do uso normal do cliente Node.js HTTPS; ele apenas adiciona o cabeçalho de autorização.
   
    ```js
   https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```
    Veja como o arquivo sender.js deve se parecer:
   
    ```js
    const https = require('hyco-https');
       
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```

