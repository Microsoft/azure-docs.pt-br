---
title: Conectar usuários de um aplicativo Web | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo Web que faz logon de usuários (visão geral)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 960c00f516aac0c804718e439ae3ed9691759329
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010670"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Cenário: Aplicativo Web que conecta usuários

Saiba tudo o que você precisa para criar um aplicativo Web que usa a plataforma de identidade da Microsoft para conectar usuários.

## <a name="getting-started"></a>Introdução

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Se você quiser criar seu primeiro aplicativo Web portátil (ASP.NET Core) que conecte os usuários, siga este guia de início rápido:

[Início rápido: ASP.NET Core aplicativo Web que assina usuários](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Se você quiser entender como adicionar a entrada a um aplicativo Web ASP.NET existente, tente o início rápido a seguir:

[Início rápido: aplicativo Web ASP.NET que assina usuários](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Se você for um desenvolvedor de Java, tente o início rápido a seguir:

[Início Rápido: Adicionar uma entrada com a Microsoft a um aplicativo Web Java](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

Se você desenvolver com Python, experimente o início rápido a seguir:

[Início Rápido: Adicionar a opção Entrar com a conta da Microsoft a um aplicativo Web do Python](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Visão geral

Você adiciona autenticação ao seu aplicativo Web para que ele possa conectar usuários. A adição de autenticação permite que seu aplicativo Web acesse informações de perfil limitadas para personalizar a experiência para os usuários.

Os aplicativos Web autenticam um usuário em um navegador da Web. Nesse cenário, o aplicativo Web direciona o navegador do usuário para conectá-lo ao Azure Active Directory (Azure AD). O Azure AD retorna uma resposta de entrada por meio do navegador do usuário, que contém declarações sobre o usuário em um token de segurança. A entrada de usuários aproveita o protocolo padrão do [Open ID Connect](./v2-protocols-oidc.md) , simplificado pelo uso de [bibliotecas](scenario-web-app-sign-user-app-configuration.md#microsoft libraries supporting web apps)de middleware.

![O aplicativo Web conecta usuários](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Como uma segunda fase, você pode habilitar seu aplicativo para chamar APIs da Web em nome do usuário conectado. Esta próxima fase é um cenário diferente, que você encontrará no [aplicativo Web que chama APIs da Web](scenario-web-app-call-api-overview.md).

> [!NOTE]
> A adição de entrada a um aplicativo Web é sobre a proteção do aplicativo Web e a validação de um token de usuário, que é o que as bibliotecas de  **middleware** fazem. No caso do .NET, esse cenário ainda não requer a MSAL (biblioteca de autenticação da Microsoft), que é sobre a aquisição de um token para chamar APIs protegidas. As bibliotecas de autenticação serão introduzidas no cenário de acompanhamento, quando o aplicativo Web precisar chamar APIs da Web.

## <a name="specifics"></a>Especificações

- Durante o registro do aplicativo, você precisará fornecer um ou vários URIs de resposta (se você implantar seu aplicativo em vários locais). Em alguns casos (ASP.NET e ASP.NET Core), você precisará habilitar o token de ID. Por fim, você desejará configurar um URI de saída para que seu aplicativo reaja aos usuários saindo.
- No código do seu aplicativo, você precisará fornecer a autoridade para a qual seu aplicativo Web delega a entrada. Talvez você queira personalizar a validação de token (em particular, em cenários de parceiros).
- Os aplicativos Web oferecem suporte a qualquer tipo de conta. Para obter mais informações, consulte [tipos de conta com suporte](v2-supported-account-types.md).

## <a name="recommended-reading"></a>Leitura recomendada

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Próximas etapas

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Vá para o próximo artigo neste cenário, registro de [aplicativo](./scenario-web-app-sign-user-app-registration.md?tabs=aspnetcore).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Vá para o próximo artigo neste cenário, registro de [aplicativo](./scenario-web-app-sign-user-app-registration.md?tabs=aspnet).

# <a name="java"></a>[Java](#tab/java)

Vá para o próximo artigo neste cenário, registro de [aplicativo](./scenario-web-app-sign-user-app-registration.md?tabs=java).

# <a name="python"></a>[Python](#tab/python)

Vá para o próximo artigo neste cenário, registro de [aplicativo](./scenario-web-app-sign-user-app-registration.md?tabs=python).

---