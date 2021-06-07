---
title: Remover contas do cache de token ao sair | Azure
titleSuffix: Microsoft identity platform
description: Saiba como remover uma conta do cache de token ao sair
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9fc271dfa9edbedac8527009dd2b2180b7c5e7cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98756254"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Um aplicativo Web que chama APIs da Web: remover contas do cache de token na saída global

Você aprendeu a adicionar a entrada ao seu aplicativo Web no [aplicativo Web que conecta os usuários: entrar e sair](scenario-web-app-sign-user-sign-in.md).

A saída é diferente para um aplicativo Web que chama APIs da Web. Quando o usuário sai do seu aplicativo ou de qualquer aplicativo, você deve remover os tokens associados a esse usuário do cache de token.

## <a name="intercept-the-callback-after-single-sign-out"></a>Interceptar o retorno de chamada após a saída única

Para limpar a entrada de cache de token associada à conta que foi desconectada, seu aplicativo pode interceptar o `logout` evento After. Os aplicativos Web armazenam tokens de acesso para cada usuário em um cache de token. Ao interceptar o `logout` retorno de chamada após, o aplicativo Web pode remover o usuário do cache.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft. Identity. Web cuida da implementação da saída para você. Para obter detalhes, consulte [código-fonte Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L168-L176)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

O exemplo ASP.NET não remove contas do cache na saída global.

# <a name="java"></a>[Java](#tab/java)

O exemplo de Java não remove contas do cache na saída global.

# <a name="python"></a>[Python](#tab/python)

O exemplo de Python não remove contas do cache na saída global.

---

## <a name="next-steps"></a>Próximas etapas

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Vá para o próximo artigo neste cenário, [adquira um token para o aplicativo Web](./scenario-web-app-call-api-acquire-token.md?tabs=aspnetcore).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Vá para o próximo artigo neste cenário, [adquira um token para o aplicativo Web](./scenario-web-app-call-api-acquire-token.md?tabs=aspnet).

# <a name="java"></a>[Java](#tab/java)

Vá para o próximo artigo neste cenário, [adquira um token para o aplicativo Web](./scenario-web-app-call-api-acquire-token.md?tabs=java).

# <a name="python"></a>[Python](#tab/python)

Vá para o próximo artigo neste cenário, [adquira um token para o aplicativo Web](./scenario-web-app-call-api-acquire-token.md?tabs=python).

---