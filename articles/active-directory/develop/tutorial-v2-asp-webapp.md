---
title: 'Tutorial: Criar um aplicativo Web ASP.NET que usa a plataforma de identidade da Microsoft para autenticação | Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, você criará um aplicativo Web ASP.NET que usa a plataforma de identidade da Microsoft e o middleware OWIN para habilitar o logon do usuário.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40
ms.openlocfilehash: 17cff66d3f8c816099bcc61322a1d331bfe58872
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226297"
---
# <a name="tutorial-add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>Tutorial: Adicionar a entrada com a conta da Microsoft a um aplicativo Web ASP.NET

Neste tutorial, você criará um aplicativo Web ASP.NET MVC que conecta usuários usando o middleware OWIN (Open Web Interface para .NET) e a plataforma de identidade da Microsoft.

Quando você concluir este guia, seu aplicativo poderá aceitar entradas de contas pessoais de sites como outlook.com e live.com. Além disso, as contas corporativas e de estudante de qualquer empresa ou organização integradas à plataforma de identidade da Microsoft poderão entrar no aplicativo.

Neste tutorial:

> [!div class="checklist"]
> * Criar um projeto de *aplicativo Web ASP.NET* no Visual Studio
> * Adicionar os componentes do middleware OWIN (Interface da Web Aberta para .NET)
> * Adicionar código para entrada e saída do usuário
> * Registrar o aplicativo no portal do Azure
> * Testar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) com a carga de trabalho do **ASP.NET e desenvolvimento da Web** instalada

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como o aplicativo de exemplo gerado por este guia funciona

![Mostra como funciona o aplicativo de exemplo gerado por este tutorial](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

O aplicativo de exemplo que você criará baseia-se no cenário em que você usa o navegador para acessar um site ASP.NET que solicita a um usuário que faça a autenticação por meio de um botão Entrar. Nesse cenário, a maior parte do trabalho de renderização da página da Web ocorre no lado do servidor.

## <a name="libraries"></a>Bibliotecas

Este guia usa as seguintes bibliotecas:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware que permite que um aplicativo use OpenIdConnect para autenticação|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware que permite que um aplicativo mantenha a sessão de usuário usando cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Middleware que permite que aplicativos baseados em OWIN sejam executados no IIS (Serviços de Informações da Internet) usando o pipeline de solicitação do ASP.NET|

## <a name="set-up-your-project"></a>Configurar o seu projeto

Esta seção descreve como instalar e configurar o pipeline de autenticação por meio do middleware OWIN em um projeto ASP.NET usando o OpenID Connect.

> Prefere baixar este projeto do Visual Studio de exemplo? [Baixe um projeto](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) e vá para [Registrar seu aplicativo](#register-your-application) para configurar o exemplo de código antes de executá-lo.

### <a name="create-your-aspnet-project"></a>Criar seu projeto do ASP.NET

1. No Visual Studio: Acesse **Arquivo** > **Novo** > **Projeto**.
2. Em **Visual C#\Web**, selecione **Aplicativo Web ASP.NET (.NET Framework)** .
3. Dê um nome ao aplicativo e selecione **OK**.
4. Selecione **Vazio** e, em seguida, marque a caixa de seleção para adicionar referências do **MVC**.

## <a name="add-authentication-components"></a>Adicionar componentes de autenticação

1. No Visual Studio: Acesse **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.
2. Adicione *Pacotes NuGet do middleware OWIN* digitando o seguinte na janela do Console do Gerenciador de Pacotes:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

### <a name="about-these-libraries"></a>Sobre estas bibliotecas
Estas bibliotecas permitem o SSO (logon único) usando o OpenID Connect por meio da autenticação baseada em cookie. Depois que a autenticação for concluída e o token que representa o usuário for enviado ao aplicativo, o middleware OWIN criará um cookie de sessão. Em seguida, o navegador usará esse cookie nas próximas solicitações, de modo que o usuário não precise digitar a senha novamente e nenhuma verificação adicional será necessária.

## <a name="configure-the-authentication-pipeline"></a>Configurar o pipeline de autenticação

As etapas a seguir são usadas para criar uma classe de Inicialização do middleware OWIN para configurar a autenticação do OpenID Connect. Essa classe é executada automaticamente quando o processo do IIS é iniciado.

> [!TIP]
> Se o projeto não tiver um arquivo `Startup.cs` na pasta raiz:
> 1. Clique com o botão direito do mouse na pasta raiz do projeto e, em seguida, selecione **Adicionar** > **Novo Item** > **Classe de Inicialização OWIN**.<br/>
> 2. Nomeie-o **Startup.cs**.
>
>> Verifique se a classe selecionada é uma classe de Inicialização OWIN e não uma classe C# padrão. Confirme isso verificando se [assembly: OwinStartup(typeof({NameSpace}.Startup))] é exibido acima do namespace.

1. Adicione as referências *OWIN* e *Microsoft.IdentityModel* a Startup.cs:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Substitua a classe de Inicialização pelo seguinte código:

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Microsoft identity platform.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed of the Microsoft identity platform and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect
        /// </summary>
        /// <param name="app"></param>
        public void Configuration(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
                new OpenIdConnectAuthenticationOptions
                {
                    // Sets the ClientId, authority, RedirectUri as obtained from web.config
                    ClientId = clientId,
                    Authority = authority,
                    RedirectUri = redirectUri,
                    // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                    PostLogoutRedirectUri = redirectUri,
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false // This is a simplification
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

> [!NOTE]
> A configuração de `ValidateIssuer = false` é uma simplificação deste início rápido. Em aplicativos reais, você precisará validar o emissor.
> Confira as amostras para saber como fazer isso.

### <a name="more-information"></a>Mais informações

Os parâmetros fornecidos em *OpenIDConnectAuthenticationOptions* servem como coordenadas para seu aplicativo se comunicar com a plataforma de identidade da Microsoft. Como o middleware do OpenID Connect usa cookies em segundo plano, você também precisará configurar a autenticação de cookie, como mostra o código anterior. O valor *ValidateIssuer* instrui OpenIdConnect a não restringir o acesso a uma organização específica.

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Adicionar um controlador para manipular solicitações de entrada e saída

Para criar um controlador para expor métodos de entrada e saída, siga estas etapas:

1.  Clique com o botão direito do mouse na pasta **Controladores** e selecione **Adicionar** > **Controlador**.
2.  Selecione **Controlador MVC (versão do .NET) – Vazio**.
3.  Selecione **Adicionar**.
4.  Nomeie-o **HomeController** e, em seguida, selecione **Adicionar**.
5.  Adicione referências OWIN à classe:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. Adicione os dois seguintes métodos para manipular a entrada e a saída do controlador iniciando um desafio de autenticação:

    ```csharp
    /// <summary>
    /// Send an OpenID Connect sign-in request.
    /// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
    /// </summary>
    public void SignIn()
    {
        if (!Request.IsAuthenticated)
        {
            HttpContext.GetOwinContext().Authentication.Challenge(
                new AuthenticationProperties{ RedirectUri = "/" },
                OpenIdConnectAuthenticationDefaults.AuthenticationType);
        }
    }

    /// <summary>
    /// Send an OpenID Connect sign-out request.
    /// </summary>
    public void SignOut()
    {
        HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
    }
    ```

## <a name="create-the-apps-home-page-for-user-sign-in"></a>Criar a home page do aplicativo para entrada do usuário

No Visual Studio, crie uma exibição para adicionar o botão Entrar e para exibir informações de usuário após a autenticação:

1.  Clique com o botão direito do mouse na pasta **Exibições\Início** e selecione **Adicionar exibição**.
2.  Nomeie a nova exibição **Índice**.
3.  Adicione o seguinte HTML, que inclui o botão de conexão, ao arquivo:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Guide</title>
    </head>
    <body>
    @if (!Request.IsAuthenticated)
    {
        <!-- If the user is not authenticated, display the sign-in button -->
        <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
            <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
            <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
            <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
            <rect x="150" y="129" width="122" height="122" fill="#F35325" />
            <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
            <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
            <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
            <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
            </svg>
        </a>
    }
    else
    {
        <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
        <br /><br />
        @Html.ActionLink("See Your Claims", "Index", "Claims")
        <br /><br />
        @Html.ActionLink("Sign out", "SignOut", "Home")
    }
    @if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
    {
        <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
    }
    </body>
    </html>
    ```

### <a name="more-information"></a>Mais informações
Esta página adiciona um botão de conexão no formato SVG com uma tela de fundo preta:<br/>![Botão Entrar com a conta da Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Para obter mais botões de entrada, acesse [Diretrizes de identidade visual](./howto-add-branding-in-azure-ad-apps.md "Diretrizes de identidade Visual").

## <a name="add-a-controller-to-display-users-claims"></a>Adicionar um controlador para exibir as declarações do usuário
Esse controlador demonstra os usos do atributo `[Authorize]` para proteger um controlador. Esse atributo restringe o acesso ao controlador permitindo apenas usuários autenticados. O seguinte código usa o atributo para exibir as declarações de usuário que foram recuperadas como parte da entrada:

1.  Clique com o botão direito do mouse na pasta **Controladores** e, em seguida, selecione **Adicionar** > **Controlador**.
2.  Selecione **Controlador MVC {versão} – Vazio**.
3.  Selecione **Adicionar**.
4.  Dê a ele o nome **ClaimsController**.
5.  Substitua o código da classe do controlador pelo código a seguir. Isso adiciona o atributo `[Authorize]` à classe:

    ```csharp
    [Authorize]
    public class ClaimsController : Controller
    {
        /// <summary>
        /// Add user's claims to viewbag
        /// </summary>
        /// <returns></returns>
        public ActionResult Index()
        {
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;

            //You get the user's first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;

            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;

            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;

            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;

            return View();
        }
    }
    ```

### <a name="more-information"></a>Mais informações
Devido ao uso do atributo `[Authorize]`, todos os métodos desse controlador podem ser executados apenas se o usuário está autenticado. Se o usuário não estiver autenticado e tentar acessar o controlador, o OWIN iniciará um desafio de autenticação e forçará o usuário a se autenticar. O código anterior examina a lista de declarações em busca de atributos de usuário específicos incluídos no token de ID do usuário. Esses atributos incluem o nome completo do usuário e o nome de usuário, bem como a entidade de identificador de usuário global. Ele também contém a *ID de Locatário*, que representa a ID da organização do usuário.

## <a name="create-a-view-to-display-the-users-claims"></a>Criar uma exibição para exibir as declarações do usuário

No Visual Studio, crie uma nova exibição para exibir as declarações do usuário em uma página da Web:

1.  Clique com o botão direto do mouse na pasta **Exibições\Declarações** e, em seguida, selecione **Adicionar Exibição**.
2.  Nomeie a nova exibição **Índice**.
3.  Adicione o seguinte HTML ao arquivo:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Sample</title>
        <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
    </head>
    <body style="padding:50px">
        <h3>Main Claims:</h3>
        <table class="table table-striped table-bordered table-hover">
            <tr><td>Name</td><td>@ViewBag.Name</td></tr>
            <tr><td>Username</td><td>@ViewBag.Username</td></tr>
            <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
            <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
        </table>
        <br />
        <h3>All Claims:</h3>
        <table class="table table-striped table-bordered table-hover table-condensed">
        @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
        {
            <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
        }
        </table>
        <br />
        <br />
        @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
    </body>
    </html>
    ```

## <a name="register-your-application"></a>Registre seu aplicativo

Para registrar seu aplicativo e adicionar suas informações de registro de aplicativo à sua solução, você tem duas opções:

### <a name="option-1-express-mode"></a>Opção 1: Modo Expresso

Para registrar seu aplicativo rapidamente, siga estas etapas:

1. Acesse a experiência de início rápido do <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs" target="_blank">Portal do Azure – Registros de aplicativo<span class="docon docon-navigate-external x-hidden-focus"></span></a>.  
1. Insira um nome para seu aplicativo e selecione **Registrar**.
1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo com um único clique.

### <a name="option-2-advanced-mode"></a>Opção 2: Modo Avançado

Para registrar seu aplicativo e adicionar as informações de registro do aplicativo à solução manualmente, siga estas etapas:

1. Abra o Visual Studio e, em seguida:
   1. no Gerenciador de Soluções, selecione o projeto para exibir a janela Propriedades (se uma janela Propriedades não for exibida, pressione F4).
   1. Altere habilitado para SSL `True`.
   1. Clique com o botão direito do mouse no projeto no Visual Studio, selecione **Propriedades** e, em seguida, selecione a guia **Web**. Na seção **Servidores**, altere a configuração de **URL do projeto** para **URL do SSL**.
   1. Copie a URL do SSL. Você adicionará essa URL à lista de URIs de Redirecionamento na lista de URIs de Redirecionamento do portal de Registro na próxima etapa.<br/><br/>![Propriedades do projeto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
   
1. Entre no <a href="https://portal.azure.com/" target="_blank">Portal do Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
1. Pesquise **Azure Active Directory** e selecione-o.
1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
1. Insira um **Nome** para seu aplicativo, por exemplo, `ASPNET-Tutorial`. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
1. Adicione a URL do SSL copiada do Visual Studio na etapa 1 (por exemplo, `https://localhost:44368/`) no **URI de Redirecionamento**.
1. Selecione **Registrar**.
1. Em **Gerenciar**, selecione **Autenticação**.
1. Na seção **Concessão Implícita e fluxos híbridos**, selecione **Tokens de ID** e, em seguida, **Salvar**.
1. Adicione o seguinte ao arquivo web.config, localizado na pasta raiz, na seção `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Substitua `ClientId` pelo ID do aplicativo que você acabou de registrar.
1. Substitua `redirectUri` com a URL do SSL de seu projeto.

## <a name="test-your-code"></a>Testar seu código

Para testar o aplicativo no Visual Studio, pressione F5 para executar o projeto. O navegador será aberto na localização http://<span></span>localhost:{port}, e você verá o botão **Entrar com a conta da Microsoft**. Selecione o botão para iniciar o processo de entrada.

Quando estiver pronto para executar o teste, use uma conta do Azure AD (conta corporativa ou de estudante) ou uma conta pessoal Microsoft (<span>live.</span>com ou <span>outlook.</span>com) para entrar.

![Botão Entrar com a conta da Microsoft mostrado na página de logon no navegador](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Entre na sua conta da Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Permissões e consentimento na plataforma de identidade da Microsoft
Os aplicativos que se integram à plataforma de identidade da Microsoft seguem um modelo de autorização que dá aos usuários e administradores controle sobre como os dados podem ser acessados. Depois que um usuário se autenticar na plataforma de identidade da Microsoft para acessar esse aplicativo, ele deverá fornecer o consentimento às permissões solicitadas pelo aplicativo ("Exibir o seu perfil básico" e "Manter o acesso aos dados aos quais você permitiu acesso"). Depois de aceitar essas permissões, o usuário será direcionado aos resultados do aplicativo. No entanto, o usuário poderá receber uma página **Consentimento do administrador necessário** se ocorrer uma das seguintes ações:

- O desenvolvedor de aplicativos adiciona permissões extras que exigem o **consentimento do administrador**.
- Ou o locatário está configurado (em **Aplicativos Empresariais – > Configurações de Usuário**), em que os usuários não podem fornecer consentimento aos aplicativos que acessam os dados da empresa em seu nome.

Para obter mais informações, veja [Permissões e consentimento na plataforma de identidade da Microsoft](./v2-permissions-and-consent.md).

### <a name="view-application-results"></a>Veja os resultados de aplicativo

Depois de entrar, o usuário será redirecionado para a home page do seu site. A home page é a URL HTTPS especificada nas informações de registro de aplicativo no Portal de Registro de Aplicativo da Microsoft. A home page inclui uma mensagem de boas-vindas *"Olá, \<user>"* , um link para sair e um link para ver as declarações do usuário. O link para as declarações do usuário é conectado ao controlador de Declarações criado anteriormente.

### <a name="view-the-users-claims"></a>Exibir as declarações do usuário

Para exibir as declarações do usuário, selecione o link para navegar até a exibição do controlador disponível somente para usuários autenticados.

#### <a name="view-the-claims-results"></a>Exibir os resultados de declarações

Depois de navegar para a exibição do controlador, você deverá visualizar uma tabela que contém as propriedades básicas para o usuário:

|Propriedade |Valor |Descrição |
|---|---|---|
|**Nome** |Nome completo do usuário | O nome e o sobrenome do usuário
|**Nome de usuário** |usuário<span>@domain.com</span> | O nome de usuário que é usado para identificar o usuário|
|**Assunto** |Assunto |Uma cadeia de caracteres que identifica de maneira exclusiva o usuário na Web|
|**ID do locatário** |Guid | Um **GUID** que representa de maneira exclusiva a organização do Azure AD do usuário|

Além disso, você deverá ver uma tabela de todas as declarações que estão na solicitação de autenticação. Para obter mais informações, confira a [lista de declarações incluídas em um token de ID](./id-tokens.md).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Testar o acesso a um método que tenha um atributo Autorizar (opcional)

Para testar o acesso como um usuário anônimo a um controlador protegido pelo atributo `Authorize`, siga estas etapas:

1. Selecione o link para desconectar o usuário e conclua o processo de saída.
2. No navegador, digite http://<span></span>localhost:{port}/claims para acessar o controlador que está protegido pelo atributo `Authorize`.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Resultados esperados após o acesso a um controlador protegido

Você será solicitado a autenticar para usar a exibição do controlador protegido.

## <a name="advanced-options"></a>Opções Avançadas

### <a name="protect-your-entire-website"></a>Proteja todo o seu site

Para proteger todo o site, no arquivo **Global.asax**, adicione o atributo `AuthorizeAttribute` ao filtro `GlobalFilters` no método `Application_Start`:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```

### <a name="restrict-who-can-sign-in-to-your-application"></a>Restringir quem pode se conectar ao seu aplicativo

Por padrão, quando você compila o aplicativo compilado por este guia, o aplicativo aceitará entradas de contas pessoais (incluindo outlook.com, live.com e outros), bem como contas corporativas ou de estudante de qualquer empresa ou organização que foi integrada à plataforma de identidade da Microsoft. Essa é uma opção recomendada para aplicativos SaaS.

Para restringir o acesso de entrada do usuário para o aplicativo, há várias opções disponíveis.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Opção 1: Restrinja usuários da instância do Active Directory de uma única organização entre no seu aplicativo (único locatário)

Essa opção é frequentemente usada para *aplicativos de linha de negócios*: Se desejar que seu aplicativo aceite entradas apenas de contas pertencentes a uma instância específica do Azure AD (incluindo *contas de convidados* dessa instância), siga estas etapas:

1. No arquivo web.config, altere o valor para o parâmetro `Tenant` de `Common` para o nome do locatário da organização, como `contoso.onmicrosoft.com`.
2. Na [classe de inicialização OWIN](#configure-the-authentication-pipeline), defina o argumento `ValidateIssuer` como `true`.

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>Opção 2: Restringir o acesso aos usuários em uma lista específica de organizações

É possível restringir acesso de entrada apenas às contas de usuários que estão em uma organização do Azure AD que esteja na lista de organizações permitidas:
1. Na [classe de inicialização OWIN](#configure-the-authentication-pipeline), defina o argumento `ValidateIssuer` como `true`.
2. Defina o valor do parâmetro `ValidIssuers` para a lista de organizações permitidas.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Opção 3: Usar um método personalizado para validar emissores

É possível implementar um método personalizado para validar emissores usando o parâmetro **IssuerValidator**. Para obter mais informações sobre como usar esse parâmetro, confira [classe TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

Saiba como chamar APIs Web protegidas em aplicativos Web com a plataforma de identidade da Microsoft:

> [!div class="nextstepaction"]
> [Aplicativos Web chamando APIs Web](scenario-web-app-sign-user-overview.md)
