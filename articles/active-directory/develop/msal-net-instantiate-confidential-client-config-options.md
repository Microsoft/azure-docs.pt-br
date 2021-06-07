---
title: Criar uma instância de um aplicativo cliente confidencial (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar uma instância de um aplicativo cliente confidencial com opções de configuração usando a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
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
ms.openlocfilehash: d477c419bb677a6b8f24a3aae26c403e47cc96cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99583935"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Criar uma instância de um aplicativo cliente confidencial com opções de configuração usando MSAL.NET

Este artigo descreve como criar uma instância de um [aplicativo cliente confidencial](msal-client-applications.md) usando a MSAL.net (biblioteca de autenticação da Microsoft para .net).  O aplicativo é instanciado com as opções de configuração definidas em um arquivo de configurações.

Antes de inicializar um aplicativo, primeiro você precisa [registrá](quickstart-register-app.md) -lo para que seu aplicativo possa ser integrado à plataforma de identidade da Microsoft. Após o registro, talvez você precise das seguintes informações (que podem ser encontradas no portal do Azure):

- A ID do cliente (uma cadeia de caracteres que representa um GUID)
- A URL do provedor de identidade (chamada de instância) e o público-alvo de entrada para seu aplicativo. Esses dois parâmetros são coletivamente conhecidos como autoridade.
- A ID do locatário se você estiver escrevendo um aplicativo de linha de negócios somente para sua organização (também chamado de aplicativo de locatário único).
- O segredo do aplicativo (cadeia de caracteres secreta do cliente) ou certificado (do tipo X509Certificate2) se for um aplicativo cliente confidencial.
- Para aplicativos Web e, às vezes, para aplicativos cliente públicos (em particular quando seu aplicativo precisa usar um agente), você também terá definido o redirectUri em que o provedor de identidade entrará em contato com o seu aplicativo com os tokens de segurança.

## <a name="configure-the-application-from-the-config-file"></a>Configurar o aplicativo a partir do arquivo de configuração
O nome das propriedades das opções em MSAL.NET corresponde ao nome das propriedades do `AzureADOptions` no ASP.NET Core, portanto, você não precisa escrever nenhum código de União.

Uma configuração de aplicativo ASP.NET Core é descrita em um *appsettings.jsno* arquivo:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

A partir do MSAL.NET v3. x, você pode configurar seu aplicativo cliente confidencial do arquivo de configuração.

Na classe em que você deseja configurar e instanciar seu aplicativo, declare um `ConfidentialClientApplicationOptions` objeto.  Associe a configuração lida da origem (incluindo a appconfig.jsno arquivo) à instância das opções de aplicativo, usando o `IConfigurationRoot.Bind()` método do [Microsoft.Extensions.Configuração. Pacote NuGet do Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder):

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Isso permite que o conteúdo da seção "AzureAD" do *appsettings.jsno* arquivo seja associado às propriedades correspondentes do `ConfidentialClientApplicationOptions` objeto.  Em seguida, crie um `ConfidentialClientApplication` objeto:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Adicionar configuração de tempo de execução
Em um aplicativo cliente confidencial, normalmente você tem um cache por usuário. Portanto, será necessário obter o cache associado ao usuário e informar ao construtor de aplicativos que você deseja usá-lo. Da mesma forma, você pode ter um URI de redirecionamento calculado dinamicamente. Nesse caso, o código é o seguinte:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

