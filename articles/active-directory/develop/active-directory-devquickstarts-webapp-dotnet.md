---
title: "Introdução ao aplicativo Web .NET do Azure AD | Microsoft Docs"
description: Criar um aplicativo Web MVC do .NET que se integra ao Azure AD para entrar.
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: e15a41a4-dc5d-4c90-b3fe-5dc33b9a1e96
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 3c1e558c9d41e385f80939203a3457b74e30973b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="aspnet-web-app-sign-in-and-sign-out-with-azure-ad"></a>Entrada e saída do aplicativo Web ASP.NET com o Azure AD
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

O Azure AD (Azure Active Directory) torna simples e direto terceirizar o gerenciamento de identidades de seu aplicativo Web, fornecendo uma única entrada e uma única saída com apenas algumas linhas de código. Você pode fazer com que os usuários entrem e saiam de aplicativos Web do ASP.NET usando a implementação da Microsoft do middleware OWIN (Open Web Interface para .NET). O middleware OWIN voltado para a comunidade está incluído no .NET Framework 4.5. Este artigo mostra como usar o OWIN para:

* Fazer com que usuários entrem em aplicativos Web usando o Azure AD como o provedor de identidade.
* Exibir informações do usuário.
* Desconectar os usuários dos aplicativos.

## <a name="before-you-get-started"></a>Antes de começar
* Baixe o [esqueleto do aplicativo](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) ou baixe a [amostra concluída](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).
* Você também precisará de um locatário do Azure AD no qual registrar o aplicativo. Se você ainda não tiver um locatário do Azure AD, [saiba como obter um](active-directory-howto-tenant.md).

Quando estiver pronto, siga os procedimentos nas próximas quatro seções.

## <a name="step-1-register-the-new-app-with-azure-ad"></a>Etapa 1: registrar o novo aplicativo com o Azure AD
Para configurar o aplicativo para autenticar usuários, primeiro registre-o em seu locatário, fazendo o seguinte:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Na barra superior, clique no nome da conta. Na lista **Diretório** , selecione o locatário do Active Directory em que você deseja registrar o aplicativo.
3. Clique em **Mais serviços** no painel esquerdo e selecione **Azure Active Directory**.
4. Clique em **Registros do aplicativo** e, em seguida, selecione **Adicionar**.
5. Siga os prompts para criar um **Aplicativo Web e/ou uma API Web**.
  * **Nome** descreve o aplicativo aos usuários.
  * A **URL de logon** é a URL base do aplicativo. URL de padrão do esqueleto é https://localhost:44320/.
6. Depois de você concluir o registro, o Azure AD atribui uma ID do aplicativo exclusiva ao aplicativo. Copie o valor da página do aplicativo para usar nas próximas seções.
7. Na página **Configurações** -> **Propriedades** do aplicativo, atualize o URI da ID do Aplicativo. O **URI da ID do Aplicativo** é um identificador exclusivo do aplicativo. A convenção de nomenclatura é `https://<tenant-domain>/<app-name>` (por exemplo, `https://contoso.onmicrosoft.com/my-first-aad-app`).

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Etapa 2: configurar o aplicativo para usar o pipeline de autenticação OWIN
Nesta etapa, você configurará o middleware OWIN para usar o protocolo de autenticação OpenID Connect. Você usa o OWIN para emitir solicitações de entrada e saída, gerenciar sessões de usuário, obter informações do usuário e assim por diante.

1. Para começar, adicione os pacotes do NuGet de middleware OWIN ao projeto usando o Console do Gerenciador de Pacotes.

     ```
     PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
     PM> Install-Package Microsoft.Owin.Security.Cookies
     PM> Install-Package Microsoft.Owin.Host.SystemWeb
     ```

2. Para adicionar uma classe de inicialização do OWIN ao projeto chamado `Startup.cs`, clique com o botão direito do mouse no projeto, selecione **Adicionar**, **Novo Item** e então pesquise por **OWIN**. O middleware OWIN invoca o método **Configuration(...)** quando o aplicativo é iniciado.
3. Altere a declaração de classe para `public partial class Startup`. Já implementamos parte dessa classe para você em outro arquivo. No método **Configuration(...)**, faça uma chamada para método **ConfgureAuth(...)** para configurar a autenticação para o aplicativo.  

     ```C#
     public partial class Startup
     {
         public void Configuration(IAppBuilder app)
         {
             ConfigureAuth(app);
         }
     }
     ```

4. Abra o arquivo App_Start\Startup.Auth.cs e implemente o método **ConfigureAuth(...)** . Os parâmetros que você fornece em *OpenIDConnectAuthenticationOptions* servirão como coordenadas para seu aplicativo se comunicar com o Azure AD. Você também precisa configurar a autenticação de Cookies – o middleware OpenID Connect usa cookies em segundo plano.

     ```C#
     public void ConfigureAuth(IAppBuilder app)
     {
         app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

         app.UseCookieAuthentication(new CookieAuthenticationOptions());

         app.UseOpenIdConnectAuthentication(
             new OpenIdConnectAuthenticationOptions
             {
                 ClientId = clientId,
                 Authority = authority,
                 PostLogoutRedirectUri = postLogoutRedirectUri,
                 Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = context =>
                        {
                            context.HandleResponse();
                            context.Response.Redirect("/Error?message=" + context.Exception.Message);
                            return Task.FromResult(0);
                        }
                    }
             });
     }
     ```

5. Abra o arquivo web.config na raiz do projeto e insira os valores de configuração na seção `<appSettings>`.
  * `ida:ClientId`: O GUID que você copiou do Portal do Azure em "Etapa 1: Registrar o novo aplicativo com o Azure AD".
  * `ida:Tenant`: o nome do seu locatário do Azure AD (por exemplo, contoso.onmicrosoft.com).
  * `ida:PostLogoutRedirectUri`: indica ao Azure AD para o qual um usuário deverá ser redirecionado após uma solicitação de saída ser concluída com êxito.

## <a name="step-3-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Etapa 3: usar o OWIN para emitir solicitações de entrada e saída ao Azure AD
O aplicativo agora está configurado corretamente para se comunicar com o Azure AD usando o protocolo de autenticação OpenID Connect. O OWIN cuidou de todos os detalhes da criação de mensagens de autenticação, validação de tokens do Azure AD e manutenção da sessão do usuário. Tudo o que falta é oferecer aos usuários uma maneira de entrar e sair.

1. Você pode usar autorizar marcas em seus controladores para exigir que os usuários entrem antes de acessarem uma determinada página. Para fazer isso, abra Controllers\HomeController.cs e, em seguida, adicione a marcação `[Authorize]` à ação About.

     ```C#
     [Authorize]
     public ActionResult About()
     {
       ...
     ```

2. Você também pode usar o OWIN para emitir diretamente solicitações de autenticação de dentro de seu código. Para fazer isso, abra Controllers\AccountController.cs. Em seguida, nas ações SignIn() e SignOut(), emita as solicitações de desafio e de saída do OpenID Connect.

     ```C#
     public void SignIn()
     {
         // Send an OpenID Connect sign-in request.
         if (!Request.IsAuthenticated)
         {
             HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
         }
     }
     public void SignOut()
     {
         // Send an OpenID Connect sign-out request.
         HttpContext.GetOwinContext().Authentication.SignOut(
              OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
     }
     ```

3. Abra Views\Shared\_LoginPartial.cshtml para mostrar os links de entrada e saída do aplicativo ao usuário e para imprimir o nome do usuário em uma exibição.

    ```HTML
    @if (Request.IsAuthenticated)
    {
     <text>
         <ul class="nav navbar-nav navbar-right">
             <li class="navbar-text">
                 Hello, @User.Identity.Name!
             </li>
             <li>
                 @Html.ActionLink("Sign out", "SignOut", "Account")
             </li>
         </ul>
     </text>
    }
    else
    {
     <ul class="nav navbar-nav navbar-right">
         <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
     </ul>
    }
    ```

## <a name="step-4-display-user-information"></a>Etapa 4: exibir informações do usuário
Ao autenticar usuários com o OpenID Connect, o Azure AD retorna um id_token para o aplicativo que contém "declarações" ou afirmações sobre o usuário. Você pode usar essas declarações para personalizar o aplicativo fazendo o seguinte:

1. Abra o arquivo Controllers\HomeController.cs. Você pode acessar as declarações do usuário em seus controladores por meio do objeto principal de segurança `ClaimsPrincipal.Current` .

 ```C#
 public ActionResult About()
 {
     ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
     ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
     ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
     ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
     ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

     return View();
 }
 ```

2. Compile e execute o aplicativo. Se você ainda não fez isso, agora é o momento de criar um novo usuário em seu locatário com um domínio onmicrosoft.com. Faça assim:

  a. Entre com esse usuário e observe como a identidade do usuário é refletida na barra superior.

  b. Saia e entre novamente como outro usuário em seu locatário.

  c. Se você estiver se sentindo particularmente ambicioso, registre e execute outra instância deste aplicativo (com sua própria clientId) e veja o logon único em ação.

## <a name="next-steps"></a>Próximas etapas
Para referência, veja [a amostra concluída](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip) (sem seus valores de configuração).

Agora você pode passar para tópicos mais avançados. Por exemplo, experimento [Proteger uma API Web com o Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
