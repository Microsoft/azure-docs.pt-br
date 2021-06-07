---
title: Tutorial – Criar um aplicativo Blazor Server que usa a plataforma de identidade da Microsoft para autenticação | Azure
titleSuffix: Microsoft identity platform
description: Neste tutorial, você vai configurar a autenticação usando a plataforma de identidade da Microsoft em um aplicativo Blazor Server.
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: 5a631d9ae7a7d1792e3c4e4a2cbf8281e1168283
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99226010"
---
# <a name="tutorial-create-a-blazor-server-app-that-uses-the-microsoft-identity-platform-for-authentication"></a>Tutorial: Criar um aplicativo Blazor Server que usa a plataforma de identidade da Microsoft para autenticação

Neste tutorial, você criará um aplicativo Blazor Server que conecta os usuários e obtém dados do Microsoft Graph usando a plataforma de identidade da Microsoft e registrando seu aplicativo no Azure AD (Azure Active Directory).

Também temos um tutorial para o [Blazor WASM](tutorial-blazor-webassembly.md).

Neste tutorial:

> [!div class="checklist"]
> * Criar um aplicativo Blazor Server configurado para usar o Azure AD (Azure Active Directory) para autenticação
> * Manipular a autenticação e a autorização usando o Microsoft.Identity.Web
> * Recuperar dados de uma API Web protegida, Microsoft Graph

## <a name="prerequisites"></a>Pré-requisitos

- [SDK do .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1)
- Um locatário do Azure AD no qual você pode registrar um aplicativo. Se você não tiver acesso a um locatário do Azure AD, poderá obter um registrando-se no [Programa para Desenvolvedores do Microsoft 365](https://developer.microsoft.com/microsoft-365/dev-program) ou criando uma [conta gratuita do Azure](https://azure.microsoft.com/free).

## <a name="register-the-app-in-the-azure-portal"></a>Registrar o aplicativo no portal do Azure

Todo aplicativo que usar o Azure AD (Azure Active Directory) para autenticação precisará ser registrado no Azure AD. Siga as instruções em [Registrar um aplicativo](quickstart-register-app.md) com estas adições:

- Para **Tipos de contas com suporte**, selecione **Contas somente neste diretório organizacional**.
- Deixe a lista suspensa **URI de Redirecionamento** definida como **Web** e digite `https://localhost:5001/signin-oidc`. A porta padrão para um aplicativo em execução no Kestrel é 5001. Se o aplicativo estiver disponível em uma porta diferente, especifique o número da porta em vez de `5001`.

Em **Gerenciar**, selecione **Autenticação** > **Concessão implícita e fluxos híbridos**. Selecione **Tokens de acesso** e **Tokens de ID** e escolha **Salvar**.

Por fim, como o aplicativo chama uma API protegida (nesse caso, o Microsoft Graph), ele precisa de um segredo do cliente para verificar a identidade ao solicitar um token de acesso para chamar essa API.

1. No mesmo registro de aplicativo, em **Gerenciar**, selecione **Certificados e segredos**.
2. Crie um **Segredo do cliente** que nunca expira.
3. Anote o **Valor** do segredo, pois você o usará na próxima etapa. Você não poderá acessá-lo novamente depois de sair deste painel. No entanto, você poderá recriá-lo conforme necessário.

## <a name="create-the-app-using-the-net-cli"></a>Criar o aplicativo usando a CLI do .NET

Execute o comando a seguir para baixar os modelos para Microsoft.Identity.Web, que usaremos neste tutorial.

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::0.4.0-preview
```

Em seguida, execute o comando a seguir para criar o aplicativo. Substitua os espaços reservados no comando pelas informações apropriadas da página de visão geral do aplicativo e execute o comando em um shell de comando. A localização de saída especificada com a opção `-o|--output` criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| Espaço reservado   | Nome do portal do Azure       | Exemplo                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | ID do aplicativo (cliente) | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | ID do diretório (locatário)   | `e86c78e2-0000-0000-0000-918e0565a45e` |

Agora, navegue até o novo aplicativo Blazor em seu editor e adicione o segredo do cliente ao arquivo *appsettings.json*, substituindo o texto "secret-from-app-registration".

```json
"ClientSecret": "secret-from-app-registration",
```

## <a name="test-the-app"></a>Testar o aplicativo

Agora você pode criar e executar o aplicativo. Ao executar esse aplicativo de modelo, você precisa especificar a estrutura a ser executada usando --framework. Este tutorial usa o SDK do .NET Core 3.1.

```dotnetcli
dotnet run --framework netcoreapp3.1
```

No navegador, navegue até `https://localhost:5001` e faça logon usando uma conta de usuário do Azure AD para ver o aplicativo em execução.

## <a name="retrieving-data-from-microsoft-graph"></a>Recuperar dados do Microsoft Graph

O [Microsoft Graph](/graph/overview) oferece uma variedade de APIs que fornecem acesso aos dados do Microsoft 365 dos seus usuários. Usando a plataforma de identidade da Microsoft como o provedor de identidade para seu aplicativo, você tem acesso mais fácil a essas informações porque o Microsoft Graph dá suporte direto aos tokens emitidos pela plataforma de identidade da Microsoft. Nesta seção, você adicionará código para exibir os emails do usuário conectado na página "buscar dados" do aplicativo.

Antes de começar, faça logoff do seu aplicativo, pois você fará alterações nas permissões necessárias e seu token atual não funcionará. Se você ainda não fez isso, execute o aplicativo novamente e selecione **Fazer logoff** antes de atualizar o código abaixo.

Agora, você atualizará o registro e o código do aplicativo para extrair o email de um usuário e exibir as mensagens no aplicativo. Para fazer isso, primeiro estenda as permissões de registro do aplicativo no Azure AD para habilitar o acesso aos dados de email. Em seguida, adicione o código ao aplicativo Blazor para recuperar e exibir esses dados em uma das páginas.

1. No portal do Azure, selecione seu aplicativo em **Registros de aplicativo**.
1. Em **Gerenciar**, selecione **Permissões de API**.
1. Selecione **Adicionar uma permissão** > **Microsoft Graph**.
1. Selecione **Permissões Delegadas** e procure e selecione a permissão **Mail.Read**.
1. Escolha **Adicionar permissões**.

No arquivo *appsettings.json*, atualize seu código para que ele busque o token apropriado com as permissões corretas. Adicione "mail.read" após o escopo "user.read" em "DownstreamAPI". Isso vai especificar a quais escopos (ou permissões) o aplicativo solicitará acesso.

```json
"Scopes": "user.read mail.read"
```

Em seguida, atualize o código no arquivo *FetchData.Razor* para recuperar dados de email em vez dos detalhes do clima padrão (aleatório). Substitua o código nesse arquivo pelo seguinte:

```csharp
@page "/fetchdata"

@inject IHttpClientFactory HttpClientFactory
@inject Microsoft.Identity.Web.ITokenAcquisition TokenAcquisitionService

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
        _httpClient = HttpClientFactory.CreateClient();


        // get a token
        var token = await TokenAcquisitionService.GetAccessTokenForUserAsync(new string[] { "User.Read", "Mail.Read" });

        // make API call
        _httpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}

```

Iniciar o aplicativo. Você observará que as permissões adicionadas recentemente são solicitadas, indicando que tudo está funcionando conforme o esperado. Agora, além dos dados básicos do perfil do usuário, o aplicativo vai solicitar acesso aos dados de email.

Depois de dar consentimento, navegue até a página "Buscar dados" para ler alguns emails.

:::image type="content" source="./media/tutorial-blazor-server/final-app-2.png" alt-text="Captura de tela do aplicativo final. Ele tem um título que diz Olá, Nicholas e mostra uma lista de emails que pertencem a Nicholas.":::

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como chamar aplicativos Web que conectam usuários em nossa série de cenários de várias partes:

> [!div class="nextstepaction"]
> [Cenário: Aplicativo Web que conecta usuários](scenario-web-app-sign-user-overview.md)
