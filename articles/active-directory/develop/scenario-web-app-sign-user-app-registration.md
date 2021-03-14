---
title: Registrar um aplicativo Web que assina usuários | Azure
titleSuffix: Microsoft identity platform
description: Saiba como registrar um aplicativo Web que faz logon em usuários
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 96fbe676d4b929d2c1ee2efce0d10f3b5dc7c6ab
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103185"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>Aplicativo Web que assina usuários: registro de aplicativo

Este artigo explica as etapas de registro do aplicativo para um aplicativo Web que assina usuários.

Para registrar seu aplicativo, você pode usar:

- Os [guias de início rápido do aplicativo Web](#register-an-app-by-using-the-quickstarts). Além de ser uma ótima primeira experiência com a criação de um aplicativo, os guias de início rápido no portal do Azure contêm um botão chamado **fazer essa alteração para mim**. Você pode usar esse botão para definir as propriedades necessárias, mesmo para um aplicativo existente. Adapte os valores dessas propriedades ao seu próprio caso. Em particular, a URL da API Web para seu aplicativo provavelmente será diferente do padrão proposto, o que também afetará o URI de saída.
- O portal do Azure para [registrar seu aplicativo manualmente](#register-an-app-by-using-the-azure-portal).
- PowerShell e ferramentas de linha de comando.

## <a name="register-an-app-by-using-the-quickstarts"></a>Registrar um aplicativo usando os guias de início rápido

Você pode usar esses links para inicializar a criação de seu aplicativo Web:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Registrar um aplicativo usando o portal do Azure

> [!NOTE]
> O portal a ser usado é diferente dependendo se o aplicativo é executado no Microsoft Azure nuvem pública ou em uma nuvem nacional ou soberanas. Para obter mais informações, consulte [nuvens nacionais](./authentication-national-cloud.md#app-registration-endpoints).


1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>. 
1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
1. Pesquise **Azure Active Directory** e selecione-o.
1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo:
   1. Insira um **Nome** para seu aplicativo, por exemplo, `AspNetCore-WebApp`. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
   1. Escolha os tipos de conta com suporte para seu aplicativo. (Consulte [tipos de conta com suporte](./v2-supported-account-types.md).)
   1. Para **URI de redirecionamento**, adicione o tipo de aplicativo e o destino do URI que aceitará respostas de token retornadas após a autenticação bem-sucedida. Por exemplo, insira `https://localhost:44321`.
   1. Selecione **Registrar**.
1. Em **gerenciar**, selecione **autenticação** e, em seguida, adicione as seguintes informações:
   1. Na seção **da Web** , adicione `https://localhost:44321/signin-oidc` como um **URI de redirecionamento**.
   1. Na **URL de logoff de front-Channel**, digite `https://localhost:44321/signout-oidc` .
   1. Em **Concessão implícita e fluxos híbridos**, selecione **Tokens de ID**.
   1. Clique em **Salvar**.
   
# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo:
   1. Insira um **Nome** para seu aplicativo, por exemplo, `MailApp-openidconnect-v2`. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
   1. Escolha os tipos de conta com suporte para seu aplicativo. (Consulte [tipos de conta com suporte](./v2-supported-account-types.md).)
   1. Na seção **URI de redirecionamento (opcional)** , selecione **Web** na caixa de combinação e insira um **URI de redirecionamento** de `https://localhost:44326/` .
   1. Selecione **Registrar** para criar o aplicativo.
1. Em **Gerenciar**, selecione **Autenticação**.
1. Na seção **Concessão implícita e fluxos híbridos**, selecione **Tokens de ID**. Este exemplo requer que o [fluxo de concessão implícita](v2-oauth2-implicit-grant-flow.md) seja habilitado para conectar o usuário.
1. Clique em **Salvar**.

# <a name="java"></a>[Java](#tab/java)

1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo: 
    1. Insira um **Nome** para seu aplicativo, por exemplo, `java-webapp`. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde. 
    1. Selecione **contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)**.
    1. Selecione **registrar** para registrar o aplicativo.
1. Em **Gerenciar**, escolha **Autenticação** > **Adicionar uma plataforma**.
1. Selecione **Web**.
1. Para **URI de redirecionamento**, insira o mesmo número de porta e host, seguido pelo `/msal4jsample/secure/aad` para a página de entrada. 
1. Selecione **Configurar**.
1. Na seção **da Web** , use o host e o número da porta, seguido pelo `/msal4jsample/graph/me` como um **URI de redirecionamento** para a página de informações do usuário.
Por padrão, o exemplo usa:
   - `http://localhost:8080/msal4jsample/secure/aad`
   - `http://localhost:8080/msal4jsample/graph/me`

1. Clique em **Salvar**.
1. Em **Gerenciar**, selecione **Certificados e Segredos**.
1. Na seção **segredos do cliente** , selecione **novo segredo do cliente** e, em seguida:

   1. Insira uma descrição de chave.
   1. Selecione a duração da chave **em 1 ano**.
   1. Selecione **Adicionar**.
   1. Quando o valor da chave for exibido, copie-o para mais tarde. Esse valor não será exibido novamente ou poderá ser recuperado por outros meios.

# <a name="python"></a>[Python](#tab/python)

1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo:
   1. Insira um **Nome** para seu aplicativo, por exemplo, `python-webapp`. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
   1. Altere os **tipos de conta com suporte** para **contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)**.
   1. Na seção **URI de redirecionamento (opcional)** , selecione **Web** na caixa de combinação e insira o seguinte URI de redirecionamento: `http://localhost:5000/getAToken` .
   1. Selecione **Registrar** para criar o aplicativo.
1. Na página **Visão geral** do aplicativo, localize o valor de **ID do aplicativo (cliente)** e registre-o para uso posterior. Você precisará dele para definir o arquivo de configuração do Visual Studio para este projeto.
1. Em **Gerenciar**, selecione **Certificados e Segredos**.
1. Na seção **segredos do cliente** , selecione **novo segredo do cliente** e, em seguida:
   1. Insira uma descrição de chave.
   1. Selecione uma duração de chave igual a **Em 1 ano**.
   1. Selecione **Adicionar**.
   1. Quando o valor da chave for exibido, copie-o. Você precisará dela mais tarde.
---

## <a name="register-an-app-by-using-powershell"></a>Registrar um aplicativo usando o PowerShell

> [!NOTE]
> Atualmente, o PowerShell do Azure AD cria aplicativos com apenas os seguintes tipos de conta com suporte:
>
> - MyOrg (contas neste diretório organizacional somente)
> - AnyOrg (contas em qualquer diretório organizacional)
>
> Você pode criar um aplicativo que conecte usuários com suas contas pessoais da Microsoft (por exemplo, Skype, Xbox ou Outlook.com). Primeiro, crie um aplicativo multilocatário. Os tipos de conta com suporte são contas em qualquer diretório organizacional. Em seguida, altere a [`accessTokenAcceptedVersion`](./reference-app-manifest.md#accesstokenacceptedversion-attribute) propriedade para **2** e a [`signInAudience`](./reference-app-manifest.md#signinaudience-attribute) propriedade para `AzureADandPersonalMicrosoftAccount` no [manifesto do aplicativo](./reference-app-manifest.md) do portal do Azure. Para obter mais informações, consulte a [etapa 1,3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) no tutorial de ASP.NET Core. Você pode generalizar essa etapa para aplicativos Web em qualquer idioma.

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, [configuração de código do aplicativo](scenario-web-app-sign-user-app-configuration.md).
