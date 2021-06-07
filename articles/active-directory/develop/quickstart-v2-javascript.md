---
title: 'Início Rápido: Conectar usuários em aplicativos de página única JavaScript | Azure'
titleSuffix: Microsoft identity platform
description: Neste guia de início rápido, você aprenderá como um aplicativo JavaScript pode chamar uma API que exige tokens de acesso emitidos pela plataforma de identidade da Microsoft.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: 8e35342bd704f662d41f676f58e2cc14b54f29a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023377"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>Início Rápido: Conectar usuários e obter um token de acesso em um JavaScript SPA

Neste guia de início rápido, você baixará e executará um exemplo de código que demonstra como um SPA (aplicativo de página única) JavaScript pode conectar usuários e chamar o Microsoft Graph. O exemplo de código também demonstra como obter um token de acesso para chamar a API do Microsoft Graph ou qualquer API Web.

Confira [Como o exemplo funciona](#how-the-sample-works) para ver uma ilustração.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (para editar arquivos de projeto)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrar e baixar o aplicativo de início rápido
> Para iniciar seu aplicativo de início rápido, use qualquer uma das opções a seguir.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1 (Expresso): Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código
>
> 1. Acesse a experiência de início rápido do <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">portal do Azure – Registros de aplicativo</a>.
> 1. Insira um nome para seu aplicativo.
> 1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
> 1. Selecione **Registrar**.
> 1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2 (Manual): Registrar e configurar manualmente o aplicativo e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
>
> 1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
> 1. Pesquise **Azure Active Directory** e selecione-o.
> 1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
> 1. Insira um **Nome** para seu aplicativo. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
> 1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
> 1. Selecione **Registrar**. Na página **Visão geral** do aplicativo, anote o valor de **ID do aplicativo (cliente)** para uso posterior.
> 1. Este início rápido requer que o [fluxo de concessão implícita](v2-oauth2-implicit-grant-flow.md) seja ativado. Em **Gerenciar**, selecione **Autenticação**.
> 1. Em **Configurações da Plataforma** > **Adicionar uma plataforma**. Selecione **Web**.
> 1. Defina o valor do **URI de Redirecionamento** como `http://localhost:3000/`. 
> 1. Selecione **Tokens de Acesso** e **Tokens de ID** em **Concessão implícita e fluxos híbridos**.
> 1. Selecione **Configurar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Etapa 1: Configurar seu aplicativo no portal do Azure
> Para que o exemplo de código deste guia de início rápido funcione, adicione um **URI de Redirecionamento** igual a `http://localhost:3000/` e habilite **Concessão implícita**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-javascript/green-check.png) Seu aplicativo já está configurado com esses atributos.

#### <a name="step-2-download-the-project"></a>Etapa 2: Baixe o projeto

> [!div renderon="docs"]
> Para executar o projeto com um servidor Web usando Node.js, [baixe os arquivos de projeto de núcleo](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Executar o projeto com um servidor Web usando o Node.js

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Baixe o exemplo de código](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Etapa 3: Configurar o aplicativo JavaScript
>
> Na pasta *JavaScriptSPA*, edite *authConfig.js* e defina os valores `clientID`, `authority` e `redirectUri` em `msalConfig`.
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Em que:
> - `Enter_the_Application_Id_Here` é a **ID do aplicativo (cliente)** que você registrou.
>
>    Para encontrar o valor da **ID do Aplicativo (cliente)** , acesse a página **Visão Geral** do aplicativo no portal do Azure.
> - `Enter_the_Cloud_Instance_Id_Here` é a instância da nuvem do Azure. Para a nuvem principal ou global do Azure, basta inserir `https://login.microsoftonline.com/` . Para nuvens **nacionais** (por exemplo, China), confira [Nuvens nacionais](./authentication-national-cloud.md).
> - `Enter_the_Tenant_info_here` é definido como uma das seguintes opções:
>    - Se o seu aplicativo for compatível com as *contas neste diretório organizacional*, substitua esse valor pela **ID do locatário** ou **Nome do locatário** (por exemplo, `contoso.microsoft.com`).
>
>    Para encontrar o valor da **ID do Aplicativo (locatário)** , acesse a página **Visão Geral** do registro do aplicativo no portal do Azure.
>    - Se o aplicativo tem suporte para *contas em qualquer diretório organizacional*, substitua esse valor por `organizations`.
>    - Se o seu aplicativo tem suporte para *contas em qualquer diretório organizacional e contas pessoais Microsoft*, substitua esse valor por `common`. Para restringir o suporte a *contas pessoais da Microsoft*, substitua esse valor por `consumers`.
>
>    Para encontrar o valor dos **Tipos de conta com suporte**, acesse a página **Visão Geral** do registro de aplicativo no portal do Azure.
> - `Enter_the_Redirect_Uri_Here` é `http://localhost:3000/`.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Etapa 3: seu aplicativo está configurado e pronto para ser executado
> Configuramos seu projeto com os valores das propriedades do seu aplicativo.

> [!div renderon="docs"]
>
> Em seguida, ainda na mesma pasta, edite o arquivo *graphConfig.js* para definir o `graphMeEndpoint` e o `graphMeEndpoint` para o objeto `apiConfig`.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> Em que:
> - *\<Enter_the_Graph_Endpoint_Here>* é o ponto de extremidade no qual as chamadas à API serão feitas. Para o serviço principal ou global da API do Microsoft Graph, basta inserir `https://graph.microsoft.com/`. Para obter mais informações, confira [Implantação em uma nuvem nacional](/graph/deployments)
>
> #### <a name="step-4-run-the-project"></a>Etapa 4: Executar o projeto

Execute o projeto com um servidor Web usando o [Node.js](https://nodejs.org/en/download/):

1. Para iniciar o servidor, execute o seguinte comando no diretório do projeto:
    ```cmd
    npm install
    npm start
    ```
1. Abra um navegador da Web e vá para `http://localhost:3000/`.

1. Selecione **Entrar** para iniciar a conexão e chame a API do Microsoft Graph.

Depois que o navegador carregar o aplicativo, selecione **Entrar**. Na primeira vez em que entrar, será solicitado que você forneça seu consentimento para permitir que o aplicativo acesse seu perfil e faça logon. Depois que você se conectar com êxito, as informações do seu perfil de usuário deverão ser exibidas na página.

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como o exemplo funciona

![Como o exemplo de SPA do JavaScript funciona: 1. O SPA inicia a entrada. 2. O SPA adquire um token de ID da plataforma de identidade da Microsoft. 3. O SPA chama o token de aquisição. 4. A plataforma de identidade da Microsoft retorna um token de acesso para o SPA. 5. O SPA faz e a solicitação HTTP GET com o token de acesso para a API do Microsoft Graph. 6. A API do Graph retorna uma resposta HTTP para o SPA.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

A biblioteca MSAL conecta usuários e solicita os tokens que são usados para acessar uma API protegida pela plataforma de identidade da Microsoft. O arquivo *index.html* do início rápido contém uma referência à biblioteca:

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```

É possível substituir a versão anterior pela mais recente em [versões MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

Como alternativa, se tiver instalado o Node.js, você poderá baixar a versão mais recente por meio do npm (Gerenciador de Pacotes do Node.js):

```cmd
npm install msal
```

### <a name="msal-initialization"></a>Inicialização da MSAL

O código de início rápido também mostra como inicializar a biblioteca MSAL:

```javascript
  // Config object to be passed to Msal on creation
  const msalConfig = {
    auth: {
      clientId: "75d84e7a-40bx-f0a2-91b9-0c82d4c556aa", // this is a fake id
      authority: "https://login.microsoftonline.com/common",
      redirectUri: "http://localhost:3000/",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

const myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

|Where  | Descrição |
|---------|---------|
|`clientId`     | A ID do aplicativo que está registrado no portal do Azure.|
|`authority`    | (Opcional) A URL da autoridade que dá suporte aos tipos de conta, conforme descrito na seção de configuração. A autoridade padrão é `https://login.microsoftonline.com/common`. |
|`redirectUri`     | O URI de reposta/redirecionamento configurado do registro de aplicativo. Nesse caso, `http://localhost:3000/`. |
|`cacheLocation`  | (Opcional) Define o armazenamento do navegador para o estado de autenticação. O padrão é sessionStorage.   |
|`storeAuthStateInCookie`  | (Opcional) A biblioteca que armazena o estado de solicitação da autenticação exigido para validação dos fluxos de autenticação nos cookies do navegador. Esse cookie é definido para os navegadores IE e Microsoft Edge reduzirem determinados [problemas conhecidos](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

Para saber mais sobre opções configuráveis disponíveis, confira [Inicializar aplicativos cliente](msal-js-initializing-client-applications.md).

### <a name="sign-in-users"></a>Conectar usuários

O snippet de código a seguir mostra como realizar a conexão de usuários:

```javascript
// Add scopes for the id token to be used at Microsoft identity platform endpoints.
const loginRequest = {
    scopes: ["openid", "profile", "User.Read"],
};

myMSALObj.loginPopup(loginRequest)
    .then((loginResponse) => {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

|Where  | Descrição |
|---------|---------|
| `scopes`   | (Opcional) Contém os escopos que estão sendo solicitados para o consentimento do usuário no momento da conexão. Por exemplo, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para as APIs Web personalizadas (ou seja, `api://<Application ID>/access_as_user`). |

Como alternativa, talvez você queira usar o método `loginRedirect` para redirecionar a página atual para a página de entrada, em vez de uma janela pop-up.

### <a name="request-tokens"></a>Solicitar tokens

A MSAL usa três métodos para adquirir tokens: `acquireTokenRedirect`, `acquireTokenPopup` e `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Obter um token de usuário no modo silencioso

O método `acquireTokenSilent` manipula as aquisições e a renovação de tokens sem nenhuma interação do usuário. Após o método `loginRedirect` ou `loginPopup` ser executado pela primeira vez, `acquireTokenSilent` é o método normalmente usado para obter tokens usados para acessar recursos protegidos nas próximas chamadas. As chamadas para solicitar ou renovar tokens são feitas no modo silencioso.

```javascript

const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenSilent(tokenRequest)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

|Where  | Descrição |
|---------|---------|
| `scopes`   | Contém os escopos solicitados para retorno no token de acesso da API. Por exemplo, `[ "mail.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para as APIs Web personalizadas (ou seja, `api://<Application ID>/access_as_user`).|

#### <a name="get-a-user-token-interactively"></a>Obter um token de usuário interativamente

Há situações em que você força os usuários a interagir com a plataforma de identidade da Microsoft. Por exemplo:
* Os usuários podem precisar reinserir as credenciais porque a senha expirou.
* Seu aplicativo está solicitando acesso a escopos de recursos adicionais com os quais o usuário precisa concordar.
* A autenticação de dois fatores é necessária.

O padrão usual recomendado para a maioria dos aplicativos é chamar `acquireTokenSilent` primeiro, depois capturar a exceção e, em seguida, chamar `acquireTokenPopup` (ou `acquireTokenRedirect`) para iniciar uma solicitação interativa.

Chamar o `acquireTokenPopup` resulta em uma janela pop-up para entrada. (Ou `acquireTokenRedirect` resulta no redirecionamento dos usuários para a plataforma de identidade da Microsoft). Nessa janela, os usuários precisam interagir confirmando suas credenciais, dando consentimento ao recurso necessário ou concluindo a autenticação de dois fatores.

```javascript
// Add here scopes for access token to be used at MS Graph API endpoints.
const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenPopup(requestObj)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> [!NOTE]
> Este início rápido usa os métodos `loginRedirect` e `acquireTokenRedirect` com o Microsoft Internet Explorer devido a um [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relacionado ao tratamento de janelas pop-up pelo Internet Explorer.

## <a name="next-steps"></a>Próximas etapas

Para obter um guia passo a passo mais detalhado sobre como criar aplicativo para este início rápido, confira:

> [!div class="nextstepaction"]
> [Tutorial: Conectar usuários e chamar a API do Microsoft Graph em um SPA (aplicativo de página única) JavaScript](tutorial-v2-javascript-spa.md)
