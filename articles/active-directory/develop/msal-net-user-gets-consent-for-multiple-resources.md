---
title: Obter consentimento para vários recursos (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como um usuário pode obter o consentimento para vários recursos usando a MSAL.NET (biblioteca de autenticação da Microsoft para .NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: af9dbcbb6c32969b66cd446db634e83ce81f3c92
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98754730"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>O usuário obtém consentimento para vários recursos usando o MSAL.NET
A plataforma de identidade da Microsoft não permite que você obtenha um token para vários recursos de uma vez. Ao usar a MSAL.NET (biblioteca de autenticação da Microsoft para .NET), o parâmetro escopos no método de token de aquisição deve conter apenas escopos para um único recurso. No entanto, você pode conceder o consentimento a vários recursos antecipadamente especificando escopos adicionais usando o `.WithExtraScopeToConsent` método Builder.

> [!NOTE]
> A obtenção de consentimento para vários recursos funciona para a plataforma de identidade da Microsoft, mas não para Azure AD B2C. O Azure AD B2C dá suporte apenas ao consentimento do administrador, não ao consentimento do usuário.

Por exemplo, se você tiver dois recursos que têm 2 escopos cada:

- https: \/ /mytenant.onmicrosoft.com/customerapi (com 2 escopos `customer.read` e `customer.write` )
- https: \/ /mytenant.onmicrosoft.com/vendorapi (com 2 escopos `vendor.read` e `vendor.write` )

Você deve usar o `.WithExtraScopeToConsent` modificador que tem o parâmetro *extraScopesToConsent* , conforme mostrado no exemplo a seguir:

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

Isso lhe obterá um token de acesso para a primeira API Web. Em seguida, quando precisar acessar a segunda API Web, você pode adquirir o token do cache de token silenciosamente:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
