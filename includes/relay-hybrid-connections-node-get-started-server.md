---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 531866ece1c4acacb926c9e9c6598158c1aa077f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172512"
---
### <a name="create-a-nodejs-application"></a>Criar um aplicativo do Node.js

Crie um novo arquivo JavaScript chamado `listener.js`.

### <a name="add-the-relay-npm-package"></a>Adicionar o pacote NPM de retransmissão

Execute `npm install hyco-ws` em um prompt de comando do Node na pasta do projeto.

### <a name="write-some-code-to-receive-messages"></a>Escrever código para receber mensagens

1. Adicione a seguinte constante ao início do arquivo `listener.js`.
   
    ```js
    const WebSocket = require('hyco-ws');
    ```
2. Adicione as seguintes constantes ao arquivo `listener.js` para obter os detalhes de conexão híbrida. Substitua os espaços reservados entre colchetes pelos valores obtidos quando você criou a conexão híbrida.
   
   1. `const ns` - o namespace de retransmissão. Use o nome totalmente qualificado do namespace, por exemplo, `{namespace}.servicebus.windows.net`.
   2. `const path` - o nome da conexão híbrida.
   3. `const keyrule` - o nome da chave SAS.
   4. `const key` - o valor da chave SAS.

3. Adicione o seguinte código ao arquivo `listener.js`:
   
    ```js
    var wss = WebSocket.createRelayedServer(
    {
        server : WebSocket.createRelayListenUri(ns, path),
        token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(event.data);
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```
    Veja como o arquivo listener.js deve se parecer:
   
    ```js
    const WebSocket = require('hyco-ws');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var wss = WebSocket.createRelayedServer(
        {
            server : WebSocket.createRelayListenUri(ns, path),
            token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
        }, 
        function (ws) {
            console.log('connection accepted');
            ws.onmessage = function (event) {
                console.log(event.data);
            };
            ws.on('close', function () {
                console.log('connection closed');
            });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```

