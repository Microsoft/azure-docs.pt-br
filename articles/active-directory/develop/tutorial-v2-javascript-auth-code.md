---
title: 'Tutorial: Criar um aplicativo de página única JavaScript que usa o fluxo de código de autenticação | Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, você cria um SPA JavaScript que pode conectar usuários e usar o fluxo de código de autenticação para obter um token de acesso da plataforma de identidade da Microsoft e chamar a API do Microsoft Graph.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 1ec046ca6b42a5ca8f33b0347c562c85abd42684
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756172"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-app-spa-using-auth-code-flow"></a>Tutorial: Conectar usuários e chamar a API do Microsoft Graph de um SPA (aplicativo de página única) JavaScript usando o fluxo de código de autenticação

Neste tutorial, você criará um SPA (aplicativo de página única) JavaScript que conecta usuários e chama o Microsoft Graph usando o fluxo de código de autorização com PKCE. O SPA que você cria usa a MSAL (Biblioteca de Autenticação da Microsoft) para JavaScript v2.0.

Neste tutorial:
> [!div class="checklist"]
> * Executar o fluxo do código de autorização do OAuth 2.0 com PKCE
> * Entrar em contas Microsoft pessoais, bem como contas corporativas e de estudante
> * Adquirir um token de acesso
> * Chame a API do Microsoft Graph ou a sua API que exige tokens de acesso obtidos da plataforma de identidade da Microsoft

A MSAL.js 2.0 tem aprimoramentos com relação à MSAL.js 1.0, dando suporte ao fluxo do código de autorização no navegador em vez do fluxo de concessão implícita. A MSAL.js 2.0 **NÃO** dá suporte ao fluxo implícito.

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/download/) para executar um servidor Web local
* [Visual Studio Code](https://code.visualstudio.com/download) ou outro editor de código

## <a name="how-the-tutorial-app-works"></a>Como o aplicativo tutorial funciona

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="Diagrama mostrando o fluxo do código de autorização em um aplicativo de página única":::

O aplicativo criado neste tutorial permite que um SPA JavaScript consulte a API do Microsoft Graph adquirindo tokens de segurança da plataforma de identidade da Microsoft. Nesse cenário, depois que um usuário se conecta, um token de acesso é adicionado às solicitações HTTP no cabeçalho de autorização. A aquisição e a renovação de tokens são manipuladas pela Biblioteca de Autenticação da Microsoft (MSAL.js) para JavaScript.

Este tutorial usa a seguinte biblioteca:

[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) a Biblioteca de Autenticação da Microsoft para o pacote de navegador do JavaScript v2.0

## <a name="get-the-completed-code-sample"></a>Obter o exemplo de código concluído

Prefere baixar o projeto de exemplo completo deste tutorial? Para executar o projeto usando um servidor Web local como o Node.js, clone o repositório [ms-identity-javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2):

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

Em seguida, pule para a [etapa de configuração](#register-your-application) para configurar o exemplo de código antes de executá-lo.

Para continuar com o tutorial e criar o aplicativo por conta própria, vá para a próxima seção, [Pré-requisitos](#prerequisites).

## <a name="create-your-project"></a>Criar seu projeto

Quando tiver o [Node.js](https://nodejs.org/en/download/) instalado, crie uma pasta para hospedar seu aplicativo, por exemplo, *msal-spa-tutorial*.

Em seguida, implemente um servidor Web pequeno do [Express](https://expressjs.com/) para atender ao seu arquivo *index.html*.

1. Primeiro, altere o diretório do projeto em seu terminal e execute os comandos seguintes comandos `npm`:
    ```console
    npm init -y
    npm install @azure/msal-browser
    npm install express
    npm install morgan
    npm install yargs
    ```
2. Em seguida, crie um arquivo chamado *server.js* e adicione o seguinte código:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');
   const argv = require('yargs')
      .usage('Usage: $0 -p [PORT]')
      .alias('p', 'port')
      .describe('port', '(Optional) Port Number - default is 3000')
      .strict()
      .argv;

   const DEFAULT_PORT = 3000;

   //initialize express.
   const app = express();

   // Initialize variables.
   let port = DEFAULT_PORT; // -p {PORT} || 3000;
   if (argv.p) {
      port = argv.p;
   }

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use("/lib", express.static(path.join(__dirname, "../../lib/msal-browser/lib")));

   // Setup app folders
   app.use(express.static('app'));

   // Set up a route for index.html.
   app.get('*', function (req, res) {
      res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log(`Listening on port ${port}...`);
    ```

Agora você tem um servidor Web pequeno para atender ao seu SPA. Depois de concluir o restante do tutorial, a estrutura de arquivos e pastas de seu projeto deve ser semelhante à seguinte:

```
msal-spa-tutorial/
├── app
│   ├── authConfig.js
│   ├── authPopup.js
│   ├── authRedirect.js
│   ├── graphConfig.js
│   ├── graph.js
│   ├── index.html
│   └── ui.js
└── server.js
```

## <a name="create-the-spa-ui"></a>Criar a interface do usuário do SPA

1. Crie uma pasta *app* no diretório do projeto e, nela, crie um arquivo *index.html* para seu SPA JavaScript. Esse arquivo implementa uma interface do usuário criada com o **Bootstrap 4 Framework** e importa arquivos de script para configuração, autenticação e chamadas à API.

    No arquivo *index.html*, adicione o seguinte código:

    ```html
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <title>Tutorial | MSAL.js JavaScript SPA</title>

        <!-- IE support: add promises polyfill before msal.js  -->
        <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
        <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.4/js/msal-browser.js" integrity="sha384-7sxY2tN3GMVE5jXH2RL9AdbO6s46vUh9lUid4yNCHJMUzDoj+0N4ve6rLOmR88yN" crossorigin="anonymous"></script>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
          <a class="navbar-brand" href="/">Microsoft identity platform</a>
          <div class="btn-group ml-auto dropleft">
              <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                Sign In
              </button>
          </div>
        </nav>
        <br>
        <h5 class="card-header text-center">JavaScript SPA calling Microsoft Graph API with MSAL.js</h5>
        <br>
        <div class="row" style="margin:auto" >
        <div id="card-div" class="col-md-3" style="display:none">
        <div class="card text-center">
          <div class="card-body">
            <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails</h5>
            <div id="profile-div"></div>
            <br>
            <br>
            <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
            <br>
            <br>
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mail</button>
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

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
        <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

        <!-- importing app scripts (load order is important) -->
        <script type="text/javascript" src="./authConfig.js"></script>
        <script type="text/javascript" src="./graphConfig.js"></script>
        <script type="text/javascript" src="./ui.js"></script>

        <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
        <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
        <script type="text/javascript" src="./authPopup.js"></script>
        <script type="text/javascript" src="./graph.js"></script>
      </body>
    </html>
    ```

2. Em seguida, também na pasta *app*, crie um arquivo chamado *ui.js* e adicione o código a seguir. Este arquivo vai acessar e atualizar elementos DOM.

    ```JavaScript
    // Select DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("SignIn");
    const cardDiv = document.getElementById("card-div");
    const mailButton = document.getElementById("readMail");
    const profileButton = document.getElementById("seeProfile");
    const profileDiv = document.getElementById("profile-div");

    function showWelcomeMessage(account) {
        // Reconfiguring DOM elements
        cardDiv.style.display = 'initial';
        welcomeDiv.innerHTML = `Welcome ${account.username}`;
        signInButton.setAttribute("onclick", "signOut();");
        signInButton.setAttribute('class', "btn btn-success")
        signInButton.innerHTML = "Sign Out";
    }

    function updateUI(data, endpoint) {
        console.log('Graph API responded at: ' + new Date().toString());

        if (endpoint === graphConfig.graphMeEndpoint) {
            const title = document.createElement('p');
            title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
            const email = document.createElement('p');
            email.innerHTML = "<strong>Mail: </strong>" + data.mail;
            const phone = document.createElement('p');
            phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
            const address = document.createElement('p');
            address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
            profileDiv.appendChild(title);
            profileDiv.appendChild(email);
            profileDiv.appendChild(phone);
            profileDiv.appendChild(address);

        } else if (endpoint === graphConfig.graphMailEndpoint) {
            if (data.value.length < 1) {
                alert("Your mailbox is empty!")
            } else {
                const tabList = document.getElementById("list-tab");
                tabList.innerHTML = ''; // clear tabList at each readMail call
                const tabContent = document.getElementById("nav-tabContent");

                data.value.map((d, i) => {
                    // Keeping it simple
                    if (i < 10) {
                        const listItem = document.createElement("a");
                        listItem.setAttribute("class", "list-group-item list-group-item-action")
                        listItem.setAttribute("id", "list" + i + "list")
                        listItem.setAttribute("data-toggle", "list")
                        listItem.setAttribute("href", "#list" + i)
                        listItem.setAttribute("role", "tab")
                        listItem.setAttribute("aria-controls", i)
                        listItem.innerHTML = d.subject;
                        tabList.appendChild(listItem)

                        const contentItem = document.createElement("div");
                        contentItem.setAttribute("class", "tab-pane fade")
                        contentItem.setAttribute("id", "list" + i)
                        contentItem.setAttribute("role", "tabpanel")
                        contentItem.setAttribute("aria-labelledby", "list" + i + "list")
                        contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
                        tabContent.appendChild(contentItem);
                    }
                });
            }
        }
    }
    ```

## <a name="register-your-application"></a>Registre seu aplicativo

Siga as etapas em [Aplicativo de página única: registro de aplicativo](scenario-spa-app-registration.md) para criar um registro de aplicativo para o SPA.

Na etapa [URI de redirecionamento: MSAL.js 2.0 com fluxo do código de autenticação](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow), insira `http://localhost:3000`, o local padrão onde o aplicativo deste tutorial é executado.

Se quiser usar uma porta diferente, insira `http://localhost:<port>`, em que `<port>` é o número da porta TCP preferencial. Se você especificar um número da porta diferente de `3000`, atualize também *server.js* com seu número de porta preferencial.

### <a name="configure-your-javascript-spa"></a>Configurar o JavaScript SPA

Crie um arquivo chamado *authConfig.js* na pasta *app* que conterá os parâmetros de configuração para autenticação, depois adicione a ele o seguinte código:

```javascript
const msalConfig = {
  auth: {
    clientId: "Enter_the_Application_Id_Here",
    authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
    redirectUri: "Enter_the_Redirect_Uri_Here",
  },
  cache: {
    cacheLocation: "sessionStorage", // This configures where your cache will be stored
    storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
  }
};

// Add scopes here for ID token to be used at Microsoft identity platform endpoints.
const loginRequest = {
 scopes: ["openid", "profile", "User.Read"]
};

// Add scopes here for access token to be used at Microsoft Graph API endpoints.
const tokenRequest = {
 scopes: ["User.Read", "Mail.Read"]
};
```

Modifique os valores na seção `msalConfig` conforme descrito aqui:

- `Enter_the_Application_Id_Here`: a **ID do Aplicativo (cliente)** do aplicativo que você registrou.
- `Enter_the_Cloud_Instance_Id_Here`: a instância de nuvem do Azure na qual o aplicativo está registrado.
  - Para a nuvem principal (ou *global*) do Azure, insira `https://login.microsoftonline.com`.
  - Para nuvens **nacionais** (por exemplo, China), você pode encontrar os valores apropriados em [Nuvens nacionais](authentication-national-cloud.md).
- `Enter_the_Tenant_info_here` deve ser uma destas opções:
  - Se o aplicativo tem suporte para *contas neste diretório organizacional*, substitua esse valor pela **ID do locatário** ou pelo **Nome do locatário**. Por exemplo, `contoso.microsoft.com`.
  - Se o aplicativo tem suporte para *contas em qualquer diretório organizacional*, substitua esse valor por `organizations`.
  - Se o seu aplicativo tem suporte para *contas em qualquer diretório organizacional e contas pessoais Microsoft*, substitua esse valor por `common`.
  - Para restringir o suporte a *contas pessoais da Microsoft*, substitua esse valor por `consumers`.
- `Enter_the_Redirect_Uri_Here` é `http://localhost:3000`.

O valor de `authority` em *authConfig.js* deverá ser semelhante ao seguinte se você estiver usando a nuvem global do Azure:

```javascript
authority: "https://login.microsoftonline.com/common",
```

Ainda na pasta *app*, crie um arquivo chamado *graphConfig.js*. Adicione o seguinte código para fornecer ao aplicativo os parâmetros de configuração para chamar a API do Microsoft Graph:

```javascript
// Add the endpoints here for Microsoft Graph API services you'd like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
};
```

Modifique os valores na seção `graphConfig` conforme descrito aqui:

- `Enter_the_Graph_Endpoint_Here` é a instância da API do Microsoft Graph com a qual o aplicativo deve se comunicar.
  - Para o ponto de extremidade **global** da API do Microsoft Graph, substitua as duas instâncias dessa cadeia de caracteres por `https://graph.microsoft.com`.
  - Para pontos de extremidade em implantações de nuvens **nacionais**, confira [Implantações de nuvens nacionais](/graph/deployments) na documentação do Microsoft Graph.

Os valores de `graphMeEndpoint` e `graphMailEndpoint` em seu *graphConfig.js* deverão ser semelhantes ao seguinte se você estiver usando o ponto de extremidade global:

```javascript
graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
```

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-user"></a>Use a MSAL (Biblioteca de Autenticação da Microsoft) para conectar o usuário

### <a name="pop-up"></a>Pop-up

Na pasta *app*, crie um arquivo chamado *authPopup.js* e adicione o seguinte código de autenticação e aquisição de token para o pop-up de logon:

```JavaScript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let username = "";

function loadPage() {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    const currentAccounts = myMSALObj.getAllAccounts();
    if (currentAccounts === null) {
        return;
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
        console.warn("Multiple accounts detected.");
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
        showWelcomeMessage(currentAccounts[0]);
    }
}

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        loadPage();
    }
}

function signIn() {
    myMSALObj.loginPopup(loginRequest).then(handleResponse).catch(error => {
        console.error(error);
    });
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenPopup(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
        console.warn("silent token acquisition fails. acquiring token using redirect");
        if (error instanceof msal.InteractionRequiredAuthError) {
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
                console.log(tokenResponse);

                return tokenResponse;
            }).catch(error => {
                console.error(error);
            });
        } else {
            console.warn(error);
        }
    });
}

function seeProfile() {
    getTokenPopup(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenPopup(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}

loadPage();
```

### <a name="redirect"></a>Redirecionar

Crie um arquivo chamado *authRedirect.js* na pasta *app* e adicione o seguinte código de autenticação e aquisição de token para o redirecionamento de logon:

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;
let username = "";

// Redirect: once login is successful and redirects with tokens, call Graph API
myMSALObj.handleRedirectPromise().then(handleResponse).catch(err => {
    console.error(err);
});

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        /**
         * See here for more info on account retrieval:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
         */
        const currentAccounts = myMSALObj.getAllAccounts();
        if (currentAccounts === null) {
            return;
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
            console.warn("Multiple accounts detected.");
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
            showWelcomeMessage(currentAccounts[0]);
        }
    }
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenRedirect(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            if (error instanceof msal.InteractionRequiredAuthError) {
                // fallback to interaction when silent call fails
                return myMSALObj.acquireTokenRedirect(request);
            } else {
                console.warn(error);
            }
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenRedirect(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}
```

### <a name="how-the-code-works"></a>Como o código funciona

Quando um usuário selecione o botão **Entrar** pela primeira vez, o método `signIn` chama `loginPopup` para conectar o usuário. O método `loginPopup` abre uma janela pop-up com o *ponto de extremidade da plataforma de identidade da Microsoft* para solicitar e validar as credenciais do usuário. Após uma entrada bem-sucedida, *msal.js* inicia o [fluxo de código de autorização](v2-oauth2-auth-code-flow.md).

Neste ponto, um código de autorização protegido por PKCE é enviado para o ponto de extremidade de token protegido por CORS e é trocado por tokens. Um token de ID, um token de acesso e um token de atualização são recebidos pelo seu aplicativo e processados por *msal.js* e as informações contidas no token são armazenadas em cache.

O token de ID contém informações básicas sobre o usuário, como o nome de exibição dele. Se você planeja usar dados fornecidos pelo token de ID, seu servidor de back-end *precisa* validá-los para ter certeza de que o token foi emitido para um usuário válido para o seu aplicativo.

O token de acesso tem um tempo de vida limitado e expira após 24 horas. O token de atualização pode ser usado para obter silenciosamente novos tokens de acesso.

O SPA criado neste tutorial chama `acquireTokenSilent` e/ou `acquireTokenPopup` para adquirir um *token de acesso* usado para consultar a API do Microsoft Graph para obter informações de perfil do usuário. Se você precisar de uma amostra que valida o token de ID, confira o aplicativo de exemplo [active-directory-javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) no GitHub. Esta amostra usa um ASP.NET Web API para validação de token.

#### <a name="get-a-user-token-interactively"></a>Obter um token de usuário interativamente

Depois da entrada inicial, o aplicativo não deve solicitar que os usuários autentiquem novamente sempre que precisam acessar um recurso protegido (ou seja, solicitar um token). Para evitar essas solicitações de reautenticação, chame `acquireTokenSilent`. Entretanto, há algumas situações em que talvez seja necessário forçar os usuários a interagir com a plataforma de identidade da Microsoft. Por exemplo:

- Os usuários precisam reinserir suas credenciais, pois a senha expirou.
- Seu aplicativo está solicitando acesso a um recurso e você precisa do consentimento do usuário.
- A autenticação de dois fatores é necessária.

Chamar `acquireTokenPopup` abre uma janela pop-up (ou `acquireTokenRedirect` redireciona usuários à plataforma de identidade da Microsoft). Nessa janela, os usuários precisam interagir confirmando suas credenciais, concedendo consentimento ao recurso necessário ou concluindo a autenticação de dois fatores.

#### <a name="get-a-user-token-silently"></a>Obter um token de usuário no modo silencioso

O método `acquireTokenSilent` manipula a aquisição e a renovação de tokens sem nenhuma interação do usuário. Após `loginPopup` (ou `loginRedirect`) ser executado pela primeira vez, `acquireTokenSilent` é o método usado normalmente para obter tokens usados para acessar recursos protegidos nas próximas chamadas. (Chamadas para solicitar ou renovar tokens são feitas de modo silencioso.) `acquireTokenSilent` pode falhar em alguns casos. Por exemplo, a senha do usuário pode ter expirado. O aplicativo pode tratar essa exceção de duas maneiras:

1. Fazer uma chamada para `acquireTokenPopup` imediatamente, o que dispara um prompt de entrada do usuário. Esse padrão é usado frequentemente em aplicativos online em que não há nenhum conteúdo não autenticado no aplicativo disponível para o usuário. O exemplo gerado por essa instalação guiada usa esse padrão.
1. Indicar visualmente ao usuários que uma conexão interativa é necessária e, portanto, o usuário pode escolher o momento certo para se conectar ou o aplicativo pode tentar `acquireTokenSilent` novamente mais tarde. Essa técnica normalmente é usada quando o usuário pode usar outra funcionalidade do aplicativo sem ser interrompido. Por exemplo, pode haver conteúdo não autenticado disponível no aplicativo. Nessa situação, o usuário pode decidir quando deseja entrar para acessar o recurso protegido ou para atualizar as informações desatualizadas.

> [!NOTE]
> Este tutorial usa os métodos `loginPopup` e `acquireTokenPopup` por padrão. Se você está usando o Internet Explorer, recomendamos que use os métodos `loginRedirect` e `acquireTokenRedirect` devido a um [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) com o Internet Explorer e janelas pop-up. Para ver um exemplo de como obter o mesmo resultado usando métodos de redirecionamento, confira [*authRedirect.js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js) no GitHub.

## <a name="call-the-microsoft-graph-api"></a>Chamar a API do Microsoft Graph

Crie um arquivo chamado *graph.js* na pasta *app* e adicione o código a seguir para fazer chamadas REST para a API do Microsoft Graph:

```javascript
// Helper function to call Microsoft Graph API endpoint
// using authorization bearer token scheme
function callMSGraph(endpoint, token, callback) {
    const headers = new Headers();
    const bearer = `Bearer ${token}`;

    headers.append("Authorization", bearer);

    const options = {
        method: "GET",
        headers: headers
    };

    console.log('request made to Graph API at: ' + new Date().toString());

    fetch(endpoint, options)
        .then(response => response.json())
        .then(response => callback(response, endpoint))
        .catch(error => console.log(error));
}
```

No aplicativo de exemplo criado por este tutorial, o método `callMSGraph()` é usado para fazer uma solicitação HTTP `GET` em um recurso protegido que exige um token. Depois, a solicitação retorna o conteúdo para o chamador. Esse método adiciona o token adquirido no *cabeçalho de Autorização HTTP*. No aplicativo de exemplo criado neste tutorial, o recurso protegido é o ponto de extremidade *me* da API do Microsoft Graph, que exibe as informações de perfil do usuário conectado.

## <a name="test-your-application"></a>Teste seu aplicativo

Você concluiu a criação do aplicativo e agora está pronto para iniciar o servidor Web Node.js e testar a funcionalidade do aplicativo.

1. Inicie o servidor Web Node.js executando o seguinte comando de dentro da raiz da pasta do aplicativo:

   ```console
   npm start
   ```
1. No navegador, navegue até `http://localhost:3000` ou `http://localhost:<port>`, em que `<port>` é a porta na qual o servidor Web está escutando. Você deve ver o conteúdo do seu arquivo *index.html* e o botão **Entrar**.

### <a name="sign-in-to-the-application"></a>Entrar no aplicativo

Depois que o navegador carregar seu arquivo *index.html*, selecione **Entrar**. Você deverá entrar na plataforma de identidade da Microsoft:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-01-signin-dialog.png" alt-text="Navegador da Web exibindo caixa de diálogo de entrada":::

### <a name="provide-consent-for-application-access"></a>Fornecer autorização para acesso de aplicativo

Na primeira vez que entrar no aplicativo, você será solicitado a conceder acesso ao seu perfil e a conectá-lo:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="Caixa de diálogo de conteúdo exibida no navegador da Web":::

Se você concordar com as permissões solicitadas, os aplicativos Web exibirão seu nome de usuário, indicando um logon bem-sucedido:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-03-signed-in.png" alt-text="Resultados de uma entrada bem-sucedida no navegador da Web":::

### <a name="call-the-graph-api"></a>Chamar a API do Graph

Depois de entrar, selecione **Ver Perfil** para exibir as informações do perfil do usuário retornadas na resposta da chamada à API do Microsoft Graph:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-04-see-profile.png" alt-text="Informações de perfil do Microsoft Graph exibidas no navegador":::

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

A API do Microsoft Graph requer o escopo *user.read* para ler o perfil do usuário. Por padrão, este escopo é adicionado automaticamente a cada aplicativo registrado no portal do Azure. Outras APIs do Microsoft Graph, bem como APIs personalizadas do servidor de back-end, podem exigir escopos adicionais. Por exemplo, a API do Microsoft Graph requer o escopo *Mail.Read* para listar o email do usuário.

À medida que você adiciona escopos, os usuários podem ser solicitados a fornecer consentimento adicional para os escopos adicionados.

Se uma API de back-end não exigir um escopo, o que não é recomendado, você poderá usar `clientId` como o escopo nas chamadas para obter tokens.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

Se quiser se aprofundar no desenvolvimento de aplicativos de página única JavaScript na plataforma de identidade da Microsoft, confira nossa série de cenários com várias partes:

> [!div class="nextstepaction"]
> [Cenário: Aplicativo de página única](scenario-spa-overview.md)