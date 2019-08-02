---
title: Início rápido do aplicativo Web ASP.NET Core na plataforma de identidade da Microsoft | Azure
description: Saiba como implementar a Entrada da Microsoft em um aplicativo Web do ASP.NET Core usando o OpenID Connect
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
ms.openlocfilehash: 4723b224d61b2ccc2b563150befa5ea2d33453ad
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335621"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Início Rápido: Adicionar entrada com a Microsoft para um aplicativo Web do ASP.NET Core

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Neste início rápido, você aprenderá como um aplicativo Web ASP.NET Core pode autenticar contas pessoais (hotmail.com, outlook.com, outras) e contas corporativas e de estudante de qualquer instância do Azure AD (Azure Active Directory).

![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrar e baixar o aplicativo de início rápido
> Você tem duas opções para iniciar o aplicativo de início rápido:
> * [Express] [Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código
>
> 1. Acesse o [portal do Azure – Registros de aplicativo](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Insira um nome para seu aplicativo e selecione **Registrar**.
> 1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo com apenas um clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
> Para registrar seu aplicativo e adicionar manualmente as informações de registro do aplicativo à solução, execute estas etapas:
>
> 1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
> 1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina sua sessão do portal para o locatário desejado do Azure AD.
> 1. Navegue até a página [Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) da plataforma de identidade da Microsoft para desenvolvedores.
> 1. Selecione **Novo registro**.
> 1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo:
>    - Na seção **Nome**, insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo, por exemplo, `AspNetCore-Quickstart`.
>    - Na **URI de redirecionamento**, adicione `https://localhost:44321/` e selecione **Registrar**.
> 1. Selecione o menu **Autenticação** e, em seguida, adicione as seguintes informações:
>    - Nas **URIs de redirecionamento**, adicione `https://localhost:44321/signin-oidc` e selecione **Salvar**.
>    - Na seção **Configurações avançadas**, defina **URL de Saída** como `https://localhost:44321/signout-oidc`.
>    - Sob **Concessão implícita**, marque **Tokens de ID**.
>    - Clique em **Salvar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Etapa 1: Configurar seu aplicativo no portal do Azure
> Para que o exemplo de código deste início rápido funcione, você precisa adicionar URLs de resposta como `https://localhost:44321/` e `https://localhost:44321/signin-oidc`, adicionar a URL de logoff como `https://localhost:44321/signout-oidc` e solicitar a emissão de tokens de ID pelo ponto de extremidade de autorização.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essa alteração para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-aspnet-webapp/green-check.png) Seu aplicativo já está configurado com esses atributos.

#### <a name="step-2-download-your-aspnet-core-project"></a>Etapa 2: Baixar o projeto do ASP.NET Core

- [Baixar a solução do Visual Studio 2019](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Etapa 3: Configurar o projeto do Visual Studio

1. Extraia o arquivo zip para uma pasta local na pasta raiz, por exemplo, **C:\Azure-Samples**
1. Se você usa o Visual Studio 2019, abra a solução no Visual Studio (opcional).
1. Edite o arquivo **appsettings.json**. Localize `ClientId` e atualize o valor de `ClientId` com o valor da **ID do Aplicativo (cliente)** do aplicativo que você registrou. 

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "Enter_the_Tenant_Info_Here"
    ```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Este guia de início rápido oferece suporte a Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> Em que:
> - `Enter_the_Application_Id_here` - é a **ID do aplicativo (cliente)** para o aplicativo registrado no portal do Azure. Você pode encontrar **ID do aplicativo (cliente)** na página **Visão geral** do aplicativo.
> - `Enter_the_Tenant_Info_Here` - é uma das seguintes opções:
>   - Se seu aplicativo dá suporte a **Contas apenas neste diretório organizacional**, substitua esse valor pela **ID do Locatário** ou pelo **Nome do locatário** (por exemplo, contoso.microsoft.com)
>   - Se seu aplicativo dá suporte a **Contas em qualquer diretório organizacional**, substitua esse valor por `organizations`
>   - Se seu aplicativo dá suporte a **Todos os usuários de contas da Microsoft**, substitua esse valor por `common`
>
> > [!TIP]
> > Para encontrar os valores de **ID do aplicativo (cliente)** , **ID de diretório (locatário)** e **Tipos de conta com suporte**, vá para a página **Visão Geral** do aplicativo no portal do Azure.

## <a name="more-information"></a>Mais informações

Esta seção apresenta uma visão geral do código necessário para a entrada de usuários. Essa visão geral pode ser útil para entender como o código funciona, os argumentos principais e também caso você queira adicionar entrada a um aplicativo ASP.NET Core existente.

### <a name="startup-class"></a>Classe de inicialização

O middleware *Microsoft.AspNetCore.Authentication* usa a classe de inicialização executada quando o processo de hospedagem é inicializado:

```csharp
public void ConfigureServices(IServiceCollection services)
{
  services.Configure<CookiePolicyOptions>(options =>
  {
    // This lambda determines whether user consent for non-essential cookies is needed for a given request.
    options.CheckConsentNeeded = context => true;
    options.MinimumSameSitePolicy = SameSiteMode.None;
  });

  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
          .AddAzureAD(options => Configuration.Bind("AzureAd", options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
    options.Authority = options.Authority + "/v2.0/";         // Microsoft identity platform

    options.TokenValidationParameters.ValidateIssuer = false; // accept several tenants (here simplified)
  });

  services.AddMvc(options =>
  {
     var policy = new AuthorizationPolicyBuilder()
                     .RequireAuthenticatedUser()
                     .Build();
     options.Filters.Add(new AuthorizeFilter(policy));
  })
  .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
}
```

O método `AddAuthentication` configura o serviço para adicionar a autenticação baseada em cookies, que é usada em cenários do navegador, e para definir o desafio para OpenId Connect. 

A linha que contém `.AddAzureAd` adiciona a autenticação de plataforma de identidade da Microsoft ao seu aplicativo. Em seguida, ele é configurado para se conectar usando o ponto de extremidade de plataforma de identidade da Microsoft.

> |Where  |  |
> |---------|---------|
> | ClientId  | ID do aplicativo (cliente) do aplicativo registrado no portal do Azure. |
> | Authority | O ponto de extremidade do STS para o usuário autenticar. Geralmente, é <https://login.microsoftonline.com/{tenant}/v2.0> para a nuvem pública, em que {tenant} é o nome ou Id do seu locatário, ou *common* para uma referência ao ponto de extremidade comum (usado para aplicativos multilocatário) |
> | TokenValidationParameters | Uma lista de parâmetros para validação de token. Nesse caso, `ValidateIssuer` é definido como `false` para indicar que pode aceitar entradas de quaisquer contas corporativas, de estudante ou pessoais. |


> [!NOTE]
> A configuração de `ValidateIssuer = false` é uma simplificação para este início rápido. Em aplicativos reais, você precisa validar o emissor.
> Consulte os exemplos para entender como fazer isso.

### <a name="protect-a-controller-or-a-controllers-method"></a>Proteger um controlador ou um método do controlador

Você pode proteger um controlador ou métodos do controlador usando o atributo `[Authorize]`. Esse atributo restringe o acesso ao controlador ou aos métodos, permitindo apenas usuários autenticados, ou seja, o desafio de autenticação poderá ser iniciado para acessar o controlador se o usuário não estiver autenticado.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

Confira o repositório do GitHub para este tutorial do ASP.NET Core para obter mais informações, incluindo instruções sobre como adicionar autenticação a um novo aplicativo Web do ASP.NET Core, como chamar o Microsoft Graph e outras APIs da Microsoft, como chamar suas próprias APIs, como adicionar autorização, como conectar usuários nas nuvens nacionais ou com identidades sociais e muito mais:

> [!div class="nextstepaction"]
> [Tutorial do aplicativo Web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

Ajude-nos a melhorar a plataforma de identidade da Microsoft. Deixe sua opinião respondendo a uma breve pesquisa de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa da plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)