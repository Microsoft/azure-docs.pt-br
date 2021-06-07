---
title: Adquirir um token para chamar uma API Web (aplicativo de área de trabalho) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo da área de trabalho que chama APIs Web para adquirir um token para o aplicativo
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/06/2021
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: c63ee686ae218a696069465bb8d2d1d7413a998e
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799081"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Aplicativo da área de trabalho que chama as APIs Web: Adquirir um token

Depois de criar uma instância do aplicativo cliente público, você a usará para adquirir um token que será usado para chamar uma API Web.

## <a name="recommended-pattern"></a>Padrão recomendado

A API Web é definida por seus `scopes`. Qualquer que seja a experiência fornecida no seu aplicativo, o padrão a ser usado é:

- Tente, sistematicamente, obter um token do cache de token chamando `AcquireTokenSilent`.
- Caso essa chamada falhe, use o fluxo de `AcquireToken` que você deseja usar, o qual é representado aqui por `AcquireTokenXX`.

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>No MSAL.NET

```csharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>Na MSAL para iOS e macOS

Objective-C:

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Swift:

```swift
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
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

No nó MSAL, você adquire tokens por meio do fluxo de código de autorização com a chave de prova para a troca de código (PKCE). O nó MSAL usa um cache de token na memória para ver se há contas de usuário no cache. Se houver, o objeto Account poderá ser passado para o `acquireTokenSilent()` método para recuperar um token de acesso em cache.

```JavaScript

const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);
const msalTokenCache = pca.getTokenCache();

let accounts = await msalTokenCache.getAllAccounts();

    if (accounts.length > 0) {

        const silentRequest = {
            account: accounts[0], // Index must match the account that is trying to acquire token silently
            scopes: ["user.read"],
        };
    
        pca.acquireTokenSilent(silentRequest).then((response) => {
            console.log("\nSuccessful silent token acquisition");
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => console.log(error));
    } else {
        const {verifier, challenge} = await msal.cryptoProvider.generatePkceCodes();

        const authCodeUrlParameters = {
            scopes: ["User.Read"],
            redirectUri: "your_redirect_uri",
            codeChallenge: challenge, // PKCE Code Challenge
            codeChallengeMethod: "S256" // PKCE Code Challenge Method 
        };
        
        // get url to sign user in and consent to scopes needed for application
        pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            console.log(response);
        
            const tokenRequest = {
                code: response["authorization_code"],
                codeVerifier: verifier // PKCE Code Verifier 
                redirectUri: "your_redirect_uri",
                scopes: ["User.Read"],
            };
            
            // acquire a token by exchanging the code
            pca.acquireTokenByCode(tokenRequest).then((response) => {
                console.log("\nResponse: \n:", response);
            }).catch((error) => {
                console.log(error);
            });
        }).catch((error) => console.log(JSON.stringify(error)));
    }
```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```
---

Aqui apresentamos as várias maneiras de adquirir tokens em um aplicativo de área de trabalho.

## <a name="acquire-a-token-interactively"></a>Adquirir um token interativamente

O exemplo a seguir mostra o código mínimo para se obter um token de modo interativo para ler o perfil do usuário com Microsoft Graph.

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>No MSAL.NET

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

### <a name="mandatory-parameters"></a>Parâmetros obrigatórios

`AcquireTokenInteractive` tem apenas um parâmetro obrigatório, ``scopes``, o qual contém uma enumeração de cadeias de caracteres que definem os escopos para os quais um token é necessário. Caso o token seja para o Microsoft Graph, os escopos necessários poderão ser encontrados na referência de API de cada API do Microsoft Graph na seção chamada "Permissões". Por exemplo, para [listar os contatos do usuário](/graph/api/user-list-contacts), o escopo "User.Read", "Contacts.Read" deve ser usado. Para obter mais informações, confira [Referência de permissões do Microsoft Graph](/graph/permissions-reference).

No Android, você também precisa especificar a atividade pai usando `.WithParentActivityOrWindow`, conforme mostrado, de modo que o token volte para essa atividade pai após a interação. Caso não o especifique, uma exceção será gerada ao chamar `.ExecuteAsync()`.

### <a name="specific-optional-parameters-in-msalnet"></a>Parâmetros opcionais específicos no MSAL.NET

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

A interface do usuário é importante por ser interativa. `AcquireTokenInteractive` tem um parâmetro opcional específico que pode especificar, para plataformas que têm suporte para ele, a interface do usuário pai. Quando usado em um aplicativo de área de trabalho, `.WithParentActivityOrWindow` tem um tipo diferente, o qual depende da plataforma. Como alternativa, você pode omitir o parâmetro de janela pai opcional para criar uma janela, se não quiser controlar onde a caixa de diálogo de entrada aparece na tela. Isso seria aplicável a aplicativos que são baseados em linha de comando, usados para passar chamadas para qualquer outro serviço de back-end e não precisam de nenhuma interação com o usuário.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Comentários:

- No .NET Standard, `object` esperado é `Activity` no Android, `UIViewController` no iOS, `NSWindow` no MAC e `IWin32Window` ou `IntPr` no Windows.
- No Windows, você deve chamar `AcquireTokenInteractive` do thread da interface do usuário para que o navegador inserido obtenha o contexto apropriado de sincronização da interface do usuário. Não chamar a partir do thread da interface do usuário pode fazer com que as mensagens não sejam bombeadas corretamente, além de levar a cenários de deadlock com a interface do usuário. Uma maneira de chamar as MSALs (Bibliotecas de autenticação da Microsoft) do thread da interface do usuário caso você já não esteja no thread da interface do usuário é usar o `Dispatcher` no WPF.
- Caso esteja usando o WPF para obter uma janela de um controle do WPF, você poderá usar a classe `WindowInteropHelper.Handle`. Então, a chamada é de um controle do WPF (`this`):

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` é usado para controlar a interatividade com o usuário por meio da especificação de um prompt.

![Imagem mostrando os campos na estrutura do prompt. Esses valores constantes controlam a interatividade com o usuário definindo o tipo de prompt exibido pelo método WithPrompt ().](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

A classe define as constantes a seguir:

- ``SelectAccount`` força o STS (serviço de token de segurança) a apresentar a caixa de diálogo da seleção de conta que contém contas para as quais o usuário tem uma sessão. Essa opção é útil quando os desenvolvedores de aplicativos desejarem permitir que os usuários escolham entre diferentes identidades. Essa opção faz com que a MSAL envie ``prompt=select_account`` para o provedor de identidade. Essa é a opção padrão. Ele faz um bom trabalho ao fornecer a melhor experiência possível com base nas informações disponíveis, como a conta e a presença de uma sessão para o usuário. Não a altere, a menos que você tenha um bom motivo para isso.
- ``Consent`` permite que o desenvolvedor do aplicativo force usuário a dar seu consentimento, mesmo que ele já tenha sido concedido antes. Nesse caso, a MSAL envia `prompt=consent` para o provedor de identidade. Essa opção pode ser usada em alguns aplicativos focados em segurança nos quais o controle da organização exige que a caixa de diálogo de consentimento seja exibida ao usuário sempre que o aplicativo for usado.
- ``ForceLogin`` permite que o desenvolvedor do aplicativo solicite que o usuário forneça credenciais pelo serviço, mesmo que essa solicitação possa não ser necessária. Essa opção pode ser útil para permitir que o usuário entre novamente caso a aquisição de um token falhe. Nesse caso, a MSAL envia `prompt=login` para o provedor de identidade. Às vezes, ele é usado em aplicativos com foco em segurança nos quais o controle da organização exige que o usuário se reconecte sempre que acessar partes específicas de um aplicativo.
- ``Never`` (somente para .NET 4.5 e WinRT) não fará uma solicitação ao usuário, mas tentará usar o cookie armazenado no modo de exibição da Web oculto inserido. Para obter mais informações, consulte modos de exibição da Web no MSAL.NET. O uso dessa opção pode falhar. Nesse caso, `AcquireTokenInteractive` gera uma exceção para notificar que uma interação da interface do usuário seja necessária. Você precisará usar outro parâmetro `Prompt`.
- ``NoPrompt`` não enviará nenhuma solicitação para o provedor de identidade. Essa opção só é útil para as políticas de perfil de edição do Azure AD (Azure Active Directory) B2C. Para obter mais informações, consulte [Especificações do Azure AD B2C](https://aka.ms/msal-net-b2c-specificities).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Esse modificador é usado em um cenário avançado, no qual você deseja que o usuário concorde com vários recursos com antecedência e não deseja usar o consentimento incremental, que normalmente é usado com o MSAL.NET/plataforma de identidade da Microsoft. Para obter mais informações, consulte [Ter o consentimento de usuário antecipado para vários recursos](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Uma interface do usuário da Web é um mecanismo usado para invocar um navegador. Esse mecanismo pode ser um controle WebBrowser da interface do usuário dedicada ou uma maneira de delegar a abertura do navegador.
A MSAL fornece implementações da interface do usuário da Web para a maioria das plataformas, mas há casos em que você pode desejar hospedar o navegador por conta própria:

- Plataformas que não são explicitamente cobertas pela MSAL, por exemplo, Blazor, Unity e Mono em áreas de trabalho.
- Você deseja testar o aplicativo na interface do usuário e usar um navegador automatizado que possa ser usado com Selenium.
- O navegador e o aplicativo que executa a MSAL estão em processos separados.

##### <a name="at-a-glance"></a>Visão rápida

Para fazer isso, você fornece `start Url` à MSAL, que precisa ser exibido em um navegador da sua escolha para que o usuário final possa inserir itens como o nome de usuário.
Após a conclusão da autenticação, seu aplicativo precisará passar de volta para a MSAL `end Url`, a qual contém um código fornecido pelo Azure AD.
O host de `end Url` é sempre `redirectUri`. Para interceptar o `end Url`, siga um destes procedimentos:

- Monitore os redirecionamentos de navegador até chegar ao `redirect Url`.
- Faça com que o navegador redirecione para uma URL monitorada por você.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi é um ponto de extensibilidade

`WithCustomWebUi` é um ponto de extensibilidade que pode ser usado para você fornecer sua própria interface do usuário em aplicativos cliente públicos. Você também pode permitir que o usuário passe pelo ponto de extremidade/Authorize do provedor de identidade e permitir que eles entrem e consintam. Com isso, o MSAL.NET pode resgatar o código de autenticação e obter um token. Por exemplo, ele é usado no Visual Studio para que os aplicativos de elétrons (por exemplo, comentários do Visual Studio) forneçam a interação com a Web, mas deixe que o MSAL.NET faça a maior parte do trabalho. Você também pode usá-lo caso deseje fornecer a automação da interface do usuário. Em aplicativos cliente públicos, o MSAL.NET usa a PKCE (Chave de Prova para Troca de Código) padrão para garantir que a segurança seja respeitada. Somente o MSAL.NET pode resgatar o código. Para obter mais informações, consulte [RFC 7636 – Chave de Prova para Troca de Código por clientes públicos do OAuth](https://tools.ietf.org/html/rfc7636).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>Usar o WithCustomWebUi

Para usar o `.WithCustomWebUI`, siga as etapas a seguir.

  1. Implemente a interface `ICustomWebUi`. Para obter mais informações, consulte [este site](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Implemente um método `AcquireAuthorizationCodeAsync` e aceite a URL do código de autorização calculada por MSAL.NET. Depois, permita que o usuário percorra a interação com o provedor de identidade e retorne a URL pela qual o provedor de identidade teria chamado sua implementação novamente junto com o código de autorização. Caso tenha problemas, sua implementação deverá gerar uma exceção `MsalExtensionException` para uma boa cooperação com a MSAL.
  2. Na chamada de `AcquireTokenInteractive`, use o modificador `.WithCustomUI()` passando a instância da interface do usuário da Web personalizada.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Exemplos da implementação do ICustomWebUi na automação de teste: SeleniumWebUI

A equipe do MSAL.NET reescreveu os testes da interface do usuário para usar esse mecanismo de extensibilidade. Caso tenha interesse, examine a classe [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) no código-fonte MSAL.NET.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Ofereça uma ótima experiência com o SystemWebViewOptions

No MSAL.NET 4.1 [`SystemWebViewOptions`](/dotnet/api/microsoft.identity.client.systemwebviewoptions), você pode especificar:

- O URI a ser acessado (`BrowserRedirectError`) ou o fragmento HTML a ser exibido (`HtmlMessageError`) em caso de erros de entrada ou de consentimento no navegador da Web do sistema.
- O URI a ser acessado (`BrowserRedirectSuccess`) ou o fragmento HTML a ser exibido (`HtmlMessageSuccess`) em caso de entrada ou de consentimento bem-sucedidos.
- A ação a ser executada para iniciar o navegador do sistema. Você pode fornecer sua própria implementação por meio da definição do delegado `OpenBrowserAsync`. A classe também fornece uma implementação padrão para dois navegadores: `OpenWithEdgeBrowserAsync` e `OpenWithChromeEdgeBrowserAsync` para o Microsoft Edge e [Microsoft Edge no Chromium](https://www.windowscentral.com/faq-edge-chromium) respectivamente.

Para usar essa estrutura, escreva algo semelhante ao exemplo a seguir:

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>Outros parâmetros opcionais

Para saber mais sobre todos os outros parâmetros opcionais de `AcquireTokenInteractive`, consulte [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>Na MSAL para iOS e macOS

Objective-C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
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

Swift:

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

No nó MSAL, você adquire tokens por meio do fluxo de código de autorização com a chave de prova para a troca de código (PKCE). O processo tem duas etapas: primeiro, o aplicativo obtém uma URL que pode ser usada para gerar um código de autorização. Essa URL pode ser aberta em um navegador de sua escolha, em que o usuário pode inserir suas credenciais e será Redirecionado de volta para o `redirectUri` (registrado durante o registro do aplicativo) com um código de autorização. Em segundo lugar, o aplicativo passa o código de autorização recebido para o `acquireTokenByCode()` método que o troca para um token de acesso.

```JavaScript
const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

const {verifier, challenge} = await msal.cryptoProvider.generatePkceCodes();

const authCodeUrlParameters = {
    scopes: ["User.Read"],
    redirectUri: "your_redirect_uri",
    codeChallenge: challenge, // PKCE Code Challenge
    codeChallengeMethod: "S256" // PKCE Code Challenge Method 
};

// get url to sign user in and consent to scopes needed for application
pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
    console.log(response);

    const tokenRequest = {
        code: response["authorization_code"],
        codeVerifier: verifier // PKCE Code Verifier 
        redirectUri: "your_redirect_uri",
        scopes: ["User.Read"],
    };
    
    // acquire a token by exchanging the code
    pca.acquireTokenByCode(tokenRequest).then((response) => {
        console.log("\nResponse: \n:", response);
    }).catch((error) => {
        console.log(error);
    });
}).catch((error) => console.log(JSON.stringify(error)));
```

# <a name="python"></a>[Python](#tab/python)

A MSAL do Python não fornece um método de token de aquisição interativo diretamente. Em vez disso, ela requer que o aplicativo envie uma solicitação de autorização em sua implementação do fluxo de interação do usuário para obter um código de autorização. Em seguida, esse código pode ser passado para o método `acquire_token_by_authorization_code` para se obter o token.

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])

```
---

## <a name="integrated-windows-authentication"></a>Autenticação Integrada do Windows

Para conectar um usuário de domínio em um domínio ou em um computador ingressado no Azure AD, use a IWA (Autenticação Integrada do Windows).

### <a name="constraints"></a>Restrições

- A IWA pode ser usada somente por usuários *federados+* , ou seja, usuários criados no Active Directory e apoiados pelo Azure AD. Os usuários criados diretamente no Azure AD, sem apoio do Active Directory usuários *gerenciados* não podem usar esse fluxo de autenticação. Essa limitação não afeta o fluxo de nome de usuário e senha.
- A IWA é para aplicativos escritos para as plataformas .NET Framework, .NET Core e UWP (Plataforma Universal do Windows).
- A IWA não ignora a [MFA (autenticação multifator)](../authentication/concept-mfa-howitworks.md). Caso a MFA esteja configurada, a IWA poderá falhar caso um desafio de MFA seja necessário, pois esta exige a interação do usuário.
  
    A IWA é não interativa, mas a MFA requer a interatividade do usuário. Você não controla o momento em que o provedor de identidade solicita que a MFA seja executada; o administrador do locatário é quem faz isso. De acordo com nossas observações, a MFA será solicitada quando você for entrar estando em um país/região diferente, quando não estiver conectado via VPN a uma rede corporativa e, às vezes, até mesmo estando conectado via VPN. Não espere que haja um conjunto determinístico de regras. O Azure AD usa a IA (inteligência artificial) para aprender continuamente se a MFA é necessária. Faça o fallback para um prompt de usuário como uma autenticação interativa ou um fluxo de código de dispositivo caso a IWA falhe.

- A autoridade passada em `PublicClientApplicationBuilder` precisa ser:
  - Com locatários do formulário `https://login.microsoftonline.com/{tenant}/`, em que `tenant` é o GUID que representa a ID do locatário ou um domínio associado ao locatário.
  - Para contas corporativas ou de estudante: `https://login.microsoftonline.com/organizations/`.
  - Não há suporte para contas pessoais da Microsoft. Você não pode usar locatários /common ou /consumers.

- Como a Autenticação Integrada do Windows é um fluxo silencioso:
  - O usuário do seu aplicativo deve ter consentido o uso do aplicativo anteriormente.
  - Ou, o administrador de locatários deve ter consentido o uso do aplicativo anteriormente para todos os usuários no locatário.
  - Em outras palavras:
    - Ou você, como desenvolvedor, selecionou o botão **Concessão** no portal do Azure por conta própria.
    - Ou, um administrador de locatários selecionou o botão **Conceder/revogar consentimento do administrador para {domínio de locatário}** na guia **Permissões de API** do registro do aplicativo. Para obter mais informações, consulte [adicionar permissões para acessar sua API Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).
    - Ou, você forneceu uma forma de os usuários consentirem com o aplicativo. Para obter mais informações, consulte [Solicitar consentimento de usuário individual](./v2-permissions-and-consent.md#requesting-individual-user-consent).
    - Ou, você forneceu uma forma de o administrador do locatário consentir com o aplicativo. Para obter mais informações, consulte [Consentimento do administrador](./v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

- Esse fluxo está habilitado para aplicativos de área de trabalho .NET, .NET Core e UWP.

Para obter mais informações sobre consentimento, consulte [permissões e consentimento da plataforma de identidade da Microsoft](./v2-permissions-and-consent.md).

### <a name="learn-how-to-use-it"></a>Saiba como usá-lo

# <a name="net"></a>[.NET](#tab/dotnet)

Em MSAL.NET, use:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Geralmente, você precisa de apenas um parâmetro (`scopes`). Dependendo de como o administrador do Windows configurou as políticas, os aplicativos em seu computador Windows podem não ter permissão para pesquisar o usuário conectado. Nesse caso, use um segundo método, `.WithUsername()`, e passe o nome de usuário conectado em um formato UPN, por exemplo, `joe@contoso.com`.

O exemplo a seguir apresenta o caso mais atual, com explicações dos tipos de exceções que você pode obter e suas atenuações.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

Para obter a lista de possíveis modificadores de AcquireTokenByIntegratedWindowsAuthentication, consulte [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

Essa extração é das [amostras de desenvolvimento da MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

Esse fluxo não se aplica ao macOS.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Esse fluxo ainda não tem suporte no nó MSAL.

# <a name="python"></a>[Python](#tab/python)

Ainda não há suporte para esse fluxo na MSAL Python.

---

## <a name="username-and-password"></a>Nome de usuário e senha

Você também pode adquirir um token ao fornecer o nome de usuário e a senha. Esse fluxo é limitado e não é recomendado, mas ainda há casos de uso em que ele é necessário.

### <a name="this-flow-isnt-recommended"></a>Esse fluxo não é recomendado.

O fluxo de nome de usuário e senha *não é recomendado* porque fazer com que seu aplicativo solicite a senha de um usuário não é seguro. Para obter mais informações, consulte [qual é a solução para o crescente problema de senhas?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/) O fluxo preferencial para adquirir um token silenciosamente em computadores Windows ingressados no domínio é a [Autenticação Integrada do Windows](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Também é possível usar o [fluxo de código do dispositivo](https://aka.ms/msal-net-device-code-flow).

Usar um nome de usuário e uma senha é útil em alguns casos, como em cenários DevOps. Mas essa não é uma boa opção se você quiser usar um nome de usuário e senha em cenários interativos nos quais você fornece sua própria interface do usuário. Ao usar um nome de usuário e uma senha, você está deixando de usar várias coisas:

- Princípios básicos da identidade moderna. Uma senha pode sofrer phishing e ser reproduzida devido à possibilidade de um segredo compartilhado ser interceptado. Ela é incompatível com opções sem senha.
- Os usuários que precisam executar a MFA não conseguem entrar porque não há nenhuma interação.
- Os usuários não podem usar o SSO (logon único).

### <a name="constraints"></a>Restrições

As restrições a seguir também se aplicam:

- O fluxo de nome de usuário e senha não é compatível com o acesso condicional e com a autenticação multifator. Como consequência, se o aplicativo for executado em um locatário do Azure AD em que o administrador de locatários exige autenticação multifator, você não poderá usar esse fluxo. Muitas organizações fazem isso.
- Mas só funciona para contas corporativas e de estudante (e não na MSA).
- O fluxo está disponível na área de trabalho do .NET e no .NET Core, mas não na UWP.

### <a name="b2c-specifics"></a>Especificações de B2C

Para obter mais informações, consulte [Credenciais de Senha do Proprietário do Recurso (ROPC) com B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="use-it"></a>Use

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication` contém o método `AcquireTokenByUsernamePassword`.

A amostra a seguir apresenta um caso simplificado.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

O exemplo a seguir apresenta o caso mais atual, com explicações dos tipos de exceções que você pode obter e suas atenuações.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

Para obter mais informações sobre todos os modificadores que podem ser aplicados ao `AcquireTokenByUsernamePassword`, consulte [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

A extração a seguir é das [amostras de desenvolvimento da MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

Não há suporte para esse fluxo na MSAL para macOS.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Essa extração é dos [exemplos de desenvolvimento do nó MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/standalone-samples/username-password). No trecho de código abaixo, o nome de usuário e a senha são codificados apenas para fins ilustrativos. Isso deve ser evitado na produção. Em vez disso, uma interface de usuário básica solicitando que o usuário insira seu nome de usuário/senha seria recomendada. 

```JavaScript
const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

// For testing, enter your username and password below.
// In production, replace this with a UI prompt instead.
const usernamePasswordRequest = {
    scopes: ["user.read"],
    username: "", // Add your username here
    password: "", // Add your password here
};

pca.acquireTokenByUsernamePassword(usernamePasswordRequest).then((response) => {
    console.log("acquired token by password grant");
}).catch((error) => {
    console.log(error);
});
```

# <a name="python"></a>[Python](#tab/python)

Essa extração é das [amostras de desenvolvimento da MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

---

## <a name="command-line-tool-without-a-web-browser"></a>Ferramenta de linha de comando sem um navegador da Web

### <a name="device-code-flow"></a>Fluxo de código do dispositivo

Se você estiver escrevendo uma ferramenta de linha de comando que não tem controles da Web e não desejar ou não quiser usar os fluxos anteriores, use o fluxo de código do dispositivo.

A autenticação interativa com o Azure AD requer um navegador da Web. Para obter mais informações, consulte [Uso de navegadores da Web](https://aka.ms/msal-net-uses-web-browser). Para autenticar usuários em dispositivos ou sistemas operacionais que não fornecem um navegador da Web, o fluxo de código do dispositivo permite que o usuário use outro dispositivo, como um computador ou um telefone celular, para entrar interativamente. Usando o fluxo de código do dispositivo, o aplicativo obtém tokens por meio de um processo de duas etapas projetado para esses dispositivos ou sistemas operacionais. Como exemplos desses aplicativos, temos aqueles que são executados em iOT ou em ferramentas de linha de comando (CLI). A ideia é que:

1. Sempre que a autenticação do usuário for necessária, o aplicativo fornecerá um código para o usuário. O usuário será solicitado a usar outro dispositivo, como um smartphone conectado à Internet, para acessar uma URL, por exemplo, `https://microsoft.com/devicelogin`. Depois, o usuário é solicitado a inserir o código. Isso feito, a página da Web conduz o usuário por uma experiência de autenticação normal, a qual inclui solicitações de consentimento e autenticação multifator, se necessário.

2. Após a autenticação bem-sucedida, o aplicativo de linha de comando recebe os tokens necessários por meio de um canal de apoio e os usa para executar as chamadas à API Web necessárias.

### <a name="use-it"></a>Use

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication` contém um método denominado `AcquireTokenWithDeviceCode`.

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Esse método usa como parâmetros:

- O `scopes` para o qual solicitar um token de acesso.
- Um retorno de chamada que recebe o `DeviceCodeResult`.

  ![Propriedades DeviceCodeResult](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

O código de exemplo a seguir apresenta a Sinopse da maioria dos casos atuais, com explicações do tipo de exceções que você pode obter e sua mitigação. Para obter um exemplo de código totalmente funcional, consulte [Active-Directory-dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) no github.

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }
}

private static async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }

    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Essa extração é das [amostras de desenvolvimento da MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

Esse fluxo não se aplica ao macOS.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Essa extração é dos [exemplos de desenvolvimento do nó MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/standalone-samples/device-code).

```JavaScript
const msal = require('@azure/msal-node');

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

const deviceCodeRequest = {
    deviceCodeCallback: (response) => (console.log(response.message)),
    scopes: ["user.read"],
    timeout: 20,
};

pca.acquireTokenByDeviceCode(deviceCodeRequest).then((response) => {
    console.log(JSON.stringify(response));
}).catch((error) => {
    console.log(JSON.stringify(error));
});
```

# <a name="python"></a>[Python](#tab/python)

Essa extração é das [amostras de desenvolvimento da MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

---

## <a name="file-based-token-cache"></a>Cache de token baseado em arquivo

Na MSAL.NET, um cache de token na memória é fornecido por padrão.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>A serialização pode ser personalizada para aplicativos de área de trabalho do Windows e aplicativos Web ou APIs Web

No caso do .NET Framework e do .NET Core, se você não fizer nada extra, o cache de token na memória durará tanto quanto o aplicativo. Para entender por que a serialização não é fornecida pronta para o uso, lembre-se de que os aplicativos MSAL .NET de área de trabalho ou .NET Core podem ser aplicativos de console ou do Windows (os quais teriam acesso ao sistema de arquivos), *mas também* aplicativos Web ou APIs Web. É possível que esses aplicativos Web e APIs Web usem alguns mecanismos de cache específicos, como bancos de dados, caches distribuídos e caches Redis. Para ter um aplicativo de cache de token persistente na área de trabalho do .NET ou .NET Core, será necessário personalizar a serialização.

As classes e interfaces envolvidas na serialização do cache de token são dos seguintes tipos:

- ``ITokenCache``, que define os eventos para assinar solicitações de serialização do cache de token e os métodos para serializar ou desserializar o cache em vários formatos (ADAL v3.0, MSAL 2.x e MSAL 3.x = ADAL v5.0).
- ``TokenCacheCallback`` é um retorno de chamada passado aos eventos para permitir que você controle a serialização. Eles serão chamados com argumentos do tipo ``TokenCacheNotificationArgs``.
- ``TokenCacheNotificationArgs`` fornece apenas o ``ClientId`` do aplicativo e uma referência ao usuário para o qual o token está disponível.

  ![Diagrama de serialização de cache de token](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> A MSAL.NET cria os caches de token para você e fornece o cache `IToken` quando você chama as propriedades `UserTokenCache` e `AppTokenCache` de um aplicativo. Você não deve implementar a interface por conta própria. Ao implementar uma serialização do cache de token personalizada, sua responsabilidade é:
>
> - Reagir a eventos `BeforeAccess` e `AfterAccess` ou a suas contrapartes *Assíncronas*. O delegado `BeforeAccess` é responsável por desserializar o cache. O delegado `AfterAccess` é responsável por serializar o cache.
> - Tenham em mente que parte desses eventos armazena ou carrega blobs, os quais são passados por meio do argumento do evento para qualquer armazenamento desejado.

As estratégias são diferentes dependendo se você está escrevendo uma serialização de cache de token para um aplicativo cliente público, como uma área de trabalho, ou um aplicativo cliente confidencial, como um aplicativo Web ou uma API Web, ou um aplicativo daemon.

Desde a MSAL v2.x, você tem várias opções. Sua escolha depende de se você deseja serializar o cache somente para o formato MSAL.NET, que é um cache de formato unificado comum com a MSAL, mas também entre as plataformas. Ou, também é possível dar suporte à serialização do cache de token [herdado](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) da ADAL v3.

A personalização da serialização do cache de token para compartilhar o estado de SSO entre ADAL.NET 3.x, ADAL.NET 5.x e MSAL.NET é parcialmente explicada na amostra [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

### <a name="simple-token-cache-serialization-msal-only"></a>Serialização do cache de token simples (somente MSAL)

O exemplo a seguir é uma implementação ingênua da serialização personalizada de um cache de token para aplicativos da área de trabalho. Aqui, o cache de token de usuário está em um arquivo na mesma pasta que o aplicativo ou, em uma pasta por usuário por aplicativo, no caso em que o aplicativo é um [aplicativo de área de trabalho empacotado](/windows/msix/desktop/desktop-to-uwp-behind-the-scenes). Para obter o código completo, consulte o seguinte exemplo: [Active-Directory-dotnet-desktop-msgraph-v2](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2).

Depois de compilar o aplicativo, você habilita a serialização chamando ``TokenCacheHelper.EnableSerialization()`` e passando o `UserTokenCache` do aplicativo.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Essa classe auxiliar é semelhante trecho de código a seguir:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
   try
   {
    // For packaged desktop apps (MSIX packages) the executing assembly folder is read-only. 
    // In that case we need to use Windows.Storage.ApplicationData.Current.LocalCacheFolder.Path + "\msalcache.bin" 
    // which is a per-app read/write folder for packaged apps.
    // See https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-behind-the-scenes
    CacheFilePath = System.IO.Path.Combine(Windows.Storage.ApplicationData.Current.LocalCacheFolder.Path, "msalcache.bin3");
   }
   catch (System.InvalidOperationException)
   {
    // Fall back for an un-packaged desktop app
    CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin";
   }
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static string CacheFilePath { get; private set; }

  private static readonly object FileLock = new object();

  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Uma versão prévia de um serializador baseado em arquivo de cache de token de qualidade do produto para aplicativos cliente públicos para aplicativos da área de trabalho em execução no Windows, Mac e Linux está disponível na biblioteca de software livre [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal). É possível incluí-lo nos aplicativos do pacote NuGet a seguir: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Aviso de isenção de responsabilidade: A biblioteca Microsoft.Identity.Client.Extensions.Msal é uma extensão do MSAL.NET. É possível que as classes nessas bibliotecas façam seu caminho no MSAL.NET no futuro, seja na forma como estão ou com alterações significativas.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Serialização do cache de token duplo (cache unificado MSAL + ADAL v3)

Seria interessante implementar a serialização de cache de token com o formato de cache unificado. Esse formato é comum à ADAL.NET 4.x e MSAL.NET 2.x, e com outros MSALs da mesma geração ou mais antigos, na mesma plataforma. Inspire-se no seguinte código:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Nesse momento, a classe auxiliar é semelhante ao código a seguir:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="advanced-accessing-the-users-cached-tokens-in-background-apps-and-services"></a>Avançadas Acessando os tokens em cache do usuário em aplicativos e serviços em segundo plano

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, [chame uma API da Web do aplicativo de desktop](scenario-desktop-call-api.md).
