---
title: Azure AD B2C e MSAL.NET
titleSuffix: Microsoft identity platform
description: Considerações ao usar Azure AD B2C com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2020
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: bdb9e12fdf721204ce98d23e5d5aeea535ddf23d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574802"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Usar o MSAL.NET para conectar usuários com identidades sociais

Você pode usar o MSAL.NET para conectar usuários com identidades sociais usando [Azure Active Directory B2C (Azure ad B2C)](../../active-directory-b2c/overview.md). Azure AD B2C é criado com base na noção de políticas. No MSAL.NET, a especificação de uma política se traduz em fornecer uma autoridade.

- Ao instanciar o aplicativo cliente público, especifique a política como parte da autoridade.
- Quando você quiser aplicar uma política, chame uma substituição de `AcquireTokenInteractive` que aceita o `authority` parâmetro.

Este artigo se aplica a MSAL.NET 3. x. Para MSAL.NET 2. x, consulte [Azure ad B2C especificos no MSAL 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x) no Wiki do MSAL.net no github.

## <a name="authority-for-an-azure-ad-b2c-tenant-and-policy"></a>Autoridade para um locatário Azure AD B2C e uma política

O formato de autoridade para Azure AD B2C é: `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`

- `azureADB2CHostname` -O nome do locatário de Azure AD B2C mais o host. Por exemplo, *contosob2c.b2clogin.com*.
- `tenant` -O nome de domínio ou a ID de diretório (locatário) do locatário Azure AD B2C. Por exemplo, *contosob2c.onmicrosoft.com* ou um GUID, respectivamente.
- `policyName` -O nome do fluxo do usuário ou da política personalizada a ser aplicada. Por exemplo, uma política de inscrição/entrada como *b2c_1_susi*.

Para obter mais informações sobre autoridades de Azure AD B2C, consulte [definir URLs de redirecionamento para b2clogin.com](../../active-directory-b2c/b2clogin.md).

## <a name="instantiating-the-application"></a>Instanciando o aplicativo

Forneça a autoridade chamando `WithB2CAuthority()` quando você criar o objeto de aplicativo:

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string AzureADB2CHostname = "fabrikamb2c.b2clogin.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://{AzureADB2CHostname}/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Adquirir um token para aplicar uma política

Adquirir um token para uma API protegida por Azure AD B2C em um aplicativo cliente público exige que você use as substituições com uma autoridade:

```csharp
AuthenticationResult authResult = null;
IEnumerable<IAccount> accounts = await application.GetAccountsAsync(policy);
IAccount account = accounts.FirstOrDefault();
try
{
    authResult = await application.AcquireTokenSilent(scopes, account)
                      .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    authResult = await application.AcquireTokenInteractive(scopes)
                        .WithAccount(account)
                        .WithParentActivityOrWindow(ParentActivityOrWindow)
                        .ExecuteAsync();
}  
```

No snippet de código anterior:

- `policy` é uma cadeia de caracteres que contém o nome do seu Azure AD B2C fluxo de usuário ou política personalizada (por exemplo, `PolicySignUpSignIn` ).
- `ParentActivityOrWindow` é necessário para o Android (a atividade) e é opcional para outras plataformas que dão suporte a uma interface do usuário pai como Windows no Microsoft Windows e UIViewController no iOS. Para obter mais informações sobre a caixa de diálogo da interface do usuário, consulte [WithParentActivityOrWindow](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow) no wiki do MSAL.

A aplicação de um fluxo de usuário ou de uma política personalizada (por exemplo, permitindo que o usuário edite seu perfil ou Redefina sua senha) é feita no momento chamando `AcquireTokenInteractive` . Para essas duas políticas, você não usa o resultado de token/autenticação retornado.

## <a name="profile-edit-policies"></a>Políticas de edição de perfil

Para permitir que os usuários entrem com uma identidade social e editem seu perfil, aplique a política Azure AD B2C Editar perfil.

Faça isso chamando `AcquireTokenInteractive` com a autoridade para essa política. Como o usuário já está conectado e tem uma sessão de cookie ativa, use `Prompt.NoPrompt` para impedir que a caixa de diálogo de seleção de conta seja exibida.

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await application.GetAccountsAsync(PolicyEditProfile);
    IAccount account = accounts.FirstOrDefault();
    try
    {
        var authResult = await application.AcquireTokenInteractive(scopes)
                            .WithPrompt(Prompt.NoPrompt),
                            .WithAccount(account)
                            .WithB2CAuthority(AuthorityEditProfile)
                            .ExecuteAsync();
     }
    catch
    {
    }
}
```

## <a name="resource-owner-password-credentials-ropc"></a>Credenciais de senha do proprietário do recurso (ROPC)

Para obter mais informações sobre o fluxo ROPC, consulte [entrar com a concessão de credenciais de senha do proprietário do recurso](v2-oauth-ropc.md).

O fluxo ROPC **não é recomendado** porque a solicitação de uma senha ao usuário em seu aplicativo não é segura. Para obter mais informações sobre esse problema, consulte [qual é a solução para o crescente problema de senhas?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/).

Usando o nome de usuário/senha em um fluxo ROPC, você sacrifica várias coisas:

- Principais princípios da identidade moderna: a senha pode ser inpeixe ou reproduzida porque o segredo compartilhado pode ser interceptado. Por definição, ROPC é incompatível com fluxos sem senha.
- Os usuários que precisam fazer a MFA não poderão entrar (como não há nenhuma interação).
- Os usuários não poderão usar o SSO (logon único).

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Configurar o fluxo ROPC no Azure AD B2C

Em seu locatário do Azure AD B2C, crie um novo fluxo de usuário e selecione **entrar usando ROPC** para habilitar o ROPC para o fluxo do usuário. Para obter mais informações, consulte [Configurar o fluxo de credenciais de senha do proprietário do recurso](../../active-directory-b2c/add-ropc-policy.md).

`IPublicClientApplication` contém o `AcquireTokenByUsernamePassword` método:

```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Esse `AcquireTokenByUsernamePassword` método usa os seguintes parâmetros:

- Os *escopos* para os quais obter um token de acesso.
- Um *nome de usuário*.
- Uma *senha* de SecureString para o usuário.

### <a name="limitations-of-the-ropc-flow"></a>Limitações do fluxo de ROPC

O fluxo ROPC **só funciona para contas locais**, em que os usuários se registraram com Azure ad B2C usando um endereço de email ou nome de usuário. Esse fluxo não funciona ao federar um provedor de identidade externo com suporte pelo Azure AD B2C (Facebook, Google etc.).

## <a name="google-auth-and-embedded-webview"></a>Google auth e Embedded WebView

Se você estiver usando o Google como um provedor de identidade, recomendamos o uso do navegador do sistema, pois o Google não permite a [autenticação de webviews inseridos](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Atualmente, `login.microsoftonline.com` é uma autoridade confiável com o Google e funcionará com o WebView incorporado. No entanto, `b2clogin.com` não é uma autoridade confiável com o Google, portanto os usuários não poderão se autenticar.

Forneceremos uma atualização para esse [problema](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) se as coisas mudarem.

## <a name="token-caching-in-msalnet"></a>Cache de token em MSAL.NET

### <a name="known-issue-with-azure-ad-b2c"></a>Problema conhecido com o Azure AD B2C

O MSAL.NET dá suporte a um [cache de token](/dotnet/api/microsoft.identity.client.tokencache). A chave de cache de token é baseada nas declarações retornadas pelo IdP (provedor de identidade).

Atualmente, o MSAL.NET precisa de duas declarações para criar uma chave de cache de token:

- `tid` (a ID de locatário do Azure AD)
- `preferred_username`

Ambas as declarações podem estar ausentes em cenários de Azure AD B2C porque nem todos os provedores de identidade social (Facebook, Google e outros) os retornam nos tokens que retornam para Azure AD B2C.

Um sintoma desse cenário é que MSAL.NET retorna `Missing from the token response` quando você acessa o valor da `preferred_username` declaração em tokens emitidos por Azure ad B2C. MSAL usa o `Missing from the token response` valor de `preferred_username` para manter a compatibilidade cruzada de cache entre bibliotecas.

### <a name="workarounds"></a>Soluções Alternativas

#### <a name="mitigation-for-missing-tenant-id"></a>Mitigação para a ID de locatário ausente

A solução alternativa sugerida é usar o [cache pela política](#acquire-a-token-to-apply-a-policy) descrita anteriormente.

Como alternativa, você pode usar a `tid` declaração se estiver usando [políticas personalizadas](../../active-directory-b2c/custom-policy-get-started.md) no Azure ad B2C. Políticas personalizadas podem retornar declarações adicionais para seu aplicativo usando a [transformação declarações](../../active-directory-b2c/claims-transformation-technical-profile.md).

#### <a name="mitigation-for-missing-from-the-token-response"></a>Mitigação para "ausente da resposta do token"

Uma opção é usar a `name` declaração em vez de `preferred_username` . Para incluir a `name` declaração em tokens de ID emitidos por Azure ad B2C, selecione **nome de exibição** ao configurar o fluxo do usuário.

Para obter mais informações sobre como especificar quais declarações são retornadas por seus fluxos de usuário, consulte [tutorial: criar fluxos de usuário em Azure ad B2C](../../active-directory-b2c/tutorial-create-user-flows.md).

## <a name="next-steps"></a>Próximas etapas

Mais detalhes sobre como adquirir tokens interativamente com o MSAL.NET para aplicativos Azure AD B2C são fornecidos no exemplo a seguir.

| Amostra | Plataforma | Descrição|
|------ | -------- | -----------|
|[Active-Directory-B2C-xamarin-nativo](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Um aplicativo Xamarin Forms que usa MSAL.NET para autenticar usuários por meio de Azure AD B2C e, em seguida, acessar uma API Web com os tokens retornados.|
