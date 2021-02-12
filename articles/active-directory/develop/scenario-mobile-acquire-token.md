---
title: Adquirir um token para chamar uma API da Web (aplicativos móveis) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo móvel que chama as APIs Web. (Obtenha um token para o aplicativo.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 6e6eda3d711710ea7450165ab02d7a260067bfcb
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582546"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>Obter um token para um aplicativo móvel que chama APIs da Web

Antes que seu aplicativo possa chamar APIs da Web protegidas, ele precisa de um token de acesso. Este artigo orienta você pelo processo de obter um token usando a MSAL (biblioteca de autenticação da Microsoft).

## <a name="define-a-scope"></a>Definir um escopo

Ao solicitar um token, defina um escopo. O escopo determina quais dados seu aplicativo pode acessar.

A maneira mais fácil de definir um escopo é combinar as APIs da Web desejadas `App ID URI` com o escopo `.default` . Essa definição informa à plataforma de identidade da Microsoft que seu aplicativo requer todos os escopos definidos no Portal.

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Obter tokens

### <a name="acquire-tokens-via-msal"></a>Adquirir tokens via MSAL

O MSAL permite que os aplicativos adquiram tokens de forma silenciosa e interativa. Quando você chama `AcquireTokenSilent()` ou `AcquireTokenInteractive()` , MSAL retorna um token de acesso para os escopos solicitados. O padrão correto é fazer uma solicitação silenciosa e, em seguida, retornar a uma solicitação interativa.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
```

#### <a name="ios"></a>iOS

Primeiro, tente adquirir um token silenciosamente:

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";

MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        // Access token to call the web API
        NSString *accessToken = result.accessToken;
    }

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```

```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"

guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in

    guard let authResult = result, error == nil else {

    let nsError = error! as NSError

    if (nsError.domain == MSALErrorDomain &&
        nsError.code == MSALError.interactionRequired.rawValue) {

            // Interactive auth will be required, call acquireToken()
            return
         }
         return
     }

    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier

    // Access token to call the web API
    let accessToken = authResult.accessToken
}
```

Se MSAL retornar `MSALErrorInteractionRequired` , tente adquirir tokens interativamente:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(authPresentationViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

O MSAL para iOS e macOS dá suporte a vários modificadores para obter um token de forma interativa ou silenciosa:
* [Parâmetros comuns para obter um token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Parâmetros para obter um token interativo](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Parâmetros para obter um token silencioso](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

O exemplo a seguir mostra o código mínimo para obter um token interativamente. O exemplo usa Microsoft Graph para ler o perfil do usuário.

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

#### <a name="mandatory-parameters-in-msalnet"></a>Parâmetros obrigatórios em MSAL.NET

`AcquireTokenInteractive` tem apenas um parâmetro obrigatório: `scopes` . O `scopes` parâmetro enumera cadeias de caracteres que definem os escopos para os quais um token é necessário. Se o token for para Microsoft Graph, você poderá encontrar os escopos necessários na referência de API de cada API Microsoft Graph. Na referência, vá para a seção "permissões".

Por exemplo, para [listar os contatos do usuário](/graph/api/user-list-contacts), use o escopo "User. Read", "Contacts. Read". Para obter mais informações, confira [Referência de permissões do Microsoft Graph](/graph/permissions-reference).

No Android, você pode especificar a atividade pai ao criar o aplicativo usando o `PublicClientApplicationBuilder` . Se você não especificar a atividade pai nesse momento, mais tarde poderá especificá-la usando `.WithParentActivityOrWindow` o como na seção a seguir. Se você especificar a atividade pai, o token retornará para essa atividade pai após a interação. Se você não especificá-lo, a `.ExecuteAsync()` chamada lançará uma exceção.

#### <a name="specific-optional-parameters-in-msalnet"></a>Parâmetros opcionais específicos no MSAL.NET

As seções a seguir explicam os parâmetros opcionais no MSAL.NET.

##### <a name="withprompt"></a>WithPrompt

O `WithPrompt()` parâmetro controla a interatividade com o usuário especificando um prompt.

![Imagem mostrando os campos na estrutura do prompt. Esses valores constantes controlam a interatividade com o usuário definindo o tipo de prompt exibido pelo parâmetro WithPrompt ().](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

A classe define as constantes a seguir:

- `SelectAccount` força o serviço de token de segurança (STS) a apresentar a caixa de diálogo de seleção de conta. A caixa de diálogo contém as contas para as quais o usuário tem uma sessão. Você pode usar essa opção quando quiser permitir que o usuário escolha entre diferentes identidades. Essa opção faz com que a MSAL envie `prompt=select_account` para o provedor de identidade.

    A `SelectAccount` constante é o padrão e fornece efetivamente a melhor experiência possível com base nas informações disponíveis. As informações disponíveis podem incluir a conta, a presença de uma sessão para o usuário e assim por diante. Não altere esse padrão, a menos que você tenha um bom motivo para fazê-lo.
- `Consent` permite que você solicite o consentimento do usuário mesmo que o consentimento tenha sido concedido antes. Nesse caso, a MSAL envia `prompt=consent` para o provedor de identidade.

    Talvez você queira usar a `Consent` constante em aplicativos com foco em segurança, em que a governança da organização exige que os usuários vejam a caixa de diálogo de consentimento sempre que usarem o aplicativo.
- `ForceLogin` permite que o serviço solicite credenciais ao usuário mesmo se o prompt não for necessário.

    Essa opção pode ser útil se a aquisição de token falhar e você quiser permitir que o usuário entre novamente. Nesse caso, a MSAL envia `prompt=login` para o provedor de identidade. Talvez você queira usar essa opção em aplicativos com foco em segurança, em que o controle da organização exige que o usuário entre cada vez que acessarem partes específicas do aplicativo.
- `Never` é somente para .NET 4,5 e Windows Runtime (WinRT). Essa constante não solicitará o usuário, mas tentará usar o cookie armazenado na exibição da Web oculta inserida. Para obter mais informações, consulte [usando navegadores da Web com MSAL.net](./msal-net-web-browsers.md).

    Se essa opção falhar, o `AcquireTokenInteractive` lançará uma exceção para notificá-lo de que uma interação de interface do usuário é necessária. Em seguida, use outro `Prompt` parâmetro.
- `NoPrompt` não envia um prompt para o provedor de identidade.

    Essa opção é útil somente para políticas de edição de perfil no Azure Active Directory B2C. Para obter mais informações, consulte [especificações do B2C](https://aka.ms/msal-net-b2c-specificities).

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Use o `WithExtraScopeToConsent` modificador em um cenário avançado em que você deseja que o usuário forneça consentimento antecipado para vários recursos. Você pode usar esse modificador quando não quiser usar o consentimento incremental, que normalmente é usado com o MSAL.NET ou a plataforma de identidade da Microsoft. Para obter mais informações, consulte [Ter o consentimento de usuário antecipado para vários recursos](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

Este é um exemplo de código:

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Outros parâmetros opcionais

Para saber mais sobre os outros parâmetros opcionais para `AcquireTokenInteractive` , consulte a [documentação de referência para AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods).

### <a name="acquire-tokens-via-the-protocol"></a>Adquirir tokens por meio do protocolo

Não recomendamos o uso direto do protocolo para obter tokens. Se você fizer isso, o aplicativo não dará suporte a alguns cenários que envolvem SSO (logon único), gerenciamento de dispositivos e acesso condicional.

Ao usar o protocolo para obter tokens para aplicativos móveis, faça duas solicitações:

* Obtenha um código de autorização.
* Troque o código por um token.

#### <a name="get-an-authorization-code"></a>Obter um código de autorização

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>Obter acesso e atualizar o token

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, [chamando uma API da Web](scenario-mobile-call-api.md).