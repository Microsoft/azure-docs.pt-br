---
title: 'Início rápido: Chamar o Microsoft Graph em um aplicativo de console Node.js | Azure'
titleSuffix: Microsoft identity platform
description: Neste início rápido, você baixa e executa um exemplo de código que mostra como um aplicativo de console Node.js pode obter um token de acesso e chamar uma API protegida por um ponto de extremidade da plataforma de identidade da Microsoft usando a própria identidade do aplicativo
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 4360810d460c5fc8598ce302ad8b82f65d2d819e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653738"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-nodejs-console-app-using-apps-identity"></a>Início rápido: Adquirir um token e chamar a API do Microsoft Graph em um aplicativo de console Node.js usando a identidade do aplicativo

Neste guia de início rápido, você baixará e executará um exemplo de código que demonstra como um aplicativo de console Node.js pode obter um token de acesso usando a identidade do aplicativo para chamar a API do Microsoft Graph e exibir uma [lista de usuários](/graph/api/user-list) no diretório. O exemplo de código demonstra como um trabalho autônomo ou um serviço Windows pode ser executado com uma identidade de aplicativo, em vez de uma identidade do usuário.

Este guia de início rápido usa a [Biblioteca de Autenticação da Microsoft para Node.js (nó MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) com a [concessão de credenciais do cliente](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) ou outro editor de código

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>Registrar e baixar o aplicativo de exemplo
>
> Siga as etapas abaixo para começar.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-the-application"></a>Etapa 1: Registrar o aplicativo
> Para registrar seu aplicativo e adicionar as informações de registro do aplicativo à solução manualmente, siga estas etapas:
>
> 1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
> 1. Pesquise **Azure Active Directory** e selecione-o.
> 1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
> 1. Insira um **Nome** para seu aplicativo, por exemplo, `msal-node-cli`. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
> 1. Selecione **Registrar**.
> 1. Em **Gerenciar**, selecione **Certificados e Segredos**.
> 1. Em **Segredos do cliente**, selecione **Novo segredo do cliente**, insira um nome e selecione **Adicionar**. Registre o valor secreto em uma localização segura para uso em uma etapa posterior.
> 1. Em **Gerenciar**, selecione **Permissões de API** > **Adicionar uma permissão**. Selecione **Microsoft Graph**.
> 1. Selecione **Permissões de aplicativo**.
> 1. No nó **Usuário**, selecione **User.Read.All** e selecione **Adicionar permissões**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-sample-app"></a>Baixar e configurar o aplicativo de exemplo
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Etapa 1: Configurar o aplicativo no portal do Azure
> Para o exemplo de código deste início rápido funcionar, você precisará criar um segredo do cliente e adicionar a permissão de aplicativo **User.Read.All** da API do Graph.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-netcore-daemon/green-check.png) Seu aplicativo já está configurado com esses atributos.

#### <a name="step-2-download-the-nodejs-sample-project"></a>Etapa 2: Baixar o projeto de exemplo do Node.js

> [!div renderon="docs"]
> [Baixe o exemplo de código](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Baixe o exemplo de código](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-nodejs-sample-project"></a>Etapa 3: Configurar o projeto de exemplo do Node.js
>
> 1. Extraia o arquivo zip para uma pasta local próxima da raiz do disco, por exemplo, *C:\Azure-Samples*.
> 1. Edite *.env* e substitua os valores dos campos `TENANT_ID`, `CLIENT_ID` e `CLIENT_SECRET` pelo seguinte snippet:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here",
>    "CLIENT_SECRET": "Enter_the_Client_Secret_Here"
>    ```
>    Em que:
>    - `Enter_the_Application_Id_Here` – é a **ID do Aplicativo (cliente)** do aplicativo que você registrou anteriormente. Localize essa ID no painel **Visão geral** do registro de aplicativo no portal do Azure.
>    - `Enter_the_Tenant_Id_Here` – substitua esse valor pela **ID do Locatário** ou pelo **Nome do locatário** (por exemplo, contoso.microsoft.com).  Localize esses valores no painel **Visão geral** do registro de aplicativo no portal do Azure.
>    - `Enter_the_Client_Secret_Here` – substitua esse valor pelo segredo do cliente criado anteriormente. Para gerar uma chave, use **Certificados e segredos** nas configurações de registro do aplicativo no portal do Azure.
>
> > [!WARNING]
> > Qualquer segredo de texto não criptografado no código-fonte representa um risco de segurança maior. Este artigo usa um segredo do cliente de texto não criptografado apenas para fins de simplicidade. Use [credenciais de certificado](active-directory-certificate-credentials.md) em vez de segredos do cliente nos seus aplicativos cliente confidenciais, especialmente os aplicativos que você pretende implantar na produção.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Etapa 3: Consentimento do administrador

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Etapa 4: Consentimento do administrador

Se você tentar executar o aplicativo neste ponto, receberá o erro *HTTP 403 – Proibido*: `Insufficient privileges to complete the operation`. Esse erro acontece porque qualquer *permissão somente do aplicativo* exige o **consentimento do administrador**, ou seja, um Administrador global do seu diretório precisa dar consentimento ao seu aplicativo. Selecione uma das opções abaixo, dependendo de sua função:

##### <a name="global-tenant-administrator"></a>Administrator de locatário global

> [!div renderon="docs"]
> Se você for um administrador de locatário global, acesse a página **Permissões de API** no Registro de Aplicativo do portal do Azure e selecione **Dar consentimento do administrador para {nome do locatário}** (em que {nome do locatário} é o nome do seu diretório).

> [!div renderon="portal" class="sxs-lookup"]
> Se você for um administrador global, acesse a página **Permissões de API**, selecione **Dar consentimento de administrador para Insira_o_nome_do_locatário_aqui**
> > [!div id="apipermissionspage"]
> > [Acesse a página Permissões de API]()

##### <a name="standard-user"></a>Usuário padrão

Se você for usuário padrão do seu locatário, precisará pedir que o Administrador global dê **consentimento do administrador** para seu aplicativo. Para fazer isso, dê a seguinte URL ao administrador:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Em que:
>> * `Enter_the_Tenant_Id_Here` – substitua esse valor pela **ID do locatário** ou pelo **Nome do locatário** (por exemplo, contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` - é a **ID do aplicativo (cliente)** que você registrou.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Etapa 4: Executar o aplicativo

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Etapa 5: Executar o aplicativo

Localize a pasta raiz do exemplo (em que `package.json` reside) em um prompt de comando ou console. Você precisará instalar as dependências deste exemplo uma só vez:

```console
npm install
```

Em seguida, execute o aplicativo via prompt de comando ou console:

```console
node . --op getUsers
```

Você deve ver, no console a saída, algum fragmento JSON que representa uma lista de usuários em seu diretório do Azure AD.

## <a name="about-the-code"></a>Observações sobre o código

Abaixo, alguns aspectos importantes do aplicativo de exemplo são discutidos.

### <a name="msal-node"></a>Nó MSAL

[MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft. Conforme descrito, este guia de início rápido solicita tokens por permissões de aplicativo (usando a própria identidade do aplicativo), em vez de permissões delegadas. O fluxo de autenticação usado nesse caso é conhecido como [fluxo de credenciais do cliente OAuth 2.0](v2-oauth2-client-creds-grant-flow.md). Para obter mais informações sobre como usar o MSAL Node com aplicativos daemon, confira [Cenário: aplicativo daemon](scenario-daemon-overview.md).

 Instale o MSAL Node executando o comando npm a seguir.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Você pode adicionar a referência da MSAL adicionando o seguinte código:

```javascript
const msal = require('@azure/msal-node');
```

Em seguida, inicialize a MSAL usando o seguinte código:

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
        clientSecret: "Enter_the_Client_Secret_Here",
   }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
```

> | Em que: |Descrição |
> |---------|---------|
> | `clientId` | É a **ID do aplicativo (cliente)** relativa ao aplicativo registrado no portal do Azure. Você pode encontrar esse valor na página **Visão Geral** do aplicativo no portal do Azure. |
> | `authority`    | O ponto de extremidade do STS para o usuário autenticar. Normalmente, `https://login.microsoftonline.com/{tenant}` para a nuvem pública, em que {tenant} é o nome do seu locatário ou ID do seu locatário.|
> | `clientSecret` | É o segredo do cliente criado para o aplicativo no portal do Azure. |

Para saber mais, confira a [documentação de referência do `ConfidentialClientApplication`](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)

### <a name="requesting-tokens"></a>Solicitando tokens

Para solicitar um token usando a identidade do aplicativo, use o método `acquireTokenByClientCredential`:

```javascript
const tokenRequest = {
    scopes: [ 'https://graph.microsoft.com/.default' ],
};

const tokenResponse = await cca.acquireTokenByClientCredential(tokenRequest);
```

> |Em que:| Descrição |
> |---------|---------|
> | `tokenRequest` | Contém os escopos solicitados. Para clientes confidenciais, ele deve usar um formato semelhante a `{Application ID URI}/.default` para indicar que os escopos solicitados são os estaticamente definidos no objeto de aplicativo definido no portal do Azure (no caso do Microsoft Graph, `{Application ID URI}` aponta para `https://graph.microsoft.com`). Para APIs Web personalizadas, o `{Application ID URI}` é definido na seção **Expor uma API** no Registro de Aplicativo do portal do Azure. |
> | `tokenResponse` | A resposta contém um token de acesso para os escopos solicitados. |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o desenvolvimento de aplicativos daemon/de console com o MSAL Node, confira o tutorial:

> [!div class="nextstepaction"]
> [Aplicativo daemon que chama APIs Web](tutorial-v2-nodejs-console.md)