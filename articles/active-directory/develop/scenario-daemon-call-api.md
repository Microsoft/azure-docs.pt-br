---
title: Chamar uma API da Web de um aplicativo daemon | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo daemon que chama uma API da Web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f4b223c9195168b445b7eb81c2709a61807fddac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578389"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Aplicativo daemon que chama APIs Web – chama uma API Web do aplicativo

Os aplicativos do daemon .NET podem chamar uma API da Web. Os aplicativos do daemon .NET também podem chamar várias APIs Web previamente aprovadas.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Chamando uma API Web a partir de um aplicativo daemon

Veja como usar o token para chamar uma API:

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="java"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Usando um cliente HTTP como [axios](https://www.npmjs.com/package/axios), chame o URI do ponto de extremidade da API com um token de acesso como o *portador de autorização*.

```JavaScript
const axios = require('axios');

async function callApi(endpoint, accessToken) {

    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('request made to web API at: ' + new Date().toString());

    try {
        const response = await axios.default.get(endpoint, options);
        return response.data;
    } catch (error) {
        console.log(error)
        return error;
    }
};
```

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

---

## <a name="calling-several-apis"></a>Chamando várias APIs

Para aplicativos daemon, as APIs da Web que você chama precisam ser aprovadas previamente. Não há nenhum consentimento incremental com aplicativos de daemon. (Não há interação do usuário.) O administrador de locatários precisa fornecer consentimento antecipadamente para o aplicativo e todas as permissões de API. Se você quiser chamar várias APIs, adquira um token para cada recurso, cada vez chamando `AcquireTokenForClient` . MSAL usará o cache de token de aplicativo para evitar chamadas de serviço desnecessárias.

## <a name="next-steps"></a>Próximas etapas

# <a name="net"></a>[.NET](#tab/dotnet)

Vá para o próximo artigo neste cenário, [vá para produção](./scenario-daemon-production.md?tabs=dotnet).

# <a name="java"></a>[Java](#tab/java)

Vá para o próximo artigo neste cenário, [vá para produção](./scenario-daemon-production.md?tabs=java).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Vá para o próximo artigo neste cenário, [vá para produção](./scenario-daemon-production.md?tabs=nodejs).

# <a name="python"></a>[Python](#tab/python)

Vá para o próximo artigo neste cenário, [vá para produção](./scenario-daemon-production.md?tabs=python).

---
