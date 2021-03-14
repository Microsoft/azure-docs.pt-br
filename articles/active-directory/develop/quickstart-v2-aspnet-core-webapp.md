---
title: 'Início Rápido: Adicionar a opção Entrar com a conta da Microsoft a um aplicativo Web ASP.NET Core | Azure'
titleSuffix: Microsoft identity platform
description: Neste guia de início rápido, você aprenderá como um aplicativo implementa a opção Entrar com a conta da Microsoft em um aplicativo Web ASP.NET Core usando o OpenID Connect
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/11/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 38130ef666de5e77ddb277d67841aa5fcfef08df
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102590"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Início Rápido: Adicionar entrada com a Microsoft para um aplicativo Web do ASP.NET Core

Neste guia de início rápido, você baixará e executará um exemplo de código que demonstra como um aplicativo Web ASP.NET Core pode conectar usuários de qualquer organização do Azure AD (Azure Active Directory).  

Confira [Como o exemplo funciona](#how-the-sample-works) para ver uma ilustração.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Pré-requisitos
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) ou [Visual Studio Code](https://code.visualstudio.com/)
> * [SDK do .NET Core 3.1 ou posterior](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrar e baixar o aplicativo de início rápido
> Você tem duas opções para iniciar o aplicativo de início rápido:
> * [Express] [Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código
>
> 1. Acesse a experiência de início rápido do <a href="https://aka.ms/aspnetcore2-1-aad-quickstart-v2/" target="_blank">portal do Azure – Registros de aplicativo</a>.
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
> 1. Insira um **Nome** para seu aplicativo, por exemplo, `AspNetCore-Quickstart`. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
> 1. Insira um **URI de Redirecionamento** de `https://localhost:44321/`.
> 1. Selecione **Registrar**.
> 1. Em **Gerenciar**, selecione **Autenticação**.
> 1. Em **URIs de Redirecionamento**, selecione **Adicionar URI** e insira `https://localhost:44321/signin-oidc`.
> 1. Insira uma **URL de logoff de front-channel** igual a `https://localhost:44321/signout-oidc`.
> 1. Em **Concessão implícita e fluxos híbridos**, selecione **Tokens de ID**.
> 1. Clique em **Salvar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Etapa 1: Configurar seu aplicativo no portal do Azure
> Para que o exemplo de código deste guia de início rápido funcione, adicione um **URI de Redirecionamento** igual a `https://localhost:44321/` e `https://localhost:44321/signin-oidc` e uma **URL de logoff de front-channel** igual a `https://localhost:44321/signout-oidc`. Os tokens de ID de solicitação serão emitidos pelo ponto de extremidade da autorização.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essa alteração para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-aspnet-webapp/green-check.png) Seu aplicativo já está configurado com esses atributos.

#### <a name="step-2-download-your-aspnet-core-project"></a>Etapa 2: Baixar o projeto do ASP.NET Core

> [!div renderon="docs"]
> [Baixar a solução do ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Execute o projeto.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Baixe o exemplo de código](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

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
>    ```
>
>    - Substitua `Enter_the_Application_Id_here` pela **ID do aplicativo (cliente)** referente ao aplicativo registrado no portal do Azure. Você pode encontrar **ID do aplicativo (cliente)** na página **Visão geral** do aplicativo.
>    - Substitua `common` por um dos seguintes:
>       - Se o aplicativo der suporte a **Contas somente neste diretório organizacional**, substitua esse valor pela **ID do diretório (locatário)** (um GUID) ou pelo **nome do locatário** (por exemplo, `contoso.onmicrosoft.com`). Você pode encontrar a **ID do diretório (locatário)** na página **Visão geral** do aplicativo.
>       - Se seu aplicativo dá suporte a **Contas em qualquer diretório organizacional**, substitua esse valor por `organizations`
>       - Se o aplicativo der suporte a **Todos os usuários de contas Microsoft**, defina esse valor como `common`
>
> Para este guia de início rápido, não altere nenhum outro valor no arquivo *appsettings.json*.
>
> #### <a name="step-4-build-and-run-the-application"></a>Etapa 4: compile e execute o aplicativo
>
> Crie e execute o aplicativo no Visual Studio selecionando o menu **Depurar** > **Iniciar Depuração** ou pressionando a tecla `F5`.
>
> Suas credenciais serão solicitadas e então será solicitado que você consinta com as permissões que seu aplicativo exige. Selecione **Aceitar** na solicitação de consentimento.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="Caixa de diálogo de consentimento mostrando as permissões que o aplicativo está solicitando do > usuário":::
>
> Depois de consentir com as permissões solicitadas, o aplicativo exibe que você fez logon com êxito usando suas credenciais do Azure Active Directory.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="Navegador da Web exibindo o aplicativo Web em execução e o usuário conectado":::

## <a name="more-information"></a>Mais informações

Esta seção apresenta uma visão geral do código necessário para a entrada de usuários. Essa visão geral pode ser útil para entender como o código funciona, os argumentos principais e também caso você queira adicionar entrada a um aplicativo ASP.NET Core existente.

### <a name="how-the-sample-works"></a>Como o exemplo funciona
![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Classe de inicialização

O middleware *Microsoft.AspNetCore.Authentication* usa a classe `Startup` executada quando o processo de hospedagem é inicializado:

```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"));

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

O método `AddAuthentication()` configura o serviço para adicionar a autenticação baseada em cookies, que é usada em cenários do navegador, e para definir o desafio para OpenID Connect.

A linha que contém `.AddMicrosoftIdentityWebApp` adiciona a autenticação de plataforma de identidade da Microsoft ao seu aplicativo. Em seguida, ele é configurado para entrar usando a plataforma de identidade da Microsoft com base nas informações da seção `AzureAD` do arquivo de configuração *appSettings.json*:

| chave *appsettings.json* | Descrição                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | **ID do aplicativo (cliente)** referente ao aplicativo registrado no portal do Azure.                                                                                       |
| `Instance`             | Ponto de extremidade do STS (serviço de token de segurança) para o usuário se autenticar. Esse valor geralmente é `https://login.microsoftonline.com/`, indicando a nuvem pública do Azure. |
| `TenantId`             | Nome do seu locatário ou sua ID de locatário (um GUID) ou *comum* para conectar usuários que tenham contas corporativas ou de estudante ou contas pessoais Microsoft.                             |

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

Você pode proteger um controlador ou métodos do controlador usando o atributo `[Authorize]`. Esse atributo restringe o acesso ao controlador ou aos métodos, permitindo apenas usuários autenticados, ou seja, o desafio de autenticação poderá ser iniciado para acessar o controlador se o usuário não estiver autenticado.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

O repositório GitHub que contém este tutorial do ASP.NET Core inclui instruções e mais exemplos de código que mostram como:

- Adicionar autenticação a um novo aplicativo Web ASP.NET Core
- Chamar o Microsoft Graph, outras APIs da Microsoft ou suas próprias APIs Web
- Adicionar autorização
- Conectar usuários em nuvens nacionais ou com identidades sociais

> [!div class="nextstepaction"]
> [Tutoriais do aplicativo Web ASP.NET Core no GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
