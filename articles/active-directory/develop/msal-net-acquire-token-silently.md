---
title: Adquirir um token silenciosamente (biblioteca de autenticação da Microsoft para .NET) | Azure
description: Saiba como adquirir um token de acesso silenciosamente (do cache de token) usando a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 309e912f2adf5249770b40a631ed62f7cb3113e5
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277956"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Obter um token do cache de token usando MSAL.NET

Quando você adquire um token de acesso usando a MSAL.NET (biblioteca de autenticação da Microsoft para .NET), o token é armazenado em cache. Quando o aplicativo precisa de um token, ele deve primeiro chamar `AcquireTokenSilent` o método para verificar se um token aceitável está no cache. Em muitos casos, é possível adquirir outro token com mais escopos com base em um token no cache. Também é possível atualizar um token quando ele está ficando próximo da expiração (pois o cache do token também contém um token de atualização).

O padrão recomendado é chamar o `AcquireTokenSilent` método primeiro.  Se `AcquireTokenSilent` falhar, adquira um token usando outros métodos.

No exemplo a seguir, o aplicativo tenta primeiro adquirir um token do cache de token.  Se uma `MsalUiRequiredException` exceção for lançada, o aplicativo adquirirá um token interativamente. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
