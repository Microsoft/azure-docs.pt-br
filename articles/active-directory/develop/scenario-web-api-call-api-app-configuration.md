---
title: Configurar uma API Web que chama APIs da Web | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar uma API Web que chama APIs da Web (configuração de código do aplicativo)
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
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686572"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Uma API Web que chama APIs da Web: configuração de código

Depois de registrar sua API Web, você pode configurar o código para o aplicativo.

O código que você usa para configurar sua API Web para que ele chame APIs da Web downstream com base no código usado para proteger uma API da Web. Para obter mais informações, consulte [API Web protegida: configuração de aplicativo](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="microsoftidentityweb"></a>Microsoft. Identity. Web

A Microsoft recomenda que você use o pacote do NuGet [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) ao desenvolver um ASP.NET Core API protegida chamando APIs da Web downstream. Consulte [API Web protegida: configuração de código | Microsoft. Identity. Web](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) para uma apresentação rápida dessa biblioteca no contexto de uma API da Web.

## <a name="client-secrets-or-client-certificates"></a>Segredos do cliente ou certificados de cliente

Considerando que sua API Web agora chama uma API da Web downstream, forneça um certificado do cliente ou um segredo do cliente na *appsettings.jsno* arquivo. Você também pode adicionar uma seção que especifica:

- A URL da API Web downstream
- Os escopos necessários para chamar a API

No exemplo a seguir, a `GraphBeta` seção especifica essas configurações.

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

Em vez de um segredo do cliente, você pode fornecer um certificado do cliente. O trecho de código a seguir mostra o uso de um certificado armazenado no Azure Key Vault.

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

O Microsoft. Identity. Web fornece várias maneiras de descrever os certificados, tanto por configuração quanto por código. Para obter detalhes, consulte [Microsoft. Identity. Web wiki-usando certificados](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) no github.

## <a name="startupcs"></a>Startup.cs

Sua API da Web precisará adquirir um token para a API downstream. Você o especifica adicionando a `.EnableTokenAcquisitionToCallDownstreamApi()` linha após `.AddMicrosoftIdentityWebApi(Configuration)` . Essa linha expõe o `ITokenAcquisition` serviço, que você pode usar em suas ações de controlador/páginas. No entanto, como você verá nos próximos dois pontos com marcadores, você pode fazer ainda mais. Você também precisará escolher uma implementação de cache de token, por exemplo `.AddInMemoryTokenCaches()` , em *Startup.cs*:

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

Se você não quiser adquirir o token por conta própria, *o Microsoft. Identity. Web* fornece dois mecanismos para chamar uma API Web downstream de outra API. A opção escolhida depende se você deseja chamar Microsoft Graph ou outra API.

### <a name="option-1-call-microsoft-graph"></a>Opção 1: chamar Microsoft Graph

Se você quiser chamar Microsoft Graph, o Microsoft. Identity. Web permite que você use diretamente o `GraphServiceClient` (exposto pelo SDK do Microsoft Graph) em suas ações de API. Para expor Microsoft Graph:

1. Adicione o pacote NuGet [Microsoft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) ao seu projeto.
1. Adicione `.AddMicrosoftGraph()` depois `.EnableTokenAcquisitionToCallDownstreamApi()` no arquivo *Startup.cs* . `.AddMicrosoftGraph()` tem várias substituições. Usando a substituição que usa uma seção de configuração como um parâmetro, o código se torna:

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

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>Opção 2: chamar uma API da Web downstream diferente de Microsoft Graph

Para chamar uma API downstream diferente de Microsoft Graph, o *Microsoft. Identity. Web* fornece `.AddDownstreamWebApi()` , que solicita tokens e chama a API da Web downstream.

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

Assim como acontece com os aplicativos Web, você pode escolher várias implementações de cache de token. Para obter detalhes, consulte [serialização de cache de token da Web da Microsoft Identity](https://aka.ms/ms-id-web/token-cache-serialization) no github.

A imagem a seguir mostra as várias possibilidades de *Microsoft. Identity. Web* e seu impacto no arquivo *Startup.cs* :

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="Diagrama de bloco mostrando opções de configuração de serviço no ponto de inicialização C S para chamar uma API Web e especificar uma implementação de cache de token":::

> [!NOTE]
> Para entender totalmente os exemplos de código aqui, familiarize-se com os [conceitos básicos de ASP.NET Core](/aspnet/core/fundamentals)e, em particular, com [Opções](/aspnet/core/fundamentals/configuration/options)e [injeção de dependência](/aspnet/core/fundamentals/dependency-injection) .

# <a name="java"></a>[Java](#tab/java)

O fluxo em nome de (OBO) é usado para obter um token para chamar a API da Web downstream. Nesse fluxo, sua API Web recebe um token de portador com permissões delegadas do usuário do aplicativo cliente e, em seguida, troca esse token por outro token de acesso para chamar a API da Web downstream.

O código a seguir usa a estrutura de segurança Spring `SecurityContextHolder` na API da Web para obter o token de portador validado. Em seguida, ele usa a biblioteca Java MSAL para obter um token para a API downstream usando a `acquireToken` chamada com `OnBehalfOfParameters` . MSAL armazena em cache o token para que as chamadas subsequentes para a API possam ser usadas `acquireTokenSilently` para obter o token armazenado em cache.

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

O fluxo em nome de (OBO) é usado para obter um token para chamar a API da Web downstream. Nesse fluxo, sua API Web recebe um token de portador com permissões delegadas do usuário do aplicativo cliente e, em seguida, troca esse token por outro token de acesso para chamar a API da Web downstream.

Uma API Web Python precisará usar algum middleware para validar o token de portador recebido do cliente. A API da Web pode obter o token de acesso para a API downstream usando a biblioteca MSAL Python chamando o [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) método. Para obter um exemplo de como usar essa API, consulte o [código de teste para Microsoft-Authentication-library-for-Python no GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472). Consulte também a discussão sobre o [problema 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) no mesmo repositório para obter uma abordagem que ignora a necessidade de um aplicativo de camada intermediária.

Você também pode ver um exemplo da implementação do fluxo OBO no exemplo [MS-Identity-Python-on-em-nome-of](https://github.com/Azure-Samples/ms-identity-python-on-behalf-of) .

---

Você também pode ver um exemplo de implementação de fluxo OBO em [Node.js e Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/Function/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocolo

Para obter mais informações sobre o protocolo OBO, consulte a [plataforma de identidade da Microsoft e o fluxo em nome de do OAuth 2,0](./v2-oauth2-on-behalf-of-flow.md).

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, [adquira um token para o aplicativo](scenario-web-api-call-api-acquire-token.md).
