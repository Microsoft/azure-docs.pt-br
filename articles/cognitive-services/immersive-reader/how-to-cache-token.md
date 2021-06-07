---
title: Armazenar o token de autenticação em cache
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como armazenar em cache o token de autenticação.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 01/14/2020
ms.author: metang
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: e79ae3914e32038e2823fb37e3eee658c95e0003
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102608760"
---
# <a name="how-to-cache-the-authentication-token"></a>Como armazenar o token de autenticação em cache

Este artigo demonstra como armazenar em cache o token de autenticação a fim de aprimorar o desempenho do aplicativo.

## <a name="using-aspnet"></a>Usando ASP.NET

Importe o pacote NuGet **Microsoft.IdentityModel.Clients.ActiveDirectory**, que é usado para adquirir tokens. Em seguida, use o código a seguir para adquirir um `AuthenticationResult`, usando os valores de autenticação que você obteve ao [criar o recurso da Leitura Avançada](./how-to-create-immersive-reader.md).

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

O objeto `AuthenticationResult` tem a propriedade `AccessToken`, que é o token real que você usará ao iniciar a Leitura Avançada usando o SDK. Ele também tem a propriedade `ExpiresOn`, que indica quando o token expirará. Antes de iniciar a Leitura Avançada, você pode verificar se o token expirou e adquirir um novo token somente se ele tiver expirado.

## <a name="using-nodejs"></a>Usando Node.js

Adicione o pacote npm [**request**](https://www.npmjs.com/package/request) ao seu projeto. Use o código a seguir para adquirir um token, usando os valores de autenticação que você obteve ao [criar o recurso da Leitura Avançada](./how-to-create-immersive-reader.md).

```javascript
router.get('/token', function(req, res) {
    request.post(
        {
            headers: { 'content-type': 'application/x-www-form-urlencoded' },
            url: `https://login.windows.net/${TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: CLIENT_ID,
                client_secret: CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, json) {
            const result = JSON.parse(json);
            return res.send({
                access_token: result.access_token,
                expires_on: result.expires_on
            });
        }
    );
});
```

A propriedade `expires_on` é a data e a hora de expiração do token, expressas como o número de segundos desde 1º de janeiro de 1970 UTC. Use esse valor para determinar se o token expirou antes de tentar adquirir um novo.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>Próximas etapas

* Explorar a [Referência do SDK da Leitura Avançada](./reference.md)