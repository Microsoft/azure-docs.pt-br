---
title: 'Tutorial: Proteger uma API Web do Node.js usando o Azure AD B2C e permitir acesso a um SPA (aplicativo de página única)'
titleSuffix: Azure AD B2C
description: Neste tutorial, saiba como usar o Active Directory B2C para proteger uma API Web do Node.js e chamá-la em um aplicativo de página única.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 737810a7d07d0d97b2e42acffa17fdd32986c48b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93421083"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>Tutorial: Proteger e permitir acesso a uma API Web do Node.js em um aplicativo de página única com o Azure AD B2C

Este tutorial mostra como chamar uma API Web do Node.js protegida pelo Azure AD B2C (Azure Active Directory B2C) em um aplicativo de página única.

Neste tutorial, o segundo de uma série de duas partes:

> [!div class="checklist"]
> * Crie um registro de aplicativo de API Web em seu locatário do Azure AD B2C
> * Configure escopos para uma API Web
> * Conceder permissões à API Web
> * Modifique um exemplo de código de API Web para trabalhar com seu locatário

No [primeiro tutorial](tutorial-single-page-app.md) desta série, você baixou o exemplo de código e o modificou para conectar usuários com um fluxo de usuário em seu locatário do Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Conclua as etapas e os pré-requisitos no [Tutorial: Habilitar a autenticação em um aplicativo de página única com o Azure AD B2C](tutorial-single-page-app.md)
* [Visual Studio Code](https://code.visualstudio.com/) ou outro editor de código
* [Node.js](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>Adicionar um aplicativo API Web

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Configurar escopos

Os escopos fornecem uma maneira de controlar o acesso a recursos protegidos. Escopos são usados pela API Web para implementar o controle de acesso com base em escopo. Por exemplo, alguns usuários podem ter acesso de leitura e gravação, enquanto outros usuários podem ter permissões somente leitura. Neste tutorial, você define as permissões de leitura e gravação para a API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Registre o valor em **Escopos** do escopo `demo.read` a ser usado em uma etapa posterior ao configurar o aplicativo de página única. O valor de escopo completo é semelhante a `https://contosob2c.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API Web protegida de outro aplicativo, é necessário conceder a esse aplicativo permissões para a API Web.

No tutorial de pré-requisitos, você criou um aplicativo de página única chamado *spaapp1*. Neste tutorial, você vai configurar o aplicativo *spaapp1* para a chamada à API Web criada na seção anterior.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Seu aplicativo Web de página única agora recebeu permissões para a API Web protegida para os escopos especificados. Um usuário autentica-se com o Azure AD B2C para usar o aplicativo de página única. O aplicativo de página única obtém um token de acesso do Azure AD B2C para acessar a API Web protegida.

## <a name="configure-the-sample"></a>Configurar o exemplo

Agora que a API Web está registrada e você definiu escopos, configure o código da API Web para funcionar com o locatário do Azure AD B2C. Neste tutorial, você configura uma API Web do Node.js de exemplo que baixou do GitHub.

[Baixe um \*arquivo morto .zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) ou clone a amostra de projeto da API Web do GitHub. Você também pode navegar diretamente até o projeto [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) no GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>Configurar a API Web

1. Abra o arquivo *config.json* no editor de códigos.
1. Modifique os valores de variáveis para refletir aqueles do registro de aplicativo que você criou anteriormente. Além disso, atualize o `policyName` com o fluxo de usuário que você criou como parte dos pré-requisitos. Por exemplo, *B2C_1_signupsignin1*.
    
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

#### <a name="enable-cors"></a>Habilitar CORS

Para permitir que seu aplicativo de página única chame a API Web do Node.js, é necessário habilitar [CORS](https://expressjs.com/en/resources/middleware/cors.html) na API Web. Em um aplicativo de produção, você deve ter cuidado com o domínio que está fazendo a solicitação, mas neste tutorial, permita solicitações de qualquer domínio.

Para habilitar o CORS, use o middleware a seguir. No exemplo de código da API Web do Node.js deste tutorial, ele já foi adicionado ao arquivo *index.js*.

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>Configure o aplicativo de página única

O SPA (aplicativo de página única) do [tutorial anterior](tutorial-single-page-app.md) da série usa o Azure AD B2C para inscrição e entrada do usuário e, por padrão, chama a API Web do Node.js protegida pelo locatário de demonstração *fabrikamb2c*.

Nesta seção, você atualiza o aplicativo Web de página única para chamar a API Web do Node.js protegida pelo *seu* locatário do Azure AD B2C (e executada no seu computador local).

Para alterar as configurações no SPA:

1. No projeto [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa] que você baixou ou clonou no tutorial anterior, abra o arquivo *apiConfig.js* dentro da pasta *JavaScriptSPA*.
1. Configure a amostra com o URI para o escopo *demo.read* criado anteriormente e a URL da API Web.
    1. Na definição `apiConfig`, substitua o valor `b2cScopes` pelo URI completo do escopo *demo.read* (o valor **Scope** que você registrou anteriormente).
    1. Altere o domínio no valor de `webApi` para o URI de redirecionamento que você adicionou quando registrou o aplicativo de API Web em uma etapa anterior.

    Como a API pode ser acessada no ponto de extremidade `/hello`, deixe */hello* no URI.

    A definição `apiConfig` deve ser semelhante ao seguinte bloco de código, mas com o nome do seu locatário do B2C no lugar de `<your-tenant-name>`:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>executar o SPA e a API Web

Agora você está pronto para testar o acesso no escopo à API do aplicativo de página única. Execute a API Web do Node.js e o aplicativo de página única JavaScript de exemplo em seu computador local. Em seguida, entre no aplicativo de página única e selecione o botão **Chamar API** para iniciar uma solicitação à API protegida.

Embora os dois aplicativos estejam sendo executados localmente quando você segue este tutorial, você os configurou para usar o Azure AD B2C para inscrição/entrada segura e para permitir acesso à API Web protegida.

### <a name="run-the-nodejs-web-api"></a>Executar a API Web Node.Js

1. Abra uma janela do console e altere para o diretório que contém a amostra da API Web do Node.js. Por exemplo:

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Execute os seguintes comandos:

    ```console
    npm install && npm update
    node index.js
    ```

    A janela de console exibe o número da porta em que o aplicativo está hospedado.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>Executar o aplicativo de página única

1. Abra outra janela do console e vá para o diretório que contém o SPA do JavaScript de exemplo. Por exemplo:

    ```console
    cd ms-identity-b2c-javascript-spa
    ```

1. Execute os seguintes comandos:

    ```console
    npm install && npm update
    npm start
    ```

    A janela de console exibe o número da porta em que o aplicativo está hospedado.

    ```console
    Listening on port 6420...
    ```

1. Navegue até `http://localhost:6420` no navegador para exibir o aplicativo.

    ![Exemplo de aplicativo de página única mostrado no navegador](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. Entre usando o endereço de email e a senha empregados no [tutorial anterior](tutorial-single-page-app.md). Após o logon bem-sucedido, você deverá ver a mensagem `User 'Your Username' logged-in`.
1. Selecione o botão **Chamar API**. O SPA obtém uma concessão de autorização do Azure AD B2C e, em seguida, acessa a API Web protegida para exibir o nome do usuário conectado:

    ![Aplicativo de página única no navegador que mostra o resultado JSON de nome de usuário retornado pela API](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Criou um registro de aplicativo de API Web em seu locatário do Azure AD B2C
> * Configurou escopos para uma API Web
> * Concedeu permissões à API Web
> * Modificou um exemplo de código de API Web para trabalhar com seu locatário

Agora que você já viu um SPA solicitar um recurso de uma API Web protegida, obtenha uma compreensão mais profunda sobre como esses tipos de aplicativos interagem entre si e com o Azure AD B2C.

> [!div class="nextstepaction"]
> [Tipos de aplicativos que podem ser usados no Active Directory B2C >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
