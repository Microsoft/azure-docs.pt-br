---
title: "Visão geral das APIs de Nó de Retransmissão do Azure | Microsoft Docs"
description: "Visão geral da API de Nó de Retransmissão"
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: b7d6e822-7c32-4cb5-a4b8-df7d009bdc85
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: f9b679f167952076db6df17711dc124931a0880c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Visão geral da API do Nó de Conexões Híbridas de Retransmissão

## <a name="overview"></a>Visão geral

O [ `hyco-ws` ](https://www.npmjs.com/package/hyco-ws) pacote de nó para as conexões de retransmissão híbridas do Azure baseia-se e estende o ['ws'](https://www.npmjs.com/package/ws) pacote NPM. Este pacote novamente exporta todas as exportações do pacote base e adiciona novos exportações que permitem a integração com o recurso de conexões híbridas do serviço de retransmissão do Azure. 

Os aplicativos existentes que `require('ws')` pode usar esse pacote com `require('hyco-ws')` em vez disso, que também permite cenários híbridos em que um aplicativo pode escutar conexões WebSocket localmente do "dentro do firewall" e por meio de conexões híbridas, tudo ao mesmo tempo.
  
## <a name="documentation"></a>Documentação

As APIs estão [documentadas no pacote principal 'ws'](https://github.com/websockets/ws/blob/master/doc/ws.md). Este artigo descreve como esse pacote difere essa linha de base. 

As principais diferenças entre o pacote básico e essa 'hyco-ws' é que ele adiciona uma nova classe de servidor exportada com `require('hyco-ws').RelayedServer`e alguns métodos auxiliares.

### <a name="package-helper-methods"></a>Métodos auxiliares de pacote

Há vários métodos de utilitário disponível na exportação do pacote que você pode fazer referência da seguinte maneira:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Os métodos auxiliares para uso com este pacote, mas também podem ser usados por um servidor de nó para habilitar clientes da web ou dispositivo criar ouvintes ou remetentes. O servidor usa esses métodos, passando-os URIs que incorporar tokens de curta duração. Esses URIs também pode ser usado com pilhas WebSocket comuns que não oferecem suporte a cabeçalhos HTTP de configuração para o handshake do WebSocket. A incorporação de tokens de autorização no URI é suportada principalmente para os cenários de uso da biblioteca externa. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Cria um URI do ouvinte da Conexão Híbrida de Retransmissão do Azure para determinado namespace e caminho. Esse URI, em seguida, pode ser usado com a versão de retransmissão da classe WebSocketServer.

- `namespaceName` (obrigatório) – o nome de domínio qualificado do namespace de retransmissão do Azure a ser usado.
- `path` (obrigatório) – o nome de uma Conexão Híbrida de Retransmissão do Azure no namespace.
- `token` (opcional) – um token de acesso de retransmissão emitido anteriormente que é incorporado no ouvinte da URI (consulte o exemplo a seguir).
- `id` (opcional) – um identificador de acompanhamento que permite o acompanhamento de diagnóstico de ponta a ponta de solicitações.

O valor `token` é opcional e só deve ser usado quando não for possível enviar cabeçalhos HTTP junto com o handshake do WebSocket, como é o caso com a pilha do WebSocket do W3C.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Cria um envio de Conexão do Azure retransmissão híbrida URI válido para o namespace específico e o caminho. Esse URI pode ser usado com qualquer cliente WebSocket.

- `namespaceName` (obrigatório) – o nome de domínio qualificado do namespace de retransmissão do Azure a ser usado.
- `path` (obrigatório) – o nome de uma Conexão Híbrida de Retransmissão do Azure no namespace.
- `token` (opcional) – um token de acesso de retransmissão emitido anteriormente que é inserido no URI de envio (consulte o exemplo a seguir).
- `id` (opcional) – um identificador de acompanhamento que permite o acompanhamento de diagnóstico de ponta a ponta de solicitações.

O valor `token` é opcional e só deve ser usado quando não for possível enviar cabeçalhos HTTP junto com o handshake do WebSocket, como é o caso com a pilha do WebSocket do W3C.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Cria um token de Assinatura de Acesso Compartilhado (SAS) da Retransmissão do Azure para o URI, a regra SAS e a chave da regra SAS de destino especificados válidos para o número de segundos determinado ou para uma hora a partir do instante atual caso o argumento de expiração seja omitido.

- `uri` (obrigatório) – o URI para o qual o token deve ser emitido. O URI é normalizado para usar o esquema HTTP e informações de cadeia de caracteres de consulta serão removidas.
- `ruleName` (obrigatório) – nome de regra SAS para uma entidade representada por determinado URI ou para o namespace representado pela parte do host do URI.
- `key` (obrigatório) – chave válida para a regra de SAS. 
- `expirationSeconds` (opcional) – o número de segundos até que o token gerado deve expirar. O padrão é 1 hora (3600) se não for especificado.

O token emitido confere os direitos associados a regra SAS especificada para o período especificado.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Esse método é funcionalmente equivalente ao método `createRelayToken` documentado anteriormente, mas retorna o token anexado corretamente para a URI de entrada.

### <a name="class-wsrelayedserver"></a>Classe ws.RelayedServer

O `hycows.RelayedServer` classe é uma alternativa para o `ws.Server` classe não escuta na rede local, mas delegados escuta para o serviço de retransmissão do Azure.

As duas classes são em geral compatíveis em termos de contrato, o que significa que um aplicativo existente usando a classe `ws.Server` pode ser facilmente alterado para usar a versão retransmitida. As principais diferenças são no construtor e nas opções disponíveis.

#### <a name="constructor"></a>Construtor  

```JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server: ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

O construtor `RelayedServer` dá suporte a um conjunto diferente de argumentos do que o `Server`, porque ele não é nem um ouvinte autônomo, nem pode ser inserido em uma estrutura de ouvinte HTTP existente. Também há menos opções disponíveis desde que o gerenciamento do WebSocket amplamente é delegado ao serviço de retransmissão.

Argumentos do Construtor:

- `server` (obrigatório) – o URI totalmente qualificado para um nome de Conexão Híbrida a escutar costuma ser construído com o método auxiliar WebSocket.createRelayListenUri().
- `token` (obrigatório) – esse argumento contém uma cadeia de caracteres de token emitida anteriormente ou uma função de retorno de chamada que pode ser chamada para obter uma cadeia de caracteres tal token. A opção de retorno de chamada é preferencial, pois permite renovação de tokens.

#### <a name="events"></a>Eventos

`RelayedServer`instâncias de emitem três eventos que permitem lidar com solicitações de entrada, estabelecer conexões e detectar condições de erro. Você deve assinar o evento `connect` para lidar com mensagens. 

##### <a name="headers"></a>headers

```JavaScript 
function(headers)
```

O evento `headers` é gerado antes de uma conexão de entrada ser aceita, permitindo a modificação dos cabeçalhos a enviar ao cliente. 

##### <a name="connection"></a>connection

```JavaScript
function(socket)
```

Emitido quando uma nova conexão WebSocket é aceita. O objeto é do tipo `ws.WebSocket`, igual ao do pacote básico.


##### <a name="error"></a>error

```JavaScript
function(error)
```

Se o servidor subjacente emite um erro, ele é encaminhado aqui.  

#### <a name="helpers"></a>Auxiliares

Para simplificar a inicialização de um servidor de retransmissão e a assinatura imediata conexões de entrada, o pacote expõe uma função auxiliar simples, que também é usada nos exemplos, da seguinte maneira:

##### <a name="createrelayedlistener"></a>createRelayedListener

```JavaScript
var WebSocket = require('hyco-ws');

var wss = WebSocket.createRelayedServer(
    {
        server: WebSocket.createRelayListenUri(ns, path),
        token: function() { return WebSocket.createRelayToken('http://' + ns, keyrule, key); }
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(JSON.parse(event.data));
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
});
``` 

##### <a name="createrelayedserver"></a>createRelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

Esse método chama o construtor para criar uma nova instância de RelayedServer e, em seguida, assina o retorno de chamada fornecido para o evento 'conexão'.
 
##### <a name="relayedconnect"></a>relayedConnect

O espelhamento simplesmente o `createRelayedServer` auxiliar na função, `relayedConnect` cria uma conexão de cliente e assina o evento 'open' no soquete resultante.

```JavaScript
var uri = WebSocket.createRelaySendUri(ns, path);
WebSocket.relayedConnect(
    uri,
    WebSocket.createRelayToken(uri, keyrule, key),
    function (socket) {
        ...
    }
);
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a Retransmissão do Azure, visite estes links:
* [O que é Retransmissão do Azure?](relay-what-is-it.md)
* [APIs de Retransmissão Disponíveis](relay-api-overview.md)
