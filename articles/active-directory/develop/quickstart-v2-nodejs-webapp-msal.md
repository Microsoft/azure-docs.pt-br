---
title: 'Início Rápido: Adicionar autenticação a um aplicativo Web Node com o nó MSAL | Azure'
titleSuffix: Microsoft identity platform
description: Neste guia de início rápido, você aprende a implementar a autenticação com um aplicativo Web Node.js e a MSAL (Biblioteca de Autenticação da Microsoft) para Node.js.
services: active-directory
author: mmacy
manager: celested
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2020
ms.author: marsma
ms.custom: aaddev, scenarios:getting-started, languages:js, devx-track-js
ms.openlocfilehash: 72eb6e77cfbcae662181f642393085185514eed6
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550941"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-node-web-app-using-the-auth-code-flow"></a>Início Rápido: Conectar usuários e obter um token de acesso em um aplicativo Web Node usando o fluxo de código de autenticação

Neste guia de início rápido, você baixará e executará um exemplo de código que demonstra como um aplicativo Web Node.js pode conectar usuários usando o fluxo de código de autorização. O exemplo de código também demonstra como obter um token de acesso para chamar a API do Microsoft Graph. 

Confira [Como o exemplo funciona](#how-the-sample-works) para ver uma ilustração.

Este guia de início rápido usa a biblioteca de autenticação da Microsoft para Node.js (nó MSAL) com o fluxo de código de autorização.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [crie uma assinatura do Azure gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) ou outro editor de código

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrar e baixar o aplicativo de início rápido
>
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
>
> 1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
> 1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
> 1. Insira um **Nome** para seu aplicativo. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
> 1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
> 1. Defina o valor do **URI de Redirecionamento** como `http://localhost:3000/redirect`.
> 1. Selecione **Registrar**. 
> 1. Na página **Visão geral** do aplicativo, anote o valor de **ID do aplicativo (cliente)** para uso posterior.
> 1. Em **Gerenciar**, selecione **Certificados e segredos** > **Novo segredo do cliente**.  Deixe a descrição em branco e a expiração padrão e selecione **Adicionar**.
> 1. Observe o **Valor** do **Segredo do cliente** para uso posterior.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Etapa 1: Configurar o aplicativo no portal do Azure
> Para que o exemplo de código deste início rápido funcione, você precisa criar um segredo do cliente e adicionar uma URL de resposta como **http://localhost:3000/redirect** .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essa alteração para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-windows-desktop/green-check.png) Seu aplicativo já está configurado com esses atributos.

#### <a name="step-2-download-the-project"></a>Etapa 2: Baixe o projeto

> [!div renderon="docs"]
> Para executar o projeto com um servidor Web usando Node.js, [baixe os arquivos de projeto de núcleo](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Executar o projeto com um servidor Web usando o Node.js

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Baixe o exemplo de código](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-node-app"></a>Etapa 3: Configure seu aplicativo Node
>
> Extraia o projeto e abra a pasta *MS-Identity-node-Main* e, em seguida, abra o arquivo *index.js*.
> Defina `clientID` com **a ID do Aplicativo (cliente)** .
> Defina o `clientSecret` com **Valor** do **Segredo do cliente**.
>
>```javascript
>const config = {
>    auth: {
>        clientId: "Enter_the_Application_Id_Here",
>        authority: "https://login.microsoftonline.com/common",
>        clientSecret: "Enter_the_Client_Secret_Here"
>    },
>    system: {
>        loggerOptions: {
>            loggerCallback(loglevel, message, containsPii) {
>                console.log(message);
>            },
>            piiLoggingEnabled: false,
>            logLevel: msal.LogLevel.Verbose,
>        }
>    }
>};
> ```

> [!div renderon="docs"]
>
> Modifique os valores na seção `config` conforme descrito aqui:
>
> - `Enter_the_Application_Id_Here` é a **ID do aplicativo (cliente)** que você registrou.
>
>    Para encontrar os valores de **ID do aplicativo (cliente)** , ID de diretório (locatário), acesse a página **Visão Geral** do aplicativo no portal do Azure.
> - `Enter_the_Client_Secret_Here` é o **Valor** do **Segredo do cliente** para o aplicativo que você registrou.
>
>    Para recuperar ou gerar um novo **Segredo do cliente**, em **Gerenciar**, selecione **Certificados e segredos**.
>
> O valor de `authority` padrão representa a nuvem principal (global) do Azure:
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Etapa 3: seu aplicativo está configurado e pronto para ser executado
>
> [!div renderon="docs"]
>
> #### <a name="step-4-run-the-project"></a>Etapa 4: Executar o projeto

Execute o projeto usando o Node.js:

1. Para iniciar o servidor, execute os seguintes comandos no diretório do projeto:
    ```console
    npm install
    npm start
    ```
1. Navegue até `http://localhost:3000/`.

1. Selecione **Entrar** para iniciar o processo de entrada.

    Na primeira vez em que entrar, você deverá fornecer seu consentimento para permitir que o aplicativo acesse seu perfil e conecte você. Depois que você tiver entrado com êxito, verá uma mensagem do registro na linha de comando.

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como o exemplo funciona

O exemplo, quando executado, hospeda um servidor Web no localhost, porta 3000. Quando um navegador da Web acessa esse site, o exemplo redireciona imediatamente o usuário para uma página de autenticação da Microsoft. Por isso, o exemplo não contém nenhum *HTML* ou elementos de exibição. No êxito de autenticação, a mensagem "OK" é exibida.

### <a name="msal-node"></a>Nó MSAL

A biblioteca de nó MSAL conecta usuários e solicita os tokens que são usados para acessar uma API protegida pela plataforma de identidade da Microsoft. Você pode baixar a versão mais recente usando o npm (Gerenciador de Pacotes do Node.js):

```console
npm install @azure/msal-node
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionando Auth a um aplicativo Web existente - exemplo de código GitHub >](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/auth-code)
