---
title: 'Tutorial: Conectar usuários em um aplicativo Web Node.js e Express | Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, você adicionará suporte à entrada de usuários em um aplicativo Web.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 58132b6d038abe0db02d1e58f8c8a8f64962ff49
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279707"
---
# <a name="tutorial-sign-in-users-in-a-nodejs--express-web-app"></a>Tutorial: Conectar usuários em um aplicativo Web Node.js e Express

Neste tutorial, você criará um aplicativo Web que conecta usuários. O aplicativo Web que você criará usa a [MSAL (Biblioteca de Autenticação da Microsoft) para Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Siga as etapas deste tutorial para:

> [!div class="checklist"]
> - Registrar o aplicativo no portal do Azure
> - Criar um projeto de aplicativo Web Express
> - Instalar os pacotes da biblioteca de autenticação
> - Adicionar detalhes de registro do aplicativo
> - Adicionar o código para logon de usuário
> - Testar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) ou outro editor de código

## <a name="register-the-application"></a>Registrar o aplicativo

Primeiro, conclua as etapas descritas em [Registrar um aplicativo com a plataforma de identidade da Microsoft](quickstart-register-app.md) para registrar seu aplicativo.

Use as seguintes configurações para o registro do aplicativo:

- Nome: `ExpressWebApp` (sugerido)
- Tipos de conta compatíveis: **contas em qualquer diretório organizacional (qualquer diretório do Azure AD – multilocatário) e contas Microsoft pessoais (por exemplo, Skype e Xbox)**
- Tipo de plataforma: **Web**
- URI de redirecionamento: `http://localhost:3000/redirect`
- Segredo do cliente: `*********` (registre esse valor para uso em uma etapa posterior – ele é mostrado apenas uma vez)

## <a name="create-the-project"></a>Criar o projeto

Crie uma pasta para hospedar o aplicativo, por exemplo, *ExpressWebApp*.

1. Primeiro, altere o diretório do projeto em seu terminal e execute os comandos seguintes comandos `npm`:

```console
    npm init -y
    npm install --save express
```

2. Em seguida, crie um arquivo chamado *index.js* e adicione o seguinte código:

```JavaScript
    const express = require("express");
    const msal = require('@azure/msal-node');

    const SERVER_PORT = process.env.PORT || 3000;

    // Create Express App and Routes
    const app = express();

    app.listen(SERVER_PORT, () => console.log(`Msal Node Auth Code Sample app listening on port ${SERVER_PORT}!`))
```

Agora você tem um servidor Web simples em execução na porta 3000. A estrutura de arquivos e pastas do projeto será semelhante à seguinte:

```
ExpressWebApp/
├── index.js
└── package.json
```

## <a name="install-the-auth-library"></a>Instalar a biblioteca de autenticação

Localize a raiz do diretório do projeto em um terminal e instale o pacote da MSAL Node por meio do NPM.

```console
    npm install --save @azure/msal-node
```

## <a name="add-app-registration-details"></a>Adicionar detalhes de registro do aplicativo

No arquivo *index.js* criado anteriormente, adicione o seguinte código:

```JavaScript
    // Before running the sample, you will need to replace the values in the config,
    // including the clientSecret
    const config = {
        auth: {
            clientId: "Enter_the_Application_Id",
            authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Id_here",
            clientSecret: "Enter_the_Client_secret"
        },
        system: {
            loggerOptions: {
                loggerCallback(loglevel, message, containsPii) {
                    console.log(message);
                },
                piiLoggingEnabled: false,
                logLevel: msal.LogLevel.Verbose,
            }
        }
    };
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
- `Enter_the_Client_secret`: substitua esse valor pelo segredo do cliente criado anteriormente. Para gerar uma chave, use **Certificados e segredos** nas configurações de registro do aplicativo no portal do Azure.

> [!WARNING]
> Qualquer segredo de texto não criptografado no código-fonte representa um risco de segurança maior. Este artigo usa um segredo do cliente de texto não criptografado apenas para fins de simplicidade. Use [credenciais de certificado](active-directory-certificate-credentials.md) em vez de segredos do cliente nos seus aplicativos cliente confidenciais, especialmente os aplicativos que você pretende implantar na produção.

## <a name="add-code-for-user-login"></a>Adicionar o código para logon de usuário

No arquivo *index.js* criado anteriormente, adicione o seguinte código:

```JavaScript
    // Create msal application object
    const cca = new msal.ConfidentialClientApplication(config);

    app.get('/', (req, res) => {
        const authCodeUrlParameters = {
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        // get url to sign user in and consent to scopes needed for application
        cca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            res.redirect(response);
        }).catch((error) => console.log(JSON.stringify(error)));
    });

    app.get('/redirect', (req, res) => {
        const tokenRequest = {
            code: req.query.code,
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        cca.acquireTokenByCode(tokenRequest).then((response) => {
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => {
            console.log(error);
            res.status(500).send(error);
        });
    });
```

## <a name="test-sign-in"></a>Testar a entrada

Você concluiu a criação do aplicativo e agora está pronto para testar a funcionalidade do aplicativo.

1. Inicie o aplicativo de console Node.js executando o seguinte comando na raiz da pasta do seu projeto:

```console
   node index.js
```

2. Abra uma janela do navegador e navegue até `http://localhost:3000`. Você verá uma tela de entrada:

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/sign-in-screen.png" alt-text="Exibição da tela de entrada do Azure AD":::

3. Depois de inserir suas credenciais, você verá uma tela de consentimento solicitando a aprovação das permissões para o aplicativo.

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/consent-screen.png" alt-text="Exibição da tela de consentimento do Azure AD":::

## <a name="how-the-application-works"></a>Como o aplicativo funciona

Neste tutorial, você inicializou um objeto [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) da MSAL Node transmitindo-o um objeto de configuração (*msalConfig*) que contém os parâmetros obtidos do seu registro de aplicativo do Azure AD no portal do Azure. O aplicativo Web que você criou usa o [fluxo de concessão de código de autorização OAuth 2.0](./v2-oauth2-auth-code-flow.md) para conectar usuários e obter ID e tokens de acesso.

## <a name="next-steps"></a>Próximas etapas

Caso deseje se aprofundar no desenvolvimento de aplicativos Web Node.js e Express na plataforma de identidade da Microsoft, confira nossa série de cenários de várias partes:

> [!div class="nextstepaction"]
> [Cenário: Aplicativo Web que conecta usuários](scenario-web-app-sign-user-overview.md)
