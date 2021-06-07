---
title: Limpar o cache de token (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como limpar o cache de token usando a MSAL.NET (biblioteca de autenticação da Microsoft para .NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 3049a1213b8b92153fc0fce96b2dadace01a4ca8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98064752"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Limpar o cache de token usando MSAL.NET

Quando você [adquire um token de acesso](msal-acquire-cache-tokens.md) usando a MSAL.net (biblioteca de autenticação da Microsoft para .net), o token é armazenado em cache. Quando o aplicativo precisa de um token, ele deve primeiro chamar o `AcquireTokenSilent` método para verificar se um token aceitável está no cache. 

Limpar o cache é obtido removendo as contas do cache. No entanto, isso não remove o cookie de sessão que está no navegador.  O exemplo a seguir instancia um aplicativo cliente público, obtém as contas do aplicativo e remove as contas.

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

Para saber mais sobre como adquirir e armazenar em cache tokens, leia [adquirir um token de acesso](msal-acquire-cache-tokens.md).
