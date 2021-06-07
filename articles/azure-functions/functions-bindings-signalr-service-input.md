---
title: Azure Functions Associação de entrada do serviço Signalr
description: Saiba como retornar uma URL de ponto de extremidade de serviço Signalr e um token de acesso em Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 3f3a99c83d4a18f3085419b91be947dd67f8eec4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97763312"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>Associação de entrada do serviço signalr para Azure Functions

Antes que um cliente possa se conectar ao serviço do Azure SignalR, ele deve recuperar a URL do ponto de extremidade de serviço e um token de acesso válido. A associação de entrada *SignalRConnectionInfo* produz a URL do ponto de extremidade de Serviço SignalR e um token válido que são usados para se conectar ao serviço. Como o token é limitada pelo tempo e pode ser usado para autenticar um usuário específico para uma conexão, você não deve armazenar em cache o token nem o compartilhar entre clientes. Um gatilho HTTP que use essa associação pode ser usado pelos clientes para recuperar as informações de conexão.

Para obter mais informações sobre como essa associação é usada para criar uma função "Negotiate" que pode ser consumida por um SDK de cliente do Signalr, consulte o [artigo Azure Functions desenvolvimento e configuração](../azure-signalr/signalr-concept-serverless-development-config.md) na documentação conceitos do serviço signalr.

Para obter informações sobre a instalação e detalhes de configuração, confira a [visão geral](functions-bindings-signalr-service.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que adquire as informações de conexão do SignalR usando a associação de entrada e retorna-a via HTTP.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma associação de entrada de informações de conexão do Signalr em um *function.jsno* arquivo e uma [função de script C#](functions-reference-csharp.md) que usa a associação para retornar as informações de conexão.

Aqui estão os dados de associação no arquivo *function.json*:

function.json de exemplo:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Este é o código do script C#:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de entrada de informações de conexão do SignalR em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação para retornar as informações de conexão.

Aqui estão os dados de associação no arquivo *function.json*:

function.json de exemplo:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Aqui está o código JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma associação de entrada de informações de conexão do Signalr em um *function.jsno* arquivo e uma [função do Python](functions-reference-python.md) que usa a associação para retornar as informações de conexão.

Aqui estão os dados de associação no arquivo *function.json*:

function.json de exemplo:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Aqui está o código Python:

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra uma [função Java](functions-reference-java.md) que adquire informações de conexão do signalr usando a associação de entrada e a retorna sobre http.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="authenticated-tokens"></a>Tokens autenticados

Se a função for disparada por um cliente autenticado, você poderá adicionar uma declaração de ID de usuário ao token gerado. Você pode adicionar facilmente a autenticação a um aplicativo de funções usando a [autenticação do serviço de aplicativo](../app-service/overview-authentication-authorization.md).

Autenticação do Serviço de Aplicativo define os cabeçalhos HTTP denominados `x-ms-client-principal-id` e `x-ms-client-principal-name` que contêm a ID e o nome da entidade de segurança do cliente do usuário autenticado, respectivamente.

# <a name="c"></a>[C#](#tab/csharp)

Você pode definir a `UserId` propriedade da associação com o valor de um dos cabeçalhos usando uma [expressão de associação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}` .

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Você pode definir a `userId` propriedade da associação com o valor de um dos cabeçalhos usando uma [expressão de associação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}` .

function.json de exemplo:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Este é o código do script C#:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Você pode definir a `userId` propriedade da associação com o valor de um dos cabeçalhos usando uma [expressão de associação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}` .

function.json de exemplo:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Aqui está o código JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

Você pode definir a `userId` propriedade da associação com o valor de um dos cabeçalhos usando uma [expressão de associação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}` .

function.json de exemplo:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Aqui está o código Python:

```python
def main(req: func.HttpRequest, connectionInfo: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfo,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

Você pode definir a `userId` propriedade da associação com o valor de um dos cabeçalhos usando uma [expressão de associação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}` .

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="next-steps"></a>Próximas etapas

- [Manipular mensagens do serviço de sinalização (Associação de gatilho)](./functions-bindings-signalr-service-trigger.md)
- [Enviar mensagens do serviço Signalr (Associação de saída)](./functions-bindings-signalr-service-output.md) 
