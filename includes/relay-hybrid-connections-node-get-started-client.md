---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 9d4f7faa18ee7fae158afb42b8c42287e61dd103
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172509"
---
### <a name="create-a-nodejs-application"></a>Criar um aplicativo do Node.js

Crie um novo arquivo JavaScript chamado `sender.js`.

### <a name="add-the-relay-npm-package"></a>Adicionar o pacote NPM de retransmissão

Execute `npm install hyco-ws` em um prompt de comando do Node na pasta do projeto.

### <a name="write-some-code-to-send-messages"></a>Escrever código para enviar mensagens

1. Adicione a `constants` a seguir à parte superior do arquivo `sender.js`.
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
    ```
2. Adicione as seguintes constantes ao arquivo `sender.js` para obter os detalhes de conexão híbrida. Substitua os espaços reservados entre colchetes pelos valores obtidos quando você criou a conexão híbrida.
   
   1. `const ns` - o namespace de retransmissão. Use o nome totalmente qualificado do namespace, por exemplo, `{namespace}.servicebus.windows.net`.
   2. `const path` - o nome da conexão híbrida.
   3. `const keyrule` - o nome da chave SAS.
   4. `const key` - o valor da chave SAS.

3. Adicione o seguinte código ao arquivo `sender.js`:
   
    ```js
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```
    Veja como o arquivo sender.js deve se parecer:
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```

