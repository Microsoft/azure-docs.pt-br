---
title: Chamar uma API Web de um aplicativo móvel | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo móvel que chama as APIs Web. (Chamar uma API Web.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 656cb167fb47e44c28922afed75d8c46a460aaf1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584390"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>Chamar uma API Web em um aplicativo móvel

Depois que o aplicativo entrar em um usuário e receber tokens, a MSAL (biblioteca de autenticação da Microsoft) expõe informações sobre o usuário, o ambiente do usuário e os tokens emitidos. Seu aplicativo pode usar esses valores para chamar uma API Web ou exibir uma mensagem de boas-vindas para o usuário.

Neste artigo, primeiro vamos examinar o resultado da MSAL. Em seguida, veremos como usar um token de acesso de `AuthenticationResult` ou `result` para chamar uma API Web protegida.

## <a name="msal-result"></a>Resultado MSAL
A MSAL fornece os seguintes valores: 

- `AccessToken` chama APIs Web protegidas em uma solicitação de portador HTTP.
- `IdToken` contém informações úteis sobre o usuário conectado. Essas informações incluem o nome do usuário, o locatário inicial e um identificador exclusivo para armazenamento.
- `ExpiresOn` é o tempo de expiração do token. O MSAL manipula a atualização automática de um aplicativo.
- `TenantId` é o identificador do locatário no qual o usuário se conectou. Para usuários convidados no B2B do Azure Active Directory (AAD), esse valor identifica o locatário no qual o usuário se conectou. O valor não identifica o locatário inicial do usuário.  
- `Scopes` indica os escopos que foram concedidos com seu token. Os escopos concedidos podem ser um subconjunto dos escopos que você solicitou.

MSAL também fornece uma abstração para um valor `Account`. Um valor `Account` representa a conta conectada do usuário atual:

- `HomeAccountIdentifier` Identifica o locatário inicial do usuário.
- `UserName` é o idioma preferido do nome de usuário. Esse valor pode estar vazio para usuários do Azure AD B2C.
- `AccountIdentifier` identifica o usuário conectado. Na maioria dos casos, esse valor é o mesmo que o valor de `HomeAccountIdentifier`, a menos que o usuário seja um convidado em outro locatário.

## <a name="call-an-api"></a>Chamar uma API

Depois de ter o token de acesso, é possível chamar uma API da Web. Seu aplicativo usará o token para criar uma solicitação HTTP e, em seguida, executará a solicitação.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>MSAL para iOS e macOS

Os métodos para adquirir tokens retornam um objeto `MSALResult`. `MSALResult` expõe uma propriedade `accessToken`. É possível usar `accessToken` para chamar uma API da Web. Adicione essa propriedade ao cabeçalho de autorização HTTP antes de chamar para acessar a API Web protegida.

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="make-several-api-requests"></a>Fazer várias solicitações de API

Para chamar a mesma API várias vezes ou chamar várias APIs, considere os seguintes assuntos ao compilar seu aplicativo:

- **Consentimento incremental**: a plataforma de identidade da Microsoft permite que os aplicativos obtenham o consentimento do usuário quando forem necessárias permissões em vez de todos no início. Cada vez que seu aplicativo estiver pronto para chamar uma API, ele deverá solicitar apenas os escopos de que precisa.

- **Acesso condicional**: Quando várias solicitações de API são feitas, em certos cenários, talvez seja necessário atender aos requisitos adicionais de acesso condicional. Os requisitos podem aumentar dessa forma se a primeira solicitação não tiver políticas de acesso condicional e se o seu aplicativo tentar acessar silenciosamente uma nova API que exija acesso condicional. Para lidar com esse problema, não se esqueça de detectar erros de solicitações silenciosas e esteja preparado para fazer uma solicitação interativa.  Para obter mais informações, consulte [Diretrizes para acesso condicional](../azuread-dev/conditional-access-dev-guide.md).

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>Chamar várias APIs usando o consentimento incremental e o acesso condicional

Para chamar várias APIs para o mesmo usuário, depois de adquirir um token para o usuário, você pode evitar repetidamente solicitar as credenciais ao usuário, chamando posteriormente `AcquireTokenSilent` para obter um token:

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

A interação é necessária quando:

- O usuário consentiu para a primeira API, mas agora precisa consentir para mais escopos. Nesse caso, use o consentimento incremental.
- A primeira API não exige [autenticação multifator](../authentication/concept-mfa-howitworks.md), mas a próxima API exige.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, [vá para produção](scenario-mobile-production.md).
