---
title: Configurar aplicativos da área de trabalho que chamam APIs da Web | Azure
titleSuffix: Microsoft identity platform
description: Saiba como configurar o código de um aplicativo de desktop que chama APIs da Web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 5b74c817a974378a7fefc71e4eed67e6564765be
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756565"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>Aplicativo de área de trabalho que chama APIs da Web: configuração de código

Agora que você criou seu aplicativo, você aprenderá a configurar o código com as coordenadas do aplicativo.

## <a name="microsoft-authentication-libraries"></a>Bibliotecas de autenticação da Microsoft

As seguintes MSALs (bibliotecas de autenticação da Microsoft) oferecem suporte a aplicativos de área de trabalho.

  Biblioteca de Autenticação da Microsoft | Descrição
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Dá suporte à criação de um aplicativo de área de trabalho em várias plataformas, como Linux, Windows e macOS.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Dá suporte à criação de um aplicativo de área de trabalho em várias plataformas.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Dá suporte à criação de um aplicativo de área de trabalho em várias plataformas.
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | Dá suporte a aplicativos de área de trabalho que são executados somente no macOS.

## <a name="public-client-application"></a>Aplicativo cliente público

De um ponto de vista de código, os aplicativos de área de trabalho são aplicativos cliente públicos. A configuração será um pouco diferente se você usar a autenticação interativa ou não.

# <a name="net"></a>[.NET](#tab/dotnet)

Você precisará criar e manipular MSAL.NET `IPublicClientApplication` .

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Exclusivamente por código

O código a seguir cria uma instância de um aplicativo cliente público e entra em usuários na nuvem pública Microsoft Azure com uma conta corporativa ou de estudante ou uma conta Microsoft pessoal.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Se você pretende usar a autenticação interativa ou o fluxo de código do dispositivo, como visto anteriormente, use o `.WithRedirectUri` modificador.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Usar arquivos de configuração

O código a seguir instancia um aplicativo cliente público de um objeto de configuração, que pode ser preenchido de forma programática ou lido de um arquivo de configuração.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Configuração mais elaborada

Você pode elaborar a criação de aplicativos adicionando vários modificadores. Por exemplo, se você quiser que seu aplicativo seja um aplicativo multilocatário em uma nuvem nacional, como o governo dos EUA mostrado aqui, você poderia escrever:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET também contém um modificador para Serviços de Federação do Active Directory (AD FS) 2019:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Por fim, se você quiser adquirir tokens para um locatário Azure Active Directory (Azure AD) B2C, especifique seu locatário, conforme mostrado no seguinte trecho de código:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Saiba mais

Para saber mais sobre como configurar um aplicativo de área de trabalho MSAL.NET:

- Para obter uma lista de todos os modificadores disponíveis em `PublicClientApplicationBuilder` , consulte a documentação de referência [PublicClientApplicationBuilder](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).
- Para obter uma descrição de todas as opções expostas em `PublicClientApplicationOptions` , consulte [PublicClientApplicationOptions](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) na documentação de referência.

### <a name="complete-example-with-configuration-options"></a>Exemplo completo com opções de configuração

Imagine um aplicativo de console do .NET Core que tenha o seguinte `appsettings.json` arquivo de configuração:

```json
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

Você tem pouco código para ler nesse arquivo usando o. Estrutura de configuração fornecida pela rede:

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application runs
 /// in Microsoft Azure public clouds or national or sovereign clouds)
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a JSON file
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

  // Read the auth and graph endpoint configuration
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

Agora, para criar seu aplicativo, escreva o seguinte código:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

Antes da chamada para o `.Build()` método, você pode substituir sua configuração por chamadas para `.WithXXX` métodos, como visto anteriormente.

# <a name="java"></a>[Java](#tab/java)

Aqui está a classe usada em exemplos de desenvolvimento do MSAL Java para configurar os exemplos: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
        .authority(AUTHORITY)
        .build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macos"></a>[MacOS](#tab/macOS)

O código a seguir cria uma instância de um aplicativo cliente público e entra em usuários na nuvem pública Microsoft Azure com uma conta corporativa ou de estudante ou uma conta Microsoft pessoal.

### <a name="quick-configuration"></a>Configuração rápida

Objective-C:

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift:
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Configuração mais elaborada

Você pode elaborar a criação de aplicativos adicionando vários modificadores. Por exemplo, se você quiser que seu aplicativo seja um aplicativo multilocatário em uma nuvem nacional, como o governo dos EUA mostrado aqui, você poderia escrever:

Objective-C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Swift:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, [adquira um token para o aplicativo da área de trabalho](scenario-desktop-acquire-token.md).