---
title: Publicar aplicativos cliente nativos - Azure AD | Microsoft Docs
description: Aborda como habilitar aplicativos clientes nativos para se comunicar com o Conector de Proxy do aplicativo Azure AD para fornecer acesso remoto seguro aos seus aplicativos locais.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e830b52accefa0bcb76cc3434cae2fe94eed97db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258655"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Como habilitar aplicativos cliente nativos para interagir com os aplicativos do proxy

Você pode usar o Proxy de Aplicativo do Azure Active Directory (Azure AD) para publicar aplicativos Web. No entanto, ele também pode ser usado para publicar aplicativos cliente nativos configurados com a MSAL (Biblioteca de Autenticação da Microsoft). Os aplicativos cliente nativos diferem dos aplicativos Web porque eles são instalados em um dispositivo, enquanto os aplicativos Web são acessados por meio de um navegador.

Para dar suporte a aplicativos cliente nativos, o Proxy de Aplicativo aceita tokens emitidos pelo Azure AD que são enviados no cabeçalho. O serviço Proxy de Aplicativo faz a autenticação para os usuários. Essa solução não usa tokens de aplicativo para autenticação.

![Relação entre os usuários finais, o Azure Active Directory e os aplicativos publicados](./media/application-proxy-configure-native-client-application/richclientflow.png)

Para publicar os aplicativos nativos, use a Biblioteca de Autenticação da Microsoft, que trata da autenticação e dá suporte a vários ambientes de cliente. O Proxy do Aplicativo se ajusta ao cenário do [Aplicativo da área de trabalho que chama a API Web em nome de um usuário conectado](../develop/authentication-flows-app-scenarios.md#desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user).

Este artigo explica as quatro etapas para publicar um aplicativo nativo com o Proxy de Aplicativo e a Biblioteca de Autenticação do Azure AD.

## <a name="step-1-publish-your-proxy-application"></a>Etapa 1: Publicar seu aplicativo de proxy

Publique seu aplicativo de proxy como faria com qualquer outro aplicativo e atribua aos usuários acesso a ele. Para saber mais, consulte [Publicar aplicativos com o Proxy de Aplicativo](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Etapa 2: Registrar seu aplicativo nativo

Agora, você deve registrar seu aplicativo no Azure AD, da seguinte maneira:

1. Entre no [portal do Azure Active Directory](https://aad.portal.azure.com/). O **Painel** do **centro de administração do Azure Active Directory** é exibido.
1. No painel esquerdo, selecione **Azure Active Directory**. A página de visão geral do **Azure Active Directory** aparece.
1. Na barra lateral de visão geral do Azure AD, selecione **Registros de aplicativo**. A lista de todos os registros de aplicativo é exibida.
1. Selecione **Novo registro**. A página **Registrar um aplicativo** é exibida.

   ![Criar um novo registro de aplicativo no portal do Azure](./media/application-proxy-configure-native-client-application/create.png)

1. No título **Nome**, especifique um nome de exibição voltado para o usuário para seu aplicativo.
1. No título **Tipos de conta com suporte**, selecione um nível de acesso usando estas diretrizes:

   - Para direcionar apenas para contas internas da organização, selecione **Contas somente neste diretório organizacional**.
   - Para direcionar apenas para clientes comerciais e educacionais, selecione **Contas em qualquer diretório organizacional**.
   - Para direcionar para o conjunto mais amplo de entidades da Microsoft, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
1. Em **URI de redirecionamento**, selecione **Cliente público (dispositivo móvel e desktop)** e digite o URI de redirecionamento `https://login.microsoftonline.com/common/oauth2/nativeclient` do seu aplicativo.
1. Selecione e leia as **Políticas da Plataforma da Microsoft** e selecione **Registrar**. Uma página de visão geral para o novo registro de aplicativo é criada e exibida.

Para obter informações mais detalhadas sobre como criar um novo registro de aplicativo, confira [Integrando aplicativos ao Azure Active Directory](../develop/quickstart-register-app.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Etapa 3: Conceder acesso ao seu proxy de aplicativo

Agora que você registrou seu aplicativo nativo, você pode conceder a ele acesso a outros aplicativos do seu diretório, nesse caso, para acessar o proxy do aplicativo. Para habilitar aplicativo nativo para ser exposto ao aplicativo de proxy:

1. Na barra lateral da página de registro do novo aplicativo, selecione **Permissões de API**. A página **Permissões de API** do registro do novo aplicativo é exibida.
1. Selecione **Adicionar uma permissão**. A página **Solicitar permissões de API** é exibida.
1. Nas configurações de **Selecionar uma API**, selecione **APIs que minha organização usa**. É exibida uma lista com os aplicativos do seu diretório que expõem APIs.
1. Digite a caixa de pesquisa ou role até encontrar o aplicativo que você publicou na [Etapa 1: Publicar seu aplicativo de proxy](#step-1-publish-your-proxy-application) e selecione o aplicativo de proxy.
1. No título **Que tipo de permissões seu aplicativo exige?** , selecione o tipo de permissão. Se seu aplicativo nativo precisar acessar a API do aplicativo de proxy como o usuário conectado, escolha **Permissões delegadas**.
1. No título **Selecionar permissões**, selecione a permissão desejada e selecione **Adicionar permissões**. A página **Permissões de API** para seu aplicativo nativo agora mostra o aplicativo proxy e a API de permissão que você adicionou.

## <a name="step-4-add-the-microsoft-authentication-library-to-your-code-net-c-sample"></a>Etapa 4: Adicionar a Biblioteca de Autenticação da Microsoft para seu código (exemplo de C# .NET)

Edite o código de aplicativo nativo no contexto de autenticação da MSAL (Biblioteca de Autenticação da Microsoft) para incluir o seguinte texto: 

```         
// Acquire Access Token from AAD for Proxy Application
IPublicClientApplication clientApp = PublicClientApplicationBuilder
.Create(<App ID of the Native app>)
.WithDefaultRedirectUri() // will automatically use the default Uri for native app
.WithAuthority("https://login.microsoftonline.com/{<Tenant ID>}")
.Build();

AuthenticationResult authResult = null;
var accounts = await clientApp.GetAccountsAsync();
IAccount account = accounts.FirstOrDefault();

IEnumerable<string> scopes = new string[] {"<Scope>"};

try
 {
    authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
 }
    catch (MsalUiRequiredException ex)
 {
     authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();                
 }

if (authResult != null)
 {
  //Use the Access Token to access the Proxy Application

  HttpClient httpClient = new HttpClient();
  HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
  HttpResponseMessage response = await httpClient.GetAsync("<Proxy App Url>");
 }
```

As informações necessárias no código de exemplo podem ser encontradas no portal do Azure AD, da seguinte maneira:

| Informações necessárias | Como encontrá-las no portal do Azure AD |
| --- | --- |
| \<Tenant ID> | **Azure Active Directory** > **Propriedades** > **ID do diretório** |
| \<App ID of the Native app> | **Registro de aplicativo** > *seu aplicativo nativo* > **Visão geral** > **ID do aplicativo** |
| \<Scope> | **Registro do aplicativo** > *seu aplicativo nativo* > **Permissões da API** > Clique na API de Permissão (user_impersonation) > Um painel com a legenda **user_impersonation** é exibido à direita. > O escopo da URL na caixa de edição.
| \<Proxy App Url> | a URL externa e o caminho para a API

Depois de editar o código MSAL com esses parâmetros, os usuários podem autenticar para aplicativos cliente nativos, mesmo quando eles estão fora da rede corporativa.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o fluxo do aplicativo nativo, confira [Aplicativos nativos no Azure Active Directory](../azuread-dev/native-app.md).

Saiba mais sobre como configurar o [Logon único para aplicativos no Azure Active Directory](sso-options.md#choosing-a-single-sign-on-method).