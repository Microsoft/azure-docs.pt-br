---
title: Aplicativo Web que conecta os usuários (registro de aplicativo) - plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo web que conecta os usuários (registro de aplicativo)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ae638f8cbef29c5d167a3ab59188169cbd934ef
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150220"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>Aplicativo Web que conecta os usuários - registro de aplicativo

Esta página explica as especificidades de registro de aplicativo para um aplicativo web que os usuários entrar.

Para registrar seu aplicativo, você pode usar:

- O [web app quickstarts](#register-an-app-using-the-quickstarts) -além de ser uma ótima primeira experiência com a criação de um aplicativo, guias de início rápido no portal do Azure contêm um botão chamado **fazer essa alteração para mim**. Você pode usar esse botão para definir as propriedades que necessárias, mesmo para um aplicativo existente. Você precisará adaptar os valores dessas propriedades para seu próprio caso. Em particular, a URL da API web para seu aplicativo provavelmente será diferente do padrão proposto, que também terá impacto sobre o URI de saída.
- Portal do Azure para [registrar seu aplicativo manualmente](#register-an-app-using-azure-portal)
- PowerShell e ferramentas de linha de comando

## <a name="register-an-app-using-the-quickstarts"></a>Registrar um aplicativo usando os guias de início rápido

Se você navegar até esse link, você pode criar bootstrap a criação do seu aplicativo web:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

### <a name="register-an-app-using-azure-portal"></a>Registrar um aplicativo usando o portal do Azure

> [!NOTE]
> Para usar o portal é diferente dependendo se o seu aplicativo é executado na nuvem pública do Microsoft Azure ou em uma nuvem soberana ou nacional. Para obter mais informações, consulte [nuvens nacionais](./authentication-national-cloud.md#app-registration-endpoints)

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft. Como alternativa, entrar na nuvem nacional portal do Azure de escolha.
1. Se sua conta der acesso a mais de um locatário, selecione sua conta no canto superior direito e defina a sessão do portal com o Azure AD desejado de locatário.
1. No painel de navegação à esquerda, selecione o **Azure Active Directory** de serviço e, em seguida, selecione **registros de aplicativo** > **novo registro**.
1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo:
   1. Escolha os tipos de conta com suporte para o seu aplicativo (consulte [tipos de conta com suporte](./v2-supported-account-types.md))
   1. Na seção **Nome**, insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo, por exemplo, `AspNetCore-WebApp`.
   1. Na **URI de redirecionamento**, adicione o tipo de aplicativo e o destino de URI que aceitará retornadas respostas de tokens após a autenticação com êxito. Por exemplo: `https://localhost:44321/`.  Selecione **Registrar**.
1. Selecione o menu **Autenticação** e, em seguida, adicione as seguintes informações:
   1. Na **URL de resposta**, adicione `https://localhost:44321/signin-oidc`.
   1. Na seção **Configurações avançadas**, defina **URL de Saída** como `https://localhost:44321/signout-oidc`.
   1. Sob **Concessão implícita**, marque **Tokens de ID**.
   1. Clique em **Salvar**.

### <a name="register-an-app-using-powershell"></a>Registrar um aplicativo usando o PowerShell

> [!NOTE]
> No momento, o PowerShell do Azure AD só cria aplicativos com os seguintes tipos de conta com suporte:
>
> - MyOrg (contas neste diretório organizacional apenas)
> - AnyOrg (contas em qualquer diretório organizacional).
>
> Se você quiser criar um aplicativo que os usuários entrar com suas Accounts pessoal da Microsoft (por exemplo, o Skype, XBox, Outlook.com), você poderá primeiro criar um aplicativo multilocatário (suporte para tipos de conta = contas em qualquer diretório organizacional) e, em seguida, alterar o `signInAudience` propriedade no manifesto do aplicativo do portal do Azure. Isso é explicado em detalhes na etapa [1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) do tutorial do ASP.NET Core (e pode ser generalizada para aplicativos web em qualquer linguagem).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configuração de código do aplicativo](scenario-web-app-sign-user-app-configuration.md)
