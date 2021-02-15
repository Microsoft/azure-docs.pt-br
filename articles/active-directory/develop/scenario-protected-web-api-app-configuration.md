---
title: Configurar aplicativos de API Web protegidos | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar uma API Web protegida e configurar o código do aplicativo.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: bdf74a6d8b6798de8aba33baea5afdf987ce1c34
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582478"
---
# <a name="protected-web-api-code-configuration"></a>API Web protegida: configuração de código

Para configurar o código para sua API Web protegida, entenda:

- O que define as APIs como protegidas.
- Como configurar um token de portador.
- Como validar o token.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>O que define as APIs ASP.NET e ASP.NET Core como protegidas?

Assim como os aplicativos Web, as APIs da Web ASP.NET e ASP.NET Core são protegidas porque suas ações de controlador são prefixadas com o atributo **[Authorize]** . As ações do controlador poderão ser chamadas somente se a API for chamada com uma identidade autorizada.

Considere as seguintes perguntas:

- Somente um aplicativo pode chamar uma API da Web. Como a API sabe a identidade do aplicativo que a chama?
- Se o aplicativo chamar a API em nome de um usuário, qual é a identidade do usuário?

## <a name="bearer-token"></a>Token de portador

O token de portador definido no cabeçalho quando o aplicativo é chamado contém informações sobre a identidade do aplicativo. Ele também contém informações sobre o usuário, a menos que o aplicativo Web aceite chamadas de serviço a serviço de um aplicativo daemon.

Aqui está um exemplo de código em C# que mostra um cliente que chama a API depois de adquirir um token com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET):

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Um aplicativo cliente solicita o token de portador para a plataforma de identidade *da Microsoft para a API da Web*. A API Web é o único aplicativo que deve verificar o token e exibir as declarações que ele contém. Os aplicativos cliente nunca devem tentar inspecionar as declarações em tokens.
>
> No futuro, a API Web pode exigir que o token seja criptografado. Esse requisito impediria o acesso para aplicativos cliente que podem exibir tokens de acesso.

## <a name="jwtbearer-configuration"></a>Configuração do JwtBearer

Esta seção descreve como configurar um token de portador.

### <a name="config-file"></a>Arquivo de configuração

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

#### <a name="case-where-you-used-a-custom-app-id-uri-for-your-web-api"></a>Caso em que você usou um URI de ID de aplicativo personalizado para sua API Web

Se você aceitou o URI da ID do aplicativo proposto pelo portal de registro do aplicativo, não precisará especificar o público (consulte o [URI e os escopos da ID do aplicativo](scenario-protected-web-api-app-registration.md#application-id-uri-and-scopes)). Caso contrário, você deve adicionar uma `Audience` propriedade cujo valor é o URI da ID do aplicativo para sua API da Web.

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common",
    "Audience": "custom App ID URI for your web API"
  },
  // more lines
}
```

### <a name="code-initialization"></a>Inicialização de código

Quando um aplicativo é chamado em uma ação do controlador que contém um atributo **[Authorize]** , ASP.NET e ASP.NET Core extrai o token de acesso do token de portador do cabeçalho de autorização. O token de acesso é encaminhado para o middleware JwtBearer, que chama as extensões Microsoft IdentityModel para .NET.

#### <a name="microsoftidentityweb"></a>Microsoft. Identity. Web

A Microsoft recomenda que você use o pacote NuGet [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) ao desenvolver uma API Web com ASP.NET Core.

O _Microsoft. Identity. Web_ fornece a cola entre ASP.NET Core, o middleware de autenticação e a [MSAL (biblioteca de autenticação da Microsoft)](msal-overview.md) para .net. Ele permite uma experiência de desenvolvedor mais clara e robusta e aproveita o poder da plataforma de identidade da Microsoft e Azure AD B2C.

#### <a name="using-microsoftidentityweb-templates"></a>Usando modelos Microsoft. Identity. Web

Você pode criar uma API Web do zero usando modelos de projeto Microsoft. Identity. Web. Para obter detalhes, consulte [Microsoft. Identity. Web-modelo de projeto de API Web](https://aka.ms/ms-id-web/webapi-project-templates).

#### <a name="starting-from-an-existing-aspnet-core-31-application"></a>Iniciando com base em um aplicativo ASP.NET Core 3,1 existente

Hoje, ASP.NET Core 3,1 usa a biblioteca Microsoft. AspNetCore. AzureAD. UI. O middleware é inicializado no arquivo Startup.cs.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

O middleware é adicionado à API Web por esta instrução:

```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
  services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
          .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
}
```

 Atualmente, os modelos de ASP.NET Core criam APIs da Web do Azure Active Directory (Azure AD) que conectam usuários em sua organização ou em qualquer organização. Eles não entram em usuários com contas pessoais. No entanto, você pode alterar os modelos para usar a plataforma de identidade da Microsoft usando [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) , substituindo o código em *Startup.cs*:

```csharp
using Microsoft.Identity.Web;
```

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddMicrosoftIdentityWebApiAuthentication(Configuration, "AzureAd");

 services.AddControllers();
}
```

Você também pode escrever o seguinte (que é equivalente)

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
             .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");

services.AddControllers();
}
```

> [!NOTE]
> Se você usar Microsoft. Identity. Web e não definir o `Audience` no *appsettings.jsem*, o seguinte será usado:
> -  `$"{ClientId}"` Se você tiver definido a [versão aceita do token de acesso](scenario-protected-web-api-app-registration.md#accepted-token-version) para `2` , ou para Azure ad B2C APIs Web.
> - `$"api://{ClientId}` em todos os outros casos (para [tokens de acesso](access-tokens.md)v 1.0).
> Para obter detalhes, consulte [código-fonte](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RegisterValidAudience.cs#L70-L83)Microsoft. Identity. Web.

O trecho de código anterior é extraído do [tutorial ASP.NET Core Web API incremental](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/63087e83326e6a332d05fee6e1586b66d840b08f/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Startup.cs#L23-L28). Os detalhes do **AddMicrosoftIdentityWebApiAuthentication** estão disponíveis em [Microsoft. Identity. Web](microsoft-identity-web.md). Esse método chama [AddMicrosoftIdentityWebAPI](/dotnet/api/microsoft.identity.web.microsoftidentitywebapiauthenticationbuilderextensions.addmicrosoftidentitywebapi?preserve-view=true&view=azure-dotnet-preview), que, por sua vez, instrui o middleware sobre como validar o token.

## <a name="token-validation"></a>Validação de token

No trecho anterior, o middleware JwtBearer, como o middleware OpenID Connect em aplicativos Web, valida o token com base no valor de `TokenValidationParameters` . O token é descriptografado conforme necessário, as declarações são extraídas e a assinatura é verificada. Em seguida, o middleware valida o token verificando os dados:

- Público-alvo: o token é direcionado para a API Web.
- Sub: ele foi emitido para um aplicativo que tem permissão para chamar a API da Web.
- Emissor: ele foi emitido por um serviço de token de segurança (STS) confiável.
- Expiração: seu tempo de vida está no intervalo.
- Assinatura: ela não foi violada.

Também pode haver validações especiais. Por exemplo, é possível validar que as chaves de assinatura, quando inseridas em um token, sejam confiáveis e que o token não esteja sendo repetido. Por fim, alguns protocolos exigem validações específicas.

### <a name="validators"></a>Validadores

As etapas de validação são capturadas em validadores, que são fornecidas pela biblioteca do [Microsoft IdentityModel Extensions para .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) Open-Source. Os validadores são definidos no arquivo de origem da biblioteca [Microsoft. IdentityModel. Tokens/validators. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Esta tabela descreve os validadores:

| Validador | Descrição |
|---------|---------|
| **ValidateAudience** | Garante que o token seja para o aplicativo que valida o token para você. |
| **ValidateIssuer** | Garante que o token foi emitido por um STS confiável, o que significa que ele é de alguém em que você confia. |
| **ValidateIssuerSigningKey** | Garante que o aplicativo que valida o token confie na chave que foi usada para assinar o token. Há um caso especial em que a chave é inserida no token. Mas esse caso normalmente não ocorre. |
| **ValidateLifetime** | Garante que o token ainda seja válido ou já seja válida. O validador verifica se o tempo de vida do token está no intervalo especificado pelas declarações **nobefore** e **Expires** . |
| **ValidateSignature** | Garante que o token não tenha sido adulterado. |
| **ValidateTokenReplay** | Garante que o token não seja reproduzido. Há um caso especial para alguns protocolos de uso de OneTime. |

#### <a name="customizing-token-validation"></a>Personalizando a validação de token

Os validadores são associados às propriedades da classe **TokenValidationParameters** . As propriedades são inicializadas a partir do ASP.NET e ASP.NET Core configuração.

Na maioria dos casos, você não precisa alterar os parâmetros. Aplicativos que não são locatários únicos são exceções. Esses aplicativos Web aceitam usuários de qualquer organização ou de contas pessoais da Microsoft. Os emissores nesse caso devem ser validados. O Microsoft. Identity. Web cuida da validação do emissor também. Para obter detalhes, consulte Microsoft. Identity. Web [AadIssuerValidator](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

Em ASP.NET Core, se você quiser personalizar os parâmetros de validação de token, use o trecho a seguir em seu *Startup.cs*:

```c#
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApi(Configuration);
services.Configure<JwtBearerOptions>(JwtBearerDefaults.AuthenticationScheme, options =>
{
  var existingOnTokenValidatedHandler = options.Events.OnTokenValidated;
  options.Events.OnTokenValidated = async context =>
  {
       await existingOnTokenValidatedHandler(context);
      // Your code to add extra configuration that will be executed after the current event implementation.
      options.TokenValidationParameters.ValidIssuers = new[] { /* list of valid issuers */ };
      options.TokenValidationParameters.ValidAudiences = new[] { /* list of valid audiences */};
  }
});
```

Para o ASP.NET MVC, o exemplo de código a seguir mostra como fazer a validação de token personalizada:

https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation

## <a name="token-validation-in-azure-functions"></a>Validação de token no Azure Functions

Você também pode validar tokens de acesso de entrada no Azure Functions. Você pode encontrar exemplos de tal validação nos exemplos de código a seguir no GitHub:

- .NET: [Azure-Samples/MS-Identity-dotnet-webAPI-azurefunctions](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)
- Node.js: [Azure-Samples/MS-Identity-NodeJS-webAPI-azurefunctions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)
- Python: [Azure-Samples/MS-Identity-Python-webAPI-azurefunctions)](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, verifique os [escopos e as funções de aplicativo em seu código](scenario-protected-web-api-verification-scope-app-roles.md).