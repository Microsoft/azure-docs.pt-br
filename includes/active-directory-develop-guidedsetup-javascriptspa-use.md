---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 8785f89a335f0ae7d983f267176da1656aee57a0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172528"
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Use a MSAL (Biblioteca de Autenticação da Microsoft) para conectar o usuário

1. Adicione o seguinte código no arquivo `index.html` dentro da marca `<script></script>`:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "Enter_the_Application_Id_here"
            authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };

    var graphConfig = {
        graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
    };

    // this can be used for login or token request, however in more complex situations
    // this can have diverging options
    var requestObj = {
        scopes: ["user.read"]
    };

    var myMSALObj = new Msal.UserAgentApplication(msalConfig);
    // Register Callbacks for redirect flow
    myMSALObj.handleRedirectCallback(authRedirectCallBack);


    function signIn() {

        myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
            //Login Success
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }).catch(function (error) {
            console.log(error);
        });
    }

    function acquireTokenPopupAndCallMSGraph() {
        //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
        myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenPopup(popup window)
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                    callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
                }).catch(function (error) {
                    console.log(error);
                });
            }
        });
    }


    function graphAPICallback(data) {
        document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
    }


    function showWelcomeMessage() {
        var divWelcome = document.getElementById('WelcomeMessage');
        divWelcome.innerHTML = 'Welcome ' + myMSALObj.getAccount().userName + "to Microsoft Graph API";
        var loginbutton = document.getElementById('SignIn');
        loginbutton.innerHTML = 'Sign Out';
        loginbutton.setAttribute('onclick', 'signOut();');
    }


    //This function can be removed if you do not need to support IE
    function acquireTokenRedirectAndCallMSGraph() {
         //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
         myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
             callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
         }).catch(function (error) {
             console.log(error);
             // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
             // Call acquireTokenRedirect
             if (requiresInteraction(error.errorCode)) {
                 myMSALObj.acquireTokenRedirect(requestObj);
             }
         });
     }


    function authRedirectCallBack(error, response) {
        if (error) {
            console.log(error);
        }
        else {
            if (response.tokenType === "access_token") {
                callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
            } else {
                console.log("token type is:" + response.tokenType);
            }
        }
    }

    function requiresInteraction(errorCode) {
        if (!errorCode || !errorCode.length) {
            return false;
        }
        return errorCode === "consent_required" ||
            errorCode === "interaction_required" ||
            errorCode === "login_required";
    }

    // Browser check variables
    var ua = window.navigator.userAgent;
    var msie = ua.indexOf('MSIE ');
    var msie11 = ua.indexOf('Trident/');
    var msedge = ua.indexOf('Edge/');
    var isIE = msie > 0 || msie11 > 0;
    var isEdge = msedge > 0;
    //If you support IE, our recommendation is that you sign-in using Redirect APIs
    //If you as a developer are testing using Edge InPrivate mode, please add "isEdge" to the if check
    // can change this to default an experience outside browser use
    var loginType = isIE ? "REDIRECT" : "POPUP";

    if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
    }
    else if (loginType === 'REDIRECT') {
        document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(requestObj);
        };
        if (myMSALObj.getAccount() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenRedirectAndCallMSGraph();
        }
    } else {
        console.error('Please set a valid login type');
    }
    ```

<!--start-collapse-->
### <a name="more-information"></a>Mais informações

Depois que um usuário clica no botão **Entrar** pela primeira vez, o método `signIn` chama `loginPopup` para conectar o usuário. Esse método abre uma janela pop-up com o *ponto de extremidade da plataforma de identidade da Microsoft* para solicitar e validar as credenciais do usuário. Como resultado de uma entrada bem-sucedida, o usuário é redirecionado para a página *index.html* original e um token é recebido, processado por `msal.js` e as informações contidas no token são armazenadas em cache. Esse token é conhecido como o *token de ID* e contém informações básicas sobre o usuário, como o nome de exibição do usuário. Se você planeja usar os dados fornecidos por esse token para qualquer finalidade, é necessário certificar-se de que esse token seja validado pelo servidor de back-end para ter certeza de que o token foi emitido para um usuário válido para o seu aplicativo.

O SPA gerado por este guia chama `acquireTokenSilent` e/ou `acquireTokenPopup` para adquirir um *token de acesso* usado para consultar a API do Microsoft Graph para obter informações de perfil do usuário. Se você precisar de um exemplo que valida o token de ID, dê uma olhada [neste](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Github active-directory-javascript-singlepageapp-dotnet-webapi-v2 sample") aplicativo de exemplo no GitHub – o exemplo usa uma ASP.NET Web API para validação de token.

#### <a name="getting-a-user-token-interactively"></a>Obtendo um token de usuário interativamente

Depois da entrada inicial, você não deseja solicitar aos usuários que autentiquem novamente sempre que precisam solicitar um token para acessar um recurso – portanto, *acquireTokenSilent* deve ser usado na maioria das vezes para adquirir tokens. No entanto, há situações em que é necessário forçar os usuários a interagir com o ponto de extremidade da plataforma de identidade da Microsoft – alguns exemplos incluem:

- Os usuários precisam reinserir as credenciais deles, pois a senha expirou
- Seu aplicativo está solicitando acesso a um recurso com o qual o usuário precisa concordar
- A autenticação de dois fatores é necessária

Chamar o *acquireTokenPopup* resulta em uma janela pop-up (ou o *acquireTokenRedirect* resulta no redirecionamento dos usuários para o ponto de extremidade da plataforma de identidade do Microsoft) em que os usuários precisam interagir, seja confirmando suas credenciais, fornecendo o consentimento para o recurso necessário ou concluindo a autenticação de dois fatores.

#### <a name="getting-a-user-token-silently"></a>Obtendo um token de usuário no modo silencioso

O método `acquireTokenSilent` manipula as aquisições e a renovação de tokens sem nenhuma interação do usuário. Após `loginPopup` (ou `loginRedirect`) ser executado pela primeira vez, `acquireTokenSilent` é o método normalmente usado para obter tokens usados para acessar recursos protegidos nas próximas chamadas – já que as chamadas para solicitar ou renovar tokens são feitas no modo silencioso.
`acquireTokenSilent` poderá falhar em alguns casos – por exemplo, se a senha do usuário tiver expirado. O aplicativo pode tratar essa exceção de duas maneiras:

1. Faça uma chamada a `acquireTokenPopup` imediatamente, o que resultará na solicitação de entrada do usuário. Esse padrão é usado frequentemente em aplicativos online em que não há nenhum conteúdo não autenticado no aplicativo disponível para o usuário. O exemplo gerado por essa instalação guiada usa esse padrão.

2. Os aplicativos também podem fazer uma indicação visual para o usuário de que uma conexão interativa é necessária e, portanto, o usuário pode escolher o momento certo para se conectar ou o aplicativo pode tentar `acquireTokenSilent` novamente mais tarde. Isso é frequentemente usado quando o usuário consegue usar outras funcionalidades do aplicativo sem ser interrompido – por exemplo, há conteúdo não autenticado disponível no aplicativo. Nesse caso, o usuário pode decidir quando deseja entrar para acessar o recurso protegido ou para atualizar as informações desatualizadas.

> [!NOTE]
> Este início rápido usa os métodos `loginRedirect` e `acquireTokenRedirect` quando o navegador usado for o Internet Explorer devido a um [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relacionado à manipulação de janelas pop-up pelo navegador do Internet Explorer.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Chamar a API do Microsoft Graph usando o token obtido recentemente

Adicione o seguinte código no arquivo `index.html` dentro da marca `<script></script>`:

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre como fazer uma chamada REST em uma API protegida

Nesse aplicativo de exemplo criado por esse guia, o método `callMSGraph()` é usado para fazer uma solicitação HTTP `GET` em um recurso protegido que exige um token e, em seguida, retornar o conteúdo para o chamador. Esse método adiciona o token adquirido no *cabeçalho de Autorização HTTP*. Para o aplicativo de exemplo criado por esse guia, o recurso é o ponto de extremidade *me* da API do Microsoft Graph – que exibe as informações de perfil do usuário.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adicionar um método para desconectar o usuário

Adicione o seguinte código no arquivo `index.html` dentro da marca `<script></script>`:

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```
