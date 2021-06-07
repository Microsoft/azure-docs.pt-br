---
title: Tutorial – Conectar usuários e chamar uma API protegida de um aplicativo Blazor WebAssembly
titleSuffix: Microsoft identity platform
description: Neste tutorial, conecte os usuários e chame uma API protegida usando a plataforma de identidade da Microsoft em um aplicativo Blazor WASM (WebAssembly).
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 47b4f36aec9a906317a9704a7d73bf66385d9e88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552109"
---
# <a name="tutorial-sign-in-users-and-call-a-protected-api-from-a-blazor-webassembly-app"></a>Tutorial: Conectar usuários e chamar uma API protegida de um aplicativo Blazor WebAssembly

Neste tutorial, você criará um aplicativo WebAssembly Blazor que conecta os usuários e obtém dados do Microsoft Graph usando a plataforma de identidade da Microsoft e registrando seu aplicativo no Azure AD (Azure Active Directory). 

Neste tutorial:

> [!div class="checklist"]
>
> * Criar um aplicativo Blazor WebAssembly configurado para usar o Azure AD (Azure Active Directory) para [autenticação e autorização](authentication-vs-authorization.md) usando a plataforma de identidade da Microsoft
> * Recuperar dados de uma API Web protegida, neste caso, [Microsoft Graph](/graph/overview)

Este tutorial usa o .NET Core 3.1. Os documentos do .NET contêm instruções sobre [como proteger um aplicativo Blazor WebAssembly](/aspnet/core/blazor/security/webassembly/graph-api) usando o ASP.NET Core 5.0. 

Também temos um tutorial para o [Blazor Server](tutorial-blazor-server.md). 

## <a name="prerequisites"></a>Pré-requisitos

* [SDK do .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* Um locatário do Azure AD no qual você pode registrar um aplicativo. Se você não tiver acesso a um locatário do Azure AD, poderá obter um registrando-se no [Programa para Desenvolvedores do Microsoft 365](https://developer.microsoft.com/microsoft-365/dev-program) ou criando uma [conta gratuita do Azure](https://azure.microsoft.com/free).

## <a name="register-the-app-in-the-azure-portal"></a>Registrar o aplicativo no portal do Azure

Todo aplicativo que usar o Azure AD (Azure Active Directory) para autenticação precisará ser registrado no Azure AD. Siga as instruções em [Registrar um aplicativo](quickstart-register-app.md) com estas especificações:

- Para **Tipos de contas com suporte**, selecione **Contas somente neste diretório organizacional**.
- Deixe a lista suspensa **URI de Redirecionamento** definida como **Web** e digite `https://localhost:5001/authentication/login-callback`. A porta padrão para um aplicativo em execução no Kestrel é 5001. Se o aplicativo estiver disponível em uma porta diferente, especifique o número da porta em vez de `5001`.

Após registrá-lo, em **Gerenciar**, selecione **Autenticação** > **Concessão implícita e fluxos híbridos**. Selecione **Tokens de acesso** e **Tokens de ID** e escolha **Salvar**.

## <a name="create-the-app-using-the-net-core-cli"></a>Criar o aplicativo usando a CLI do .NET Core

Para criar o aplicativo, você precisa dos modelos mais recentes do Blazor. Você pode instalá-los para a CLI do .NET Core com o seguinte comando:

```dotnetcli
dotnet new -i Microsoft.Identity.Web.ProjectTemplates::1.6.0
```

Em seguida, execute o comando a seguir para criar o aplicativo. Substitua os espaços reservados no comando pelas informações apropriadas da página de visão geral do aplicativo e execute o comando em um shell de comando. A localização de saída especificada com a opção `-o|--output` criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.

```dotnetcli
dotnet new blazorwasm2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| Espaço reservado   | Nome do portal do Azure       | Exemplo                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorWASMSample`                         |
| `{CLIENT ID}` | ID do aplicativo (cliente) | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | ID do diretório (locatário)   | `e86c78e2-0000-0000-0000-918e0565a45e` |

## <a name="test-the-app"></a>Testar o aplicativo

Agora você pode criar e executar o aplicativo. Ao executar esse aplicativo de modelo, você precisa especificar a estrutura a ser executada usando --framework. Este tutorial usa o .NET Standard 2.1, mas o modelo dá suporte a outras estruturas também.

```dotnetcli
dotnet run --framework netstandard2.1
```

No navegador, navegue até `https://localhost:5001` e faça logon usando uma conta de usuário do Azure AD para ver o aplicativo em execução e o logon dos usuários na plataforma de identidade da Microsoft.

Os componentes desse modelo que habilitam logons com o Azure AD usando a plataforma de identidade da Microsoft são explicados no [documento do ASP.NET sobre esse tópico](/aspnet/core/blazor/security/webassembly/standalone-with-azure-active-directory#authentication-package).

## <a name="retrieving-data-from-a-protected-api-microsoft-graph"></a>Recuperar dados de uma API protegida (Microsoft Graph)

O [Microsoft Graph](/graph/overview) contém APIs que fornecem acesso a dados do Microsoft 365 para seus usuários e dá suporte aos tokens emitidos pela plataforma de identidade da Microsoft, o que o torna uma boa API protegida para usar como exemplo. Nesta seção, você adicionará código para chamar o Microsoft Graph e exibir os emails do usuário na página "Buscar dados" do aplicativo.

Esta seção é escrita usando uma abordagem comum para chamar uma API protegida usando um cliente nomeado. O mesmo método pode ser usado para outras APIs protegidas que você deseja chamar. No entanto, se você planeja chamar o Microsoft Graph do seu aplicativo, pode usar o SDK do Graph para reduzir o texto clichê. Os documentos do .NET contêm instruções sobre [como usar o SDK do Graph](/aspnet/core/blazor/security/webassembly/graph-api?view=aspnetcore-5.0&preserve-view=true).

Antes de começar, faça logoff do seu aplicativo, pois você fará alterações nas permissões necessárias e seu token atual não funcionará. Se você ainda não fez isso, execute o aplicativo novamente e selecione **Fazer logoff** antes de atualizar o código abaixo.

Agora, você atualizará o registro e o código do aplicativo para extrair os emails de um usuário e exibir as mensagens no aplicativo.

Primeiro, adicione a permissão `Mail.Read` de API ao registro do aplicativo para que o Azure AD esteja ciente de que o aplicativo solicitará o acesso ao email dos usuários dele.

1. No portal do Azure, selecione seu aplicativo em **Registros de aplicativo**.
1. Em **Gerenciar**, selecione **Permissões de API**.
1. Selecione **Adicionar uma permissão** > **Microsoft Graph**.
1. Selecione **Permissões Delegadas** e procure e selecione a permissão **Mail.Read**.
1. Escolha **Adicionar permissões**.

Em seguida, adicione os itens a seguir ao arquivo *.csproj* do seu projeto em **ItemGroup** netstandard2.1. Isso permitirá que você crie o HttpClient personalizado na próxima etapa.

```xml
<PackageReference Include="Microsoft.Extensions.Http" Version="3.1.7" />
```

Em seguida, modifique o código conforme especificado nas próximas etapas. Essas alterações adicionarão [tokens de acesso](access-tokens.md) às solicitações de saída enviadas para a API do Microsoft Graph. Esse padrão é abordado mais detalhadamente nos [cenários de segurança adicionais do Blazor WebAssembly do ASP.NET Core](/aspnet/core/blazor/security/webassembly/additional-scenarios).

Primeiro, crie um arquivo chamado *GraphAPIAuthorizationMessageHandler.cs* com o código a seguir. Esse manipulador será o usuário para adicionar um token de acesso para os escopos `User.Read` e `Mail.Read` a solicitações de saída para a API do Microsoft Graph.

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Mail.Read" });
    }
}
```

Em seguida, substitua o conteúdo do método `Main` em *Program.cs* pelo código a seguir. Esse código usa o novo `GraphAPIAuthorizationMessageHandler` e adiciona `User.Read` e `Mail.Read` como escopos padrão que o aplicativo solicitará quando o usuário entrar pela primeira vez.

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
builder.RootComponents.Add<App>("app");

builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("User.Read");
    options.ProviderOptions.DefaultAccessTokenScopes.Add("Mail.Read");
});

await builder.Build().RunAsync();
```

Por fim, substitua o conteúdo da página *FetchData.razor* pelo código a seguir. Esse código busca dados de email do usuário da API do Microsoft Graph e os exibe como uma lista. No `OnInitializedAsync`, o novo `HttpClient` que usa o token de acesso apropriado é criado e usado para fazer a solicitação para a API do Microsoft Graph.

```c#
@page "/fetchdata"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject IHttpClientFactory HttpClientFactory

<p>This component demonstrates fetching data from a service.</p>

@if (messages == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h1>Hello @userDisplayName !!!!</h1>
    <table class="table">
        <thead>
            <tr>
                <th>Subject</th>
                <th>Sender</th>
                <th>Received Time</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var mail in messages)
            {
                <tr>
                    <td>@mail.Subject</td>
                    <td>@mail.Sender</td>
                    <td>@mail.ReceivedTime</td>
                </tr>
            }
        </tbody>
    </table>
}

@code {

    private string userDisplayName;
    private List<MailMessage> messages = new List<MailMessage>();

    private HttpClient _httpClient;

    protected override async Task OnInitializedAsync()
    {
        _httpClient = HttpClientFactory.CreateClient("GraphAPI");
        try {
            var dataRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me");

            if (dataRequest.IsSuccessStatusCode)
            {
                var userData = System.Text.Json.JsonDocument.Parse(await dataRequest.Content.ReadAsStreamAsync());
                userDisplayName = userData.RootElement.GetProperty("displayName").GetString();
            }

            var mailRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me/messages?$select=subject,receivedDateTime,sender&$top=10");

            if (mailRequest.IsSuccessStatusCode)
            {
                var mailData = System.Text.Json.JsonDocument.Parse(await mailRequest.Content.ReadAsStreamAsync());
                var messagesArray = mailData.RootElement.GetProperty("value").EnumerateArray();

                foreach (var m in messagesArray)
                {
                    var message = new MailMessage();
                    message.Subject = m.GetProperty("subject").GetString();
                    message.Sender = m.GetProperty("sender").GetProperty("emailAddress").GetProperty("address").GetString();
                    message.ReceivedTime = m.GetProperty("receivedDateTime").GetDateTime();
                    messages.Add(message);
                }
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            // Tokens are not valid - redirect the user to log in again
            ex.Redirect();
        }
    }

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}
```

Agora, inicie o aplicativo novamente. Você observará que deve fornecer ao aplicativo acesso para ler seu email. Isso é esperado quando um aplicativo solicita o escopo `Mail.Read`.

Depois de dar consentimento, navegue até a página "Buscar dados" para ler alguns emails.

:::image type="content" source="./media/tutorial-blazor-webassembly/final-app.png" alt-text="Captura de tela do aplicativo final. Ele tem um título que diz Olá, Nicholas e mostra uma lista de emails que pertencem a Nicholas.":::

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Melhores práticas e recomendações da plataforma de identidade da Microsoft](./identity-platform-integration-checklist.md)
