---
title: Autenticar nos serviços de comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre as várias maneiras pelas quais um aplicativo ou serviço pode se autenticar nos serviços de comunicação.
author: matthewrobertson
manager: jken
services: azure-communication-services
ms.author: marobert
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 4d6e02852dcd2d30a764417a4b5e0e012a1d2ab5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571089"
---
# <a name="authenticate-to-azure-communication-services"></a>Autenticar nos serviços de comunicação do Azure

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Este artigo fornece informações sobre como autenticar clientes para os serviços de comunicação do Azure usando *chaves de acesso* e *tokens de acesso do usuário*. Cada interação do cliente com os serviços de comunicação do Azure precisa ser autenticada.

A tabela a seguir descreve quais opções de autenticação têm suporte nas bibliotecas de cliente dos serviços de comunicação do Azure:

| Biblioteca do cliente | Chave de acesso    | Tokens de acesso do usuário |
| -------------- | ------------- | ------------------ |
| Administração | Com suporte     | Sem suporte      |
| SMS            | Com suporte     | Sem suporte      |
| Chat           | Sem suporte | Com suporte          |
| Chamando        | Sem suporte | Com suporte          |

Cada opção de autorização é descrita brevemente abaixo:

- Autenticação de **chave de acesso** para operações de administração e SMS. A autenticação de chave de acesso é adequada para aplicativos em execução em um ambiente de serviço confiável. Para autenticar com uma chave de acesso, um cliente gera um [código de autenticação de mensagem baseado em hash (HMAC)](https://en.wikipedia.org/wiki/HMAC) e o inclui dentro do `Authorization` cabeçalho de cada solicitação HTTP. Para obter mais informações, consulte [autenticar com uma chave de acesso](#authenticate-with-an-access-key).
- Autenticação de **token de acesso do usuário** para chat e chamada. Os tokens de acesso do usuário permitem que os aplicativos cliente sejam autenticados diretamente nos serviços de comunicação do Azure. Esses tokens são gerados em um serviço de provisionamento de token no servidor que você cria. Em seguida, eles são fornecidos aos dispositivos cliente que usam o token para inicializar o chat e chamar as bibliotecas de cliente. Para obter mais informações, consulte [autenticar com um token de acesso do usuário](#authenticate-with-a-user-access-token).

## <a name="authenticate-with-an-access-key"></a>Autenticar com uma chave de acesso

A autenticação de chave de acesso usa uma chave secreta compartilhada para gerar um HMAC para cada solicitação HTTP computada usando o algoritmo SHA256 e a envia no `Authorization` cabeçalho usando o `HMAC-SHA256` esquema.

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

As bibliotecas de cliente dos serviços de comunicação do Azure que usam a autenticação de chave de acesso devem ser inicializadas com a cadeia de conexão do seu recurso. Se você não estiver usando uma biblioteca de cliente, poderá gerar HMACs programaticamente usando a chave de acesso do recurso. Para saber mais sobre cadeias de conexão, visite o guia de [início rápido de provisionamento de recursos](../quickstarts/create-communication-resource.md).

### <a name="sign-an-http-request"></a>Assinar uma solicitação HTTP

Se você não estiver usando uma biblioteca de cliente para fazer solicitações HTTP para as APIs REST dos serviços de comunicação do Azure, será necessário criar HMACs programaticamente para cada solicitação HTTP. As etapas a seguir descrevem como construir o cabeçalho Authorization:

1. Especifique o carimbo de data/hora UTC (tempo Universal Coordenado) para a solicitação no `x-ms-date` cabeçalho ou no cabeçalho HTTP padrão `Date` . O serviço valida isso para proteger contra determinados ataques de segurança, incluindo ataques de repetição.
1. Hash do corpo da solicitação HTTP usando o algoritmo SHA256 e, em seguida, passá-lo, com a solicitação, por meio do `x-ms-content-sha256` cabeçalho.
1. Construa a cadeia de caracteres a ser assinada concatenando o verbo HTTP (por exemplo `GET` `PUT` , ou), o caminho de solicitação HTTP e os valores dos `Date` `Host` `x-ms-content-sha256` cabeçalhos HTTP e no seguinte formato:
    ```
    VERB + "\n"
    URLPathAndQuery + "\n"
    DateHeaderValue + ";" + HostHeaderValue + ";" + ContentHashHeaderValue
    ```
1. Gere uma assinatura HMAC-256 da cadeia de caracteres codificada em UTF-8 que você criou na etapa anterior. Em seguida, codifique os resultados como Base64. Observe que você também precisará decodificar a chave de acesso em base64. Use o seguinte formato (mostrado como pseudocódigo):
    ```
    Signature=Base64(HMAC-SHA256(UTF8(StringToSign), Base64.decode(<your_access_key>)))
    ```
1. Especifique o cabeçalho de autorização da seguinte maneira:
    ```
    Authorization="HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"  
    ```
    Em que `<hmac-sha256-signature>` é o HMAC calculado na etapa anterior.

## <a name="authenticate-with-a-user-access-token"></a>Autenticar com um token de acesso do usuário

Os tokens de acesso do usuário permitem que os aplicativos cliente sejam autenticados diretamente nos serviços de comunicação do Azure. Para fazer isso, você deve configurar um serviço confiável que autentica os usuários do aplicativo e emite tokens de acesso do usuário com a biblioteca do cliente de administração. Visite a documentação conceitual da [arquitetura do cliente e do servidor](./client-and-server-architecture.md) para saber mais sobre nossas considerações de arquitetura.

A `CommunicationUserCredential` classe contém a lógica para fornecer credenciais de token de acesso do usuário para as bibliotecas de cliente e gerenciar seu ciclo de vida.

### <a name="initialize-the-client-libraries"></a>Inicializar as bibliotecas de cliente

Para inicializar as bibliotecas de cliente dos serviços de comunicação do Azure que exigem autenticação de token de acesso do usuário, primeiro crie uma instância da `CommunicationUserCredential` classe e, em seguida, use-a para inicializar um cliente de API.

Os trechos de código a seguir mostram como inicializar a biblioteca de cliente de chat com um token de acesso do usuário:

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
// user access tokens should be created by a trusted service using the Administration client library
var token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
var userCredential = new CommunicationUserCredential(token);

// initialize the chat client library with the credential
var chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// user access tokens should be created by a trusted service using the Administration client library
const token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance with the AzureCommunicationUserCredential class
const userCredential = new AzureCommunicationUserCredential(token);

// initialize the chat client library with the credential
let chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
// user access tokens should be created by a trusted service using the Administration client library
let token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
let userCredential = try CommunicationUserCredential(token: token)

// initialize the chat client library with the credential
let chatClient = try CommunicationChatClient(credential: userCredential, endpoint: ENDPOINT_URL)
```

#### <a name="java"></a>[Java](#tab/java)

```java
// user access tokens should be created by a trusted service using the Administration client library
String token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
CommunicationUserCredential userCredential = new CommunicationUserCredential(token);

// Initialize the chat client
final ChatClientBuilder builder = new ChatClientBuilder();
builder.endpoint(ENDPOINT_URL)
    .credential(userCredential)
    .httpClient(HTTP_CLIENT);
ChatClient chatClient = builder.buildClient();
```

---

### <a name="refreshing-user-access-tokens"></a>Atualizando tokens de acesso do usuário

Os tokens de acesso do usuário são credenciais de curta duração que precisam ser reemitidas para impedir que os usuários tenham interrupções de serviço. O `CommunicationUserCredential` construtor aceita uma função de retorno de chamada de atualização que permite que você atualize os tokens de acesso do usuário antes que eles expirem. Você deve usar esse retorno de chamada para buscar um novo token de acesso de usuário do seu serviço confiável.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var userCredential = new CommunicationUserCredential(
    initialToken: token,
    refreshProactively: true,
    tokenRefresher: cancellationToken => fetchNewTokenForCurrentUser(cancellationToken)
);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const userCredential = new AzureCommunicationUserCredential({
  tokenRefresher: async () => fetchNewTokenForCurrentUser(),
  refreshProactively: true,
  initialToken: token
});
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
 let userCredential = try CommunicationUserCredential(initialToken: token, refreshProactively: true) { |completionHandler|
   let updatedToken = fetchTokenForCurrentUser()
   completionHandler(updatedToken, nil)
 }
```

#### <a name="java"></a>[Java](#tab/java)

```java
TokenRefresher tokenRefresher = new TokenRefresher() {
    @Override
    Future<String> getFetchTokenFuture() {
        return fetchNewTokenForCurrentUser();
    }
}

CommunicationUserCredential credential = new CommunicationUserCredential(tokenRefresher, token, true);
```
---

A `refreshProactively` opção permite que você decida como gerenciará o ciclo de vida do token. Por padrão, quando um token está obsoleto, o retorno de chamada bloqueará as solicitações da API e tentará atualizá-la. Quando `refreshProactively` é definido como `true` o retorno de chamada é agendado e executado de forma assíncrona antes que o token expire.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar tokens de acesso do usuário](../quickstarts/access-tokens.md)

Para obter mais informações, consulte os seguintes artigos:
- [Saber mais sobre a arquitetura cliente e servidor](../concepts/client-and-server-architecture.md)
