---
title: 'Início Rápido: Adicionar a opção Entrar com a conta da Microsoft a um aplicativo Web ASP.NET Core | Azure'
titleSuffix: Microsoft identity platform
description: Neste guia de início rápido, você aprenderá como um aplicativo implementa as credenciais da Microsoft em um aplicativo Web ASP.NET Core usando o OpenID Connect
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
ms.openlocfilehash: e7296b04e3e912e96ac8c2ed77b44288324c262f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578695"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Início Rápido: Adicionar entrada com a Microsoft para um aplicativo Web do ASP.NET Core

Neste guia de início rápido, você baixará e executará um exemplo de código que demonstra como um aplicativo Web ASP.NET Core pode conectar usuários de qualquer organização do Azure AD (Azure Active Directory).  

> [!div renderon="docs"]
> O seguinte diagrama mostra como o aplicativo de exemplo funciona:
>
> ![O diagrama da interação entre o navegador da Web, o aplicativo Web e a plataforma de identidade da Microsoft no aplicativo de exemplo.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)
>
> ## <a name="prerequisites"></a>Pré-requisitos
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) ou [Visual Studio Code](https://code.visualstudio.com/)
> * [SDK do .NET Core 3.1 ou posterior](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-app"></a>Registrar e baixar o aplicativo
> Você tem duas opções para começar a criar o seu aplicativo: configuração automática ou manual.
>
> ### <a name="automatic-configuration"></a>Configuração automática
> Se você quiser configurar automaticamente o seu aplicativo e baixar o exemplo de código, siga estas etapas:
>
> 1. Acesse a <a href="https://aka.ms/aspnetcore2-1-aad-quickstart-v2/" target="_blank">página do portal do Azure para o registro do aplicativo</a>.
> 1. Insira um nome para seu aplicativo e selecione **Registrar**.
> 1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo com apenas um clique.
>
> ### <a name="manual-configuration"></a>Configuração manual
> Se você quiser configurar manualmente o seu aplicativo e o exemplo de código, use os procedimentos a seguir.
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
> 1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar o aplicativo.
> 1. Pesquise **Azure Active Directory** e selecione-o.
> 1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
> 1. Em **Nome**, insira um nome para o seu aplicativo. Por exemplo, insira **AspNetCore-Quickstart**. Os usuários do seu aplicativo verão esse nome e você pode alterá-lo mais tarde.
> 1. Em **URI de redirecionamento**, insira **https://localhost:44321/signin-oidc** .
> 1. Selecione **Registrar**.
> 1. Em **Gerenciar**, selecione **Autenticação**.
> 1. Em **URL de logoff de front-channel**, insira **https://localhost:44321/signout-oidc** .
> 1. Em **Concessão implícita e fluxos híbridos**, selecione **Tokens de ID**.
> 1. Clique em **Salvar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Etapa 1: Configurar seu aplicativo no portal do Azure
> Para que o exemplo de código deste guia de início rápido funcione:
> - Em **URI de redirecionamento**, insira **https://localhost:44321/** e **https://localhost:44321/signin-oidc** .
> - Em **URL de logoff de front-channel**, insira **https://localhost:44321/signout-oidc** . 
>
> O ponto de extremidade da autorização emitirá tokens de ID de solicitação.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essa alteração para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-aspnet-webapp/green-check.png) Seu aplicativo já está configurado com esses atributos.

#### <a name="step-2-download-the-aspnet-core-project"></a>Etapa 2: Baixar o projeto do ASP.NET Core

> [!div renderon="docs"]
> [Baixar a solução do ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Execute o projeto.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Baixe o exemplo de código](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Etapa 3: seu aplicativo está configurado e pronto para ser executado
> Configuramos o seu projeto com os valores das propriedades do aplicativo e ele está pronto para ser executado.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>Etapa 3: Configurar seu projeto do ASP.NET Core
> 1. Extraia o arquivo .zip para uma pasta local próxima à raiz da unidade. Por exemplo, extraia no *C:\Azure-Samples*.
> 
>    É recomendável extrair os arquivos em um diretório próximo à raiz da unidade para evitar erros causados por limitações de comprimento do caminho no Windows.
> 1. Abra a solução no Visual Studio 2019.
> 1. Abra o arquivo *appsettings.json* e modifique o seguinte código:
>
>    ```json
>    "Domain": "Enter the domain of your tenant, e.g. contoso.onmicrosoft.com",
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    ```
>
>    - Substitua `Enter_the_Application_Id_here` pela ID do aplicativo (cliente) referente ao aplicativo que você registrou no portal do Azure. Você pode encontrar o valor **ID do aplicativo (cliente)** na página **Visão geral** do aplicativo.
>    - Substitua `common` por um dos seguintes:
>       - Se o aplicativo der suporte a **Contas somente neste diretório organizacional**, substitua esse valor pela ID do diretório (locatário) (um GUID) ou pelo nome do locatário (por exemplo, `contoso.onmicrosoft.com`). Você pode encontrar o valor **ID do diretório (locatário)** na página **Visão geral** do aplicativo.
>       - Se o aplicativo der suporte para **Contas em qualquer diretório organizacional**, substitua esse valor por `organizations`.
>       - Se o aplicativo der suporte a **Todos os usuários de contas Microsoft**, defina esse valor como `common`.
>
> Para este guia de início rápido, não altere nenhum outro valor no arquivo *appsettings.json*.
>
> #### <a name="step-4-build-and-run-the-application"></a>Etapa 4: compile e execute o aplicativo
>
> Crie e execute o aplicativo no Visual Studio selecionando o menu **Depurar** > **Iniciar Depuração** ou pressionando a tecla F5.
>
> As suas credenciais serão solicitadas e será solicitado que você dê consentimento para as permissões que o seu aplicativo exige. Selecione **Aceitar** na solicitação de consentimento.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="Captura de tela da caixa de diálogo de consentimento, mostrando as permissões que o aplicativo está solicitando do usuário.":::
>
> Depois de consentir com as permissões solicitadas, o aplicativo exibe que você se conectou com êxito com as suas credenciais do Azure Active Directory.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="Captura de tela de um navegador da Web que mostra o aplicativo Web em execução e o usuário conectado.":::

## <a name="more-information"></a>Mais informações

Esta seção apresenta uma visão geral do código necessário para a entrada de usuários. Essa visão geral pode ser útil para entender como o código funciona, quais são os argumentos principais e como adicionar credenciais a um aplicativo ASP.NET Core existente.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Como o exemplo funciona
>
> ![O diagrama da interação entre o navegador da Web, o aplicativo Web e a plataforma de identidade da Microsoft no aplicativo de exemplo.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Classe de inicialização

O middleware *Microsoft.AspNetCore.Authentication* usa a classe `Startup` executada quando o processo de hospedagem é iniciado:

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

O método `AddAuthentication()` configura o serviço para adicionar a autenticação baseada em cookie. Essa autenticação é usada em cenários do navegador e para definir o desafio para OpenID Connect.

A linha que contém `.AddMicrosoftIdentityWebApp` adiciona a autenticação de plataforma de identidade da Microsoft ao seu aplicativo. O aplicativo é configurado para conectar usuários com base nas seguintes informações na seção `AzureAD` do arquivo de configuração *appsettings.json*:

| chave *appsettings.json* | Descrição                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | ID do aplicativo (cliente) referente ao aplicativo registrado no portal do Azure.                                                                                       |
| `Instance`             | Ponto de extremidade do STS (serviço de token de segurança) para o usuário se autenticar. Esse valor geralmente é `https://login.microsoftonline.com/`, indicando a nuvem pública do Azure. |
| `TenantId`             | Nome do seu locatário ou da ID de locatário (um GUID) ou `common` para conectar usuários que tenham contas corporativas ou de estudante ou contas pessoais Microsoft.                             |

O método `Configure()` contém dois métodos importantes, `app.UseAuthentication()` e `app.UseAuthorization()`, que habilitam sua funcionalidade nomeada. Além disso, no método `Configure()`, você precisará registrar as rotas da Web de Identidade da Microsoft com, pelo menos, uma chamada para `endpoints.MapControllerRoute()` ou uma chamada para `endpoints.MapControllers()`:

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

### <a name="attribute-for-protecting-a-controller-or-methods"></a>Atributo para proteger um controlador ou métodos

Você pode proteger um controlador ou métodos do controlador usando o atributo `[Authorize]`. Esse atributo restringe o acesso ao controlador ou aos métodos permitindo apenas usuários autenticados. Um desafio de autenticação poderá ser iniciado para acessar o controlador se um usuário não estiver autenticado.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

O repositório GitHub que contém este tutorial do ASP.NET Core inclui instruções e mais exemplos de código que mostram como:

- Adicione a autenticação a um novo aplicativo Web ASP.NET Core.
- Chame o Microsoft Graph, outras APIs da Microsoft ou as suas APIs Web.
- Adicione a autorização.
- Conecte usuários em nuvens nacionais ou com identidades sociais.

> [!div class="nextstepaction"]
> [Tutoriais do aplicativo Web ASP.NET Core no GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
