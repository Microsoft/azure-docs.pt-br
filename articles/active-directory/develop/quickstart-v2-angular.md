---
title: 'Início Rápido: Conectar usuários em aplicativos de página única Angular – Azure'
titleSuffix: Microsoft identity platform
description: Neste guia de início rápido, você aprenderá como um aplicativo Angular pode chamar uma API que exige tokens de acesso emitidos pela plataforma de identidade da Microsoft.
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: bab92a6d7e30f5aefdd28d06b34a006d065cee3c
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105966836"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Início Rápido: Conectar usuários e obter um token de acesso em um aplicativo de página única Angular

Neste guia de início rápido, você baixará e executará um exemplo de código que demonstra como um SPA (aplicativo de página única) Angular pode conectar usuários e chamar o Microsoft Graph. O exemplo de código demonstra como obter um token de acesso para chamar a API do Microsoft Graph ou qualquer API Web.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure. [Crie um gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node.js](https://nodejs.org/en/download/).
* [Visual Studio Code](https://code.visualstudio.com/download) para editar arquivos de projeto ou [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) para executar o projeto.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>Registrar e baixar o aplicativo de início rápido
> Para iniciar o aplicativo de início rápido, use qualquer uma das opções a seguir.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>Opção 1 (expresso): registrar e configurar automaticamente o aplicativo e, em seguida, baixar o exemplo de código
>
> 1. Acesse a experiência de início rápido do <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">portal do Azure – Registros de aplicativo</a>.
> 1. Insira um nome para o aplicativo e selecione **Registrar**.
> 1. Vá até o painel do início rápido e veja o início rápido do Angular. Siga as instruções para baixar e configurar automaticamente o novo aplicativo.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Opção 2 (manual): registrar e configurar manualmente o aplicativo e o exemplo de código
>
> #### <a name="step-1-register-the-application"></a>Etapa 1: Registrar o aplicativo
>
> 1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
> 1. Siga as instruções para [registrar um aplicativo de página única](./scenario-spa-app-registration.md) no portal do Azure.
> 1. Adicione uma nova plataforma no painel **Autenticação** do seu registro de aplicativo e registre o URI de redirecionamento: `http://localhost:4200/`.
> 1. Este início rápido usa o [fluxo de concessão implícita](v2-oauth2-implicit-grant-flow.md). Na seção **Concessão implícita e fluxos híbridos**, selecione **Tokens de ID** e **Tokens de acesso**. Os tokens de ID e tokens de acesso são necessários porque esse aplicativo conecta usuários e chama uma API.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Etapa 1: Configurar o aplicativo no portal do Azure
> Para que o exemplo de código deste guia de início rápido funcione, é necessário adicionar um URI de redirecionamento como **http://localhost:4200/** e habilitar a **Concessão implícita**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-javascript/green-check.png) Seu aplicativo já está configurado com esses atributos.

#### <a name="step-2-download-the-code-sample"></a>Etapa 2: Baixar o exemplo de código
>[!div renderon="docs"]
>Para executar o projeto com um servidor Web usando Node.js, [clone o repositório de exemplo](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) ou [baixe os principais arquivos de projeto](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip). Abra os arquivos usando um editor como o Visual Studio Code.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Baixe o exemplo de código](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>Etapa 3: Configurar o aplicativo JavaScript
>
> Na pasta *src/app*, edite *app.module.ts* e defina os valores `clientId` e `authority` em `MsalModule.forRoot`.
>
>```javascript
>MsalModule.forRoot({
>    auth: {
>        clientId: 'Enter_the_Application_Id_here', // This is your client ID
>        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
>        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
>    },
>    cache: {
>        cacheLocation: 'localStorage',
>        storeAuthStateInCookie: isIE, // set to true for IE 11
>    },
>},
> //... )
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here


> [!div renderon="docs"]
>
> Substitua esses valores:
>
>|Nome do valor|Descrição|
>|---------|---------|
>|Enter_the_Application_Id_Here|Na página **Visão Geral** do seu registro de aplicativo, esta é seu valor **ID do Aplicativo(cliente)** . |
>|Enter_the_Cloud_Instance_Id_Here|Essa é a instância da nuvem do Azure. Para a nuvem principal ou global do Azure, insira **https://login.microsoftonline.com** . Para nuvens nacionais (por exemplo, China), confira [Nuvens nacionais](./authentication-national-cloud.md).|
>|Enter_the_Tenant_Info_Here| Defina como uma das seguintes opções: Se o aplicativo der suporte a *contas neste diretório organizacional*, substitua esse valor pela ID do diretório (locatário) ou pelo nome do locatário (por exemplo, **contoso.microsoft.com**). Se o aplicativo for compatível com as *contas em qualquer diretório organizacional*, substitua esse valor por **organizações**. Se o seu aplicativo for compatível com as *contas em qualquer diretório organizacional e contas pessoais da Microsoft*, substitua esse valor por **comum**. Para restringir o suporte a *contas pessoais da Microsoft*, substitua esse valor por **consumidores**. |
>|Enter_the_Redirect_Uri_Here|Substitua por **http://localhost:4200** .|
>|cacheLocation  | (Opcional) Defina o armazenamento do navegador do estado de autenticação. O padrão é **sessionStorage**.   |
>|storeAuthStateInCookie  | (Opcional) Identifique a biblioteca que armazena o estado de solicitação de autenticação. Esse estado é necessário para validar os fluxos de autenticação nos cookies do navegador. Esse cookie é definido para o Internet Explorer e o Microsoft Edge para acomodar esses dois navegadores. Para obter mais detalhes, confira os [problemas conhecidos](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues). |
>
> Para encontrar os valores de **ID do aplicativo (cliente)** , **ID de diretório (locatário)** e **Tipos de conta com suporte**, vá para a página **Visão Geral** do aplicativo no portal do Azure.

> Para saber mais sobre opções configuráveis disponíveis, confira [Inicializar aplicativos cliente](msal-js-initializing-client-applications.md).

> Você pode localizar o código-fonte da biblioteca MSAL.js no repositório [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) no GitHub.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Etapa 3: seu aplicativo está configurado e pronto para ser executado
> Configuramos seu projeto com os valores das propriedades do seu aplicativo.

> [!div renderon="docs"]
>
> Role para baixo no mesmo arquivo e atualize o `graphMeEndpoint`. 
> - Substitua a cadeia de caracteres `Enter_the_Graph_Endpoint_Herev1.0/me` por `https://graph.microsoft.com/v1.0/me`
> - `Enter_the_Graph_Endpoint_Herev1.0/me` é o ponto de extremidade no qual as chamadas à API serão feitas. Para o serviço da API do Microsoft Graph principal (global), insira `https://graph.microsoft.com/` (inclua a barra à direita). Para obter mais informações, confira a [documentação](https://docs.microsoft.com/graph/deployments).
>
>
> ```javascript
>      protectedResourceMap: [
>        ['Enter_the_Graph_Endpoint_Herev1.0/me', ['user.read']]
>      ],
> ```
>
>

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Etapa 4: Executar o projeto

Se você estiver usando o Node.js:

1. Inicie o servidor executando os seguintes comandos no diretório do projeto:

   ```console
   npm install
   npm start
   ```

1. Navegue até **http://localhost:4200/** .
1. Selecione **Fazer logon**.
1. Selecione **Perfil** para chamar o Microsoft Graph.

Depois que o navegador carregar o aplicativo, selecione **Fazer logon**. Na primeira vez que começar a entrar, você deverá fornecer seu consentimento para permitir que o aplicativo acesse seu perfil e conecte você. Depois que você se conectar com êxito, selecione **Perfil** e as informações do seu perfil do usuário serão exibidas na página.

## <a name="how-the-sample-works"></a>Como o exemplo funciona

![Diagrama que mostra como funciona o aplicativo de exemplo neste guia de início rápido](./media/quickstart-v2-angular/diagram-auth-flow-spa-angular.svg)


## <a name="next-steps"></a>Próximas etapas

Em seguida, aprenda a conectar um usuário e a adquirir tokens no tutorial do Angular:

> [!div class="nextstepaction"]
> [Tutorial do Angular](./tutorial-v2-angular.md)