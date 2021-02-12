---
title: Tratar erros e exceções em MSAL.js
titleSuffix: Microsoft identity platform
description: Saiba como lidar com erros e exceções, desafios de declarações de acesso condicional e novas tentativas em aplicativos MSAL.js.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, hahamil
ms.custom: aaddev
ms.openlocfilehash: 20d276aba2ee3260911748cbee0a16020270059a
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761346"
---
# <a name="handle-errors-and-exceptions-in-msaljs"></a>Tratar erros e exceções em MSAL.js

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msaljs"></a>Tratamento de erro no MSAL.js

A MSAL.js fornece objetos de erro que resume e classificam os diferentes tipos de erros comuns. Ele também fornece uma interface para acessar detalhes específicos dos erros, como mensagens de erro para tratá-los adequadamente.

### <a name="error-object"></a>Objeto de erro

```javascript
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```

Ao estender a classe do erro, você terá acesso às seguintes propriedades:
- `AuthError.message`:  igual a `errorMessage`.
- `AuthError.stack`: Rastreamento de pilha para erros gerados.

### <a name="error-types"></a>Tipos de erro

Os tipos de erro a seguir estão disponíveis:

- `AuthError`: Classe de erro base para a biblioteca MSAL.js, também usada para erros inesperados.

- `ClientAuthError`: classe de erro, a qual indica um problema com a Autenticação de Cliente. A maioria dos erros originados da biblioteca serão ClientAuthErrors. Esses erros resultam de coisas como chamar um método de logon quando o logon já está em andamento, q o usuário cancela o logon, e assim por diante.

- `ClientConfigurationError`: classe de erro, a qual estende o `ClientAuthError` gerado antes que as solicitações sejam feitas quando os parâmetros de configuração do usuário fornecidos estiverem malformados ou ausentes.

- `ServerError`: classe de erro, a qual representa as cadeias de caracteres de erro enviadas pelo servidor de autenticação. Esses erros poderão ser: parâmetros ou formatos da solicitação inválidos ou qualquer outro erro que impeça o servidor de autenticar ou autorizar o usuário.

- `InteractionRequiredAuthError`: classe de erro, a qual estende o `ServerError` para representar erros do servidor que exigem uma chamada interativa. Esse erro será gerado por `acquireTokenSilent` caso o usuário precise interagir com o servidor para fornecer credenciais ou consentimentos para autenticação/autorização. São exemplos de códigos de erro: `"interaction_required"`, `"login_required"` e `"consent_required"`.

Para tratamento de erro em fluxos de autenticação com métodos de redirecionamento (`loginRedirect`, `acquireTokenRedirect`) você precisará registrar o retorno de chamada, o qual será chamado com êxito ou falha após redirecionamento usando o método `handleRedirectCallback()` conforme a seguir:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Os métodos para experiência de pop-up (`loginPopup`, `acquireTokenPopup`) retornam promessas, de modo que seja possível usar o padrão de promessa (.then e .catch) para tratá-los, conforme mostrado:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Erros que exigem interação

Um erro será retornado quando você tentar usar um método não interativo de aquisição de um token, como `acquireTokenSilent`, sem que a MSAL pudesse fazê-lo silenciosamente.

As possíveis razões são:

- é necessário entrar
- é necessário consentir
- é necessário passar por uma experiência de autenticação multifator.

A correção é chamar um método interativo como `acquireTokenPopup` ou `acquireTokenRedirect`:

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

Ao obter tokens silenciosamente (com `acquireTokenSilent`) usando a MSAL.js, o aplicativo poderá receber erros quando um [desafio de declarações de acesso condicional](../azuread-dev/conditional-access-dev-guide.md), como a Política de MFA, for exigido por uma API que você está tentando acessar.

O padrão para tratar esse erro é fazer uma chamada interativa para obter o token em MSAL.js, como `acquireTokenPopup` ou `acquireTokenRedirect`, conforme no exemplo a seguir:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // call API
}).catch(function(error) {
    if (error instanceof InteractionRequiredAuthError) {
    
        // extract, if exists, claims from error message
        if (error.ErrorMessage.claims) {
            accessTokenRequest.claimsRequest = JSON.stringify(error.ErrorMessage.claims);
        }
        
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // call API
        }).catch(function(error) {
            console.log(error);
        });
    }
});
```

A obtenção interativa do token solicita a participação do usuário e oferece a oportunidade para atender à política de acesso condicional exigida.

Ao chamar uma API que exige acesso condicional, você poderá receber um desafio de declarações no erro da API. Nesse caso, você pode passar as declarações retornadas no erro para o campo `claimsRequest` da classe `AuthenticationParameters.ts` de modo a atender à política apropriada. 

Consulte [Solicitar declarações adicionais](active-directory-optional-claims.md) para obter mais detalhes.


[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Próximas etapas

Considere habilitar o [log em MSAL.js](msal-logging-js.md) para ajudá-lo a diagnosticar e depurar problemas.
