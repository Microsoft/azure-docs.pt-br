---
title: Aplicativo Web que chamadas de web APIs (chamando uma API da web) - plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo Web que chama APIs (chamando uma API da web) da web
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
ms.openlocfilehash: 3624f4e859081e53ee27b6f8415eb3f9b5a2a5fa
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785471"
---
# <a name="web-app-that-calls-web-apis---call-a-web-api"></a>Aplicativo Web que chama APIs - web chamar uma API web

Agora que você tem um token, você pode chamar uma API web protegida.

## <a name="aspnet-core"></a>ASP.NET Core

Aqui está um código simplificado da ação do `HomeController`. Esse código obtém um token para chamar o Microsoft Graph. Esse código de tempo foi adicionado, que mostra como chamar o Microsoft Graph como uma API REST. A URL para a API do graph é fornecida na `appsettings.json` de arquivos e ler em uma variável chamada `webOptions`:

```JSon
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    ...
  },
  ...
  "GraphApiUrl": "https://graph.microsoft.com"
}
```

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;

 // Calls the web API (here the graph)
 HttpClient httpClient = new HttpClient();
 httpClient.DefaultRequestHeaders.Authorization =
     new AuthenticationHeaderValue(Constants.BearerAuthorizationScheme,accessToken);
 var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

 if (response.StatusCode == HttpStatusCode.OK)
 {
  var content = await response.Content.ReadAsStringAsync();

  dynamic me = JsonConvert.DeserializeObject(content);
  return me;
 }

 ViewData["Me"] = me;
 return View();
}
```

> [!NOTE]
> Você pode usar o mesmo princípio para chamar qualquer API da web.
>
> APIs de web mais do Azure fornecem um SDK que simplifica a chamá-lo. Isso também é o caso do Microsoft Graph. Você aprenderá no próximo artigo onde encontrar um tutorial que ilustra esses aspectos.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Mover para a produção](scenario-web-app-call-api-production.md)
