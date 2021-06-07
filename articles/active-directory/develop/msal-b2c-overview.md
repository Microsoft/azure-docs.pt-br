---
title: Usar MSAL.js com Azure AD B2C
titleSuffix: Microsoft identity platform
description: A biblioteca de autenticação da Microsoft para JavaScript (MSAL.js) permite que os aplicativos funcionem com Azure AD B2C e adquirem tokens para chamar APIs da Web protegidas. Essas APIs Web podem ser o Microsoft Graph, outras APIs da Microsoft, APIs Web de terceiros ou sua própria API Web.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/05/2020
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev devx-track-js
ms.openlocfilehash: 53294a30b38bed7ab7516443277cac24e4fef4c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98063715"
---
# <a name="use-the-microsoft-authentication-library-for-javascript-to-work-with-azure-ad-b2c"></a>Use a biblioteca de autenticação da Microsoft para JavaScript para trabalhar com Azure AD B2C

A [biblioteca de autenticação da Microsoft para JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) permite que os desenvolvedores de JavaScript autentiquem usuários com identidades sociais e locais usando [Azure Active Directory B2C](../../active-directory-b2c/overview.md) (Azure ad B2C).

Usando Azure AD B2C como um serviço de gerenciamento de identidade, você pode personalizar e controlar como os clientes se inscrevem, entram e gerenciam seus perfis quando usam seus aplicativos. Azure AD B2C também permite marcar e personalizar a interface do usuário que seu aplicativo exibe durante o processo de autenticação.

As seções a seguir demonstram como:

- Proteger uma API Web do Node.js
- Suporte à entrada em um aplicativo de página única (SPA) e chamar *essa* API Web protegida
- Habilitar suporte à redefinição de senha

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não fez isso, crie um [locatário Azure ad B2C](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="nodejs-web-api"></a>API de Web Node.js

As etapas a seguir demonstram como uma **API Web** pode usar Azure ad B2C para se proteger e expor escopos selecionados a um aplicativo cliente.

O MSAL.js para o nó está atualmente em desenvolvimento. Para obter mais informações, consulte o [roteiro](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap) no github. Atualmente, recomendamos o uso do [Passport-Azure-ad](https://github.com/AzureAD/passport-azure-ad), uma biblioteca de autenticação para Node.js desenvolvida e suportada pela Microsoft.

### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo

Para proteger sua API Web com Azure AD B2C, primeiro você precisa registrá-la. Confira [Registrar seu aplicativo](../../active-directory-b2c/add-web-api-application.md) para ver as etapas detalhadas.

### <a name="step-2-download-the-sample-application"></a>Etapa 2: baixar o aplicativo de exemplo

Baixe o exemplo como um arquivo zip ou clone-o do GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Etapa 3: configurar a autenticação

1. Abra o *config.jsno* arquivo no exemplo.

2. Configure o exemplo com as credenciais do aplicativo que você obteve anteriormente ao registrar seu aplicativo. Altere as linhas de código a seguir substituindo os valores pelo nome do locatário, ID do cliente e nome da política.

    ```json
         "credentials": {
             "tenantName": "<your-tenant-name>",
             "clientID": "<your-webapi-application-ID>"
         },
         "policies": {
             "policyName": "B2C_1_signupsignin1"
         },
         "resource": {
             "scope": ["demo.read"] 
         },
    ```

Para obter mais informações, confira este [ exemplo de API webNode.js B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

## <a name="javascript-spa"></a>JavaScript SPA

As etapas a seguir demonstram como um **aplicativo de página única** pode usar Azure ad B2C para se inscrever, entrar e chamar uma API Web protegida.

### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo

Para implementar a autenticação, primeiro você precisará registrar seu aplicativo. Confira [Registrar seu aplicativo](../../active-directory-b2c/tutorial-register-applications.md) para ver as etapas detalhadas.

### <a name="step-2-download-the-sample-application"></a>Etapa 2: baixar o aplicativo de exemplo

Baixe o exemplo de código [. Arquivo ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clone o repositório github:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Etapa 3: configurar a autenticação

Há dois pontos de interesse na configuração de seu aplicativo:

- Configurar o ponto de extremidade de API e escopos expostos
- Configurar parâmetros de autenticação e escopos de token

1. Abra o arquivo *apiConfig.js* no exemplo.

2. Configure o exemplo com os parâmetros que você obteve anteriormente ao registrar sua API Web. Altere as linhas de código a seguir substituindo os valores pelo endereço de sua API Web e escopos expostos.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

1. Abra o arquivo *authConfig.js* no exemplo.

1. Configure o exemplo com os parâmetros que você obteve anteriormente ao registrar seu aplicativo de página única. Altere as linhas de código a seguir substituindo os valores pelos escopos de solicitação ClientId, metadados de autoridade e token.

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

Para obter mais informações, confira este [exemplo de aplicativo de página única do JavaScript B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

## <a name="support-password-reset"></a>Suporte à redefinição de senha

Nesta seção, você estenderá seu aplicativo de página única para usar o fluxo de usuário Azure AD B2C redefinição de senha. Embora MSAL.js atualmente não ofereça suporte a vários fluxos de usuário ou políticas personalizadas nativamente, você pode usar a biblioteca para lidar com casos de uso comuns, como a redefinição de senha.

As etapas a seguir pressupõem que você já seguiu as etapas na seção [Spa do JavaScript](#javascript-spa) anterior.

### <a name="step-1-define-the-authority-string-for-password-reset-user-flow"></a>Etapa 1: definir a cadeia de caracteres de autoridade para o fluxo de usuário de redefinição de senha

1. Primeiro, crie um objeto em que você armazene seus URIs de autoridade:

    ```javascript
        const b2cPolicies = {
            names: {
                signUpSignIn: "b2c_1_susi",
                forgotPassword: "b2c_1_reset"
            },
            authorities: {
                signUpSignIn: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
                },
                forgotPassword: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
                },
            },
        }
    ```

1. Em seguida, inicialize o objeto MSAL com a `signInSignUp` política como padrão (consulte o trecho de código anterior). Quando um usuário tenta fazer logon, é apresentada a tela a seguir:

    :::image type="content" source="media/msal-b2c-overview/user-journey-01-signin.png" alt-text="Tela de entrada exibida por Azure AD B2C":::

### <a name="step-2-catch-and-handle-authentication-errors-in-your-login-method"></a>Etapa 2: detectar e tratar erros de autenticação no seu método de logon

Quando um usuário seleciona a **senha esquecida**, seu aplicativo gera um erro que você deve capturar em seu código e, em seguida, manipular apresentando o fluxo de usuário apropriado. Nesse caso, o `b2c_1_reset` fluxo de redefinição de senha.

1. Estenda o método de entrada da seguinte maneira:

    ```javascript
    function signIn() {
      myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log("id_token acquired at: " + new Date().toString());

            if (myMSALObj.getAccount()) {
              updateUI();
            }

        }).catch(function (error) {
          console.log(error);

          // error handling
          if (error.errorMessage) {
            // check for forgot password error
            if (error.errorMessage.indexOf("AADB2C90118") > -1) {

              //call login method again with the password reset user flow
              myMSALObj.loginPopup(b2cPolicies.authorities.forgotPassword)
                .then(loginResponse => {
                  console.log(loginResponse);
                  window.alert("Password has been reset successfully. \nPlease sign-in with your new password.");
                })
            }
          }
        });
    }
    ```

1. O trecho de código anterior mostra como mostrar a tela de redefinição de senha depois de capturar o erro com o código `AADB2C90118` .

    Depois de redefinir a senha, o usuário retornará novamente ao aplicativo para entrar novamente.

    :::image type="content" source="media/msal-b2c-overview/user-journey-02-password-reset.png" alt-text="Telas de fluxo de redefinição de senha mostradas por Azure AD B2C" border="false":::

    Para obter mais informações sobre códigos de erro e tratamento de exceções, consulte [erros de MSAL e códigos de exceção](msal-error-handling-js.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre estes conceitos de Azure AD B2C:

- [Fluxos de usuário](../../active-directory-b2c/tutorial-create-user-flows.md)
- [Políticas personalizadas](../../active-directory-b2c/custom-policy-get-started.md)
- [Personalização de experiência do usuário](../../active-directory-b2c/configure-user-input.md)
