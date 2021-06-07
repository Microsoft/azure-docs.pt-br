---
title: 'Início Rápido: Aplicativo Web ASP.NET Core que conecta usuários e chama o Microsoft Graph | Azure'
titleSuffix: Microsoft identity platform
description: Neste guia de início rápido, você descobrirá como um aplicativo aproveita o Microsoft.Identity.Web para implementar a entrada da Microsoft em um aplicativo Web ASP.NET Core usando o OpenID Connect e chama o Microsoft Graph
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/10/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: efa9465adc13b50e6ae12628d21347152c3fc2c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578712"
---
# <a name="quickstart-aspnet-core-web-app-that-signs-in-users-and-calls-microsoft-graph-on-their-behalf"></a>Início Rápido: Aplicativo Web ASP.NET Core que conecta usuários e chama o Microsoft Graph em nome deles

Neste guia de início rápido, você baixará e executará um exemplo de código que demonstra como um aplicativo Web ASP.NET Core pode conectar usuários de qualquer organização do Azure AD (Azure Active Directory) e chama o Microsoft Graph.  

Confira [Como o exemplo funciona](#how-the-sample-works) para ver uma ilustração.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Pré-requisitos
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) ou [Visual Studio Code](https://code.visualstudio.com/)
> * [SDK do .NET Core 3.1 ou posterior](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-quickstart-app"></a>Registrar e baixar o aplicativo de início rápido
> Você tem duas opções para iniciar o aplicativo de início rápido:
> * [Express] [Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código
>
> 1. Acesse a experiência de início rápido do <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetCoreWebAppQuickstartPage/sourceType/docs" target="_blank">portal do Azure – Registros de aplicativo</a>.
> 1. Insira um nome para seu aplicativo e selecione **Registrar**.
> 1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo com apenas um clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
> Para registrar seu aplicativo e adicionar as informações de registro do aplicativo à solução manualmente, siga estas etapas:
>
> 1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
> 1. Pesquise **Azure Active Directory** e selecione-o.
> 1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
> 1. Insira um **Nome** para seu aplicativo, por exemplo, `AspNetCoreWebAppCallsGraph-Quickstart`. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
> 1. Insira um **URI de Redirecionamento** de `https://localhost:44321/signin-oidc`.
> 1. Selecione **Registrar**.
> 1. Em **Gerenciar**, selecione **Autenticação**.
> 1. Insira uma **URL de logoff de front-channel** igual a `https://localhost:44321/signout-oidc`.
> 1. Selecione **Salvar**.
> 1. Em **Gerenciar**, selecione **Certificados e segredos** > **Novo segredo do cliente**.
> 1. Insira uma **Descrição**, por exemplo, `clientsecret1`.
> 1. Selecione **Em 1 ano** para a validade do segredo.
> 1. Selecione **Adicionar** e registre imediatamente o **Valor** do segredo para uso em uma etapa posterior. O valor secreto *nunca é exibido novamente* e não pode ser recuperado por outros meios. Registre-o em uma localização segura como faria com qualquer senha.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Etapa 1: Configurar seu aplicativo no portal do Azure
> Para que o exemplo de código deste guia de início rápido funcione, adicione um **URI de Redirecionamento** igual a `https://localhost:44321/signin-oidc` e uma **URL de logoff de front-channel** igual a `https://localhost:44321/signout-oidc` no registro do aplicativo.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essa alteração para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-aspnet-webapp/green-check.png) Seu aplicativo já está configurado com esses atributos.

#### <a name="step-2-download-the-aspnet-core-project"></a>Etapa 2: Baixar o projeto do ASP.NET Core

> [!div renderon="docs"]
> [Baixar a solução do ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Execute o projeto.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Baixe o exemplo de código](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Etapa 3: seu aplicativo está configurado e pronto para ser executado
> Configuramos seu projeto com os valores das propriedades do aplicativo e ele está pronto para ser executado.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>Etapa 3: Configurar seu projeto do ASP.NET Core
> 1. Extraia o arquivo .zip para uma pasta local próxima à raiz da unidade. Por exemplo, em *C:\Azure-Samples*.
> 1. Abra a solução no Visual Studio 2019.
> 1. Abra o arquivo *appsettings.json* e modifique o seguinte:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    "clientSecret": "Enter_the_Client_Secret_Here"
>    ```
>
>    - Substitua `Enter_the_Application_Id_here` pela **ID do aplicativo (cliente)** referente ao aplicativo registrado no portal do Azure. Você pode encontrar **ID do aplicativo (cliente)** na página **Visão geral** do aplicativo.
>    - Substitua `common` por um dos seguintes:
>       - Se o aplicativo der suporte a **Contas somente neste diretório organizacional**, substitua esse valor pela **ID do diretório (locatário)** (um GUID) ou pelo **nome do locatário** (por exemplo, `contoso.onmicrosoft.com`). Você pode encontrar a **ID do diretório (locatário)** na página **Visão geral** do aplicativo.
>       - Se seu aplicativo dá suporte a **Contas em qualquer diretório organizacional**, substitua esse valor por `organizations`
>       - Se o aplicativo der suporte a **Todos os usuários de contas Microsoft**, defina esse valor como `common`
>    - Substitua `Enter_the_Client_Secret_Here` pelo **Segredo do cliente** você criou e registrou em uma etapa anterior.
> 
> Para este guia de início rápido, não altere nenhum outro valor no arquivo *appsettings.json*.
>
> #### <a name="step-4-build-and-run-the-application"></a>Etapa 4: compile e execute o aplicativo
>
> Crie e execute o aplicativo no Visual Studio selecionando o menu **Depurar** > **Iniciar Depuração** ou pressionando a tecla `F5`.
>
> Suas credenciais serão solicitadas e então será solicitado que você consinta com as permissões que seu aplicativo exige. Selecione **Aceitar** na solicitação de consentimento.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-01-consent.png" alt-text="Caixa de diálogo de consentimento mostrando as permissões que o aplicativo está solicitando do > usuário":::
>
> Depois de fornecer consentimento para as permissões solicitadas, o aplicativo exibirá uma mensagem indicando que você fez logon com êxito usando suas credenciais do Azure Active Directory e você verá seu endereço de email na seção "Resultado da API" da página. Isso foi extraído com o Microsoft Graph.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-02-signed-in.png" alt-text="Navegador da Web exibindo o aplicativo Web em execução e o usuário conectado":::

## <a name="about-the-code"></a>Observações sobre o código

Esta seção fornece uma visão geral do código necessário para conectar usuários e chamar a API do Microsoft Graph em nome deles. Essa visão geral pode ser útil para entender o funcionamento do código funciona, os argumentos principais e caso você queira adicionar uma entrada a um aplicativo ASP.NET Core existente e chamar o Microsoft Graph. Ela usa o [Microsoft.Identity.Web](microsoft-identity-web.md), que é um wrapper do [MSAL.NET](msal-overview.md).

### <a name="how-the-sample-works"></a>Como o exemplo funciona
![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-aspnet-core-webapp-calls-graph/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Classe de inicialização

O middleware *Microsoft.AspNetCore.Authentication* usa a classe `Startup` executada quando o processo de hospedagem é inicializado:

```csharp
  // Get the scopes from the configuration (appsettings.json)
  var initialScopes = Configuration.GetValue<string>("DownstreamApi:Scopes")?.Split(' ');

  public void ConfigureServices(IServiceCollection services)
  {
      // Add sign-in with Microsoft
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))

            // Add the possibility of acquiring a token to call a protected web API
            .EnableTokenAcquisitionToCallDownstreamApi(initialScopes)

                // Enables controllers and pages to get GraphServiceClient by dependency injection
                // And use an in memory token cache
                .AddMicrosoftGraph(Configuration.GetSection("DownstreamApi"))
                .AddInMemoryTokenCaches();

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });

      // Enables a UI and controller for sign in and sign out.
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

O método `AddAuthentication()` configura o serviço para adicionar a autenticação baseada em cookies, que é usada em cenários do navegador, e para definir o desafio para OpenID Connect.

A linha que contém `.AddMicrosoftIdentityWebApp` adiciona a autenticação de plataforma de identidade da Microsoft ao seu aplicativo. Isso é fornecido pelo [Microsoft.Identity.Web](microsoft-identity-web.md). Em seguida, ele é configurado para entrar usando a plataforma de identidade da Microsoft com base nas informações da seção `AzureAD` do arquivo de configuração *appSettings.json*:

| chave *appsettings.json* | Descrição                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | **ID do aplicativo (cliente)** referente ao aplicativo registrado no portal do Azure.                                                                                       |
| `Instance`             | Ponto de extremidade do STS (serviço de token de segurança) para o usuário se autenticar. Esse valor geralmente é `https://login.microsoftonline.com/`, indicando a nuvem pública do Azure. |
| `TenantId`             | Nome do seu locatário ou sua ID de locatário (um GUID) ou *comum* para conectar usuários que tenham contas corporativas ou de estudante ou contas pessoais Microsoft.                             |

O método `EnableTokenAcquisitionToCallDownstreamApi` permite que o seu aplicativo adquira um token para chamar APIs Web protegidas. `AddMicrosoftGraph` permite que os controladores ou o Razor Pages beneficiem-se diretamente do `GraphServiceClient` (pela injeção de dependência), e os métodos `AddInMemoryTokenCaches` permitem que o seu aplicativo se beneficie de um cache de token.

O método `Configure()` contém dois métodos importantes, `app.UseAuthentication()` e `app.UseAuthorization()`, que habilitam sua funcionalidade nomeada. Além disso, no método `Configure()`, você precisará registrar as rotas da Web de Identidade da Microsoft com, pelo menos, uma chamada a `endpoints.MapControllerRoute()` ou uma chamada a `endpoints.MapControllers()`.

```csharp
app.UseAuthentication();
app.UseAuthorization();

app.UseEndpoints(endpoints =>
{

    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    endpoints.MapRazorPages();
});

// endpoints.MapControllers(); // REQUIRED if MapControllerRoute() isn't called.
```

### <a name="protect-a-controller-or-a-controllers-method"></a>Proteger um controlador ou um método do controlador

Você pode proteger um controlador ou os respectivos métodos aplicando o atributo `[Authorize]` à classe do controlador ou a um ou mais dos métodos dele. Esse atributo `[Authorize]` restringe o acesso permitindo apenas usuários autenticados. Se o usuário ainda não estiver autenticado, um desafio de autenticação poderá ser iniciado para acessar o controlador. Neste guia de início rápido, os escopos são lidos com base no arquivo de configuração:

```CSharp
[AuthorizeForScopes(ScopeKeySection = "DownstreamApi:Scopes")]
public async Task<IActionResult> Index()
{
    var user = await _graphServiceClient.Me.Request().GetAsync();
    ViewData["ApiResult"] = user.DisplayName;

    return View();
}
 ```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

O repositório GitHub que contém o exemplo de código ASP.NET Core referenciado neste guia de início rápido inclui instruções e mais exemplos de código que mostram como:

- Adicionar autenticação a um novo aplicativo Web ASP.NET Core
- Chamar o Microsoft Graph, outras APIs da Microsoft ou suas próprias APIs Web
- Adicionar autorização
- Conectar usuários em nuvens nacionais ou com identidades sociais

> [!div class="nextstepaction"]
> [Tutoriais do aplicativo Web ASP.NET Core no GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
