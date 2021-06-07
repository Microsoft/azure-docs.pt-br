---
title: Entrada do aplicativo de página única & logout
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo de página única (entrada)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: bdfffbf8d2e416c87dd5abb8f6383c58ad270231
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584356"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Aplicativo de página única: entrar e sair

Saiba como adicionar entrada ao código para seu aplicativo de página única.

Antes que você possa obter tokens para acessar APIs em seu aplicativo, você precisa de um contexto de usuário autenticado. Você pode conectar usuários ao seu aplicativo no MSAL.js de duas maneiras:

* [Janela pop-up](#sign-in-with-a-pop-up-window), usando o `loginPopup` método
* [Redirecionar](#sign-in-with-redirect), usando o `loginRedirect` método

Opcionalmente, você também pode passar os escopos das APIs para as quais você precisa que o usuário consenti no momento da entrada.

> [!NOTE]
> Se o seu aplicativo já tiver acesso a um contexto de usuário autenticado ou a um token de ID, você poderá ignorar a etapa de logon e adquirir tokens diretamente. Para obter detalhes, consulte [SSO sem MSAL.js logon](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Escolhendo entre uma experiência de pop-up ou de redirecionamento

Você não pode usar os métodos pop-up e Redirect em seu aplicativo. A escolha entre uma experiência de pop-up ou de redirecionamento depende do seu fluxo de aplicativo:

* Se você não quiser que os usuários se afastem da página principal do aplicativo durante a autenticação, recomendamos o método pop-up. Como o redirecionamento de autenticação ocorre em uma janela pop-up, o estado do aplicativo principal é preservado.

* Se os usuários tiverem restrições de navegador ou políticas em que as janelas pop-up estiverem desabilitadas, você poderá usar o método Redirect. Use o método redirecionar com o navegador Internet Explorer, pois há [problemas conhecidos com janelas pop-up no Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

## <a name="sign-in-with-a-pop-up-window"></a>Entrar com uma janela pop-up


# <a name="javascript-msaljs-2x"></a>[JavaScript (MSAL.js 2. x)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let username = "";

const myMsal = new PublicClientApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success

        // In case multiple accounts exist, you can select
        const currentAccounts = myMsal.getAllAccounts();
    
        if (currentAccounts === null) {
            // no accounts detected
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
        }
    
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="javascript-msaljs-1x"></a>[JavaScript (MSAL.js 1. x)](#tab/javascript1)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="angular"></a>[Angular](#tab/angular)

O wrapper angular MSAL permite que você proteja rotas específicas em seu aplicativo adicionando `MsalGuard` à definição de rota. Essa proteção invocará o método para entrar quando a rota for acessada.

```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  {
    path: 'profile',
    component: ProfileComponent,
    canActivate: [
      MsalGuard
    ]
  },
  {
    path: '',
    component: HomeComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: false })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

Para uma experiência de janela pop-up, habilite a `popUp` opção de configuração. Você também pode passar os escopos que exigem consentimento da seguinte maneira:

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
            }
        }, {
            popUp: true,
            consentScopes: ["User.ReadWrite"]
        })
    ]
})
```
---

## <a name="sign-in-with-redirect"></a>Entrar com redirecionamento

# <a name="javascript-msaljs-2x"></a>[JavaScript (MSAL.js 2. x)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let username = "";

const myMsal = new PublicClientApplication(config);

function handleResponse(response) {
    //handle redirect response

    // In case multiple accounts exist, you can select
    const currentAccounts = myMsal.getAllAccounts();

    if (currentAccounts === null) {
        // no accounts detected
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
    }
}

myMsal.handleRedirectPromise(handleResponse);

myMsal.loginRedirect(loginRequest);
```

# <a name="javascript-msaljs-1x"></a>[JavaScript (MSAL.js 1. x)](#tab/javascript1)

Os métodos de redirecionamento não retornam uma promessa devido à afastamento do aplicativo principal. Para processar e acessar os tokens retornados, registre os retornos de chamada de êxito e erro antes de chamar os métodos de redirecionamento.

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

myMsal.handleRedirectCallback(authCallback);

myMsal.loginRedirect(loginRequest);
```

# <a name="angular"></a>[Angular](#tab/angular)

O código aqui é o mesmo descrito anteriormente na seção sobre como entrar com uma janela pop-up. O fluxo padrão é redirecionar.

---

## <a name="sign-out"></a>Sair

A biblioteca MSAL fornece um `logout` método que limpa o cache no armazenamento do navegador e envia uma solicitação de saída para o Azure Active Directory (Azure AD). Após a saída, a biblioteca redireciona de volta para a página inicial do aplicativo por padrão.

Você pode configurar o URI para o qual ele deve ser redirecionado após a configuração `postLogoutRedirectUri` . Esse URI também deve ser registrado como o URI de logout no registro do aplicativo.

# <a name="javascript-msaljs-2x"></a>[JavaScript (MSAL.js 2. x)](#tab/javascript2)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new PublicClientApplication(config);

// you can select which account application should sign out
const logoutRequest = {
    account: myMsal.getAccountByUsername(username)
}

myMsal.logout(logoutRequest);
```

# <a name="javascript-msaljs-1x"></a>[JavaScript (MSAL.js 1. x)](#tab/javascript1)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new UserAgentApplication(config);

myMsal.logout();
```

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            }
        })
    ]
})

// In app.component.ts
this.authService.logout();
```

---

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, [adquirindo um token para o aplicativo](scenario-spa-acquire-token.md).