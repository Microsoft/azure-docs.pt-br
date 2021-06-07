---
title: 'Tutorial: Criar um aplicativo de página única JavaScript que usa a plataforma de identidade da Microsoft para autenticação | Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, você criará um SPA (aplicativo de página única) JavaScript que usa a plataforma de identidade da Microsoft para conectar usuários e receberá um token de acesso para chamar a API do Microsoft Graph em nome deles.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/06/2020
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: dab4ac22a8b0da927f05376755463885ce32c343
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100102998"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Tutorial: Conectar usuários e chamar a API do Microsoft Graph de um SPA (aplicativo de página única) JavaScript

Neste tutorial, você criará um SPA (aplicativo de página única) JavaScript que conecta usuários e chama o Microsoft Graph usando o fluxo implícito. O SPA que você cria usa a MSAL (Biblioteca de Autenticação da Microsoft) para JavaScript v1.0.

Neste tutorial:

> [!div class="checklist"]
> * Criar um projeto em JavaScript com `npm`
> * Registrar o aplicativo no portal do Azure
> * Adicionar código para entrada e saída do usuário
> * Adicionar código para chamar a API do Microsoft Graph
> * Testar o aplicativo

>[!TIP]
> Este tutorial usa a MSAL.js v1.x, que é limitada ao uso do fluxo de concessão implícita para aplicativos de página única. Recomendamos que todos os aplicativos novos usem a [MSAL.js 2.x e o fluxo do código de autorização com suporte para PKCE e CORS](tutorial-v2-javascript-auth-code.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/download/) para executar um servidor Web local.
* [Visual Studio Code](https://code.visualstudio.com/download) ou outro editor para modificar arquivos de projeto.
* Um navegador da Web moderno. **Não há suporte** para o **Internet Explorer** no aplicativo que você cria neste tutorial devido ao uso do aplicativo de convenções [ES6](http://www.ecma-international.org/ecma-262/6.0/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como o aplicativo de exemplo gerado por este guia funciona

![Mostra como funciona o aplicativo de exemplo gerado por este tutorial](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

O aplicativo de exemplo criado por este guia permite que um SPA JavaScript consulte a API do Microsoft Graph ou uma API Web que aceita tokens da plataforma de identidade da Microsoft. Nesse cenário, depois que um usuário se conecta, um token de acesso é adicionado às solicitações HTTP por meio do cabeçalho de autorização. Esse token será usado para adquirir o perfil e os emails do usuário por meio da **API do MS Graph**.

A aquisição e a renovação de tokens são manipuladas pela [MSAL (Biblioteca de Autenticação da Microsoft) para JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js).

## <a name="set-up-your-web-server-or-project"></a>Configurar o servidor Web ou o projeto

> Prefere baixar este projeto de exemplo? [Baixe os arquivos de projeto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> Pule para a [etapa de configuração](#register-your-application) a fim de configurar o exemplo de código antes de executá-lo.

## <a name="create-your-project"></a>Criar seu projeto

Verifique se você tem o [Node.js](https://nodejs.org/en/download/) instalado e, em seguida, crie uma pasta para hospedar seu aplicativo. Lá, implementaremos um servidor Web simples do [Express](https://expressjs.com/) para atender ao seu arquivo `index.html`.

1. Usando um terminal (como Visual Studio Code terminal integrado), localize a pasta do projeto e digite:

   ```console
   npm init
   ```

2. Em seguida, instale as dependências necessárias:

   ```console
   npm install express --save
   npm install morgan --save
   ```

1. Agora, crie um arquivo .js chamado `server.js` e, depois, adicione o seguinte código:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');

   //initialize express.
   const app = express();

   // Initialize variables.
   const port = 3000; // process.env.PORT || 3000;

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use(express.static('JavaScriptSPA'))

   // Set up a route for index.html.
   app.get('*', function (req, res) {
       res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log('Listening on port ' + port + '...');
   ```

Agora você tem um servidor simples para atender ao seu SPA. A estrutura de pastas pretendida no final deste tutorial é a seguinte:

![uma representação de texto da estrutura de pastas do SPA desejada](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>Criar a interface do usuário do SPA

1. Crie um arquivo `index.html` para seu JavaScript SPA. Esse arquivo implementa uma interface do usuário criada com o **Bootstrap 4 Framework** e importa arquivos de script para configuração, autenticação e chamada à API.

   No arquivo `index.html`, adicione o seguinte código:

   ```html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
       <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

       <!-- msal.js with a fallback to backup CDN -->
       <script type="text/javascript" src="https://alcdn.msauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
       <script type="text/javascript">
         if(typeof Msal === 'undefined')document.write(unescape("%3Cscript src='https://alcdn.msftauth.net/lib/1.2.1/js/msal.js' type='text/javascript' integrity='sha384-m/3NDUcz4krpIIiHgpeO0O8uxSghb+lfBTngquAo2Zuy2fEF+YgFeP08PWFo5FiJ' crossorigin='anonymous'%3E%3C/script%3E"));
       </script>

       <!-- adding Bootstrap 4 for UI components  -->
       <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
     </head>
     <body>
       <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
         <a class="navbar-brand" href="/">MS Identity Platform</a>
         <div class="btn-group ml-auto dropleft">
           <button type="button" id="signIn" class="btn btn-secondary" onclick="signIn()">Sign In</button>
           <button type="button" id="signOut" class="btn btn-success d-none" onclick="signOut()">Sign Out</button>
       </div>
       </nav>
       <br>
       <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
       <br>
       <div class="row" style="margin:auto" >
       <div id="card-div" class="col-md-3 d-none">
       <div class="card text-center">
         <div class="card-body">
           <h5 class="card-title" id="welcomeMessage">Please sign-in to see your profile and read your mails</h5>
           <div id="profile-div"></div>
           <br>
           <br>
           <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
           <br>
           <br>
           <button class="btn btn-primary d-none" id="readMail" onclick="readMail()">Read Mails</button>
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

       <!-- replace next line with authRedirect.js if you would like to use the redirect flow -->
       <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
       <script type="text/javascript" src="./authPopup.js"></script>
       <script type="text/javascript" src="./graph.js"></script>
     </body>
   </html>
   ```

   > [!TIP]
   > Você pode substituir a versão de MSAL.js no script anterior pela versão mais recente em [versões de MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

2. Agora, crie um arquivo .js chamado `ui.js`, que acessará e atualizará os elementos DOM, depois adicione a ele o seguinte código:

   ```JavaScript
   // Select DOM elements to work with
   const welcomeDiv = document.getElementById("welcomeMessage");
   const signInButton = document.getElementById("signIn");
   const signOutButton = document.getElementById('signOut');
   const cardDiv = document.getElementById("card-div");
   const mailButton = document.getElementById("readMail");
   const profileButton = document.getElementById("seeProfile");
   const profileDiv = document.getElementById("profile-div");

   function showWelcomeMessage(account) {
     // Reconfiguring DOM elements
     cardDiv.classList.remove('d-none');
     welcomeDiv.innerHTML = `Welcome ${account.name}`;
     signInButton.classList.add('d-none');
     signOutButton.classList.remove('d-none');
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

Antes de continuar com a autenticação, registre seu aplicativo no **Azure Active Directory**.

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
1. Pesquise **Azure Active Directory** e selecione-o.
1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
1. Insira um **Nome** para seu aplicativo. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
1. Na seção **URI de Redirecionamento**, selecione a plataforma **Web** na lista suspensa e defina o valor para a URL do aplicativo com base em seu servidor Web.
1. Selecione **Registrar**.
1. Na página **Visão geral** do aplicativo, anote o valor de **ID do aplicativo (cliente)** para uso posterior.
1. Em **Gerenciar**, selecione **Autenticação**.
1. Na seção **Concessão implícita e fluxos híbridos**, selecione **Tokens de ID** e **Tokens de acesso**. Os tokens de ID e tokens de acesso são necessários porque esse aplicativo precisa conectar usuários e chamar uma API.
1. Clique em **Salvar**.

> ### <a name="set-a-redirect-url-for-nodejs"></a>Configurar a URL de redirecionamento para o Node.js
>
> Para o Node.js, você pode definir a porta do servidor Web no arquivo *server.js*. Este tutorial usa a porta 3000, mas você pode usar qualquer outra porta disponível.
>
> Para configurar uma URL de redirecionamento nas informações de registro do aplicativo, volte para o painel **Registro de Aplicativo** e siga um destes procedimentos:
>
> - Defina *`http://localhost:3000/`* como a **URL de Redirecionamento**.
> - Se você estiver usando uma porta TCP personalizada, use *`http://localhost:<port>/`* (em que *\<port>* é o número da porta TCP personalizada).
>   1. Copie o valor da **URL**.
>   1. Volte para o painel **Registro de Aplicativo** e cole o valor copiado como uma **URL de Redirecionamento**.
>

### <a name="configure-your-javascript-spa"></a>Configurar o JavaScript SPA

Crie um arquivo .js chamado `authConfig.js`, que conterá os parâmetros de configuração para autenticação, depois adicione a ele o seguinte código:

```javascript
  const msalConfig = {
    auth: {
      clientId: "Enter_the_Application_Id_Here",
      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
      redirectUri: "Enter_the_Redirect_Uri_Here",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

  // Add here scopes for id token to be used at MS Identity Platform endpoints.
  const loginRequest = {
    scopes: ["openid", "profile", "User.Read"]
  };

  // Add here scopes for access token to be used at MS Graph API endpoints.
  const tokenRequest = {
    scopes: ["Mail.Read"]
  };
```

 Em que:
 - *\<Enter_the_Application_Id_Here>* é a **ID do Aplicativo (cliente)** que você registrou.
 - *\<Enter_the_Cloud_Instance_Id_Here>* é a instância da nuvem do Azure. Para a nuvem principal ou global do Azure, basta inserir *https://login.microsoftonline.com* . Para nuvens **nacionais** (por exemplo, China), confira [Nuvens nacionais](./authentication-national-cloud.md).
 - *\<Enter_the_Tenant_info_here>* é definido como uma das seguintes opções:
   - Se o seu aplicativo for compatível com as *contas neste diretório organizacional*, substitua esse valor pela **ID do locatário** ou **Nome do locatário** (por exemplo, *contoso.microsoft.com*).
   - Se o aplicativo for compatível com as *contas em qualquer diretório organizacional*, substitua esse valor por **organizações**.
   - Se o seu aplicativo for compatível com as *contas em qualquer diretório organizacional e contas pessoais da Microsoft*, substitua esse valor por **comum**. Para restringir o suporte a *contas pessoais da Microsoft*, substitua esse valor por **consumidores**.


## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Use a MSAL (Biblioteca de Autenticação da Microsoft) para conectar o usuário

Crie um arquivo .js chamado `authPopup.js`, que conterá sua lógica de aquisição de token e autenticação, depois adicione a ele o seguinte código:

   ```JavaScript
   const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   function signIn() {
     myMSALObj.loginPopup(loginRequest)
       .then(loginResponse => {
         console.log('id_token acquired at: ' + new Date().toString());
         console.log(loginResponse);

         if (myMSALObj.getAccount()) {
           showWelcomeMessage(myMSALObj.getAccount());
         }
       }).catch(error => {
         console.log(error);
       });
   }

   function signOut() {
     myMSALObj.logout();
   }

   function callMSGraph(theUrl, accessToken, callback) {
       var xmlHttp = new XMLHttpRequest();
       xmlHttp.onreadystatechange = function () {
           if (this.readyState == 4 && this.status == 200) {
              callback(JSON.parse(this.responseText));
           }
       }
       xmlHttp.open("GET", theUrl, true); // true for asynchronous
       xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
       xmlHttp.send();
   }

   function getTokenPopup(request) {
     return myMSALObj.acquireTokenSilent(request)
       .catch(error => {
         console.log(error);
         console.log("silent token acquisition fails. acquiring token using popup");

         // fallback to interaction when silent call fails
           return myMSALObj.acquireTokenPopup(request)
             .then(tokenResponse => {
               return tokenResponse;
             }).catch(error => {
               console.log(error);
             });
       });
   }

   function seeProfile() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(loginRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
           profileButton.classList.add('d-none');
           mailButton.classList.remove('d-none');
         }).catch(error => {
           console.log(error);
         });
     }
   }

   function readMail() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(tokenRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
         }).catch(error => {
           console.log(error);
         });
     }
   }
   ```

### <a name="more-information"></a>Mais informações

Depois que um usuário selecione o botão **Entrar** pela primeira vez, o método `signIn` chama `loginPopup` para conectar o usuário. Esse método abre uma janela pop-up com o *ponto de extremidade da plataforma de identidade da Microsoft* para solicitar e validar as credenciais do usuário. Após entrar com êxito, o usuário é redirecionado de volta para a página *index.html* original. Um token é recebido, processado por `msal.js` e as informações contidas no token são armazenadas em cache. Esse token é conhecido como o *token de ID* e contém informações básicas sobre o usuário, como o nome de exibição do usuário. Se você planeja usar os dados fornecidos por esse token para qualquer finalidade, verifique se esse token foi validado pelo servidor de back-end para garantir que o token foi emitido para um usuário válido do seu aplicativo.

O SPA gerado por este guia chama `acquireTokenSilent` e/ou `acquireTokenPopup` para adquirir um *token de acesso* usado para consultar a API do Microsoft Graph para obter informações de perfil do usuário. Se você precisar de um exemplo que valide o token da ID, examine [este](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Exemplo do GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2") aplicativo de exemplo no GitHub. Esta amostra usa um ASP.NET Web API para validação de token.

#### <a name="get-a-user-token-interactively"></a>Obter um token de usuário interativamente

Depois da entrada inicial, você não deseja solicitar aos usuários que autentiquem novamente sempre que precisam solicitar um token para acessar um recurso. Portanto, *acquireTokenSilent* deve ser usado na maioria das vezes para adquirir tokens. Entretanto, há situações em que você força os usuários a interagir com a plataforma de identidade da Microsoft. Os exemplos incluem:

- Os usuários precisam reinserir suas credenciais, pois a senha expirou.
- Seu aplicativo está solicitando acesso a um recurso e você precisa do consentimento do usuário.
- A autenticação de dois fatores é necessária.

Chamar *acquireTokenPopup* abre uma janela pop-up (ou *acquireTokenRedirect* redireciona os usuários para a plataforma de identidade da Microsoft). Nessa janela, os usuários precisam interagir confirmando suas credenciais, concedendo consentimento ao recurso necessário ou concluindo a autenticação de dois fatores.

#### <a name="get-a-user-token-silently"></a>Obter um token de usuário no modo silencioso

O método `acquireTokenSilent` manipula a aquisição e a renovação de tokens sem nenhuma interação do usuário. Após `loginPopup` (ou `loginRedirect`) ser executado pela primeira vez, `acquireTokenSilent` é o método usado normalmente para obter tokens usados para acessar recursos protegidos nas próximas chamadas. (Chamadas para solicitar ou renovar tokens são feitas de modo silencioso.) `acquireTokenSilent` pode falhar em alguns casos. Por exemplo, a senha do usuário pode ter expirado. O aplicativo pode tratar essa exceção de duas maneiras:

1. Fazer uma chamada para `acquireTokenPopup` imediatamente, o que dispara um prompt de entrada do usuário. Esse padrão é usado frequentemente em aplicativos online em que não há nenhum conteúdo não autenticado no aplicativo disponível para o usuário. O exemplo gerado por essa instalação guiada usa esse padrão.

1. Os aplicativos também podem fazer uma indicação visual para o usuário de que uma conexão interativa é necessária e, portanto, o usuário pode escolher o momento certo para se conectar ou o aplicativo pode tentar `acquireTokenSilent` novamente mais tarde. Normalmente, isso é usado quando o usuário pode usar outra funcionalidade do aplicativo sem ser interrompido. Por exemplo, pode haver conteúdo não autenticado disponível no aplicativo. Nessa situação, o usuário pode decidir quando deseja entrar para acessar o recurso protegido ou para atualizar as informações desatualizadas.

> [!NOTE]
> Este guia de início rápido usa os métodos `loginPopup` e `acquireTokenPopup` por padrão. Se você estiver usando o Internet Explorer como seu navegador, é recomendável usar os métodos `loginRedirect` e `acquireTokenRedirect`, devido a um [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relacionado à maneira como o Internet Explorer lida com janelas pop-up. Se você quiser ver como obter o mesmo resultado usando `Redirect methods`, confira [estas informações](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js).

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Chamar a API do Microsoft Graph usando o token adquirido recentemente

1. Primeiro, crie um arquivo .js chamado `graphConfig.js`, que armazenará seus pontos de extremidade REST. Adicione os códigos a seguir:

   ```JavaScript
      const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
        graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
      };
   ```

   Em que:
   - *\<Enter_the_Graph_Endpoint_Here>* é a instância da API do MS Graph. Para o ponto de extremidade global da API do MS Graph, basta substituir essa cadeia de caracteres por `https://graph.microsoft.com`. Para implantações de nuvem nacional, confira [Documentação da API do Graph](/graph/deployments).

1. Em seguida, crie um arquivo .js chamado `graph.js`, que fará uma chamada REST para a API do Microsoft Graph, depois adicione a ele o seguinte código:

   ```javascript
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
       .catch(error => console.log(error))
   }
   ```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre como fazer uma chamada REST em uma API protegida

No aplicativo de exemplo criado por este guia, o método `callMSGraph()` é usado para fazer uma solicitação HTTP `GET` em um recurso protegido que exige um token. Depois, a solicitação retorna o conteúdo para o chamador. Esse método adiciona o token adquirido no *cabeçalho de Autorização HTTP*. Para o aplicativo de exemplo criado por esse guia, o recurso é o ponto de extremidade *me* da API do Microsoft Graph, que exibe as informações de perfil do usuário.

## <a name="test-your-code"></a>Testar seu código

1. Configure o servidor para escutar uma porta TCP com base no local do seu arquivo *index.html*. Para o Node.js, inicie o servidor Web para escutar a porta executando os comandos a seguir em um prompt de linha de comando da pasta de aplicativo:

   ```bash
   npm install
   npm start
   ```
1. No navegador, digite **http://localhost:3000** ou **http://localhost:{port}** , em que *port* é a porta na qual o servidor Web está escutando. Você deve ver o conteúdo do seu arquivo *index.html* e o botão **Entrar**.

Depois que o navegador carregar seu arquivo *index.html*, selecione **Entrar**. Você deverá entrar na plataforma de identidade da Microsoft:

![A janela de entrada do conta SPA do JavaScript](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Fornecer autorização para acesso de aplicativo

Na primeira vez que entrar no aplicativo, você será solicitado a conceder acesso ao seu perfil e a conectá-lo:

![A janela de "Permissões solicitadas"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Veja os resultados de aplicativo

Após você entrar, suas informações de perfil do usuário são retornadas na resposta da API do Microsoft Graph exibida:

![Resultados esperados da chamada da API do Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

A API do Microsoft Graph requer o escopo *user.read* para ler o perfil do usuário. Por padrão, este escopo é adicionado automaticamente a cada aplicativo registrado no portal de registro. Outras APIs do Microsoft Graph, bem como APIs personalizadas do servidor de back-end, podem exigir escopos adicionais. Por exemplo, a API do Microsoft Graph requer o escopo *Mail.Read* para listar os emails do usuário.

> [!NOTE]
> Talvez o usuário precise fornecer autorizações adicionais à medida que o número de escopos aumentar.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

Aprofunde-se no desenvolvimento de SPA (aplicativo de página única) na plataforma de identidade da Microsoft em nossa série de artigos de cenário de várias partes.

> [!div class="nextstepaction"]
> [Cenário: Aplicativo de página única](scenario-spa-overview.md)
