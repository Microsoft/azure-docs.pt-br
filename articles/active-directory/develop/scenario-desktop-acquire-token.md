---
title: Aplicativo da área de trabalho que chama APIs (adquirindo um token para o aplicativo) - web plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo da área de trabalho que chama as APIs da web (adquirindo um token para o aplicativo |)
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
ms.openlocfilehash: d84801d6368bcc29f08145f190c2a07c64050ced
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795097"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>Aplicativo de área de trabalho que chama APIs - web adquirir um token

Depois que você criou `IPublicClientApplication`, você usará para adquirir um token que, em seguida, você usará para chamar uma API da web.

## <a name="recommended-pattern"></a>Padrão recomendado

A API da web é definida por seu `scopes`. Seja qual for a experiência que você fornecer em seu aplicativo, o padrão que você vai querer usar é:

- Sistematicamente a tentativa de obter um token do cache de token chamando `AcquireTokenSilent`
- Se essa chamada falhar, use o `AcquireToken` fluxo que você deseja usar (representado aqui por `AcquireTokenXX`)

```CSharp
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

Aqui está o detalhe das várias maneiras para adquirir tokens em um aplicativo da área de trabalho agora

## <a name="acquiring-a-token-interactively"></a>Adquirir um token interativamente

O exemplo a seguir mostra o mínimo de código para obter um token de forma interativa para ler o perfil do usuário com o Microsoft Graph.

```CSharp
string[] scopes = new string["user.read"];
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

`AcquireTokenInteractive` tem apenas um parâmetro obrigatório ``scopes``, que contém uma enumeração de cadeias de caracteres que definem os escopos para o qual um token é necessário. Se o token for para o Microsoft Graph, os escopos necessários podem ser encontrados na referência de api de cada API do Microsoft graph na seção chamada "Permissões". Por exemplo, para [lista de contatos do usuário](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), o escopo "Read", "Contacts.Read" precisará ser usada. Consulte também [referência de permissões do Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

No Android, você também precisa especificar a atividade pai (usando `.WithParentActivityOrWindow`, veja abaixo) para que o token de volta ao que a atividade pai após a interação. Se você não especificá-lo, uma exceção será gerada ao chamar `.ExecuteAsync()`.

### <a name="specific-optional-parameters"></a>Parâmetros opcionais específicos

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Interface do usuário que está sendo interativa, é importante. `AcquireTokenInteractive` tem um parâmetro opcional específico que permite para especificar, para plataformas que dão suporte a ele, o pai da interface do usuário. Quando usado em um aplicativo da área de trabalho, `.WithParentActivityOrWindow` tem um tipo diferente, dependendo da plataforma:

```CSharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Comentários:

- No .NET Standard, o esperado `object` é um `Activity` no Android, um `UIViewController` no iOS, um `NSWindow` no MAC e uma `IWin32Window` ou `IntPr` no Windows.
- No Windows, você deve chamar `AcquireTokenInteractive` na interface de usuário do thread para que o navegador incorporado obtém o contexto de sincronização apropriado da interface do usuário.  Não chamar do thread de interface do usuário pode causar mensagens não bomba corretamente e/ou deadlock cenários com a interface do usuário. Uma maneira de chamar o MSAL do thread de interface do usuário, se você não estiver no thread da interface do usuário já é usar o `Dispatcher` no WPF.
- Se você estiver usando o WPF, para obter uma janela de um controle WPF, você pode usar `WindowInteropHelper.Handle` classe. A chamada é então, de um controle WPF (`this`):
  
  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` é usado para controlar a interatividade com o usuário com a especificação de um Prompt

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

A classe define as constantes a seguir:

- ``SelectAccount``: forçará o STS para apresentar a caixa de diálogo de seleção de conta que contém as contas para os quais o usuário tem uma sessão. Essa opção é útil quando os desenvolvedores de aplicativos desejam permitir que o usuário escolha entre diferentes identidades. Essa opção conduz a MSAL para enviar ``prompt=select_account`` ao provedor de identidade. Essa opção é o padrão, e ele faz de um bom trabalho ao fornecer a melhor experiência possível com base nas informações disponíveis (conta, a presença de uma sessão para o usuário e assim por diante. ...). Não alterá-lo, a menos que você tenha boa razão para fazê-lo.
- ``Consent``: permite que o aplicativo desenvolvedor obrigue o usuário ser solicitado o consentimento, mesmo se a permissão foi concedida antes. Nesse caso, a MSAL envia `prompt=consent` ao provedor de identidade. Essa opção pode ser usada em alguns aplicativos de segurança com foco em que o controle de organização exige que o usuário vê a caixa de diálogo de consentimento sempre que o aplicativo é usado.
- ``ForceLogin``: permite que o desenvolvedor do aplicativo para que o usuário as credenciais solicitado pelo serviço, mesmo que esse prompt do usuário não seria necessário. Essa opção pode ser útil se adquirir um token falhar, para permitir que o usuário re-entada. Nesse caso, a MSAL envia `prompt=login` ao provedor de identidade. Novamente, nós vimos é usado em alguns aplicativos de segurança com foco em que a governança de organização exige que o usuário relogs-in sempre que acessarem a partes específicas de um aplicativo.
- ``Never`` (para .NET 4.5 e o WinRT somente) não solicitar ao usuário, mas em vez disso, tentará usar o cookie armazenado na exibição da web incorporado oculto (veja a seguir: Exibições da Web em MSAL.NET). O uso dessa opção pode falhar e nesse caso `AcquireTokenInteractive` lançará uma exceção para notificar que uma interação da interface do usuário é necessária e você precisará usar outro `Prompt` parâmetro.
- ``NoPrompt``: Não enviará nenhum prompt para o provedor de identidade. Essa opção só é útil para políticas de perfil de edição do Azure AD B2C (consulte [especificidades do B2C](https://aka.ms/msal-net-b2c-specificities)).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Esse modificador é usado em um cenário avançado, onde você deseja que o usuário previamente dar consentimento para vários recursos com antecedência (e não quiser usar o consentimento incremental, o que normalmente é usado com MSAL.NET / v2.0 plataforma Microsoft identity). Para obter detalhes, consulte [explicativos: peça ao usuário consentimento com antecedência para vários recursos](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi é um ponto de extensibilidade

`WithCustomWebUi` é um ponto de extensibilidade que permite que você fornecer sua própria interface do usuário em aplicativos de cliente público e permitir que o usuário passar pelo ponto de extremidade /Authorize. do provedor de identidade e permitem que eles entrar em consentir. Em seguida, MSAL.NET pode resgatar o código de autenticação e obter um token. Por exemplo, ele é usado no Visual Studio ter elétrons aplicativos (por exemplo o Feedback do VS) fornecem a interação da web, mas deixá-la para MSAL.NET para fazer a maior parte do trabalho. Você também pode usá-lo se você quiser fornecer automação de interface do usuário. Em aplicativos de cliente público, MSAL.NET usa o padrão PKCE ([7636 RFC - chave de prova para código Exchange por clientes públicos OAuth](https://tools.ietf.org/html/rfc7636)) para garantir que a segurança seja respeitada: MSAL.NET só pode resgatar o código.

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>Como usar WithCustomWebUi

Para usar `.WithCustomWebUI`, você precisa:
  
  1. Implemente a `ICustomWebUi` interface (consulte [aqui](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Basicamente, você precisará implementar um método `AcquireAuthorizationCodeAsync` aceitar a URL do código de autorização (computada pelo MSAL.NET), permitindo que o usuário percorra a interação com o provedor de identidade e, em seguida, retornar a URL pela qual seria o provedor de identidade de volta ter chamado sua implementação de volta (incluindo o código de autorização). Se você tiver problemas, sua implementação deve lançar uma `MsalExtensionException` exceção perfeitamente cooperar com a MSAL.
  2. No seu `AcquireTokenInteractive` chamada, você pode usar `.WithCustomUI()` modificador passando a instância do seu personalizado da web da interface do usuário

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>Exemplos de implementação de ICustomWebUi na automação de teste - SeleniumWebUI

A equipe MSAL.NET reinstalado os nossos testes de interface do usuário para aproveitar esse mecanismo de extensibilidade. Se você estiver interessado, você pode ter uma olhada a [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) classe no código-fonte MSAL.NET

#### <a name="other-optional-parameters"></a>Outros parâmetros opcionais

Saiba mais sobre todos os outros parâmetros opcionais para `AcquireTokenInteractive` da documentação de referência para [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="integrated-windows-authentication"></a>Autenticação integrada do Windows

Se você quiser entrar em um usuário de domínio na máquina ingressada no domínio ou o Azure AD, você precisa usar:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

### <a name="constraints"></a>Restrições

- AcquireTokenByIntegratedWindowsAuth (IWA) só é útil para **federados** usuários apenas, que é, os usuários criados em um Active Directory e tem o suporte do Azure Active Directory. Os usuários criados diretamente no AAD, sem suporte do AD - **gerenciados** usuários - não é possível usar esse fluxo de autenticação. Essa limitação não afeta o fluxo do nome de usuário e senha.
- IWA é para aplicativos escritos para plataformas UWP, .NET Core e .NET Framework
- IWA sem ignorar o MFA (multi-factor authentication). Se MFA estiver configurada, IWA poderá falhar se um desafio de MFA é necessário, pois a MFA requer interação do usuário.
  > [!NOTE]
  > Esse é complicado. IWA não é interativo, mas 2FA exige interatividade de usuário. Você não controlar quando o provedor de identidade solicita 2FA a ser executada, o administrador do locatário faz. De nosso observações 2FA é necessário quando você fizer logon em um país diferente, quando conectados por meio de VPN a uma rede corporativa e, às vezes, mesmo quando conectado por meio de VPN. Não espere um determinístico conjunto de regras, Active Directory do Azure usa inteligência Artificial para aprender continuamente se 2FA é necessária. Você deverá fazer fallback para um prompt do usuário (interativo autenticação ou dispositivo de fluxo de código) se IWA falhar.

- A autoridade passado a `PublicClientApplicationBuilder` precisa ser:
  - ed de locatário (do formulário `https://login.microsoftonline.com/{tenant}/` onde `tenant` é o guid que representa a ID de locatário ou em um domínio associado ao locatário.
  - para qualquer trabalho e contas corporativas (`https://login.microsoftonline.com/organizations/`)

  > Não há suporte para contas pessoais da Microsoft (não é possível usar /common ou /consumers locatários)

- Como a autenticação integrada do Windows é um fluxo silencioso:
  - o usuário do seu aplicativo deve ter consentido anteriormente para usar o aplicativo
  - ou o administrador de locatário deve ter consentido anteriormente a todos os usuários no locatário para usar o aplicativo.
  - Em outras palavras:
    - ou você como desenvolvedor ter pressionado a **Grant** botão no portal do Azure por conta própria,
    - ou um administrador de locatários pressionou a **conceder/revogar o consentimento do administrador de domínio do locatário {}** botão na **permissões de API** guia do registro para o aplicativo (consulte [adicionar permissões para acessem APIs web](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis))
    - ou que você forneceu uma maneira para os usuários dar consentimento ao aplicativo (consulte [solicitar o consentimento do usuário individual](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent))
    - ou que você forneceu uma maneira para que o administrador de locatários dar consentimento para o aplicativo (consulte [consentimento do administrador](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant))

- Este fluxo está habilitado para a área de trabalho do .net, .net core e Windows Universal aplicativos UWP (). No .NET core apenas a sobrecarga de colocar o nome de usuário está disponível, como a plataforma .NET Core não pode solicitar o nome de usuário para o sistema operacional.
  
Para obter mais informações sobre o consentimento, consulte [v 2.0 permissões e consentimento](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="how-to-use-it"></a>Como usar

Você normalmente precisa de apenas um parâmetro (`scopes`). No entanto dependendo da forma que o administrador do Windows tem as políticas de configuração, pode ser possível que os aplicativos em seu computador windows não são permitidos para pesquisar o usuário conectado. Nesse caso, usar um segundo método `.WithUsername()` e passe o nome de usuário do usuário conectado como um formato UPN - `joe@contoso.com`.

O exemplo a seguir apresenta o caso mais atual, com explicações do tipo de exceções, que você pode obter e suas atenuações

```CSharp
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

Para obter a lista dos possíveis modificadores em AcquireTokenByIntegratedWindowsAuthentication, consulte [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="username--password"></a>Nome de usuário + senha

Você também pode adquirir um token, fornecendo o nome de usuário e senha. Esse fluxo é limitado e não é recomendado, mas há ainda usar casos em que é necessário.

### <a name="this-flow-isnt-recommended"></a>Este fluxo não é recomendado

Esse fluxo é **não recomendável** porque seu aplicativo pedir a um usuário para que sua senha não é seguro. Para obter mais informações sobre esse problema, consulte [deste artigo](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). É o fluxo preferencial para adquirir um token silenciosamente em máquinas do Windows ingressados no domínio [autenticação integrada do Windows](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Caso contrário, você também pode usar [fluxo de código do dispositivo](https://aka.ms/msal-net-device-code-flow)

> [!NOTE] 
> Embora isso seja útil em alguns casos (cenários de DevOps), se você quiser usar o nome de usuário e senha em cenários interativos em que você fornece seu onw da interface do usuário, você deve realmente pensar sobre como mover para longe dela. Usando o nome de usuário/senha você está dando-up várias coisas:
>
> - principais locatários de identidade moderno: senha obtém fished, reproduzidos. Como temos esse conceito de um segredo de compartilhamento pode ser interceptado.
> Isso é incompatível com sem senha.
> - os usuários que precisem de MFA não conseguirá entrar (pois não há nenhuma interação)
> - Os usuários não poderão logon único

### <a name="constraints"></a>Restrições

As seguintes restrições também se aplicam:

- O fluxo de nome de usuário e senha não é compatível com acesso condicional e autenticação multifator: Como consequência, se seu aplicativo for executado em um locatário do Azure AD em que o administrador do locatário requer a autenticação multifator, é possível usar esse fluxo. Muitas organizações fazem isso.
- Ele funciona somente para o trabalho e contas de Estudante (não MSA)
- O fluxo está disponível na área de trabalho do .net e .net core, mas não em UWP

### <a name="b2c-specifics"></a>Informações específicas do B2C

[Para obter mais informações sobre como usar ROPC com B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="how-to-use-it"></a>Como usá-lo?

`IPublicClientApplication`contém o método `AcquireTokenByUsernamePassword`

O exemplo a seguir apresenta um caso simplificado

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
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

O exemplo a seguir apresenta o caso mais atual, com explicações do tipo de exceções, que você pode obter e suas atenuações

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAync();
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

Para obter detalhes sobre todos os modificadores que podem ser aplicados a `AcquireTokenByUsernamePassword`, consulte [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="command-line-tool-without-web-browser"></a>Ferramenta de linha de comando (sem o navegador da web)

### <a name="device-code-flow-why-and-how"></a>O fluxo de código de dispositivo por quê? e como?

Se você estiver escrevendo uma ferramenta de linha de comando (que não tem controles da Web) e não pode ou não quiser usar os fluxos anteriores, você precisará usar `AcquireTokenWithDeviceCode`.

Autenticação interativa com o Azure AD requer um navegador da web (para obter detalhes, consulte [uso de navegadores da web](https://aka.ms/msal-net-uses-web-browser)). No entanto, para autenticar usuários em dispositivos ou sistemas operacionais que não fornecem um navegador da Web, o fluxo de código de dispositivo permite que o usuário use outro dispositivo (por exemplo, outro computador ou um telefone celular) para entrar interativamente. Usando o fluxo de código do dispositivo, o aplicativo obtém tokens por meio de um processo de duas etapas, projetado especialmente para esse dispositivos/sistema operacional. Exemplos de tais aplicativos são aplicativos executados no iOT ou ferramentas de linha de comando (CLI). A ideia é que:

1. Sempre que a autenticação do usuário é necessária, o aplicativo fornece um código e pede ao usuário para usar outro dispositivo (como um smartphone conectado à internet) para navegar até uma URL (por exemplo, `https://microsoft.com/devicelogin`), em que o usuário será solicitado a inserir o código. Se feito, a página da web levará o usuário por meio de uma experiência de autenticação normal, incluindo solicitações de consentimento e a autenticação multifator, se necessário.

2. Após a autenticação bem-sucedida, o aplicativo de linha de comando receberá os símbolos necessários por meio de um canal de retorno e os usará para executar as chamadas à API da web que precisa.

### <a name="code"></a>Código

`IPublicClientApplication`contém um método chamado `AcquireTokenWithDeviceCode`

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Esse método aceita como parâmetros:

- O `scopes` para solicitar um token de acesso
- Um retorno de chamada que receberá o `DeviceCodeResult`

  ![imagem](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

O código de exemplo a seguir apresenta o caso mais atual, com explicações sobre o tipo de exceções, que você pode obter e seu tamanho máximo.

```CSharp
static async Task<AuthenticationResult> GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication pca = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 AuthenticationResult result = null;
 var accounts = await app.GetAccountsAsync();

 // All AcquireToken* methods store the tokens in the cache, so check the cache first
 try
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
       .ExecuteAsync();
 }
 catch (MsalUiRequiredException ex)
 {
  // A MsalUiRequiredException happened on AcquireTokenSilent.
  // This indicates you need to call AcquireTokenInteractive to acquire a token
  System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
 }

 try
 {
  result = await app.AcquireTokenWithDeviceCode(scopes,
      deviceCodeCallback =>
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
  // If you use a CancellationToken, and call the Cancel() method on it, then this may be triggered
  // to indicate that the operation was cancelled.
  // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
  // for more detailed information on how C# supports cancellation in managed threads.
 }
 catch (MsalClientException ex)
 {
  // Verification code expired before contacting the server
  // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
  // call to `AcquireTokenWithDeviceCode` is not cancelled in between
 }
}
```

## <a name="file-based-token-cache"></a>Cache de token baseado em arquivo

Na MSAL.NET, um cache de token na memória é fornecido por padrão.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>A serialização é personalizável em aplicativos da área de trabalho do Windows e aplicativos de web/APIs web

No caso do .NET Framework e .NET core, se você não fizer nada adicional, o cache de token na memória tem duração para a duração do aplicativo. Para entender por que a serialização não for fornecida para uso imediato, lembre-se de .NET da MSAL aplicativos de área de trabalho/core podem ser aplicativos do Windows (que teriam acesso ao sistema de arquivos), ou console **, mas também** aplicativos da Web ou API da web. Esses aplicativos Web e APIs da web podem usar alguns mecanismos de cache específica, como bancos de dados, os caches distribuídos, caches redis e assim por diante. Para que um aplicativo de cache de token persistente na área de trabalho do .NET ou Core, você precisará personalizar a serialização.

Classes e interfaces envolvidas na serialização do cache de token são os seguintes tipos:

- ``ITokenCache``, que define os eventos para assinar solicitações de serialização do cache de token, bem como métodos para serializar ou desserializar o cache em vários formatos (v3.0 ADAL, MSAL 2.x e MSAL 3. x = ADAL v5.0)
- ``TokenCacheCallback`` é um retorno de chamada passado aos eventos para permitir que você controle a serialização. elas se chamarão com argumentos de tipo ``TokenCacheNotificationArgs``.
- ``TokenCacheNotificationArgs`` fornece apenas o ``ClientId`` do aplicativo e uma referência para o usuário para o qual o token está disponível

  ![imagem](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> A MSAL.NET cria os caches de token para você e fornece o cache `IToken` quando você chama os métodos `GetUserTokenCache` e `GetAppTokenCache` de um aplicativo. Você não deve para implementar a interface por conta própria. Ao implementar uma serialização do cache de token personalizada, sua responsabilidade é:
>
> - Reagir aos `BeforeAccess` e `AfterAccess` "eventos" (ou se eles *Async* equivalente). O`BeforeAccess` delegado é responsável para desserializar o cache, enquanto o `AfterAccess` um é responsável pela serialização do cache.
> - Parte desses eventos armazena ou carrega blobs, os quais são passados por meio do argumento do evento para qualquer armazenamento desejado.

As estratégias são diferentes dependendo se você estiver escrevendo uma serialização de cache de token para um aplicativo cliente público (Desktop) ou um aplicativo cliente confidencial (web/API web de aplicativo, o aplicativo de daemon).

Desde o MSAL V2.x você tem várias opções, dependendo se você deseja serializar o cache somente para o formato MSAL.NET (cache de formato unificado que é comum com a MSAL, mas também em todas as plataformas), ou se você deseja dar suporte a [herdados](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) Serialização de cache de token do ADAL V3.

A personalização de serialização de cache de Token para compartilhar o estado SSO entre ADAL.NET 3. x, ADAL.NET 5.x e MSAL.NET é explicado em parte do exemplo a seguir: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>Serialização do cache de token simples (somente MSAL)

Segue abaixo um exemplo de uma implementação ingênua da serialização personalizada de um cache de token para aplicativos da área de trabalho. Aqui, o cache de token de usuário em um arquivo na mesma pasta que o aplicativo.

Depois de compilar o aplicativo, você habilitar a serialização ao chamar ``TokenCacheHelper.EnableSerialization()`` passando o aplicativo `UserTokenCache`

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Essa classe auxiliar se parece com o trecho de código a seguir:

```CSharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

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

Uma visualização de um cache de token de qualidade do produto serializador baseados em arquivo para aplicativos de cliente público (para aplicativos da área de trabalho em execução no Windows, Mac e linux) está disponível a partir de [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) biblioteca de código-fonte aberto. É possível incluí-lo nos aplicativos do pacote NuGet a seguir: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Isenção de responsabilidade. A biblioteca Microsoft.Identity.Client.Extensions.Msal é uma extensão ao longo de MSAL.NET. Essas bibliotecas de classes podem transformar sua maneira em MSAL.NET no futuro, como está ou com alterações significativas.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Serialização de cache de token dupla (cache MSAL unificada + ADAL V3)

Se você quiser implementar a serialização de cache de token com a Unificação de cache formato (comuns a ADAL.NET 4.x e MSAL.NET 2. x e com outros MSALs da mesma geração ou anterior, na mesma plataforma), você pode obter inspirado pelo código a seguir :

```CSharp
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

Desta vez a classe auxiliar se parece com o código a seguir:

```CSharp
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
   usertokenCache = cache;
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   usertokenCache.SetBeforeAccess(BeforeAccessNotification);
   usertokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Token cache
  /// </summary>
  static ITokenCache usertokenCache;

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

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Chamar uma API web do aplicativo da área de trabalho](scenario-desktop-call-api.md)
