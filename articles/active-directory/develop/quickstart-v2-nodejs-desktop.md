---
title: 'Início rápido: Chamar o Microsoft Graph de um aplicativo da área de trabalho Node.js | Azure'
titleSuffix: Microsoft identity platform
description: Neste guia de início rápido, saiba como um aplicativo da área de trabalho Node.js Electron pode conectar usuários e obter um token de acesso para chamar uma API protegida por um ponto de extremidade da plataforma de identidade da Microsoft
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: beef869b891fe6e3f0ea2f667763cb310008b2fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653262"
---
# <a name="quickstart-acquire-an-access-token-and-call-the-microsoft-graph-api-from-an-electron-desktop-app"></a>Início rápido: Adquirir um token de acesso e chamar a API do Microsoft Graph em um aplicativo da área de trabalho do Electron

Neste guia de início rápido, você baixará e executará um exemplo de código que demonstra como um aplicativo da área de trabalho Electron pode conectar usuários e adquirir um token de acesso para chamar a API do Microsoft Graph.

Este guia de início rápido usa a [Biblioteca de Autenticação da Microsoft para Node.js (MSAL Node)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) com o [fluxo de código de autorização com PKCE](v2-oauth2-auth-code-flow.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) ou outro editor de código

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>Registrar e baixar o aplicativo de exemplo
>
> Siga as etapas abaixo para começar.
>
> #### <a name="step-1-register-the-application"></a>Etapa 1: Registrar o aplicativo
> Para registrar seu aplicativo e adicionar as informações de registro do aplicativo à solução manualmente, siga estas etapas:
>
> 1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
> 1. Pesquise **Azure Active Directory** e selecione-o.
> 1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
> 1. Insira um **Nome** para seu aplicativo, por exemplo, `msal-node-desktop`. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
> 1. Selecione **Registrar** para criar o aplicativo.
> 1. Em **Gerenciar**, selecione **Autenticação**.
> 1. Selecione **Adicionar uma plataforma** > **Aplicativos móveis e de área de trabalho**.
> 1. Na seção **URIs de Redirecionamento**, insira `msal://redirect`.
> 1. Selecione **Configurar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Etapa 1: Configurar o aplicativo no portal do Azure
> Para que o exemplo de código deste início rápido funcione, você precisará adicionar uma URL de resposta como **msal://redirect**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essa alteração para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-windows-desktop/green-check.png) Seu aplicativo já está configurado com esses atributos.

#### <a name="step-2-download-the-electron-sample-project"></a>Etapa 2: Baixar o projeto de exemplo do Electron

> [!div renderon="docs"]
> [Baixe o exemplo de código](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Baixe o exemplo de código](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-electron-sample-project"></a>Etapa 3: Configurar o projeto de exemplo do Electron
>
> 1. Extraia o arquivo zip para uma pasta local próxima da raiz do disco, por exemplo, *C:\Azure-Samples*.
> 1. Edite *.env* e substitua os valores dos campos `TENANT_ID` e `CLIENT_ID` pelo seguinte snippet:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here"
>    ```
>    Em que:
>    - `Enter_the_Application_Id_Here` - é a **ID do aplicativo (cliente)** que você registrou.
>    - `Enter_the_Tenant_Id_Here` – substitua esse valor pela **ID do locatário** ou pelo **Nome do locatário** (por exemplo, contoso.microsoft.com)
>
> > [!TIP]
> > Para encontrar os valores de **ID do aplicativo (cliente)** , **ID de diretório (locatário)** , acesse a página **Visão Geral** do aplicativo no portal do Azure.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Etapa 4: Executar o aplicativo

> [!div renderon="docs"]
> #### <a name="step-4-run-the-application"></a>Etapa 4: Executar o aplicativo

Você precisará instalar as dependências deste exemplo uma só vez:

```console
npm install
```

Em seguida, execute o aplicativo via prompt de comando ou console:

```console
npm start
```

Você deve ver a interface do usuário do aplicativo com um botão **Entrar**.

## <a name="about-the-code"></a>Observações sobre o código

Abaixo, alguns aspectos importantes do aplicativo de exemplo são discutidos.

### <a name="msal-node"></a>Nó MSAL

[MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft. Para obter mais informações sobre como usar o MSAL Node com aplicativos da área de trabalho, confira [este artigo](scenario-desktop-overview.md).

Instale o MSAL Node executando o comando npm a seguir.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Você pode adicionar a referência do MSAL Node adicionando o seguinte código:

```javascript
const { PublicClientApplication } = require('@azure/msal-node');
```

Em seguida, inicialize a MSAL usando o seguinte código:

```javascript
const MSAL_CONFIG = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
    },
};

const pca = new PublicClientApplication(MSAL_CONFIG);
```

> | Em que: |Descrição |
> |---------|---------|
> | `clientId` | É a **ID do aplicativo (cliente)** relativa ao aplicativo registrado no portal do Azure. Você pode encontrar esse valor na página **Visão Geral** do aplicativo no portal do Azure. |
> | `authority`    | O ponto de extremidade do STS para o usuário autenticar. Normalmente, `https://login.microsoftonline.com/{tenant}` para a nuvem pública, em que {tenant} é o nome do seu locatário ou ID do seu locatário.|

### <a name="requesting-tokens"></a>Solicitando tokens

No primeiro segmento do fluxo de código de autorização com PKCE, prepare e envie uma solicitação de código de autorização com os parâmetros apropriados. Em seguida, no segundo segmento do fluxo, ouça a resposta do código de autorização. Depois que o código for obtido, troque-o para obter um token.

```javascript
// The redirect URI you setup during app registration with a custom file protocol "msal"
const redirectUri = "msal://redirect";

const cryptoProvider = new CryptoProvider();

const pkceCodes = {
    challengeMethod: "S256", // Use SHA256 Algorithm
    verifier: "", // Generate a code verifier for the Auth Code Request first
    challenge: "" // Generate a code challenge from the previously generated code verifier
};

/**
 * Starts an interactive token request
 * @param {object} authWindow: Electron window object
 * @param {object} tokenRequest: token request object with scopes
 */
async function getTokenInteractive(authWindow, tokenRequest) {

    /**
     * Proof Key for Code Exchange (PKCE) Setup
     *
     * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod
     * parameters in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
     * second leg (acquireTokenByCode() API).
     */

    const {verifier, challenge} = await cryptoProvider.generatePkceCodes();

    pkceCodes.verifier = verifier;
    pkceCodes.challenge = challenge;

    const authCodeUrlParams = {
        redirectUri: redirectUri
        scopes: tokenRequest.scopes,
        codeChallenge: pkceCodes.challenge, // PKCE Code Challenge
        codeChallengeMethod: pkceCodes.challengeMethod // PKCE Code Challenge Method
    };

    const authCodeUrl = await pca.getAuthCodeUrl(authCodeUrlParams);

    // register the custom file protocol in redirect URI
    protocol.registerFileProtocol(redirectUri.split(":")[0], (req, callback) => {
        const requestUrl = url.parse(req.url, true);
        callback(path.normalize(`${__dirname}/${requestUrl.path}`));
    });

    const authCode = await listenForAuthCode(authCodeUrl, authWindow); // see below

    const authResponse = await pca.acquireTokenByCode({
        redirectUri: redirectUri,
        scopes: tokenRequest.scopes,
        code: authCode,
        codeVerifier: pkceCodes.verifier // PKCE Code Verifier
    });

    return authResponse;
}

/**
 * Listens for auth code response from Azure AD
 * @param {string} navigateUrl: URL where auth code response is parsed
 * @param {object} authWindow: Electron window object
 */
async function listenForAuthCode(navigateUrl, authWindow) {

    authWindow.loadURL(navigateUrl);

    return new Promise((resolve, reject) => {
        authWindow.webContents.on('will-redirect', (event, responseUrl) => {
            try {
                const parsedUrl = new URL(responseUrl);
                const authCode = parsedUrl.searchParams.get('code');
                resolve(authCode);
            } catch (err) {
                reject(err);
            }
        });
    });
}
```

> |Em que:| Descrição |
> |---------|---------|
> | `authWindow` | Janela atual do Electron em andamento. |
> | `tokenRequest` | Contém os escopos que estão sendo solicitados, por exemplo, `"User.Read"` para o Microsoft Graph ou `"api://<Application ID>/access_as_user"` para APIs Web personalizadas. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o desenvolvimento de aplicativo da área de trabalho Electron com o MSAL Node, confira o tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Conectar usuários e chamar a API do Microsoft Graph em um aplicativo da área de trabalho do Electron](tutorial-v2-nodejs-desktop.md)