---
title: Adicionar entrada a uma API Web do MVC do .NET usando o ponto de extremidade do Azure AD v2.0 | Microsoft Docs
description: "Agora você tem uma API da Web .NET MVC que aceita tokens de contas da Microsoft pessoais, e contas corporativas ou de estudante."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: e77bc4e0-d0c9-4075-a3f6-769e2c810206
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: bb332196aef59a497a1f80b67ab5c1eeef3dee0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="secure-an-mvc-web-api"></a>Proteger uma API Web do MVC
Com o ponto de extremidade v2.0 do Azure Active Directory, você pode proteger uma API Web usando tokens de acesso [OAuth 2.0](active-directory-v2-protocols.md) , permitindo que usuários com contas pessoais, corporativas ou de estudante da Microsoft acessem sua API Web com segurança.

> [!NOTE]
> Nem todos os recursos e cenários do Azure Active Directory têm suporte no ponto de extremidade v2.0.  Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).
>
>

Em APIs da Web ASP.NET, você pode conseguir isso usando o middleware OWIN da Microsoft, incluso no .NET Framework 4.5.  Usaremos aqui o OWIN para compilar uma API Web MVC de "Lista de Tarefas" que permite aos clientes criar e ler tarefas da lista de tarefas pendentes do usuário.  A API Web validará se as solicitações de entrada contém um token de acesso válido e rejeitará as solicitações não aprovadas na validação em uma rota protegida.  Este exemplo foi criado usando o Visual Studio 2015.

## <a name="download"></a>Baixar
O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet).  Para acompanhar, você pode [baixar o esqueleto do aplicativo como um .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) ou clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

O aplicativo de esqueleto inclui todo o código clichê de uma API simples, porém estão faltando todas as partes relacionadas à identidade. Se você não quiser acompanhar, clone ou [baixe o exemplo completo](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip).

```
git clone https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

## <a name="register-an-app"></a>Registrar um aplicativo
Crie um novo aplicativo em [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ou siga estas [etapas detalhadas](active-directory-v2-app-registration.md).  Não se esqueça de:

* Copiar a **ID do Aplicativo** designada ao seu aplicativo, você precisará dela logo.

Essa solução do Visual Studio também contém um "TodoListClient”, que é um aplicativo WPF simples.  O TodoListClient é usado para demonstrar como um usuário se conecta e como um cliente pode emitir solicitações para sua API Web.  Nesse caso, tanto o TodoListClient como TodoListService são representados pelo mesmo aplicativo.  Para configurar o TodoListClient, você deverá também:

* Adicione a plataforma **Móvel** de seu aplicativo.

## <a name="install-owin"></a>Instalar a OWIN
Agora que você registrou um aplicativo, é preciso configurar seu aplicativo para se comunicar com o ponto de extremidade v2.0 para validar tokens e solicitações recebidos.

* Para começar, abra a solução e adicione os pacotes do NuGet de middleware OWIN ao projeto TodoListService usando o Console do Gerenciador de Pacotes.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
PM> Install-Package Microsoft.IdentityModel.Protocol.Extensions -ProjectName TodoListService
```

## <a name="configure-oauth-authentication"></a>Configurar a autenticação OAuth
* Adicionar uma classe de inicialização OWIN no projeto TodoListService chamado `Startup.cs`.  Clique com o botão direito do mouse no projeto --> **Adicionar** --> **Novo item** --> pesquise por "OWIN".  O middleware OWIN invocará o método `Configuration(…)` quando seu aplicativo for iniciado.
* Altere a declaração de classe para `public partial class Startup`; já implementamos parte dessa classe para você em outro arquivo.  No método `Configuration(…)`, faça uma chamada para ConfgureAuth(...) para configurar a autenticação para seu aplicativo Web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

* Abra o arquivo `App_Start\Startup.Auth.cs` e implemente o método `ConfigureAuth(…)`, que vai configurar a API da Web para aceitar tokens do ponto de extremidade v2.0.

```C#
public void ConfigureAuth(IAppBuilder app)
{
        var tvps = new TokenValidationParameters
        {
                // In this app, the TodoListClient and TodoListService
                // are represented using the same Application Id - we use
                // the Application Id to represent the audience, or the
                // intended recipient of tokens.

                ValidAudience = clientId,

                // In a real applicaiton, you might use issuer validation to
                // verify that the user's organization (if applicable) has
                // signed up for the app.  Here, we'll just turn it off.

                ValidateIssuer = false,
        };

        // Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
        // The options provided here tell the middleware about the type of tokens
        // that will be recieved, which are JWTs for the v2.0 endpoint.

        // NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
        // metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
        // OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
        // metadata document.

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {
                AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
        });
}
```

* Agora você pode usar `[Authorize]` atributos para proteger os controladores e ações com a autenticação de portador OAuth 2.0.  Decore a classe `Controllers\TodoListController.cs` com uma marca de autorização.  Isso forçará o usuário a entrar antes de acessar essa página.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

* Quando um chamador autorizado invoca com êxito uma das `TodoListController` APIs, a ação pode precisar ter acesso às informações sobre o chamador.  O OWIN fornece acesso às declarações dentro do token de portador por meio do objeto `ClaimsPrincipal` .  

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

* Por fim, abra o arquivo `web.config` na raiz do projeto TodoListService e insira os valores de configuração na seção `<appSettings>`.
  * Seu `ida:Audience` é a **ID do Aplicativo** do aplicativo que você inseriu no portal.

## <a name="configure-the-client-app"></a>Configurar o aplicativo do cliente
Antes que você possa ver o Serviço de Lista de Tarefas em ação, você precisa configurar o Cliente de Lista de Tarefas para poder obter tokens do ponto de extremidade v2.0 e fazer chamadas para o serviço.

* No projeto TodoListClient, abra `App.config` e insira seus valores de configuração na seção `<appSettings>`.
  * Sua ID do Aplicativo `ida:ClientId` que você copiou do portal.

Por fim, limpe, compile e execute cada projeto!  Agora você tem uma API da Web .NET MVC que aceita tokens de contas da Microsoft pessoais e contas corporativas ou de estudante.  Entre na TodoListClient e chame sua API da Web para adicionar tarefas à Lista de Tarefas do usuário.

Para referência, o exemplo concluído (sem os valores de configuração) [é fornecido como um .zip aqui](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip), ou você pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## <a name="next-steps"></a>Próximas etapas
Agora você pode passar para tópicos adicionais.  Você pode desejar experimentar:

[Chamar uma API Web em um aplicativo Web >>](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

Para obter recursos adicionais, consulte:

* [Guia do desenvolvedor do v2.0 >>](active-directory-appmodel-v2-overview.md)
* [Marca “azure-active-directory” do StackOverflow >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para nossos produtos
Recomendamos que você obtenha notificações sobre a ocorrência de incidentes de segurança visitando [esta página](https://technet.microsoft.com/security/dd252948) e assinando os alertas do Security Advisory.
