---
title: Inicializar aplicativos cliente do MSAL.js | Azure
titleSuffix: Microsoft identity platform
description: Saiba como inicializar aplicativos cliente usando a biblioteca de autenticação da Microsoft para JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/17/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: a6d7b760ffd1931fa5dcdb3a67dd02f2798957a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100365830"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inicializar aplicativos cliente usando MSAL.js

Este artigo descreve como inicializar a biblioteca de autenticação da Microsoft para JavaScript (MSAL.js) com uma instância de um aplicativo de agente do usuário.

O aplicativo de agente de usuário é uma forma de aplicativo cliente público em que o código do cliente é executado em um agente de usuário, como um navegador da Web. Esses clientes não armazenam segredos porque o contexto do navegador é acessível de forma aberta.

Para saber mais sobre os tipos de aplicativo cliente e opções de configuração de aplicativo, consulte [aplicativos cliente públicos e confidenciais no MSAL](msal-client-applications.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de inicializar um aplicativo, primeiro você precisa [registrá-lo com o portal do Azure](scenario-spa-app-registration.md), estabelecendo uma relação de confiança entre seu aplicativo e a plataforma de identidade da Microsoft.

Depois de registrar seu aplicativo, você precisará de alguns ou de todos os valores a seguir que possam ser encontrados no portal do Azure.

| Valor | Obrigatório | Descrição |
|:----- | :------: | :---------- |
| ID do aplicativo (cliente) | Obrigatório | Um GUID que identifica exclusivamente seu aplicativo na plataforma de identidade da Microsoft. |
| Authority | Opcional | A URL do provedor de identidade (a *instância*) e o *público-alvo de entrada* para seu aplicativo. A instância e o público de entrada, quando concatenados, compõem a *autoridade*. |
| ID do diretório (locatário) | Opcional | Especifique isso se você estiver criando um aplicativo de linha de negócios somente para sua organização, geralmente chamado de *aplicativo de locatário único*. |
| URI de redirecionamento | Opcional | Se você estiver criando um aplicativo Web, o `redirectUri` especifica onde o provedor de identidade (a plataforma de identidade da Microsoft) deve retornar os tokens de segurança que ele emitiu. |

## <a name="initialize-msaljs-2x-apps"></a>Inicializar aplicativos MSAL.js 2. x

Inicialize o contexto de autenticação MSAL instanciando um [PublicClientApplication][msal-js-publicclientapplication] com um objeto de [configuração][msal-js-configuration] . A propriedade de configuração mínima necessária é a `clientID` do seu aplicativo, mostrada como a **ID do aplicativo (cliente)** na página **visão geral** do registro do aplicativo no portal do Azure.

Aqui está um exemplo de objeto de configuração e instanciação de um `PublicClientApplication` :

```javascript
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111",
        authority: "https://login.microsoftonline.com/common",
        knownAuthorities: [],
        redirectUri: "https://localhost:3001",
        postLogoutRedirectUri: "https://localhost:3001/logout",
        navigateToLoginRequestUrl: true
    },
    cache: {
        cacheLocation: "sessionStorage",
        storeAuthStateInCookie: false
    },
    system: {
        loggerOptions: {
            loggerCallback: (level: LogLevel, message: string, containsPii: boolean): void => {
                if (containsPii) {
                    return;
                }
                switch (level) {
                    case LogLevel.Error:
                        console.error(message);
                        return;
                    case LogLevel.Info:
                        console.info(message);
                        return;
                    case LogLevel.Verbose:
                        console.debug(message);
                        return;
                    case LogLevel.Warning:
                        console.warn(message);
                        return;
                }
            },
            piiLoggingEnabled: false
        },
        windowHashTimeout: 60000,
        iframeHashTimeout: 6000,
        loadFrameTimeout: 0
    }
};

// Create an instance of PublicClientApplication
const msalInstance = new PublicClientApplication(msalConfig);

// Handle the redirect flows
msalInstance.handleRedirectPromise().then((tokenResponse) => {
    // Handle redirect response
}).catch((error) => {
    // Handle redirect error
});
```

### `handleRedirectPromise`

Invoque [handleRedirectPromise][msal-js-handleredirectpromise] quando seu aplicativo usa os fluxos de redirecionamento. Ao usar os fluxos de redirecionamento, o `handleRedirectPromise` deve ser executado em cada carregamento de página.

Há três resultados possíveis da promessa:

- `.then` é invocado e `tokenResponse` é a verdade: o aplicativo está retornando de uma operação de redirecionamento que foi bem-sucedida.
- `.then` é invocado e `tokenResponse` é false ( `null` ): o aplicativo não está retornando de uma operação de redirecionamento.
- `.catch` é invocado: o aplicativo está retornando de uma operação de redirecionamento e ocorreu um erro.

## <a name="initialize-msaljs-1x-apps"></a>Inicializar aplicativos MSAL.js 1. x

Inicialize o contexto de autenticação MSAL 1. x instanciando um [UserAgentApplication][msal-js-useragentapplication] com um objeto de configuração. A propriedade de configuração mínima necessária é a `clientID` do seu aplicativo, mostrada como a **ID do aplicativo (cliente)** na página **visão geral** do registro do aplicativo no portal do Azure.

Para métodos de autenticação com fluxos de redirecionamento ([loginRedirect][msal-js-loginredirect] e [acquireTokenRedirect][msal-js-acquiretokenredirect]) no MSAL.js 1.2. x ou anterior, você deve registrar explicitamente um retorno de chamada para êxito ou erro por meio do `handleRedirectCallback()` método. O registro explícito do retorno de chamada é necessário no MSAL.js 1.2. x e anteriores, pois os fluxos de redirecionamento não retornam promessas como os métodos com uma experiência de pop-up. O registro do retorno de chamada é *opcional* no MSAL.js versão 1.3. x e posterior.

```javascript
// Configuration object constructed
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111"
    }
}

// Create UserAgentApplication instance
const msalInstance = new UserAgentApplication(msalConfig);

function authCallback(error, response) {
    // Handle redirect response
}

// Register a redirect callback for Success or Error (when using redirect methods)
// **REQUIRED** in MSAL.js 1.2.x and earlier
// **OPTIONAL** in MSAL.js 1.3.x and later
msalInstance.handleRedirectCallback(authCallback);
```

## <a name="single-instance-and-configuration"></a>Única instância e configuração

Ambos MSAL.js 1. x e 2. x são projetados para ter uma única instância e configuração do `UserAgentApplication` ou `PublicClientApplication` , respectivamente, para representar um único contexto de autenticação.

Várias instâncias do `UserAgentApplication` ou `PublicClientApplication` não são recomendadas, pois causam entradas e comportamento conflitantes do cache no navegador.

## <a name="next-steps"></a>Próximas etapas

Este exemplo de código do MSAL.js 2. x no GitHub demonstra a instanciação de um [PublicClientApplication][msal-js-publicclientapplication] com um objeto de [configuração][msal-js-configuration] :

[Azure-Samples/MS-Identity-JavaScript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2)

<!-- LINKS - External -->
[msal-browser]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/
[msal-core]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/
[msal-js-acquiretokenredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html#acquiretokenredirect
[msal-js-configuration]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal.html#configuration
[msal-js-handleredirectpromise]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html#handleredirectpromise
[msal-js-loginredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html#loginredirect
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html
