---
title: Mover o aplicativo Web que faz logon dos usuários para produção | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo Web que faz logon de usuários (mover para produção)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e4a47112d2f66edc8af9b7f100d48bc205f2e85e
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584290"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Aplicativo Web que conecta usuários: mover para produção

Agora que você sabe como obter um token para chamar APIs da Web, aqui estão algumas coisas a serem consideradas ao mover seu aplicativo para produção.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="troubleshooting"></a>Solução de problemas
Quando os usuários entram no aplicativo Web pela primeira vez, eles precisarão consentir. No entanto, em algumas organizações, os usuários podem ver uma mensagem semelhante à seguinte: *AppName precisa de permissões para acessar recursos em sua organização que somente um administrador pode conceder. Peça a um administrador para conceder permissão a este aplicativo antes de poder usá-lo.*
Isso ocorre porque o administrador de locatários **desabilitou** a capacidade de consentimento dos usuários. Nesse caso, entre em contato com seus administradores de locatários para que eles façam um consentimento de administrador para os escopos exigidos pelo aplicativo.

## <a name="same-site"></a>Mesmo site

Verifique se você entendeu possíveis problemas com as novas versões do navegador Chrome: [como lidar com alterações de cookie SameSite no navegador Chrome](howto-handle-samesite-cookie-changes-chrome-browser.md).

O pacote NuGet Microsoft. Identity. Web lida com os problemas mais comuns de SameSite.

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Aprofunde-se: tutorial do aplicativo Web ASP.NET Core

Saiba mais sobre outras maneiras de conectar usuários com este ASP.NET Core tutorial: 

[Habilite seus aplicativos Web para conectar usuários e chamar APIs com a plataforma de identidade da Microsoft para desenvolvedores](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)

Este tutorial progressivo tem código pronto para produção para um aplicativo Web, incluindo como adicionar entrada com contas no:

- Sua organização
- Várias organizações
- Contas corporativas ou de estudante ou contas pessoais da Microsoft
- [Azure AD B2C](../../active-directory-b2c/overview.md)
- Nuvens nacionais

## <a name="sample-code-java-web-app"></a>Código de exemplo: aplicativo Web Java

Saiba mais sobre o aplicativo Web Java deste exemplo no GitHub: 

[Um aplicativo Web Java que conecta usuários com a plataforma de identidade da Microsoft e chama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)

## <a name="next-steps"></a>Próximas etapas

Depois que seu aplicativo Web entra em usuários, ele pode chamar APIs da Web em nome dos usuários conectados. Chamar APIs da Web do aplicativo Web é o objeto do seguinte cenário: [aplicativo Web que chama APIs da Web](scenario-web-app-call-api-overview.md).