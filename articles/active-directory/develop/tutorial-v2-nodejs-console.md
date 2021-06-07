---
title: 'Tutorial: Chamar o Microsoft Graph em um aplicativo de console Node.js | Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, você criará um aplicativo de console para chamar o Microsoft Graph em um aplicativo de console Node.js.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 33d3712e25a06419e0ccc5914cdddfae7d85a371
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645782"
---
# <a name="tutorial-call-the-microsoft-graph-api-in-a-nodejs-console-app"></a>Tutorial: Chamar a API do Microsoft Graph em um aplicativo de console Node.js

Neste tutorial, você criará um aplicativo de console que chama a API do Microsoft Graph usando a própria identidade. O aplicativo de console que você criará usa a [MSAL (Biblioteca de Autenticação da Microsoft) para Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Siga as etapas deste tutorial para:

> [!div class="checklist"]
> - Registrar o aplicativo no portal do Azure
> - Criar um projeto de aplicativo de console Node.js
> - Adicionar a lógica de autenticação ao aplicativo
> - Adicionar detalhes de registro do aplicativo
> - Adicionar um método para chamar uma API Web
> - Testar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) ou outro editor de código

## <a name="register-the-application"></a>Registrar o aplicativo

Primeiro, conclua as etapas descritas em [Registrar um aplicativo com a plataforma de identidade da Microsoft](quickstart-register-app.md) para registrar seu aplicativo.

Use as seguintes configurações para o registro do aplicativo:

- Nome: `NodeConsoleApp` (sugerido)
- Tipos de conta compatíveis: **Contas somente neste diretório organizacional**
- Permissões de API: **APIs da Microsoft** > **Microsoft Graph** > **Permissões de Aplicativo** > `User.Read.All`
- Segredo do cliente: `*********` (registre esse valor para uso em uma etapa posterior – ele é mostrado apenas uma vez)

## <a name="create-the-project"></a>Criar o projeto

Crie uma pasta para hospedar o aplicativo, por exemplo, *NodeConsoleApp*.

1. Primeiro, altere o diretório do projeto no terminal e execute os seguintes comandos do NPM:

```console
    npm init -y
    npm install --save dotenv yargs axios @azure/msal-node
```

2. Depois, crie uma pasta chamada *bin*. Em seguida, dentro dessa pasta, crie um arquivo chamado *index.js* e adicione o seguinte código:

```JavaScript
#!/usr/bin/env node

// read in env settings
require('dotenv').config();

const yargs = require('yargs');

const fetch = require('./fetch');
const auth = require('./auth');

const options = yargs
    .usage('Usage: --op <operation_name>')
    .option('op', { alias: 'operation', describe: 'operation name', type: 'string', demandOption: true })
    .argv;

async function main() {
    console.log(`You have selected: ${options.op}`);

    switch (yargs.argv['op']) {
        case 'getUsers':

            try {
                // here we get an access token
                const authResponse = await auth.getToken(auth.tokenRequest);

                // call the web API with the access token
                const users = await fetch.callApi(auth.apiConfig.uri, authResponse.accessToken);

                // display result
                console.log(users);
            } catch (error) {
                console.log(error);
            }

            break;
        default:
            console.log('Select a Graph operation first');
            break;
    }
};

main();
```

Esse arquivo referencia dois outros módulos do Node: *auth.js*, que contém uma implementação da MSAL Node para adquirir tokens de acesso, e *fetch.js*, que contém um método para fazer uma solicitação HTTP à API do Microsoft Graph com um token de acesso. Depois de concluir o restante do tutorial, a estrutura de arquivos e pastas de seu projeto deve ser semelhante à seguinte:

```
NodeConsoleApp/
├── bin
│   ├── auth.js
│   ├── fetch.js
│   ├── index.js
├── package.json
└── .env
```

## <a name="add-authentication-logic"></a>Adicionar a lógica de autenticação

Dentro da pasta *bin*, crie outro arquivo chamado *auth.js* e adicione o código a seguir para adquirir um token de acesso a fim de apresentá-lo ao chamar a API do Microsoft Graph.

```JavaScript
const msal = require('@azure/msal-node');

/**
 * Configuration object to be passed to MSAL instance on creation.
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md
 */
const msalConfig = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: process.env.AAD_ENDPOINT + process.env.TENANT_ID,
        clientSecret: process.env.CLIENT_SECRET,
    }
};

/**
 * With client credentials flows permissions need to be granted in the portal by a tenant administrator.
 * The scope is always in the format '<resource>/.default'. For more, visit:
 * https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow
 */
const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '.default'],
};

const apiConfig = {
    uri: process.env.GRAPH_ENDPOINT + 'v1.0/users',
};

/**
 * Initialize a confidential client application. For more info, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md
 */
const cca = new msal.ConfidentialClientApplication(msalConfig);

/**
 * Acquires token with client credentials.
 * @param {object} tokenRequest
 */
async function getToken(tokenRequest) {
    return await cca.acquireTokenByClientCredential(tokenRequest);
}

module.exports = {
    apiConfig: apiConfig,
    tokenRequest: tokenRequest,
    getToken: getToken
};
```

No snippet de código acima, primeiro criamos um objeto de configuração (*msalConfig*) e o transmitimos para inicializar um [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) da MSAL. Em seguida, criamos um método para adquirir tokens por meio das **credenciais do cliente** e, finalmente, expomos esse módulo para ser acessado pelo *main.js*. Os parâmetros de configuração deste módulo foram extraídos de um arquivo de ambiente, que criaremos na próxima etapa.

## <a name="add-app-registration-details"></a>Adicionar detalhes de registro do aplicativo

Crie um arquivo de ambiente para armazenar os detalhes de registro do aplicativo que serão usados ao adquirir tokens. Para fazer isso, crie um arquivo chamado *.env* dentro da pasta raiz do exemplo (*NodeConsoleApp*) e adicione o seguinte código:

```
# Credentials
TENANT_ID=Enter_the_Tenant_Id_Here
CLIENT_ID=Enter_the_Application_Id_Here
CLIENT_SECRET=Enter_the_Client_Secret_Here

# Endpoints
AAD_ENDPOINT=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT=Enter_the_Graph_Endpoint_Here
```

Preencha esses detalhes com os valores obtidos do portal de registro de aplicativo do Azure:

- `Enter_the_Tenant_Id_here` deve ser uma destas opções:
  - Se o aplicativo tem suporte para *contas neste diretório organizacional*, substitua esse valor pela **ID do locatário** ou pelo **Nome do locatário**. Por exemplo, `contoso.microsoft.com`.
  - Se o aplicativo tem suporte para *contas em qualquer diretório organizacional*, substitua esse valor por `organizations`.
  - Se o seu aplicativo tem suporte para *contas em qualquer diretório organizacional e contas pessoais Microsoft*, substitua esse valor por `common`.
  - Para restringir o suporte a *contas pessoais da Microsoft*, substitua esse valor por `consumers`.
- `Enter_the_Application_Id_Here`: a **ID do Aplicativo (cliente)** do aplicativo que você registrou.
- `Enter_the_Cloud_Instance_Id_Here`: a instância de nuvem do Azure na qual o aplicativo está registrado.
  - Para a nuvem principal (ou *global*) do Azure, insira `https://login.microsoftonline.com`.
  - Para nuvens **nacionais** (por exemplo, China), você pode encontrar os valores apropriados em [Nuvens nacionais](authentication-national-cloud.md).
- `Enter_the_Graph_Endpoint_Here` é a instância da API do Microsoft Graph com a qual o aplicativo deve se comunicar.
  - Para o ponto de extremidade **global** da API do Microsoft Graph, substitua as duas instâncias dessa cadeia de caracteres por `https://graph.microsoft.com`.
  - Para pontos de extremidade em implantações de nuvens **nacionais**, confira [Implantações de nuvens nacionais](/graph/deployments) na documentação do Microsoft Graph.

## <a name="add-a-method-to-call-a-web-api"></a>Adicionar um método para chamar uma API Web

Na pasta *bin*, crie outro arquivo chamado *fetch.js* e adicione o seguinte código para fazer chamadas REST à API do Microsoft Graph:

```javascript
const axios = require('axios');

/**
 * Calls the endpoint with authorization bearer token.
 * @param {string} endpoint
 * @param {string} accessToken
 */
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

module.exports = {
    callApi: callApi
};
```

Aqui, o método `callApi` é usado para fazer uma solicitação HTTP `GET` em um recurso protegido que exige um token de acesso. Depois, a solicitação retorna o conteúdo para o chamador. Esse método adiciona o token adquirido no *cabeçalho de Autorização HTTP*. O recurso protegido aqui é o [ponto de extremidade de usuários](/graph/api/user-list) da API do Microsoft Graph que exibe os usuários no locatário em que esse aplicativo está registrado.

## <a name="test-the-app"></a>Testar o aplicativo

Você concluiu a criação do aplicativo e agora está pronto para testar a funcionalidade do aplicativo.

Inicie o aplicativo de console Node.js executando o seguinte comando na raiz da pasta do seu projeto:

```console
node . --op getUsers
```

Isso resultará em uma resposta JSON da API do Microsoft Graph, e você verá uma matriz de objetos de usuário no console:

```console
You have selected: getUsers
request made to web API at: Fri Jan 22 2021 09:31:52 GMT-0800 (Pacific Standard Time)
{
    '@odata.context': 'https://graph.microsoft.com/v1.0/$metadata#users',
    value: [
        {
            displayName: 'Adele Vance'
            givenName: 'Adele',
            jobTitle: 'Retail Manager',
            mail: 'AdeleV@msaltestingjs.onmicrosoft.com',
            mobilePhone: null,
            officeLocation: '18/2111',
            preferredLanguage: 'en-US',
            surname: 'Vance',
            userPrincipalName: 'AdeleV@msaltestingjs.onmicrosoft.com',
            id: 'a6a218a5-f5ae-462a-acd3-581af4bcca00'
        }
    ]
}
```
:::image type="content" source="media/tutorial-v2-nodejs-console/screenshot.png" alt-text="Interface de linha de comando exibindo a resposta do Graph":::

## <a name="how-the-application-works"></a>Como o aplicativo funciona

Este aplicativo usa a [concessão das credenciais do cliente OAuth 2.0](./v2-oauth2-client-creds-grant-flow.md). Esse tipo de concessão normalmente é usado para interações de servidor para servidor que devem ser executadas em segundo plano, sem interação imediata com um usuário. O fluxo de concessão de credenciais permite que um serviço Web (um cliente confidencial) use credenciais próprias, em vez de representar um usuário, para autenticação ao chamar outro serviço Web. Os tipos de aplicativos compatíveis com esse modelo de autenticação geralmente são **daemons** ou **contas de serviço**.

O escopo a ser solicitado para um fluxo de credenciais do cliente é o nome do recurso seguido por `/.default`. Essa notação instrui o Azure AD (Azure Active Directory) a usar as permissões no nível do aplicativo declaradas estaticamente durante o registro de aplicativo. Além disso, essas permissões de API precisam ser concedidas por um **administrador de locatários**.

## <a name="next-steps"></a>Próximas etapas

Caso deseje se aprofundar no desenvolvimento de aplicativos de console Node.js na plataforma de identidade da Microsoft, confira nossa série de cenários de várias partes:

> [!div class="nextstepaction"]
> [Cenário: Aplicativo daemon](scenario-daemon-overview.md)