---
title: Aplicativo de desktop que chamadas de web (configuração do código) – APIs de plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo da área de trabalho que chama APIs (configuração de código do aplicativo) de web
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
ms.openlocfilehash: 600b6db1eb3d3b422d62e49c5bc816a1a56370f9
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798521"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Aplicativo da área de trabalho que chama APIs - configuração de código de web

Agora que você criou seu aplicativo, você aprenderá a configurar o código com coordenadas do aplicativo.

## <a name="msal-libraries"></a>Bibliotecas MSAL

A biblioteca MSAL única que dão suporte a aplicativos de desktop é MSAL.NET

## <a name="public-client-application"></a>Aplicativo cliente público

Do ponto de vista do código, aplicativos da área de trabalho são aplicativos de cliente público e é por isso que você compilará e manipular MSAL.NET `IPublicClientApplication`. Novamente as coisas será um pouco diferente se você usar a autenticação interativa ou não.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Exclusivamente pelo código

O código a seguir cria uma instância de um aplicativo cliente público, usuários de entrar na nuvem público do Microsoft Azure, com um trabalho e conta de estudante ou uma conta pessoal da Microsoft.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Se você pretende usar a autenticação interativa ou fluxo de código de dispositivo, como visto acima, você deseja usar o `.WithRedirectUri` modificador:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>Usando arquivos de configuração

O código a seguir cria uma instância de um aplicativo cliente público de um objeto de configuração, que pode ser preenchido programaticamente ou ler de um arquivo de configuração

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Configuração mais elaborada

Você pode elaborar, adicionando um número de modificadores de criação. Por exemplo, se desejar que o aplicativo seja um aplicativo multilocatário em uma nuvem Nacional (aqui governo dos EUA), você poderia escrever:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET também contém um modificador de 2019 ADFS:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Por fim, se você quiser adquirir tokens de um locatário do Azure AD B2C, pode especificar seu locatário, conforme mostrado no trecho de código a seguir:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Saiba mais

Para saber mais sobre como configurar um aplicativo de área de trabalho MSAL.NET:

- Para obter a lista de todos os modificadores disponíveis no `PublicClientApplicationBuilder`, consulte a documentação de referência [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- Para obter a descrição de todas as opções expostas na `PublicClientApplicationOptions` ver [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions), na documentação de referência

## <a name="complete-example-with-configuration-options"></a>Exemplo completo, com opções de configuração

Imagine um aplicativo de console .NET Core que tem as seguintes `appsettings.json` arquivo de configuração:

```JSon
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

Você tem pouco código para ler esse arquivo usando o .NET fornecido a estrutura de configuração;

```CSharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
 /// in Microsoft Azure public clouds or national / sovereign clouds
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a json file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint config
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

Agora, para criar seu aplicativo, você apenas precisará escrever o código a seguir:

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

e antes da chamada para o `.Build()` método, você pode substituir a configuração com chamadas para `.WithXXX` métodos como visto anteriormente.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adquirir um token para um aplicativo da área de trabalho](scenario-desktop-acquire-token.md)
