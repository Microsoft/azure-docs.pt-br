---
title: Mover o aplicativo da área de trabalho chamando APIs da Web para produção | Azure
titleSuffix: Microsoft identity platform
description: Saiba como mover um aplicativo de área de trabalho que chama APIs da Web para produção
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 22e61ea767d781dc9da54d61143c1b2524e06e94
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584407"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>Aplicativo de área de trabalho que chama APIs da Web: mover para produção

Neste artigo, você aprende a mover seu aplicativo de área de trabalho que chama APIs da Web para produção.

## <a name="handle-errors-in-desktop-applications"></a>Tratar erros em aplicativos de área de trabalho

Nos fluxos diferentes, você aprendeu como lidar com os erros dos fluxos silenciosos, conforme mostrado nos trechos de código. Você também viu que há casos em que a interação é necessária, como no consentimento incremental e no acesso condicional.

## <a name="have-the-user-consent-upfront-for-several-resources"></a>Ter o consentimento do usuário antecipado para vários recursos

> [!NOTE]
> A obtenção de consentimento para vários recursos funciona para a plataforma de identidade da Microsoft, mas não para o Azure Active Directory (Azure AD) B2C. O Azure AD B2C dá suporte apenas ao consentimento do administrador, não ao consentimento do usuário.

Você não pode obter um token para vários recursos de uma vez com a plataforma de identidade da Microsoft. O `scopes` parâmetro pode conter escopos para um único recurso. Você pode garantir que o usuário tenha o mesmo consentimento para vários recursos usando o `extraScopesToConsent` parâmetro.

Por exemplo, você pode ter dois recursos que têm dois escopos cada:

- `https://mytenant.onmicrosoft.com/customerapi` com os escopos `customer.read` e `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` com os escopos `vendor.read` e `vendor.write`

Neste exemplo, use o `.WithExtraScopesToConsent` modificador que tem o `extraScopesToConsent` parâmetro.

Por exemplo:

### <a name="in-msalnet"></a>No MSAL.NET

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
                     .WithExtraScopesToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

### <a name="in-msal-for-ios-and-macos"></a>Na MSAL para iOS e macOS

Objective-C:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Swift:

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Essa chamada Obtém um token de acesso para a primeira API Web.

Ao chamar a segunda API Web, chame a `AcquireTokenSilent` API.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>A conta pessoal da Microsoft requer o reconsentimento sempre que o aplicativo é executado

Para usuários de contas pessoais da Microsoft, solicitar o consentimento de cada chamada de cliente nativo (desktop ou aplicativo móvel) para autorizar é o comportamento pretendido. A identidade nativa do cliente é inerentemente insegura, o que é contrário da identidade confidencial do aplicativo cliente. Os aplicativos cliente confidenciais trocam um segredo com a plataforma de identidade da Microsoft para provar sua identidade. A plataforma de identidade da Microsoft optou por mitigar essa insegurança para os serviços do consumidor solicitando o consentimento do usuário sempre que o aplicativo for autorizado.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Próximas etapas

Para experimentar exemplos adicionais, consulte [aplicativos cliente públicos móveis e de desktop](sample-v2-code.md#desktop-and-mobile-public-client-apps).



