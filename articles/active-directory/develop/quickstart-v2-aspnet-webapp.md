---
title: 'Início Rápido: Adicionar a entrada com a conta da Microsoft a um aplicativo Web ASP.NET | Azure'
titleSuffix: Microsoft identity platform
description: Neste guia de início rápido, aprenda a implementar as credenciais da Microsoft a um aplicativo Web ASP.NET usando o OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, contperf-fy21q1
ms.openlocfilehash: 87948ed04f7b50820d94993d4c4fbcf2dfd94b31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578678"
---
# <a name="quickstart-add-microsoft-identity-platform-sign-in-to-an-aspnet-web-app"></a>Início Rápido: Adicionar a entrada da plataforma de identidade da Microsoft a um aplicativo Web ASP.NET

Neste guia de início rápido, você baixará e executará um exemplo de código que demonstra como um aplicativo Web ASP.NET pode conectar usuários de qualquer organização do Azure AD (Azure Active Directory). 

> [!div renderon="docs"]
> O seguinte diagrama mostra como o aplicativo de exemplo funciona:
>
> ![O diagrama da interação entre o navegador da Web, o aplicativo Web e a plataforma de identidade da Microsoft no aplicativo de exemplo.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)
>
> ## <a name="prerequisites"></a>Pré-requisitos
>
> * Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
> * [.NET Framework 4.7.2+](https://dotnet.microsoft.com/download/visual-studio-sdks)
>
> ## <a name="register-and-download-the-app"></a>Registrar e baixar o aplicativo
> Você tem duas opções para começar a criar o seu aplicativo: configuração automática ou manual.
>
> ### <a name="automatic-configuration"></a>Configuração automática
> Se você quiser configurar automaticamente o seu aplicativo e baixar o exemplo de código, siga estas etapas:
>
> 1. Acesse a <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs" target="_blank">página do portal do Azure para o registro do aplicativo</a>.
> 1. Insira um nome para seu aplicativo e selecione **Registrar**.
> 1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo com apenas um clique.
>
> ### <a name="manual-configuration"></a>Configuração manual
> Se você quiser configurar manualmente o seu aplicativo e o exemplo de código, use os procedimentos a seguir.
>
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
>
> 1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar o aplicativo.
> 1. Pesquise **Azure Active Directory** e selecione-o.
> 1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
> 1. Em **Nome**, insira um nome para o seu aplicativo. Por exemplo, insira **ASPNET-Quickstart**. Os usuários do seu aplicativo verão esse nome e você pode alterá-lo mais tarde.
> 1. Adicione **https://localhost:44368/** em **URI de redirecionamento** e selecione **Registrar**.
> 1. Em **Gerenciar**, selecione **Autenticação**.
> 1. Na seção **Concessão implícita e fluxos híbridos**, selecione **Tokens de ID**.
> 1. Clique em **Salvar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Etapa 1: Configurar seu aplicativo no portal do Azure
> Para que o exemplo de código deste guia de início rápido funcione, insira **https://localhost:44368/** em **URI de redirecionamento**.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essa alteração para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-aspnet-webapp/green-check.png): o seu aplicativo já está configurado com esse atributo.

#### <a name="step-2-download-the-project"></a>Etapa 2: Baixe o projeto

> [!div renderon="docs"]
> [Baixar a solução do Visual Studio 2019](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Execute o projeto usando o Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Baixe o exemplo de código](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Etapa 3: seu aplicativo está configurado e pronto para ser executado
> Configuramos o seu projeto com os valores das propriedades do seu aplicativo.

> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>Etapa 3: Executar o projeto do Visual Studio

1. Extraia o arquivo. zip em uma pasta local próxima à pasta raiz. Por exemplo, extraia em *C:\Azure-Samples*.
   
   É recomendável extrair os arquivos em um diretório próximo à raiz da unidade para evitar erros causados por limitações de comprimento do caminho no Windows.
2. Abra a solução no Visual Studio (*AppModelv2-WebApp-OpenIDConnect-DotNet.sln*).
3. Dependendo da versão do Visual Studio, pode ser necessário clicar com o botão direito do mouse no projeto **AppModelv2-WebApp-OpenIDConnect-DotNet** e selecionar **Restaurar os pacotes do NuGet**.
4. Abra o Console do Gerenciador de Pacotes selecionando **Exibir** > **Outras Janelas** > **Console do Gerenciador de Pacotes**. Em seguida, execute `Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r`.

> [!div renderon="docs"]
> 5. Edite *Web.config* e substitua os parâmetros `ClientId`, `Tenant` e `redirectUri` por:
>    ```xml
>    <add key="ClientId" value="Enter_the_Application_Id_here" />
>    <add key="Tenant" value="Enter_the_Tenant_Info_Here" />
>    <add key="redirectUri" value="https://localhost:44368/" />
>    ```
>    Neste código:
>
>    - `Enter_the_Application_Id_here` é a ID do aplicativo (cliente) do registro do aplicativo que você criou anteriormente. Localize a ID do aplicativo (cliente) na página **Visão Geral** do aplicativo em **Registros de aplicativo** no portal do Azure.
>    - `Enter_the_Tenant_Info_Here` é uma das seguintes opções:
>      - Se o seu aplicativo dá suporte a **Somente minha organização**, substitua esse valor pela ID do diretório (locatário) ou pelo nome do locatário (por exemplo, `contoso.onmicrosoft.com`). Localize a ID do diretório (locatário) na página **Visão Geral** do aplicativo em **Registros de aplicativo** no portal do Azure.
>      - Se o aplicativo der suporte para **Contas em qualquer diretório organizacional**, substitua esse valor por `organizations`.
>      - Se o aplicativo der suporte a **Todos os usuários de contas Microsoft**, substitua esse valor por `common`.
>    - `redirectUri` é o **URI de redirecionamento** que você inseriu anteriormente em **Registros de aplicativo** no portal do Azure.
>

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>Mais informações

Esta seção apresenta uma visão geral do código necessário para a entrada de usuários. Essa visão geral pode ser útil para entender como o código funciona, quais são os argumentos principais e como adicionar credenciais a um aplicativo ASP.NET existente.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Como o exemplo funciona
>
> ![O diagrama da interação entre o navegador da Web, o aplicativo Web e a plataforma de identidade da Microsoft no aplicativo de exemplo.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>Pacotes do NuGet de middleware OWIN

Você pode configurar o pipeline de autenticação com a autenticação baseada em cookies usando o OpenID Connect no ASP.NET com pacotes de middleware OWIN. Você pode instalar esses pacotes executando os seguintes comandos no Console do Gerenciador de Pacotes no Visual Studio:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>Classe de inicialização do OWIN

O middleware OWIN usa uma *classe de inicialização* que é executada quando o processo de hospedagem é iniciado. Neste guia de início rápido, o arquivo *startup.cs* está na pasta raiz. O seguinte código mostra os parâmetros usados neste guia de início rápido:

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the client ID, authority, and redirect URI as obtained from Web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it's using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the code id_token, which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.CodeIdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organization, set ValidateIssuer to true and the 'tenant' setting in Web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use the ValidIssuers parameter
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to the OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Where  | Descrição |
> |---------|---------|
> | `ClientId`     | A ID do aplicativo registrado no portal do Azure. |
> | `Authority`    | O ponto de extremidade do STS (serviço de token de segurança) para o usuário se autenticar. Geralmente, ele é `https://login.microsoftonline.com/{tenant}/v2.0` para a nuvem pública. Nessa URL, *{tenant}* é o nome do seu locatário, da ID do locatário ou de `common` para uma referência ao ponto de extremidade comum. (O ponto de extremidade comum é usado para aplicativos multilocatários.) |
> | `RedirectUri`  | A URL para a qual os usuários são enviados após a autenticação na plataforma de identidade da Microsoft. |
> | `PostLogoutRedirectUri`     | A URL para a qual os usuários são enviados após saírem. |
> | `Scope`     | A lista de escopos que estão sendo solicitados, separados por espaços. |
> | `ResponseType`     | A solicitação da resposta da autenticação contém um código de autorização e um token de ID. |
> | `TokenValidationParameters`     | Uma lista de parâmetros para validação de token. Nesse caso, `ValidateIssuer` é definido como `false` para indicar que ele pode aceitar entradas de todos os tipos de conta corporativa ou de estudante ou pessoal. |
> | `Notifications`     | Uma lista de delegados que podem ser executados em mensagens `OpenIdConnect`. |


> [!NOTE]
> A configuração de `ValidateIssuer = false` é uma simplificação deste início rápido. Em aplicativos reais, valide o emissor. Consulte os exemplos para entender como fazer isso.

### <a name="authentication-challenge"></a>Desafio de autenticação

Você pode forçar um usuário a entrar solicitando um desafio de autenticação em seu controlador:

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> Solicitar um desafio de autenticação usando esse método é opcional. Normalmente, você o usaria quando quisesse que uma exibição fosse acessível de usuários autenticados e não autenticados. Como alternativa, você pode proteger os controladores usando o método descrito na próxima seção.

### <a name="attribute-for-protecting-a-controller-or-a-controller-actions"></a>Atributo para proteger um controlador ou ações do controlador

Você pode proteger um controlador ou ações do controlador usando o atributo `[Authorize]`. Esse atributo restringe o acesso ao controlador ou às ações permitindo que apenas usuários autenticados acessem as ações no controlador. Um desafio de autenticação ocorrerá automaticamente quando um usuário não autenticado tentar acessar uma das ações ou controladores decorados pelo atributo `[Authorize]`.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter um guia passo a passo completo sobre a criação de aplicativos e novos recursos, incluindo uma explicação completa deste guia de início rápido, experimente o tutorial do ASP.NET.

> [!div class="nextstepaction"]
> [Adicionar a entrada a um aplicativo Web ASP.NET](tutorial-v2-asp-webapp.md)
