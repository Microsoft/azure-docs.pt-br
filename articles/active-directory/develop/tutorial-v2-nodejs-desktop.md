---
title: 'Tutorial: Conectar usuários e chamar a API do Microsoft Graph de um aplicativo da área de trabalho do Electron | Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, você criará um aplicativo da área de trabalho do Electron que pode conectar usuários e usar o fluxo de código de autenticação para obter um token de acesso da plataforma de identidade da Microsoft e chamar a API do Microsoft Graph.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: e9e36a3ed2cab05eb63168452bc8472656ab5efb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644282"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-in-an-electron-desktop-app"></a>Tutorial: Conectar usuários e chamar a API do Microsoft Graph em um aplicativo da área de trabalho do Electron

Neste tutorial, você criará um aplicativo da área de trabalho do Electron que conecta usuários e chama o Microsoft Graph usando o fluxo de código de autorização com PKCE. O aplicativo da área de trabalho que você criará usa a [MSAL (Biblioteca de Autenticação da Microsoft) para Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Siga as etapas deste tutorial para:

> [!div class="checklist"]
> - Registrar o aplicativo no portal do Azure
> - Criar um projeto de aplicativo da área de trabalho do Electron
> - Adicionar a lógica de autenticação ao aplicativo
> - Adicionar um método para chamar uma API Web
> - Adicionar detalhes de registro do aplicativo
> - Testar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

- [Node.js](https://nodejs.org/en/download/)
- [Electron](https://www.electronjs.org/)
- [Visual Studio Code](https://code.visualstudio.com/download) ou outro editor de código

## <a name="register-the-application"></a>Registrar o aplicativo

Primeiro, conclua as etapas descritas em [Registrar um aplicativo com a plataforma de identidade da Microsoft](quickstart-register-app.md) para registrar seu aplicativo.

Use as seguintes configurações para o registro do aplicativo:

- Nome: `ElectronDesktopApp` (sugerido)
- Tipos de conta compatíveis: **contas em qualquer diretório organizacional (qualquer diretório do Azure AD – multilocatário) e contas Microsoft pessoais (por exemplo, Skype e Xbox)**
- Tipo de plataforma: **Aplicativos móveis e para desktop**
- URI de redirecionamento: `msal://redirect`

## <a name="create-the-project"></a>Criar o projeto

Crie uma pasta para hospedar o aplicativo, por exemplo, *ElectronDesktopApp*.

1. Primeiro, altere o diretório do projeto em seu terminal e execute os comandos seguintes comandos `npm`:

    ```console
    npm init -y
    npm install --save @azure/msal-node axios bootstrap dotenv jquery popper.js
    npm install --save-dev babel electron@10.1.6 webpack
    ```

2. Depois, crie uma pasta chamada *Aplicativo*. Dentro dessa pasta, crie um arquivo chamado *index.html* que servirá como a interface do usuário. Adicione o seguinte código a ele:

    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
        <title>MSAL Node Electron Sample App</title>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="../node_modules/bootstrap/dist/css/bootstrap.min.css">

        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
    </head>

    <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand">Microsoft identity platform</a>
            <div class="btn-group ml-auto dropleft">
                <button type="button" id="signIn" class="btn btn-secondary" aria-expanded="false">
                    Sign in
                </button>
                <button type="button" id="signOut" class="btn btn-success" hidden aria-expanded="false">
                    Sign out
                </button>
            </div>
        </nav>
        <br>
        <h5 class="card-header text-center">Electron sample app calling MS Graph API using MSAL Node</h5>
        <br>
        <div class="row" style="margin:auto">
            <div id="cardDiv" class="col-md-3" style="display:none">
                <div class="card text-center">
                    <div class="card-body">
                        <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails
                        </h5>
                        <div id="profileDiv"></div>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="seeProfile">See Profile</button>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="readMail">Read Mails</button>
                    </div>
                </div>
            </div>
            <br>
            <br>
            <div class="col-md-4">
                <div class="list-group" id="list-tab" role="tablist">
                </div>
            </div>
            <div class="col-md-5">
                <div class="tab-content" id="nav-tabContent">
                </div>
            </div>
        </div>
        <br>
        <br>

        <script>
            window.jQuery = window.$ = require('jquery');
            require("./renderer.js");
        </script>

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="../node_modules/jquery/dist/jquery.js"></script>
        <script src="../node_modules/popper.js/dist/umd/popper.js"></script>
        <script src="../node_modules/bootstrap/dist/js/bootstrap.js"></script>
    </body>

    </html>
    ```

3. Em seguida, crie um arquivo chamado *main.js* e adicione o seguinte código:

    ```JavaScript
    require('dotenv').config()

    const path = require('path');
    const { app, ipcMain, BrowserWindow } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    const { callEndpointWithToken } = require('./fetch');
    const AuthProvider = require('./AuthProvider');

    const authProvider = new AuthProvider();
    let mainWindow;

    function createWindow () {
        mainWindow = new BrowserWindow({
            width: 800,
            height: 600,
            webPreferences: {
            nodeIntegration: true
            }
        });

        mainWindow.loadFile(path.join(__dirname, './index.html'));
        };

    app.on('ready', () => {
        createWindow();
    });

    app.on('window-all-closed', () => {
        app.quit();
    });


    // Event handlers
    ipcMain.on(IPC_MESSAGES.LOGIN, async() => {
        const account = await authProvider.login(mainWindow);

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
    });

    ipcMain.on(IPC_MESSAGES.LOGOUT, async() => {
        await authProvider.logout();
        await mainWindow.loadFile(path.join(__dirname, './index.html'));
    });

    ipcMain.on(IPC_MESSAGES.GET_PROFILE, async() => {

        const tokenRequest = {
            scopes: ['User.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_PROFILE, graphResponse);
    });

    ipcMain.on(IPC_MESSAGES.GET_MAIL, async() => {

        const tokenRequest = {
            scopes: ['Mail.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account;

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_MAIL, graphResponse);
    });
    ```

No snippet de código acima, inicializamos um objeto de janela principal do Electron e criamos alguns manipuladores de eventos para interações com a janela do Electron. Também importamos parâmetros de configuração, criamos uma instância da classe *authProvider* para processar a entrada, a saída e a aquisição de token e chamamos a API do Microsoft Graph.

4. Na mesma pasta (*Aplicativo*), crie outro arquivo chamado *renderer.js* e adicione o seguinte código:

    ```JavaScript
    const { ipcRenderer } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    // UI event handlers
    document.querySelector('#signIn').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGIN);
    });

    document.querySelector('#signOut').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGOUT);
    });

    document.querySelector('#seeProfile').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_PROFILE);
    });

    document.querySelector('#readMail').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_MAIL);
    });

    // Main process message subscribers
    ipcRenderer.on(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, (event, account) => {
        showWelcomeMessage(account);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_PROFILE, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_MAIL, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`);
    });

    // DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("signIn");
    const signOutButton = document.getElementById("signOut");
    const cardDiv = document.getElementById("cardDiv");
    const profileDiv = document.getElementById("profileDiv");
    const tabList = document.getElementById("list-tab");
    const tabContent = document.getElementById("nav-tabContent");

    function showWelcomeMessage(account) {
        cardDiv.style.display = "initial";
        welcomeDiv.innerHTML = `Welcome ${account.name}`;
        signInButton.hidden = true;
        signOutButton.hidden = false;
    }

    function clearTabs() {
        tabList.innerHTML = "";
        tabContent.innerHTML = "";
    }

    function updateUI(data, endpoint) {

        console.log(`Graph API responded at: ${new Date().toString()}`);

        if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`) {
            setProfile(data);
        } else if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`) {
            setMail(data);
        }
    }

    function setProfile(data) {
        profileDiv.innerHTML = ''

        const title = document.createElement('p');
        const email = document.createElement('p');
        const phone = document.createElement('p');
        const address = document.createElement('p');

        title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
        email.innerHTML = "<strong>Mail: </strong>" + data.mail;
        phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
        address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;

        profileDiv.appendChild(title);
        profileDiv.appendChild(email);
        profileDiv.appendChild(phone);
        profileDiv.appendChild(address);
    }

    function setMail(data) {
        const mailInfo = data;
        if (mailInfo.value.length < 1) {
            alert("Your mailbox is empty!")
        } else {
            clearTabs();
            mailInfo.value.slice(0, 10).forEach((d, i) => {
                    createAndAppendListItem(d, i);
                    createAndAppendContentItem(d, i);
            });
        }
    }

    function createAndAppendListItem(d, i) {
        const listItem = document.createElement("a");
        listItem.setAttribute("class", "list-group-item list-group-item-action")
        listItem.setAttribute("id", "list" + i + "list")
        listItem.setAttribute("data-toggle", "list")
        listItem.setAttribute("href", "#list" + i)
        listItem.setAttribute("role", "tab")
        listItem.setAttribute("aria-controls", i)
        listItem.innerHTML = d.subject;
        tabList.appendChild(listItem);
    }

    function createAndAppendContentItem(d, i) {
        const contentItem = document.createElement("div");
        contentItem.setAttribute("class", "tab-pane fade")
        contentItem.setAttribute("id", "list" + i)
        contentItem.setAttribute("role", "tabpanel")
        contentItem.setAttribute("aria-labelledby", "list" + i + "list")

        if (d.from) {
            contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
            tabContent.appendChild(contentItem);
        }
    }
    ```

5. Por fim, crie um arquivo chamado *constants.js* que armazenará as constantes de cadeias de caracteres para descrever os **eventos** do aplicativo:

    ```JavaScript
    const IPC_MESSAGES = {
        SHOW_WELCOME_MESSAGE: 'SHOW_WELCOME_MESSAGE',
        LOGIN: 'LOGIN',
        LOGOUT: 'LOGOUT',
        GET_PROFILE: 'GET_PROFILE',
        SET_PROFILE: 'SET_PROFILE',
        GET_MAIL: 'GET_MAIL',
        SET_MAIL: 'SET_MAIL'
    }

    module.exports = {
        IPC_MESSAGES: IPC_MESSAGES,
    }
    ```

Agora você tem uma GUI simples e interações para seu aplicativo do Electron. Depois de concluir o restante do tutorial, a estrutura de arquivos e pastas de seu projeto deve ser semelhante à seguinte:

```
ElectronDesktopApp/
├── App
│   ├── authProvider.js
│   ├── constants.js
│   ├── fetch.js
│   ├── main.js
│   ├── renderer.js
│   ├── index.html
├── package.json
└── .env
```

## <a name="add-authentication-logic-to-your-app"></a>Adicionar a lógica de autenticação ao aplicativo

Na pasta *Aplicativo*, crie um arquivo chamado *AuthProvider.js*. Isso conterá uma classe de provedor de autenticação que processará o logon, o logoff, a aquisição de token, a seleção de conta e as tarefas de autenticação relacionadas usando a MSAL Node. Adicione o seguinte código a ele:

```JavaScript
const { PublicClientApplication, LogLevel, CryptoProvider } = require('@azure/msal-node');
const { protocol } = require('electron');
const path = require('path');
const url = require('url');

/**
 * To demonstrate best security practices, this Electron sample application makes use of
 * a custom file protocol instead of a regular web (https://) redirect URI in order to
 * handle the redirection step of the authorization flow, as suggested in the OAuth2.0 specification for Native Apps.
 */
const CUSTOM_FILE_PROTOCOL_NAME = process.env.REDIRECT_URI.split(':')[0]; // e.g. msal://redirect

/**
 * Configuration object to be passed to MSAL instance on creation.
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md
 */
const MSAL_CONFIG = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: `${process.env.AAD_ENDPOINT_HOST}${process.env.TENANT_ID}`,
        redirectUri: process.env.REDIRECT_URI,
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: LogLevel.Verbose,
        }
    }
};

class AuthProvider {

    clientApplication;
    cryptoProvider;
    authCodeUrlParams;
    authCodeRequest;
    pkceCodes;
    account;

    constructor() {
        /**
         * Initialize a public client application. For more information, visit:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-public-client-application.md
         */
        this.clientApplication = new PublicClientApplication(MSAL_CONFIG);
        this.account = null;

        // Initialize CryptoProvider instance
        this.cryptoProvider = new CryptoProvider();

        this.setRequestObjects();
    }

    /**
     * Initialize request objects used by this AuthModule.
     */
    setRequestObjects() {
        const requestScopes =  ['openid', 'profile', 'User.Read'];
        const redirectUri = process.env.REDIRECT_URI;

        this.authCodeUrlParams = {
            scopes: requestScopes,
            redirectUri: redirectUri
        };

        this.authCodeRequest = {
            scopes: requestScopes,
            redirectUri: redirectUri,
            code: null
        }

        this.pkceCodes = {
            challengeMethod: "S256", // Use SHA256 Algorithm
            verifier: "", // Generate a code verifier for the Auth Code Request first
            challenge: "" // Generate a code challenge from the previously generated code verifier
        };
    }

    async login(authWindow) {
        const authResult = await this.getTokenInteractive(authWindow, this.authCodeUrlParams);
        return this.handleResponse(authResult);
    }

    async logout() {
        if (this.account) {
            await this.clientApplication.getTokenCache().removeAccount(this.account);
            this.account = null;
        }
    }

    async getToken(authWindow, tokenRequest) {
        let authResponse;

        authResponse = await this.getTokenInteractive(authWindow, tokenRequest);

        return authResponse.accessToken || null;
    }

    // This method contains an implementation of access token acquisition in authorization code flow
    async getTokenInteractive(authWindow, tokenRequest) {

        /**
         * Proof Key for Code Exchange (PKCE) Setup
         *
         * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod parameters
         * in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
         * second leg (acquireTokenByCode() API).
         *
         * MSAL Node provides PKCE Generation tools through the CryptoProvider class, which exposes
         * the generatePkceCodes() asynchronous API. As illustrated in the example below, the verifier
         * and challenge values should be generated previous to the authorization flow initiation.
         *
         * For details on PKCE code generation logic, consult the
         * PKCE specification https://tools.ietf.org/html/rfc7636#section-4
         */

        const {verifier, challenge} = await this.cryptoProvider.generatePkceCodes();

        this.pkceCodes.verifier = verifier;
        this.pkceCodes.challenge = challenge;

        const authCodeUrlParams = {
            ...this.authCodeUrlParams,
            scopes: tokenRequest.scopes,
            codeChallenge: this.pkceCodes.challenge, // PKCE Code Challenge
            codeChallengeMethod: this.pkceCodes.challengeMethod // PKCE Code Challenge Method
        };

        const authCodeUrl = await this.clientApplication.getAuthCodeUrl(authCodeUrlParams);

        protocol.registerFileProtocol(CUSTOM_FILE_PROTOCOL_NAME, (req, callback) => {
            const requestUrl = url.parse(req.url, true);
            callback(path.normalize(`${__dirname}/${requestUrl.path}`));
        });

        const authCode = await this.listenForAuthCode(authCodeUrl, authWindow);

        const authResponse = await this.clientApplication.acquireTokenByCode({
            ...this.authCodeRequest,
            scopes: tokenRequest.scopes,
            code: authCode,
            codeVerifier: this.pkceCodes.verifier // PKCE Code Verifier
        });

        return authResponse;
    }

    // Listen for authorization code response from Azure AD
    async listenForAuthCode(navigateUrl, authWindow) {

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

    /**
     * Handles the response from a popup or redirect. If response is null, will check if we have any accounts and attempt to sign in.
     * @param response
     */
    async handleResponse(response) {
        if (response !== null) {
            this.account = response.account;
        } else {
            this.account = await this.getAccount();
        }

        return this.account;
    }

    /**
     * Calls getAllAccounts and determines the correct account to sign into, currently defaults to first account found in cache.
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    async getAccount() {
        const cache = this.clientApplication.getTokenCache();
        const currentAccounts = await cache.getAllAccounts();

        if (currentAccounts === null) {
            console.log('No accounts detected');
            return null;
        }

        if (currentAccounts.length > 1) {
            // Add choose account code here
            console.log('Multiple accounts detected, need to add choose account code.');
            return currentAccounts[0];
        } else if (currentAccounts.length === 1) {
            return currentAccounts[0];
        } else {
            return null;
        }
    }
}

module.exports = AuthProvider;
```

No snippet de código acima, primeiro, inicializamos o `PublicClientApplication` da MSAL Node transmitindo um objeto de configuração (`msalConfig`). Depois, expomos os métodos `login`, `logout` e `getToken` a serem chamados pelo módulo principal (*main.js*). Em `login` e `getToken`, adquirimos tokens de ID e de acesso, respectivamente, solicitando primeiro um código de autorização e trocando-o por um token usando a API pública `acquireTokenByCode` da MSAL Node.

## <a name="add-a-method-to-call-a-web-api"></a>Adicionar um método para chamar uma API Web

Crie outro arquivo chamado *fetch.js*. Esse arquivo conterá um cliente HTTP do Axios para fazer chamadas REST à API do Microsoft Graph.

```JavaScript
const axios = require('axios');

/**
 * Makes an Authorization 'Bearer' request with the given accessToken to the given endpoint.
 * @param endpoint
 * @param accessToken
 */
async function callEndpointWithToken(endpoint, accessToken) {
    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('Request made at: ' + new Date().toString());

    const response = await axios.default.get(endpoint, options);

    return response.data;
}

module.exports = {
    callEndpointWithToken: callEndpointWithToken,
};
```

## <a name="add-app-registration-details"></a>Adicionar detalhes de registro do aplicativo

Por fim, crie um arquivo de ambiente para armazenar os detalhes de registro do aplicativo que serão usados ao adquirir tokens. Para fazer isso, crie um arquivo chamado *.env* na pasta raiz do exemplo (*ElectronDesktopApp*) e adicione o seguinte código:

```
# Credentials
CLIENT_ID=Enter_the_Application_Id_Here
TENANT_ID=Enter_the_Tenant_Id_Here

# Configuration
REDIRECT_URI=msal://redirect

# Endpoints
AAD_ENDPOINT_HOST=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT_HOST=Enter_the_Graph_Endpoint_Here

# RESOURCES
GRAPH_ME_ENDPOINT=v1.0/me
GRAPH_MAIL_ENDPOINT=v1.0/me/messages

# SCOPES
GRAPH_SCOPES=User.Read Mail.Read
```

Preencha esses detalhes com os valores obtidos do portal de registro de aplicativo do Azure:

- `Enter_the_Tenant_Id_here` deve ser uma destas opções:
  - Se o aplicativo tem suporte para *contas neste diretório organizacional*, substitua esse valor pela **ID do locatário** ou pelo **Nome do locatário**. Por exemplo, `contoso.microsoft.com`.
  - Se o aplicativo tem suporte para *contas em qualquer diretório organizacional*, substitua esse valor por `organizations`.
  - Se o seu aplicativo tem suporte para *contas em qualquer diretório organizacional e contas pessoais Microsoft*, substitua esse valor por `common`.
  - Para restringir o suporte a *contas pessoais da Microsoft*, substitua esse valor por `consumers`.
- `Enter_the_Application_Id_Here`: a **ID do Aplicativo (cliente)** do aplicativo que você registrou.
- `Enter_the_Cloud_Instance_Id_Here`: a instância de nuvem do Azure na qual o aplicativo está registrado.
  - Para a nuvem principal (ou *global*) do Azure, insira `https://login.microsoftonline.com/`.
  - Para nuvens **nacionais** (por exemplo, China), você pode encontrar os valores apropriados em [Nuvens nacionais](authentication-national-cloud.md).
- `Enter_the_Graph_Endpoint_Here` é a instância da API do Microsoft Graph com a qual o aplicativo deve se comunicar.
  - Para o ponto de extremidade **global** da API do Microsoft Graph, substitua as duas instâncias dessa cadeia de caracteres por `https://graph.microsoft.com/`.
  - Para pontos de extremidade em implantações de nuvens **nacionais**, confira [Implantações de nuvens nacionais](/graph/deployments) na documentação do Microsoft Graph.

## <a name="test-the-app"></a>Testar o aplicativo

Você concluiu a criação do aplicativo e agora está pronto para iniciar o aplicativo da área de trabalho Electron e testar a funcionalidade dele.

1. Inicie o aplicativo executando o seguinte comando na raiz da pasta do projeto:

```console
electron App/main.js
```

2. Na janela principal do aplicativo, você verá o conteúdo do arquivo *index.html* e o botão **Entrar**.

## <a name="test-sign-in-and-sign-out"></a>Testar a entrada e a saída

Depois que o arquivo *index.html* for carregado, escolha **Entrar**. Você deverá entrar na plataforma de identidade da Microsoft:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-01-signin-prompt.png" alt-text="prompt de entrada":::

Se você concordar com as permissões solicitadas, os aplicativos Web exibirão seu nome de usuário, indicando um logon bem-sucedido:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-03-after-signin.png" alt-text="entrada bem-sucedida":::

## <a name="test-web-api-call"></a>Testar a chamada à API Web

Depois de entrar, selecione **Ver Perfil** para exibir as informações do perfil do usuário retornadas na resposta da chamada à API do Microsoft Graph:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-04-profile.png" alt-text="informações de perfil do Microsoft Graph":::

Selecione **Ler Emails** para ver as mensagens na conta do usuário. Você verá uma tela de consentimento:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-05-consent-mail.png" alt-text="tela de consentimento para a permissão read.mail":::

Após o consentimento, você verá as mensagens retornadas na resposta da chamada à API do Microsoft Graph:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-06-mails.png" alt-text="informações de email do Microsoft Graph":::

## <a name="how-the-application-works"></a>Como o aplicativo funciona

Quando um usuário seleciona o botão **Entrar** pela primeira vez, o método GET `getTokenInteractive` de *AuthProvider.js* é chamado. Esse método redireciona o usuário para entrar com o *ponto de extremidade da plataforma de identidade da Microsoft* e validar as credenciais do usuário e obtém um **código de autorização**. Depois, esse código é trocado por um token de acesso por meio da API pública `acquireTokenByCode` da MSAL Node.

Neste ponto, um código de autorização protegido por PKCE é enviado para o ponto de extremidade de token protegido por CORS e é trocado por tokens. Um token de ID, um token de acesso e um token de atualização são recebidos pelo seu aplicativo e processados pela MSAL Node, e as informações contidas nos tokens são armazenadas em cache.

O token de ID contém informações básicas sobre o usuário, como o nome de exibição dele. O token de acesso tem um tempo de vida limitado e expira após 24 horas. Se você pretende usar esses tokens para acessar um recurso protegido, o servidor back-end *precisa* validá-los a fim de garantir que o token foi emitido para um usuário válido para o seu aplicativo.

O aplicativo da área de trabalho criado neste tutorial faz uma chamada REST à API do Microsoft Graph usando um token de acesso como o token de portador no cabeçalho de solicitação ([RFC 6750](https://tools.ietf.org/html/rfc6750)).

A API do Microsoft Graph requer o escopo *user.read* para ler o perfil do usuário. Por padrão, este escopo é adicionado automaticamente a cada aplicativo registrado no portal do Azure. Outras APIs do Microsoft Graph, bem como APIs personalizadas do servidor de back-end, podem exigir escopos adicionais. Por exemplo, a API do Microsoft Graph requer o escopo *Mail.Read* para listar o email do usuário.

À medida que você adiciona escopos, os usuários podem ser solicitados a fornecer consentimento adicional para os escopos adicionados.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

Caso deseje se aprofundar no desenvolvimento de aplicativos da área de trabalho Node.js e Electron na plataforma de identidade da Microsoft, confira nossa série de cenários de várias partes:

> [!div class="nextstepaction"]
> [Cenário: Aplicativo da área de trabalho que chama APIs Web](scenario-desktop-overview.md)
