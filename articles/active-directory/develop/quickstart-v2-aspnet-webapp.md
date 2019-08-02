---
title: Início rápido do servidor Web ASP.NET na plataforma de identidade da Microsoft | Azure
description: Saiba como implementar a Entrada da Microsoft em um aplicativo Web do ASP.NET usando o OpenID Connect.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: edda3b6d669bf78fc4febbe993bc30c244abeeeb
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334169"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Início Rápido: Adicionar a opção Entrar com uma Conta da Microsoft a um aplicativo Web ASP.NET

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Neste início rápido, você aprenderá como um aplicativo Web ASP.NET pode entrar em contas pessoais (hotmail.com, outlook.com, outros) e contas corporativas e de estudante de qualquer instância do Azure AD (Azure Active Directory).

![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrar e baixar o aplicativo de início rápido
> Você tem duas opções para iniciar o aplicativo de início rápido:
> * [Express] [Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código
>
> 1. Acesse o novo painel do [portal do Azure – Registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs).
> 1. Insira um nome para o aplicativo e clique em **Registrar**.
> 1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo com apenas um clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
> Para registrar seu aplicativo e adicionar as informações de registro do aplicativo à solução manualmente, siga estas etapas:
>
> 1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
> 1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina sua sessão do portal para o locatário desejado do Azure AD.
> 1. Navegue até a página [Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) da plataforma de identidade da Microsoft para desenvolvedores.
> 1. Selecione **Novo registro**.
> 1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo:
>      - Na seção **Nome**, insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo, por exemplo, `ASPNET-Quickstart`.
>      - Adicione `https://localhost:44368/` na **URI de redirecionamento** e clique em **Registrar**.
Selecione o menu **Autenticação** menu, defina **Tokens de ID** em **Concessão Implícita**e selecione **Salvar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Etapa 1: Configurar seu aplicativo no portal do Azure
> Para que o exemplo de código deste início rápido funcione, você precisa adicionar uma URL de resposta como `https://localhost:44368/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essa alteração para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-aspnet-webapp/green-check.png) Seu aplicativo já está configurado com esse atributo

#### <a name="step-2-download-your-project"></a>Etapa 2: Baixar o seu projeto

[Baixar a solução do Visual Studio 2019](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Etapa 3: Configurar o projeto do Visual Studio

1. Extraia o arquivo zip para uma pasta local mais próxima da pasta raiz, por exemplo, **C:\Azure-Samples**
1. Abra a solução no Visual Studio (AppModelv2-WebApp-OpenIDConnect-DotNet.sln)
1. Dependendo da versão do Visual Studio, pode ser necessário clicar com o botão direito do mouse no projeto `AppModelv2-WebApp-OpenIDConnect-DotNet` e **Restaurar os pacotes do NuGet**
1. Abra o Console do Gerenciador de Pacotes (Exibir -> Outras Janelas -> Console do Gerenciador de Pacotes) e execute `Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r`
1. Edite **Web.config** e substitua os parâmetros `ClientId` e `Tenant` por:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="Tenant" value="Enter_the_Tenant_Info_Here" />
    ```
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Este início rápido dá suporte a Enter_the_Supported_Account_Info_Here. 

> [!div renderon="docs"]
> Em que:
> - `Enter_the_Application_Id_here`: é a ID do aplicativo que você registrou.
> - `Enter_the_Tenant_Info_Here`: é uma das opções abaixo:
>   - Se seu aplicativo dá suporte a **Somente minha organização**, substitua esse valor pela **ID do Locatário** ou pelo **Nome do locatário** (por exemplo, contoso.microsoft.com)
>   - Se seu aplicativo dá suporte a **Contas em qualquer diretório organizacional**, substitua esse valor por `organizations`
>   - Se seu aplicativo dá suporte a **Todos os usuários de contas da Microsoft**, substitua esse valor por `common`
>
> > [!TIP]
> > Para encontrar os valores de *ID do aplicativo*, *ID de diretório (locatário)* e *Tipos de conta com suporte*, vá para a página **Visão Geral**

## <a name="more-information"></a>Mais informações

Esta seção apresenta uma visão geral do código necessário para a entrada de usuários. Esta visão geral pode ser útil para entender como o código funciona, os argumentos principais e também caso você queira adicionar entrada a um aplicativo ASP.NET existente.

### <a name="owin-middleware-nuget-packages"></a>Pacotes do NuGet de middleware OWIN

Você pode configurar o pipeline de autenticação com a autenticação baseada em cookies usando o OpenID Connect no ASP.NET com pacotes de Middleware OWIN. Você pode instalar esses pacotes executando os seguintes comandos no **Console do Gerenciador de Pacotes** do Visual Studio:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb  
```

### <a name="owin-startup-class"></a>Classe de Inicialização OWIN

O middleware OWIN usa uma *classe de inicialização* que é executada quando o processo de hospedagem é inicializado. Neste início rápido, o arquivo *startup.cs* está localizado na pasta raiz. O código a seguir mostra o parâmetro usado por este início rápido:

```csharp
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
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Where  |  |
> |---------|---------|
> | `ClientId`     | ID do aplicativo referente ao aplicativo registrado no portal do Azure |
> | `Authority`    | O ponto de extremidade do STS para o usuário autenticar. Geralmente <https://login.microsoftonline.com/{tenant}/v2.0> para a nuvem pública, em que {tenant} é o nome do seu locatário, sua Id do locatário ou *common* para uma referência ao ponto de extremidade comum (usado para aplicativos multilocatário) |
> | `RedirectUri`  | URL à qual os usuários são enviados após a autenticação com relação ao ponto de extremidade da plataforma de identidade da Microsoft |
> | `PostLogoutRedirectUri`     | URL à qual os usuários são enviados após saírem |
> | `Scope`     | A lista de escopos que estão sendo solicitados, separados por espaços |
> | `ResponseType`     | Solicitação de que a resposta da autenticação contenha um token de ID |
> | `TokenValidationParameters`     | Uma lista de parâmetros para validação de token. Nesse caso, `ValidateIssuer` é definido como `false` para indicar que ele pode aceitar entradas de quaisquer tipos de conta corporativa ou de estudante ou pessoal |
> | `Notifications`     | Uma lista de delegados que podem ser executados em diferentes mensagens *OpenIdConnect* |


> [!NOTE]
> A configuração de `ValidateIssuer = false` é uma simplificação para este início rápido. Em aplicativos reais, você precisa validar o emissor.
> Consulte os exemplos para entender como fazer isso.

### <a name="initiate-an-authentication-challenge"></a>Iniciar um desafio de autenticação

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
> Solicitar um desafio de autenticação usando o método acima é opcional e normalmente é usado quando você deseja que uma exibição fique acessível para usuários autenticados e não autenticados. Como alternativa, você pode proteger os controladores usando o método descrito na próxima seção.

### <a name="protect-a-controller-or-a-controllers-method"></a>Proteger um controlador ou um método do controlador

Você pode proteger um controlador ou ações do controlador usando o atributo `[Authorize]`. Esse atributo restringe o acesso ao controlador ou ações permitindo que somente usuários autenticados possam acessar as ações no controlador, o que significa que o desafio de autenticação ocorrerá automaticamente quando um usuário *não autenticado* tentar acessar uma das ações ou controlador decoradas pelo atributo `[Authorize]`.

## <a name="next-steps"></a>Próximas etapas

Experimente o tutorial do ASP.NET para um guia passo a passo completo sobre a criação de aplicativos e novos recursos, incluindo uma explicação completa deste início rápido.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Conheça as etapas para criar o aplicativo usado neste início rápido

> [!div class="nextstepaction"]
> [Tutorial de entrada](./tutorial-v2-asp-webapp.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Ajude-nos a melhorar a plataforma de identidade da Microsoft. Deixe sua opinião respondendo a uma breve pesquisa de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa da plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)