---
title: 'Início Rápido: Proteger uma API Web ASP.NET Core com a plataforma de identidade da Microsoft | Azure'
titleSuffix: Microsoft identity platform
description: Neste guia de início rápido, você baixará e modificará um exemplo de código que demonstra como proteger uma API Web ASP.NET Core usando a plataforma de identidade da Microsoft para autorização.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 30593c51f17b99989409ddd22c9c1caa28468039
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720824"
---
# <a name="quickstart-protect-an-aspnet-core-web-api-with-the-microsoft-identity-platform"></a>Guia de início rápido: Proteger uma API Web ASP.NET Core com a plataforma de identidade da Microsoft

Neste guia de início rápido, você baixará um exemplo de código da API Web ASP.NET Core e examinará como ele restringe o acesso aos recursos somente às contas autorizadas. O exemplo dá suporte à autorização de contas Microsoft pessoais e contas em qualquer organização do Azure AD (Azure Active Directory).

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Pré-requisitos
>
> - Conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> - [Locatário do Azure Active Directory](quickstart-create-new-tenant.md)
> - [SDK do .NET Core 3.1 ou posterior](https://dotnet.microsoft.com/)
> - [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) ou [Visual Studio Code](https://code.visualstudio.com/)
>
> ## <a name="step-1-register-the-application"></a>Etapa 1: Registrar o aplicativo
>
> Primeiro, registre a API Web no seu locatário do Azure AD e adicione um escopo seguindo estas etapas:
>
> 1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual deseja registrar um aplicativo.
> 1. Pesquise **Azure Active Directory** e selecione-o.
> 1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
> 1. Em **Nome**, insira um nome para o seu aplicativo. Por exemplo, insira **AspNetCoreWebApi-Quickstart**. Os usuários do seu aplicativo verão esse nome e você pode alterá-lo mais tarde.
> 1. Selecione **Registrar**.
> 1. Em **Gerenciar**, selecione **Expor uma API** > **Adicionar um escopo**. Em **URI da ID do Aplicativo**, aceite o padrão selecionando **Salvar e continuar** e insira os seguintes detalhes:
>    - **Nome do escopo**: `access_as_user`
>    - **Quem pode consentir?** : **Administradores e usuários**
>    - **Nome de exibição de consentimento do administrador**: `Access AspNetCoreWebApi-Quickstart`
>    - **Descrição do consentimento do administrador:** `Allows the app to access AspNetCoreWebApi-Quickstart as the signed-in user.`
>    - **Nome de exibição do consentimento do usuário**: `Access AspNetCoreWebApi-Quickstart`
>    - **Descrição do consentimento do usuário**: `Allow the application to access AspNetCoreWebApi-Quickstart on your behalf.`
>    - **Estado**: **Enabled**
> 1. Selecione **Adicionar escopo** para concluir a adição do escopo.

## <a name="step-2-download-the-aspnet-core-project"></a>Etapa 2: Baixar o projeto do ASP.NET Core

> [!div renderon="docs"]
> [Baixe a solução do ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/archive/aspnetcore3-1.zip) no GitHub.

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div renderon="docs"]
> ## <a name="step-3-configure-the-aspnet-core-project"></a>Etapa 3: Configurar o projeto do ASP.NET Core
>
> Nesta etapa, configure o código de exemplo para trabalhar com o registro do aplicativo criado anteriormente.
>
> 1. Extraia o arquivo .zip para uma pasta próxima à raiz da unidade. Por exemplo, extraia no *C:\Azure-Samples*.
>
>    É recomendável extrair os arquivos em um diretório próximo à raiz da unidade para evitar erros causados por limitações de comprimento do caminho no Windows.
>
> 1. Abra a solução na pasta *webapi* no editor de códigos.
> 1. Abra o arquivo *appsettings.json* e modifique o seguinte código:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```
>
>    - Substitua `Enter_the_Application_Id_here` pela ID do aplicativo (cliente) referente ao aplicativo que você registrou no portal do Azure. Encontre a ID do aplicativo (cliente) na página **Visão geral** do aplicativo.
>    - Substitua `Enter_the_Tenant_Info_Here` por um dos seguintes:
>       - Se o aplicativo dá suporte às **Contas somente neste diretório organizacional**, substitua esse valor pela ID de diretório (locatário) (um GUID) ou pelo nome do locatário (por exemplo, `contoso.onmicrosoft.com`). Encontre a ID de diretório (locatário) na página **Visão geral** do aplicativo.
>       - Se o aplicativo der suporte para **Contas em qualquer diretório organizacional**, substitua esse valor por `organizations`.
>       - Se o aplicativo der suporte a **Todos os usuários de contas Microsoft**, defina esse valor como `common`.
>
> Para este guia de início rápido, não altere nenhum outro valor no arquivo *appsettings.json*.

## <a name="how-the-sample-works"></a>Como o exemplo funciona

A API Web recebe um token de um aplicativo cliente, e o código na API Web valida o token. Esse cenário é explicado mais detalhadamente em [Cenário: API Web protegida](scenario-protected-web-api-overview.md).

### <a name="startup-class"></a>Classe de inicialização

O middleware *Microsoft.AspNetCore.Authentication* usa a classe `Startup` que é executada quando o processo de hospedagem é iniciado. No método `ConfigureServices`, o método de extensão `AddMicrosoftIdentityWebApi` fornecido pelo *Microsoft.Identity.Web* é chamado.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");
    }
```

O método `AddAuthentication()` configura o serviço para adicionar a autenticação baseada em JwtBearer.

A linha que contém `.AddMicrosoftIdentityWebApi` adiciona a autorização da plataforma de identidade da Microsoft à API Web. Em seguida, ela é configurada para validar os tokens de acesso emitidos pela plataforma de identidade da Microsoft com base nas informações na seção `AzureAD` do arquivo de configuração *appsettings.json*:

| chave *appsettings.json* | Descrição                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | ID do aplicativo (cliente) referente ao aplicativo registrado no portal do Azure.                                                                                       |
| `Instance`             | Ponto de extremidade do STS (serviço de token de segurança) para o usuário se autenticar. Esse valor geralmente é `https://login.microsoftonline.com/`, indicando a nuvem pública do Azure. |
| `TenantId`             | Nome do seu locatário ou sua ID de locatário (um GUID) ou `common` para conectar usuários que tenham contas corporativas ou de estudante ou contas pessoais Microsoft.                             |

O método `Configure()` contém dois métodos importantes, `app.UseAuthentication()` e `app.UseAuthorization()`, que habilitam a funcionalidade nomeada:

```csharp
// The runtime calls this method. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protecting-a-controller-a-controllers-method-or-a-razor-page"></a>Como proteger um controlador, um método do controlador ou uma página Razor

Você pode proteger um controlador ou métodos do controlador usando o atributo `[Authorize]`. Esse atributo restringe o acesso ao controlador ou aos métodos permitindo apenas usuários autenticados. Um desafio de autenticação poderá ser iniciado para acessar o controlador se um usuário não estiver autenticado.

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
```

### <a name="validation-of-scope-in-the-controller"></a>Validação do escopo no controlador

O código na API verifica se os escopos necessários estão no token usando `HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);`:

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        // The web API will only accept tokens 1) for users, and 2) having the "access_as_user" scope for this API
        static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
            HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

            // some code here
        }
    }
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

O repositório GitHub que contém este exemplo de código ASP.NET Core da API Web inclui instruções e mais exemplos de código que mostram como:

- Adicionar a autenticação a uma nova API Web ASP.NET Core.
- Chamar a API Web em um aplicativo da área de trabalho.
- Chamar APIs downstream como o Microsoft Graph e outras APIs da Microsoft.

> [!div class="nextstepaction"]
> [Tutoriais da API Web ASP.NET Core no GitHub](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)
