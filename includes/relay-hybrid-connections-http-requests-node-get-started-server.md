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
ms.openlocfilehash: fdd4e26839661ab9765b7d496e7f60c3686ba637
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553922"
---
### <a name="create-a-nodejs-application"></a>Criar um aplicativo do Node.js

Crie um novo arquivo JavaScript chamado `listener.js`.

### <a name="add-the-relay-npm-package"></a>Adicionar o pacote NPM de retransmissão

Execute `npm install hyco-https` em um prompt de comando do Node na pasta do projeto.

### <a name="write-some-code-to-handle-requests"></a>Escrever um código para manipular solicitações

1. Adicione a seguinte constante ao início do arquivo `listener.js`.

    ```js
    const https = require('hyco-https');
    ```
2. Adicione as seguintes constantes ao arquivo `listener.js` para obter os detalhes de conexão híbrida. Substitua os espaços reservados entre colchetes pelos valores obtidos quando você criou a conexão híbrida.

   1. `const ns` - o namespace de retransmissão. Use o nome totalmente qualificado do namespace, por exemplo, `{namespace}.servicebus.windows.net`.
   2. `const path` - o nome da conexão híbrida.
   3. `const keyrule` - o nome da chave SAS.
   4. `const key` - o valor da chave SAS.

3. Adicione o seguinte código ao arquivo `listener.js`. :

    Você perceberá que o código não é muito diferente de qualquer exemplo de servidor HTTP simples que você encontra em tutoriais de iniciantes do Node.js, com a exceção do uso de `createRelayedServer`, em vez da função `createServer` típica.

    ```js
    var uri = https.createRelayListenUri(ns, path);
    var server = https.createRelayedServer(
        {
            server : uri,
            token : () => https.createRelayToken(uri, keyrule, key)
        },
        (req, res) => {
            console.log('request accepted: ' + req.method + ' on ' + req.url);
            res.setHeader('Content-Type', 'text/html');
            res.end('<html><head><title>Hey!</title></head><body>Relayed Node.js Server!</body></html>');
        });

    server.listen( (err) => {
            if (err) {
              return console.log('something bad happened', err)
            }
            console.log(`server is listening on ${port}`)
          });

    server.on('error', (err) => {
        console.log('error: ' + err);
    });
    ```
    Veja como o arquivo listener.js deve se parecer:
   
    ```js
    const https = require('hyco-https');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var uri = https.createRelayListenUri(ns, path);
    var server = https.createRelayedServer(
        {
            server : uri,
            token : () => https.createRelayToken(uri, keyrule, key)
        },
        (req, res) => {
            console.log('request accepted: ' + req.method + ' on ' + req.url);
            res.setHeader('Content-Type', 'text/html');
            res.end('<html><head><title>Hey!</title></head><body>Relayed Node.js Server!</body></html>');
        });

    server.listen( (err) => {
            if (err) {
              return console.log('something bad happened', err)
            }
            console.log(`server is listening on ${port}`)
          });

    server.on('error', (err) => {
        console.log('error: ' + err);
    });
    ```

