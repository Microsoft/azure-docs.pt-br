---
title: Configurar uma API Web que chama APIs Web | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar uma API Web que chama APIs Web (configuração do código do aplicativo)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5072ae58d3a9412237e70a9bc98970296ce1e1fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686572"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Uma API Web que chama APIs Web: configuração do código

Depois de registrar sua API Web, você pode configurar o código para o aplicativo.

O código que você usa para configurar sua API Web para que ela chame compilações de APIs Web downstream com base no código usado para proteger uma API Web. Para obter mais informações, confira [API Web protegida: configuração de aplicativos](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="microsoftidentityweb"></a>Microsoft.Identity.Web

A Microsoft recomenda o uso do pacote do NuGet [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) ao desenvolver uma API do ASP.NET Core protegida que chama APIs Web downstream. Confira [API Web protegida: configuração do código | Microsoft.Identity.Web](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) para uma apresentação rápida dessa biblioteca no contexto de uma API Web.

## <a name="client-secrets-or-client-certificates"></a>Segredos do cliente ou certificados do cliente

Considerando que sua API Web agora chama uma API Web downstream, forneça um segredo do cliente ou um certificado do cliente no arquivo *appsettings.json*. Você também pode adicionar uma seção que especifica:

- a URL da API Web downstream
- os escopos necessários para chamar a API

No exemplo a seguir, a seção `GraphBeta` especifica essas configurações.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
    }
}
```

Em vez de um segredo do cliente, você pode fornecer um certificado do cliente. O snippet de código a seguir mostra o uso de um certificado armazenado no Azure Key Vault.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
   ]
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

O Microsoft.Identity.Web fornece várias maneiras de descrever os certificados, tanto por configuração quanto por código. Para obter mais detalhes, confira [Wiki Microsoft.Identity.Web – usando certificados](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) no GitHub.

## <a name="startupcs"></a>Startup.cs

Sua API Web precisará adquirir um token para a API downstream. Você o especifica adicionando a linha `.EnableTokenAcquisitionToCallDownstreamApi()` após `.AddMicrosoftIdentityWebApi(Configuration)`. Essa linha expõe o serviço `ITokenAcquisition`, que você pode usar em suas ações de controlador/páginas. No entanto, como você verá nos próximos dois marcadores, pode ser ainda mais simples. Você também precisará escolher uma implementação de cache de token, por exemplo `.AddInMemoryTokenCaches()`, em *Startup.cs*:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Se você não quiser adquirir o token por conta própria, o *Microsoft.Identity.Web* fornece dois mecanismos para chamar uma API Web downstream de outra API. A opção escolhida depende se você deseja chamar o Microsoft Graph ou outra API.

### <a name="option-1-call-microsoft-graph"></a>Opção 1: chamar o Microsoft Graph

Se você quiser chamar o Microsoft Graph, o Microsoft.Identity.Web permitirá que você use diretamente o `GraphServiceClient` (exposto pelo SDK do Microsoft Graph) em suas ações de API. Para expor o Microsoft Graph:

1. Adicione o pacote do NuGet [Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) ao seu projeto.
1. Adicione `.AddMicrosoftGraph()` depois de `.EnableTokenAcquisitionToCallDownstreamApi()` no arquivo *Startup.cs*. `.AddMicrosoftGraph()` tem várias substituições. Usando a substituição que tem uma seção de configuração como um parâmetro, o código se torna:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>Opção 2: chamar uma API Web downstream diferente do Microsoft Graph

Para chamar uma API downstream diferente do Microsoft Graph, o *Microsoft.Identity.Web* fornece `.AddDownstreamWebApi()`, que solicita tokens e chama a API Web downstream.

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Assim como acontece com os aplicativos Web, você pode escolher várias implementações de cache de token. Para obter mais detalhes, confira [Microsoft Identity Web – serialização de cache de token](https://aka.ms/ms-id-web/token-cache-serialization) no GitHub.

A seguinte imagem mostra as várias possibilidades do *Microsoft.Identity.Web* e o impacto no arquivo *Startup.cs*:

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="Diagrama de bloco mostrando opções de configuração de serviço no ponto de inicialização C S para chamar uma API Web e especificar uma implementação de cache de token":::

> [!NOTE]
> Para entender totalmente os exemplos de código aqui, você precisa estar familiarizado com os [conceitos básicos do ASP.NET Core](/aspnet/core/fundamentals) e, particularmente, com a [injeção de dependência](/aspnet/core/fundamentals/dependency-injection) e as [opções](/aspnet/core/fundamentals/configuration/options).

# <a name="java"></a>[Java](#tab/java)

O fluxo OBO (On-behalf-Of) é usado para obter um token para chamar a API Web downstream. Nesse fluxo, sua API Web recebe um token de portador com permissões delegadas do usuário do aplicativo cliente e depois troca esse token por outro token de acesso para chamar a API Web downstream.

O código abaixo usa a estrutura do Spring Security `SecurityContextHolder` na API Web para obter o token de portador validado. Em seguida, ele usa a biblioteca Java MSAL para obter um token para a API downstream usando a chamada `acquireToken` com `OnBehalfOfParameters`. O MSAL armazena em cache o token para que as chamadas subsequentes para a API possam usar `acquireTokenSilently` para obter o token armazenado em cache.

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

O fluxo OBO (On-behalf-Of) é usado para obter um token para chamar a API Web downstream. Nesse fluxo, sua API Web recebe um token de portador com permissões delegadas do usuário do aplicativo cliente e depois troca esse token por outro token de acesso para chamar a API Web downstream.

Uma API Web do Python precisará usar algum middleware para validar o token de portador recebido do cliente. A API Web pode obter o token de acesso para API downstream usando a biblioteca Python MSAL chamando o método [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of). Para obter um exemplo de como usar essa API, confira o [código de teste para microsoft-authentication-library-for-python no GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472). Confira também a discussão sobre o [problema 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) no mesmo repositório para obter uma abordagem que ignora a necessidade de um aplicativo de camada intermediária.

Você também pode ver um exemplo da implementação do fluxo OBO no exemplo [ms-identity-python-on-behalf-of](https://github.com/Azure-Samples/ms-identity-python-on-behalf-of).

---

Você também pode ver um exemplo de implementação de fluxo OBO em [Node.js e Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/Function/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocolo

Para obter mais informações sobre o protocolo OBO, confira a [plataforma de identidade da Microsoft e o fluxo On-Behalf-Of do OAuth 2.0](./v2-oauth2-on-behalf-of-flow.md).

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, [Adquirir um token para o aplicativo](scenario-web-api-call-api-acquire-token.md).
