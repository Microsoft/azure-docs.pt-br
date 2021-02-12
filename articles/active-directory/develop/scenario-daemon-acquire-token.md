---
title: Adquirir tokens para chamar uma API Web (aplicativo daemon) – a plataforma Microsoft Identity | Azure
description: Saiba como criar um aplicativo daemon que chama APIs da Web (adquirindo Tokens)
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
ms.openlocfilehash: 295897be03a7dd8e397e8202ff1cf10e6d59cdfb
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753860"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Aplicativo daemon que chama APIs da Web – adquirir um token

Depois de construir um aplicativo cliente confidencial, você pode adquirir um token para o aplicativo chamando `AcquireTokenForClient` , passando o escopo e, opcionalmente, forçando uma atualização do token.

## <a name="scopes-to-request"></a>Escopos a serem solicitados

O escopo a ser solicitado para um fluxo de credenciais de cliente é o nome do recurso seguido por `/.default` . Essa notação informa Azure Active Directory (Azure AD) para usar as *permissões no nível do aplicativo* declaradas estaticamente durante o registro do aplicativo. Além disso, essas permissões de API devem ser concedidas por um administrador de locatários.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="python"></a>[Python](#tab/python)

No MSAL Python, o arquivo de configuração é semelhante a este trecho de código:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="azure-ad-v10-resources"></a>Recursos do Azure AD (v 1.0)

O escopo usado para credenciais de cliente sempre deve ser a ID de recurso seguida por `/.default` .

> [!IMPORTANT]
> Quando o MSAL solicita um token de acesso para um recurso que aceita um token de acesso da versão 1,0, o Azure AD analisa o público-alvo desejado do escopo solicitado, levando tudo antes da última barra e usando-o como o identificador de recurso.
> Portanto, se, como o banco de dados SQL do Azure (**https: \/ /Database.Windows.net**), o recurso espera um público que termina com uma barra (para o banco de dados SQL do Azure `https://database.windows.net/` ), você precisará solicitar um escopo de `https://database.windows.net//.default` . (Observe a barra dupla.) Consulte também MSAL.NET Issue [#747: a barra à direita da URL do recurso é omitida, o que causou a falha de autenticação do SQL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="acquiretokenforclient-api"></a>API AcquireTokenForClient

Para adquirir um token para o aplicativo, você usará `AcquireTokenForClient` ou seu equivalente, dependendo da plataforma.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows, the scope is always of the shape "resource/.default" because the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator.
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application doesn't have sufficient permissions.
    // - Did you declare enough app permissions during app creation?
    // - Did the tenant admin grant permissions to the application?
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be in the form "https://resourceurl/.default"
    // Mitigation: Change the scope to be as expected.
}
```

### <a name="acquiretokenforclient-uses-the-application-token-cache"></a>AcquireTokenForClient usa o cache de token de aplicativo

No MSAL.NET, `AcquireTokenForClient` o usa o cache de token de aplicativo. (Todos os outros métodos AcquireToken *XX* usam o cache de token de usuário.) Não chame `AcquireTokenSilent` antes de chamar `AcquireTokenForClient` , porque `AcquireTokenSilent` o usa o cache de token de *usuário* . `AcquireTokenForClient` verifica o cache do token de *aplicativo* e o atualiza.

# <a name="python"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# First, the code looks up a token from the cache.
# Because we're looking for a token for the current app, not for a user,
# use None for the account parameter.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token.
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You might need this when reporting a bug.
```

# <a name="java"></a>[Java](#tab/java)

Esse código é extraído dos [exemplos de desenvolvimento do MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/).

```Java
private static IAuthenticationResult acquireToken() throws Exception {

     // Load token cache from file and initialize token cache aspect. The token cache will have
     // dummy data, so the acquireTokenSilently call will fail.
     TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

     IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);
     ConfidentialClientApplication cca =
             ConfidentialClientApplication
                     .builder(CLIENT_ID, credential)
                     .authority(AUTHORITY)
                     .setTokenCacheAccessAspect(tokenCacheAspect)
                     .build();

     IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
 }
```

---

### <a name="protocol"></a>Protocolo

Se você ainda não tiver uma biblioteca para a linguagem escolhida, talvez queira usar o protocolo diretamente:

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>Primeiro caso: acessar a solicitação de token usando um segredo compartilhado

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>Segundo caso: acessar a solicitação de token usando um certificado

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

Para obter mais informações, consulte a documentação do protocolo: [plataforma de identidade da Microsoft e o fluxo de credenciais do cliente OAuth 2,0](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>Solução de problemas

### <a name="did-you-use-the-resourcedefault-scope"></a>Você usou o escopo de recurso/. padrão?

Se você receber uma mensagem de erro informando que usou um escopo inválido, provavelmente não usou o `resource/.default` escopo.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Você se esqueceu de fornecer consentimento de administrador? Aplicativos de daemon precisam!

Se você obtiver **privilégios insuficientes para concluir o** erro de operação quando chamar a API, o administrador de locatários precisará conceder permissões ao aplicativo. Consulte a etapa 6 de registrar o aplicativo cliente acima.
Normalmente, você verá um erro semelhante a este erro:

```json
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

### <a name="are-you-calling-your-own-api"></a>Você está chamando sua própria API?

Se você chamar sua própria API Web e não foi possível adicionar uma permissão de aplicativo ao registro do aplicativo para seu aplicativo daemon, você expôs uma função de aplicativo em sua API Web?

Para obter detalhes, consulte [expondo permissões de aplicativo (funções de aplicativo)](scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles) e, em particular, [garantindo que o Azure ad emita tokens para sua API Web somente para clientes permitidos](scenario-protected-web-api-app-registration.md#ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients).

## <a name="next-steps"></a>Próximas etapas

# <a name="net"></a>[.NET](#tab/dotnet)

Vá para o próximo artigo neste cenário, [chamando uma API da Web](./scenario-daemon-call-api.md?tabs=dotnet).

# <a name="python"></a>[Python](#tab/python)

Vá para o próximo artigo neste cenário, [chamando uma API da Web](./scenario-daemon-call-api.md?tabs=python).

# <a name="java"></a>[Java](#tab/java)

Vá para o próximo artigo neste cenário, [chamando uma API da Web](./scenario-daemon-call-api.md?tabs=java).

---
