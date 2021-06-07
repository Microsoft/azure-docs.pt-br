---
title: 'Tutorial: Habilitar a autenticação em um aplicativo de página única'
titleSuffix: Azure AD B2C
description: Neste tutorial, saiba como usar o Azure Active Directory B2C para fornecer o logon do usuário para um SPA (aplicativo de página única) baseado no JavaScript.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6a9f3b864bd8aba2140c7d32d4b5474ff7b95f88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96171221"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>Tutorial: Habilitar a autenticação em um aplicativo de página única com o Azure AD B2C

Este tutorial mostrará como usar o Azure AD B2C (Azure Active Directory B2C) para entrar em um SPA (aplicativo de página única) e conectar usuários a ele usando:
* O [fluxo de código de autorização do OAuth 2.0](./authorization-code-flow.md) (usando o [MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser))
* O [fluxo de concessão implícita do OAuth 2.0](./implicit-flow-single-page-application.md) (usando o [MSAL.js 1.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core))

Neste tutorial, o primeiro de uma série de duas partes:

> [!div class="checklist"]
> * Adicione uma URL de resposta a um aplicativo registrado em seu locatário do Azure AD B2C
> * Baixe um exemplo de código do GitHub
> * Modifique o código do aplicativo de exemplo para funcionar com seu locatário
> * Inscreva-se usando seu fluxo de inscrição/entrada de usuário

O [próximo tutorial](tutorial-single-page-app-webapi.md) da série habilita a parte da API Web do exemplo de código.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você precisará dos seguintes recursos do Azure AD B2C em vigor antes de continuar com as etapas deste tutorial:

* [Locatário do Azure AD B2C](tutorial-create-tenant.md)
* [Aplicativo registrado](tutorial-register-spa.md) em seu locatário
* [Fluxos dos usuários criados](tutorial-create-user-flows.md) em seu locatário

Além disso, você precisará do seguinte no ambiente de desenvolvimento local:

* [Visual Studio Code](https://code.visualstudio.com/) ou outro editor de código
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Atualizar o aplicativo

No [segundo tutorial](./tutorial-register-spa.md) concluído como parte dos pré-requisitos, você registrou um aplicativo de página única no Azure AD B2C. Para habilitar a comunicação com o exemplo de código neste tutorial, adicione uma URL de resposta (também chamada de URI de redirecionamento) ao registro de aplicativo.

Para atualizar um aplicativo no locatário do Azure AD B2C, você pode usar a nossa nova experiência unificada de **Registros de aplicativos** ou a nossa experiência herdada **Aplicativos (Herdado)** . [Saiba mais sobre a nova experiência](./app-registrations-training-guide.md).

#### <a name="app-registrations-auth-code-flow"></a>[Registros de aplicativo (fluxo de código de autenticação)](#tab/app-reg-auth/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Clique em **Registros de aplicativo**, selecione a guia **Aplicativos próprios** e clique no aplicativo *spaapp1*.
1. Em **Aplicativo de página única**, selecione o link **Adicionar URI** e digite `http://localhost:6420`.
1. Selecione **Salvar**.
1. Selecione **Visão geral**.
1. Anote a **ID do aplicativo (cliente)** para uso em uma etapa posterior, na qual você atualize o código no aplicativo Web de página única.

#### <a name="app-registrations-implicit-flow"></a>[Registros de aplicativo (fluxo implícito)](#tab/app-reg-implicit/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Clique em **Registros de aplicativo**, selecione a guia **Aplicativos próprios** e clique no aplicativo *spaapp1*.
1. Em **Aplicativo de página única**, selecione o link **Adicionar URI** e digite `http://localhost:6420`.
1. Em **Concessão Implícita**, marque as caixas de seleção para **Tokens de Acesso** e **Tokens de ID** se já não estiverem selecionadas e selecione **Salvar**.
1. Selecione **Visão geral**.
1. Anote a **ID do aplicativo (cliente)** para uso em uma etapa posterior, na qual você atualize o código no aplicativo Web de página única.

#### <a name="applications-legacy"></a>[Aplicativos (Herdado)](#tab/applications-legacy/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Selecione **Todos os serviços** no canto superior esquerdo do portal do Azure, pesquise pelo **Azure AD B2C** e selecione-o.
1. Clique em **Aplicativos (herdados)** e selecione o aplicativo *spaapp1*.
1. Em **URL de resposta**, adicione `http://localhost:6420`.
1. Clique em **Salvar**.
1. Na página de propriedades, registre a **ID do Aplicativo**. Você pode usar a ID do aplicativo em uma etapa posterior ao atualizar o código no aplicativo Web de página única.

* * *

## <a name="get-the-sample-code"></a>Obter o código de amostra

Neste tutorial, você configurará um exemplo de código baixado do GitHub para funcionar com o locatário do B2C. A amostra descreve como um aplicativo de página única pode usar o Azure AD B2C para a inscrição e a entrada de usuário e para chamar uma API Web protegida (você habilita a API Web no próximo tutorial da série).

* Exemplo de fluxo de código de autorização do MSAL.js 2.x:

    [Baixe um arquivo zip](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip) ou clone o exemplo do GitHub:

    ```
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```
* Exemplo de fluxo implícito do MSAL.js 1.x:

    [Baixe um arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clone o exemplo do GitHub:

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="update-the-sample"></a>Atualizar a amostra

Agora que você já obteve a amostra, atualize o código com o nome do locatário do Azure AD B2C e a ID do aplicativo registrada em uma etapa anterior.

#### <a name="auth-code-flow-sample"></a>[Exemplo de fluxo de código de autenticação](#tab/config-auth/)

1. Abra o arquivo *authConfig.js* dentro da pasta de *Aplicativos*.
1. No objeto `msalConfig`, localize e substitua a atribuição para `clientId` pela **ID do aplicativo (cliente)** registrada em uma etapa anterior.
1. Abra o arquivo `policies.js`.
1. Localize as entradas em `names` e substitua a atribuição pelo nome dos fluxos de usuário criados em uma etapa anterior, por exemplo `B2C_1_signupsignin1`.
1. Localize e substitua as entradas em `authorities` conforme apropriado pelos nomes dos fluxos de usuário criados em uma etapa anterior, por exemplo `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`.
1. Localize e substitua a atribuição para `authorityDomain` por `<your-tenant-name>.b2clogin.com`.
1. Abra o arquivo `apiConfig.js`.
1. Localize a atribuição para `b2cScopes` e substitua a URL pela URL de escopo criada para a API Web, por exemplo `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]`.
1. Localize a atribuição para `webApi` e substitua a URL atual pela URL em que a API Web foi implantada na etapa 4, por exemplo `webApi: http://localhost:5000/hello`.

#### <a name="implicit-flow-sample"></a>[Exemplo de fluxo implícito](#tab/config-implicit/)

1. Abra o arquivo *authConfig.js* dentro da pasta *JavaScriptSPA*.
1. No objeto `msalConfig`, localize e substitua a atribuição para `clientId` pela **ID do aplicativo (cliente)** registrada em uma etapa anterior.
1. Abra o arquivo `policies.js`.
1. Localize as entradas em `names` e substitua a atribuição pelo nome dos fluxos de usuário criados em uma etapa anterior, por exemplo `B2C_1_signupsignin1`.
1. Localize e substitua as entradas em `authorities` conforme apropriado pelos nomes dos fluxos de usuário criados em uma etapa anterior, por exemplo `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`.
1. Abra o arquivo `apiConfig.js`.
1. Localize a atribuição para `b2cScopes` e substitua a URL pela URL de escopo criada para a API Web, por exemplo `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]`.
1. Localize a atribuição para `webApi` e substitua a URL atual pela URL em que a API Web foi implantada na etapa 4, por exemplo `webApi: http://localhost:5000/hello`.

* * *

O código de resultado deverá ser semelhante ao seguinte:

#### <a name="auth-code-flow-sample"></a>[Exemplo de fluxo de código de autenticação](#tab/review-auth/)

*authConfig.js*:

```javascript
const msalConfig = {
  auth: {
    clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    knownAuthorities: [b2cPolicies.authorityDomain],
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
};
```

*policies.js*:

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
    authorityDomain: "fabrikamb2c.b2clogin.com"
}
```

*apiConfig.js*:

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

#### <a name="implicit-flow-sample"></a>[Exemplo de fluxo implícito](#tab/review-implicit/)

*authConfig.js*:

```javascript
const msalConfig = {
  auth: {
    clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    validateAuthority: false
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
};
```

*policies.js*:

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
}
```

*apiConfig.js*:

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

* * *


## <a name="run-the-sample"></a>Execute o exemplo

1. Abra uma janela do console e acesse o diretório que contém o exemplo. 

    - Para o exemplo de fluxo de código de autorização do MSAL.js 2.x:

        ```console
        cd ms-identity-b2c-javascript-spa
        ```
    - Para o exemplo de fluxo implícito do MSAL.js 1.x: 

        ```console
        cd active-directory-b2c-javascript-msal-singlepageapp
        ```

1. Execute os seguintes comandos:

    ```console
    npm install && npm update
    npm start
    ```

    A janela do console exibe o número da porta do servidor do Node.js em execução localmente:

    ```console
    Listening on port 6420...
    ```
1. Navegue até `http://localhost:6420` para ver o aplicativo Web em execução no computador local.

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="Navegador da Web mostrando o aplicativo de página única em execução localmente":::

### <a name="sign-up-using-an-email-address"></a>Criar conta usando um endereço de email

Este aplicativo de exemplo é compatível com inscrição, entrada e redefinição de senha. Neste tutorial, você se inscreve usando um endereço de email.

1. Selecione **Entrar** para iniciar o fluxo de usuário *B2C_1_signupsignin1* especificado em uma etapa anterior.
1. O Azure AD B2C apresenta uma página de entrada que inclui um link de inscrição. Como você ainda não tem uma conta, selecione o link **Inscrever-se agora**.
1. O fluxo de trabalho de inscrição apresenta uma página para coletar e verificar a identidade do usuário usando um endereço de email. O fluxo de trabalho de inscrição também coleta a senha do usuário e os atributos solicitados definidos no fluxo de usuário.

    Use um endereço de email válido e valide-o usando o código de verificação. Defina uma senha. Insira valores para os atributos necessários.

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Página de inscrição exibida pelo fluxo de usuário do Azure AD B2C":::

1. Selecione **Criar** para criar uma conta local no diretório do Azure AD B2C.

Quando você seleciona **Criar**, o aplicativo mostra o nome do usuário conectado.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="Navegador da Web mostrando um aplicativo de página única com o usuário conectado":::

Se você quiser testar a entrada, selecione o botão **Sair** e, em seguida, selecione **Entrar** e entre com o endereço de email e a senha que você inseriu quando se inscreveu.

### <a name="what-about-calling-the-api"></a>E quanto à chamada à API?

Se você selecionar o botão **Chamar API** depois de entrar, será exibida a página de fluxo de inscrição/entrada de usuário em vez dos resultados da chamada à API. Isso é esperado porque você ainda não configurou a parte da API do aplicativo para se comunicar com um aplicativo de API Web registrado em *seu* locatário do Azure AD B2C.

Neste ponto, o aplicativo ainda está tentando se comunicar com a API registrada no locatário de demonstração (fabrikamb2c.onmicrosoft.com) e, como você não está autenticado nesse locatário, a página de inscrição/entrada é exibida.

Vá para o próximo tutorial da série para habilitar a API protegida (consulte a seção [Próximas etapas](#next-steps)).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um aplicativo de página única para trabalhar com um fluxo de usuário em seu locatário do Azure AD B2C para fornecer capacidade de inscrição e entrada. Você concluiu estas etapas:

> [!div class="checklist"]
> * Adicionou uma URL de resposta a um aplicativo registrado em seu locatário do Azure AD B2C
> * Baixou um exemplo de código do GitHub
> * Modificou o código do aplicativo de exemplo para funcionar com seu locatário
> * Inscreveu-se usando o fluxo de inscrição/entrada de usuário

Agora passe para o próximo tutorial da série para permitir acesso a uma API Web protegida por meio do SPA:

> [!div class="nextstepaction"]
> [Tutorial: Proteger e permitir acesso à API Web em um aplicativo de página única >](tutorial-single-page-app-webapi.md)