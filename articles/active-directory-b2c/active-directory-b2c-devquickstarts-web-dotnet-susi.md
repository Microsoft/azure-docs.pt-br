---
title: Azure Active Directory B2C | Microsoft Docs
description: "Como criar um aplicativo Web com inscrição/entrada, edição de perfil e redefinição de senha usando o Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: barbaraselden
ms.assetid: 30261336-d7a5-4a6d-8c1a-7943ad76ed25
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2017
ms.author: parakhj
ms.openlocfilehash: 3144ced01b524abb035dc1c6f0cdf764bec46804
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-aspnet-web-app-with-azure-active-directory-b2c-sign-up-sign-in-profile-edit-and-password-reset"></a>Criar um aplicativo Web ASP.NET com inscrição/entrada, edição de perfil e redefinição de senha do Azure Active Directory B2C

Este tutorial mostra como:

> [!div class="checklist"]
> * Adicionar recursos de identidade do Azure AD B2C ao seu aplicativo Web
> * Registrar seu aplicativo Web em seu diretório do Azure AD B2C
> * Criar inscrição/entrada, edição de perfil e política de redefinição de senha de usuário para o seu aplicativo Web

## <a name="prerequisites"></a>Pré-requisitos

- Você deve conectar seu Locatário B2C a uma conta do Azure. Você pode criar uma conta gratuita do Azure [aqui](https://azure.microsoft.com/en-us/).
- Você precisa do [Microsoft Visual Studio](https://www.visualstudio.com/) ou um programa semelhante para exibir e modificar o código de exemplo.

## <a name="create-an-azure-ad-b2c-directory"></a>Criar um diretório do AD B2C do Azure

Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos e muito mais. Se você ainda não tiver um, crie um diretório B2C antes de prosseguir neste guia.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

> [!NOTE]
> 
> Você precisa se conectar ao Locatário B2C para sua assinatura do Azure. Depois de selecionar **Criar**, selecione a opção **Associar um Locatário do Azure AD B2C existente à minha assinatura do Azure** e, em seguida, na lista suspensa de **Locatário do Azure AD B2C**, selecione o locatário que você deseja associar.

## <a name="create-and-register-an-application"></a>Criar e registrar um aplicativo

Em seguida, você precisa criar e registrar um aplicativo em seu diretório B2C. Isso fornece ao Azure AD B2C as informações de que ele precisa para se comunicar de forma segura com seu aplicativo. 

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

Quando você terminar, você terá uma API e um aplicativo nativo nas suas configurações de aplicativo.

## <a name="create-policies-on-your-b2c-tenant"></a>Criar políticas em seu locatário B2C

No AD B2C do Azure, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Este exemplo de código contém três experiências de identidade: **inscrição e entrada**, **edição de perfil** e **redefinição de senha**.  Você precisa criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md). Para cada política, certifique-se de selecionar a declaração ou atributo de Nome de exibição e copiar o nome da sua política para uso posterior.

### <a name="add-your-identity-providers"></a>Adicionar seus provedores de identidade

Nas configurações, selecione **Provedores de Identidade** e escolha Inscrição do nome de usuário ou Inscrição de email.

### <a name="create-a-sign-up-and-sign-in-policy"></a>Criar uma política de inscrição e entrada

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

### <a name="create-a-profile-editing-policy"></a>Criar uma política de edição de perfil

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

### <a name="create-a-password-reset-policy"></a>Criar uma política de redefinição de senha

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

Depois de criar as políticas, você estará pronto para compilar o aplicativo.

## <a name="download-the-sample-code"></a>Baixe o código de exemplo

O código para este tutorial é mantido no [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Você pode clonar a amostra executando:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Depois de baixar o código de exemplo, abra o arquivo .sln do Visual Studio para começar. Agora, sua solução contém dois projetos: `TaskWebApp` e `TaskService`. `TaskWebApp` é o aplicativo Web MVC com o qual o usuário interage. `TaskService` é API Web back-end do aplicativo que armazena a lista de tarefas de cada usuário. Este artigo discutirá apenas o aplicativo `TaskWebApp`. Para saber como criar `TaskService` usando o Azure AD B2C, confira [nosso tutorial da API Web .NET](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="update-code-to-use-your-tenant-and-policies"></a>Atualizar o código para usar as suas políticas e locatário

Nossa amostra é configurada para usar as políticas e a ID do cliente de nosso locatário de demonstração. Para conectar-se ao seu próprio locatário, você precisa abrir `web.config` no projeto `TaskWebApp` e substituir os valores a seguir:

* `ida:Tenant` pelo nome do locatário
* `ida:ClientId` pela ID de aplicativo do aplicativo Web
* `ida:ClientSecret` pela chave de segredo do aplicativo Web
* `ida:SignUpSignInPolicyId` pelo nome da política "Inscrever-se ou Entrar"
* `ida:EditProfilePolicyId` pelo nome de política "Editar Perfil"
* `ida:ResetPasswordPolicyId` pelo nome de política "Redefinir Senha"

## <a name="launch-the-app"></a>Iniciar o aplicativo
No Visual Studio, inicie o aplicativo. Navegue até a guia Lista de tarefas e observe que o URl é: https://login.microsoftonline.com/*YourTenantName*/oauth2/v2.0/authorize?p=*YourSignUpPolicyName*&client_id=*YourclientID*.....

Inscreva-se no aplicativo usando o seu endereço de email ou nome de usuário. Saia, em seguida, entre novamente e edite o perfil ou redefina a senha. Saia e entre como outro usuário. 

## <a name="add-social-idps"></a>Adicionar IDPs sociais

Atualmente, o aplicativo suporta inscrição e entrada usando apenas **contas locais**; contas armazenadas no seu diretório do B2C que usam um nome de usuário e senha. Com o Azure AD B2C, você pode adicionar suporte a outros **provedores de identidade** (IDPs), sem alterar qualquer código.

Para adicionar IDPs sociais ao seu aplicativo, comece seguindo as instruções detalhadas nestes artigos. Para cada IDP ao qual deseja oferecer suporte, você precisa registrar um aplicativo no sistema e obter uma ID de cliente.

* [Configurar o Facebook como um IDP](active-directory-b2c-setup-fb-app.md)
* [Configurar o Google como um IDP](active-directory-b2c-setup-goog-app.md)
* [Configurar o Amazon como um IDP](active-directory-b2c-setup-amzn-app.md)
* [Configurar o LinkedIn como um IDP](active-directory-b2c-setup-li-app.md)

Após adicionar provedores de identidade ao seu diretório de B2C, edite cada uma das suas três políticas para incluir os novos IDPs, como descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md). Depois de salvar as políticas, execute o aplicativo novamente.  Você deve ver os novos IDPs adicionados como opções de entrada e de inscrição em cada experiência de identidade.

Você pode fazer experiências com suas políticas e observar o efeito no exemplo de aplicativo. Adicione ou remova IDPs, manipule declarações de aplicativo ou altere os atributos de inscrição. Experimente até começar a entender como as políticas, solicitações de autenticação e OWIN funcionam juntos.

## <a name="sample-code-walkthrough"></a>Passo a passo do código de exemplo
As seções a seguir mostram como o código do aplicativo de exemplo está configurado. Você pode usar isso como um guia no desenvolvimento do seu futuro aplicativo.

### <a name="add-authentication-support"></a>Adicionar suporte a autenticação

Agora você pode configurar seu aplicativo para usar o Azure AD B2C. Seu aplicativo se comunica com o Azure AD B2C ao enviar solicitações de autenticação OpenID Connect. As solicitações determinam a experiência do usuário que o aplicativo deseja executar especificando a política. Você pode usar a biblioteca OWIN da Microsoft para enviar essas solicitações, executar políticas, gerenciar sessões do usuário e assim por diante.

#### <a name="install-owin"></a>Instalar a OWIN

Para começar, adicione os pacotes do NuGet de middleware do OWIN ao projeto usando o Console do Gerenciador de Pacotes do Visual Studio.

```Console
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

#### <a name="add-an-owin-startup-class"></a>Adicionar uma classe de inicialização da OWIN

Adicione uma classe de inicialização OWIN para a API chamada `Startup.cs`.  Clique com o botão direito do mouse no projeto, selecione **Adicionar** e **Novo Item** e pesquise OWIN. O middleware OWIN invocará o método `Configuration(…)` quando seu aplicativo for iniciado.

Em nossa amostra, alteramos a declaração de classe para `public partial class Startup` e implementamos a outra parte da classe em `App_Start\Startup.Auth.cs`. No método `Configuration`, adicionamos uma chamada para `ConfigureAuth`, que é definida em `Startup.Auth.cs`. Após as modificações, `Startup.cs` é semelhante ao seguinte:

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

#### <a name="configure-the-authentication-middleware"></a>Configurar do middleware de autenticação

Abra o arquivo `App_Start\Startup.Auth.cs` e implemente o método `ConfigureAuth(...)`. Os parâmetros que você fornece em `OpenIdConnectAuthenticationOptions` servem como coordenadas para seu aplicativo se comunicar com o Azure AD B2C. Se você não especificar certos parâmetros, ele usará o valor padrão. Por exemplo, não especificamos o `ResponseType` na amostra, então o valor padrão `code id_token` será usado em cada solicitação de saída para o Azure AD B2C.

Você também precisa configurar a autenticação de cookie. O middleware OpenID Connect usa cookies para manter as sessões de usuário, entre outras coisas.

```CSharp
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // Initialize variables ...

    // Configure the OWIN middleware
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseCookieAuthentication(new CookieAuthenticationOptions());
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseOpenIdConnectAuthentication(
            new OpenIdConnectAuthenticationOptions
            {
                // Generate the metadata address using the tenant and policy information
                MetadataAddress = String.Format(AadInstance, Tenant, DefaultPolicy),

                // These are standard OpenID Connect parameters, with values pulled from web.config
                ClientId = ClientId,
                RedirectUri = RedirectUri,
                PostLogoutRedirectUri = RedirectUri,

                // Specify the callbacks for each type of notifications
                Notifications = new OpenIdConnectAuthenticationNotifications
                {
                    RedirectToIdentityProvider = OnRedirectToIdentityProvider,
                    AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    AuthenticationFailed = OnAuthenticationFailed,
                },

                // Specify the claims to validate
                TokenValidationParameters = new TokenValidationParameters
                {
                    NameClaimType = "name"
                },

                // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
                Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
            }
        );
    }

    // Implement the "Notification" methods...
}
```

Em `OpenIdConnectAuthenticationOptions` acima, definimos um conjunto de funções de retorno de chamada para notificações específicas que são recebidas pelo middleware OpenID Connect. Esses comportamentos são definidos usando um objeto `OpenIdConnectAuthenticationNotifications` e armazenados na variável `Notifications`. Em nossa amostra, podemos definir três retornos de chamada diferentes dependendo do evento.

### <a name="using-different-policies"></a>Usando políticas diferentes

A notificação `RedirectToIdentityProvider` é disparada sempre que uma solicitação é feita ao Azure AD B2C. Na função de retorno de chamada `OnRedirectToIdentityProvider`, verificamos na chamada de saída se desejamos usar uma política diferente. Para fazer uma redefinição de senha ou editar um perfil, você precisa usar a política correspondente, assim como a política de redefinição de senha, em vez da política padrão de "Inscrever-se ou Entrar".

Em nossa amostra, quando um usuário deseja redefinir a senha ou editar o perfil, adicionamos a política que preferimos usar no contexto do OWIN. Isso pode ser feito fazendo o seguinte:

```CSharp
    // Let the middleware know you are trying to use the edit profile policy
    HttpContext.GetOwinContext().Set("Policy", EditProfilePolicyId);
```

Você também pode implementar a função de retorno de chamada `OnRedirectToIdentityProvider` fazendo o seguinte:

```CSharp
/*
*  On each call to Azure AD B2C, check if a policy (e.g. the profile edit or password reset policy) has been specified in the OWIN context.
*  If so, use that policy when making the call. Also, don't request a code (since it won't be needed).
*/
private Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    var policy = notification.OwinContext.Get<string>("Policy");

    if (!string.IsNullOrEmpty(policy) && !policy.Equals(DefaultPolicy))
    {
        notification.ProtocolMessage.Scope = OpenIdConnectScopes.OpenId;
        notification.ProtocolMessage.ResponseType = OpenIdConnectResponseTypes.IdToken;
        notification.ProtocolMessage.IssuerAddress = notification.ProtocolMessage.IssuerAddress.Replace(DefaultPolicy, policy);
    }

    return Task.FromResult(0);
}
```

### <a name="handling-authorization-codes"></a>Manipulando códigos de autorização

A notificação `AuthorizationCodeReceived` é disparada quando um código de autorização é recebido. O middleware OpenID Connect não dá suporte à permutação de códigos por tokens de acesso. Você pode permutar manualmente o código pelo token em uma função de retorno de chamada. Para obter mais informações, examine a [documentação](active-directory-b2c-devquickstarts-web-api-dotnet.md) que explica como fazê-lo.

### <a name="handling-errors"></a>Tratamento de erros

A notificação `AuthenticationFailed` é disparada quando a autenticação falha. Em seu método de retorno de chamada, você pode tratar os erros como desejar. No entanto, você deve adicionar uma verificação para o código de erro `AADB2C90118`. Durante a execução da política "Inscrever-se ou Entrar", o usuário tem a oportunidade de selecionar o link **Esqueceu sua senha?**. Nesse caso, o Azure AD B2C envia esse código de erro para o seu aplicativo indicando que seu aplicativo faça uma solicitação usando a política de redefinição de senha em vez disso.

```CSharp
/*
* Catch any failures received by the authentication middleware and handle appropriately
*/
private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    // Handle the error code that Azure AD B2C throws when trying to reset a password from the login page
    // because password reset is not supported by a "sign-up or sign-in policy"
    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```

### <a name="send-authentication-requests-to-azure-ad"></a>Enviar solicitações de autenticação ao AD do Azure

Seu aplicativo agora está configurado corretamente para se comunicar com o AD B2C do Azure usando o protocolo de autenticação OpenID Connect. O OWIN gerencia todos os detalhes da criação de mensagens de autenticação, validação de tokens do Azure AD B2C e manutenção da sessão do usuário. Tudo o que resta é iniciar o fluxo de cada usuário.

Quando um usuário escolhe **Inscrever-se/Entrar**, **Editar perfil** ou **Redefinir senha** no aplicativo Web, a ação associada é invocada em `Controllers\AccountController.cs`:

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign up or sign in
*/
public void SignUpSignIn()
{
    // Use the default policy to process the sign up / sign in flow
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge();
        return;
    }

    Response.Redirect("/");
}

/*
*  Called when requesting to edit a profile
*/
public void EditProfile()
{
    if (Request.IsAuthenticated)
    {
        // Let the middleware know you are trying to use the edit profile policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
        HttpContext.GetOwinContext().Set("Policy", Startup.EditProfilePolicyId);

        // Set the page to redirect to after editing the profile
        var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
        HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

        return;
    }

    Response.Redirect("/");

}

/*
*  Called when requesting to reset a password
*/
public void ResetPassword()
{
    // Let the middleware know you are trying to use the reset password policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
    HttpContext.GetOwinContext().Set("Policy", Startup.ResetPasswordPolicyId);

    // Set the page to redirect to after changing passwords
    var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
    HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

    return;
}
```

Você também pode usar OWIN para desconectar o usuário do aplicativo. Em `Controllers\AccountController.cs`, temos:

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign out
*/
public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

Além de chamar explicitamente uma política, você pode usar uma marca `[Authorize]` em seus controladores que executa uma política se o usuário não estiver conectado. Abra `Controllers\HomeController.cs` e adicione a marca `[Authorize]` ao controlador de declarações.  O OWIN seleciona a última política configurada quando a marca `[Authorize]` for atingida.

```CSharp
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

### <a name="display-user-information"></a>Exibir informações do usuário

Ao autenticar usuários com o OpenID Connect, o Azure AD B2C retorna um token de ID para o aplicativo que contém **declarações**. Esses são declarações sobre o usuário. Você pode usar as declarações para personalizar o aplicativo.

Abra o arquivo `Controllers\HomeController.cs` . Você pode acessar as declarações do usuário em seus controladores por meio do objeto principal de segurança `ClaimsPrincipal.Current` .

```CSharp
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Você pode acessar qualquer declaração de que seu aplicativo recebe da mesma maneira.  Confira na página **Declarações** uma lista de todas as declarações recebidas pelo aplicativo.
