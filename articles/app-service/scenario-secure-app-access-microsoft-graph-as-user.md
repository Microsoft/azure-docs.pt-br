---
title: Tutorial – O aplicativo Web acessa o Microsoft Graph como o usuário | Azure
description: Neste tutorial, você aprenderá a acessar dados no Microsoft Graph para um usuário conectado.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 01/28/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 3413c1a3f27b48c60ae730ad230c653928702faa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99063376"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-user"></a>Tutorial: Acessar o Microsoft Graph em um aplicativo protegido como o usuário

Saiba como acessar o Microsoft Graph de um aplicativo Web em execução no Serviço de Aplicativo do Azure.

:::image type="content" alt-text="Diagrama que mostra o acesso ao Microsoft Graph." source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Você deseja adicionar acesso a Microsoft Graph do seu aplicativo Web e executar alguma ação como o usuário conectado. Esta seção descreve como conceder permissões delegadas ao aplicativo Web e obter as informações de perfil do usuário conectado no Azure AD (Azure Active Directory).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Conceder permissões delegadas a um aplicativo Web.
> * Chamar o Microsoft Graph em um aplicativo Web para um usuário conectado.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Um aplicativo Web em execução no Serviço de Aplicativo do Azure que tem o [módulo de autenticação/autorização do Serviço de Aplicativo habilitado](scenario-secure-app-authentication-app-service.md).

## <a name="grant-front-end-access-to-call-microsoft-graph"></a>Permitir acesso de front-end para chamada ao Microsoft Graph

Agora que você habilitou a autenticação e a autorização em seu aplicativo Web, esse aplicativo é registrado com a plataforma de identidade da Microsoft e tem o respaldo de um aplicativo do Azure AD. Nesta etapa, você fornecerá as permissões ao aplicativo Web para acessar o Microsoft Graph para o usuário. (Tecnicamente, você concede ao aplicativo do Azure AD do aplicativo Web as permissões para acessar o aplicativo do Azure AD do Microsoft Graph para o usuário.)

No menu [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory** ou pesquise e selecione **Azure Active Directory** em qualquer página.

Selecione **Registros de aplicativo** > **Aplicativos próprios** > **Exibir todos os aplicativos neste diretório**. Selecione o nome do aplicativo Web e **Permissões de API**.

Escolha **Adicionar uma permissão** e selecione APIs da Microsoft e Microsoft Graph.

Selecione **Permissões delegadas** e **User.Read** na lista. Escolha **Adicionar permissões**.

## <a name="configure-app-service-to-return-a-usable-access-token"></a>Configurar o Serviço de Aplicativo para retornar um token de acesso utilizável

O aplicativo Web agora tem as permissões necessárias para acessar o Microsoft Graph como o usuário conectado. Nesta etapa, configure o recurso de autenticação e autorização do Serviço de Aplicativo para fornecer um token de acesso usado para acessar o Microsoft Graph. Para essa etapa, você precisa da ID do cliente/aplicativo do serviço downstream (Microsoft Graph). A ID do aplicativo para o Microsoft Graph é *00000003-0000-0000-c000-000000000000*.

> [!IMPORTANT]
> Se você não configurar o Serviço de Aplicativo para retornar um token de acesso utilizável, receberá um erro ```CompactToken parsing failed with error code: 80049217``` ao chamar as APIs do Microsoft Graph no código.

Acesse o [Azure Resource Explorer](https://resources.azure.com/) e, usando a árvore de recursos, localize seu aplicativo Web. A URL do recurso será semelhante a `https://resources.azure.com/subscriptions/subscription-id/resourceGroups/SecureWebApp/providers/Microsoft.Web/sites/SecureWebApp20200915115914`.

O Azure Resource Explorer agora está aberto com seu aplicativo Web selecionado na árvore de recursos. Na parte superior da página, selecione **Leitura/Gravação** para permitir a edição dos recursos do Azure.

No navegador esquerdo, faça uma busca detalhada até **config** > **authsettings**.

Na exibição **authsettings**, selecione **Editar**. Defina ```additionalLoginParams``` como a cadeia de caracteres JSON a seguir usando a ID do cliente copiada.

```json
"additionalLoginParams": ["response_type=code id_token","resource=00000003-0000-0000-c000-000000000000"],
```

Salve as configurações selecionando **PUT**. Essa configuração pode levar vários minutos para entrar em vigor. Seu aplicativo Web agora está configurado para acessar o Microsoft Graph com um token de acesso adequado. Se você não fizer isso, o Microsoft Graph retornará um erro informando que o formato do token compacto está incorreto.

## <a name="call-microsoft-graph-net"></a>Chamar o Microsoft Graph (.NET)

Seu aplicativo Web agora tem a permissão necessária e também adiciona a ID do cliente do Microsoft Graph aos parâmetros de logon. Usando a [biblioteca Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web/), o aplicativo Web Obtém um token de acesso para autenticação com Microsoft Graph. Na versão 1.2.0 e posteriores, a biblioteca Microsoft.Identity.Web é integrada com o módulo de autenticação/autorização do Serviço de Aplicativo e pode ser executada junto com ele. A biblioteca Microsoft.Identity.Web detecta que o aplicativo Web está hospedado no Serviço de Aplicativo e obtém o token de acesso do módulo de autenticação/autorização do Serviço de Aplicativo. O token de acesso é então passado para solicitações autenticadas com a API do Microsoft Graph.

Para ver esse código como parte de um aplicativo de exemplo, confira o [exemplo no GitHub](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/2-WebApp-graphapi-on-behalf).

> [!NOTE]
> A biblioteca Microsoft.Identity.Web não é necessária no seu aplicativo Web para autenticação/autorização básica nem para autenticar solicitações no Microsoft Graph. Só é possível realizar [chamadas a APIs downstream com segurança](tutorial-auth-aad.md#call-api-securely-from-server-code) quando o módulo de autenticação/autorização do Serviço de Aplicativo está habilitado.
> 
> No entanto, a autenticação/autorização do Serviço de Aplicativo foi projetada para cenários de autenticação mais básicos. Para cenários mais complexos (lidando com declarações personalizadas, por exemplo), você precisa da biblioteca Microsoft.Identity.Web ou da [Biblioteca de Autenticação da Microsoft](../active-directory/develop/msal-overview.md). Há um pouco mais de trabalho de instalação e configuração no início, mas a biblioteca Microsoft.Identity.Web pode ser executada junto com o módulo de autenticação/autorização do Serviço de Aplicativo. Posteriormente, quando o aplicativo Web precisar lidar com cenários mais complexos, você poderá desabilitar o módulo de autenticação/autorização do Serviço de Aplicativo e o Microsoft.Identity.Web já fará parte do aplicativo.

### <a name="install-client-library-packages"></a>Instalar os pacotes da biblioteca de clientes

Instale os pacotes NuGet [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) e [Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) em seu projeto usando a interface de linha de comando do .NET Core ou o Console do Gerenciador de Pacotes no Visual Studio.

# <a name="command-line"></a>[Linha de comando](#tab/command-line)

Abra uma linha de comando e alterne para o diretório que contém o arquivo de projeto.

Execute os comandos de instalação.

```dotnetcli
dotnet add package Microsoft.Identity.Web.MicrosoftGraph

dotnet add package Microsoft.Identity.Web
```

# <a name="package-manager"></a>[Gerenciador de Pacotes](#tab/package-manager)

Abra o projeto/a solução no Visual Studio e abra o console do usando o comando **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.

Execute os comandos de instalação.
```powershell
Install-Package Microsoft.Identity.Web.MicrosoftGraph

Install-Package Microsoft.Identity.Web
```

---

### <a name="startupcs"></a>Startup.cs

No arquivo *Startup.cs*, o método ```AddMicrosoftIdentityWebApp``` adiciona o Microsoft.Identity.Web ao seu aplicativo Web. O método ```AddMicrosoftGraph``` adiciona suporte ao Microsoft Graph.

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Identity.Web;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

// Some code omitted for brevity.
public class Startup
{
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))
                    .EnableTokenAcquisitionToCallDownstreamApi()
                        .AddMicrosoftGraph(Configuration.GetSection("Graph"))
                        .AddInMemoryTokenCaches();

        services.AddRazorPages();
    }
}

```

### <a name="appsettingsjson"></a>appsettings.json

O *AzureAd* especifica a configuração para a biblioteca Microsoft.Identity.Web. No [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory** no menu do portal e escolha **Registros de aplicativo**. Selecione o registro de aplicativo criado quando você habilitou o módulo autenticação/autorização do Serviço de Aplicativo. (O registro de aplicativo deve ter o mesmo nome do aplicativo Web.) Você pode encontrar a ID do locatário e a ID do cliente na página de visão geral do registro do aplicativo. O nome de domínio pode ser encontrado na página de visão geral do Azure AD do locatário.

O *Graph* especifica o ponto de extremidade o Microsoft Graph e os escopos iniciais necessários para o aplicativo.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "fourthcoffeetest.onmicrosoft.com",
    "TenantId": "[tenant-id]",
    "ClientId": "[client-id]",
    // To call an API
    "ClientSecret": "[secret-from-portal]", // Not required by this scenario
    "CallbackPath": "/signin-oidc"
  },

  "Graph": {
    "BaseUrl": "https://graph.microsoft.com/v1.0",
    "Scopes": "user.read"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="indexcshtmlcs"></a>Index.cshtml.cs

O exemplo a seguir mostra como chamar o Microsoft Graph como o usuário conectado e obter algumas informações do usuário. O objeto ```GraphServiceClient``` foi injetado no controlador, e a autenticação foi configurada para você pela biblioteca Microsoft.Identity.Web.

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Graph;
using System.IO;
using Microsoft.Identity.Web;
using Microsoft.Extensions.Logging;

// Some code omitted for brevity.

[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public class IndexModel : PageModel
{
    private readonly ILogger<IndexModel> _logger;
    private readonly GraphServiceClient _graphServiceClient;

    public IndexModel(ILogger<IndexModel> logger, GraphServiceClient graphServiceClient)
    {
        _logger = logger;
        _graphServiceClient = graphServiceClient;
    }

    public async Task OnGetAsync()
    {
        try
        {
            var user = await _graphServiceClient.Me.Request().GetAsync();
            ViewData["Me"] = user;
            ViewData["name"] = user.DisplayName;

            using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
            {
                byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                ViewData["photo"] = Convert.ToBase64String(photoByte);
            }
        }
        catch (Exception ex)
        {
            ViewData["photo"] = null;
        }
    }
}
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você concluiu este tutorial e não precisa mais do aplicativo Web nem dos recursos associados, [limpe os recursos que você criou](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
>
> * Conceder permissões delegadas a um aplicativo Web.
> * Chamar o Microsoft Graph em um aplicativo Web para um usuário conectado.

> [!div class="nextstepaction"]
> [O Serviço de Aplicativo acessa o Microsoft Graph como o aplicativo](scenario-secure-app-access-microsoft-graph-as-app.md)