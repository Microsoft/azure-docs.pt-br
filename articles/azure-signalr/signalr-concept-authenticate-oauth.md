---
title: Guia para autenticar clientes do Serviço do Azure SignalR
description: Saiba como implementar sua própria autenticação e integrá-la ao Serviço do Azure SignalR seguindo o exemplo de E2E.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: cd5b709843ba2d98170aaa58eb5c3cc1ec8883af
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734674"
---
# <a name="azure-signalr-service-authentication"></a>autenticação do Serviço do Azure SignalR

Este tutorial baseia-se no aplicativo de sala de bate-papo introduzido no início rápido. Se você não tiver completado [Crie uma sala de chat com SignalR Service](signalr-quickstart-dotnet-core.md), conclua esse exercício primeiro.

Neste tutorial, você aprenderá como implementar sua própria autenticação e integrá-la ao serviço do Microsoft Azure SignalR.

A autenticação inicialmente usada no aplicativo de sala de chat do início rápido é muito simples para cenários do mundo real. O aplicativo permite que cada cliente de declare quem é e o servidor aceita. Esta abordagem não é muito útil para aplicativos do mundo real, onde um usuário não autorizado poderia representar outros usuários para acessar dados confidenciais.

O [GitHub](https://github.com/) fornece APIs de autenticação com base em um protocolo padrão da indústria popular chamado [OAuth](https://oauth.net/). Essas APIs permitem que aplicativos de terceiros autenticam contas do GitHub. Neste tutorial, você usará essas APIs para implementar a autenticação por meio de uma conta do GitHub antes de permitir que o cliente faça logon no aplicativo da sala de chat. Depois de autenticar uma conta do GitHub, as informações da conta serão adicionadas como um cookie a ser usado pelo cliente web para autenticar.

Para obter mais informações sobre a autenticação das APIs OAuth fornecidas pelo GitHub, consulte [Noções básicas de autenticação](https://developer.github.com/v3/guides/basics-of-authentication/).

Você pode usar qualquer editor de código para concluir as etapas deste início rápido. No entanto, o [Visual Studio Code](https://code.visualstudio.com/) é uma opção excelente nas plataformas Windows, macOS e Linux.

O código para este tutorial está disponível para download no [repositório AzureSignalR-samples do GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/GitHubChat).

![OAuth Complete hospedado no Azure](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Registrar um novo aplicativo OAuth com sua conta do GitHub
> * Adicionar um controlador de autenticação para oferecer suporte à autenticação do GitHub
> * Implantar seu aplicativo da web ASP.NET Core ao Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o tutorial, você deve ter os pré-requisitos a seguir:

- Uma conta criada em [GitHub](https://github.com/)
- [Git](https://git-scm.com/)
- [SDK do .NET Core](https://www.microsoft.com/net/download/windows)
- [Azure cloud Shell](../cloud-shell/quickstart.md) configurado para o ambiente bash.
- Faça o download ou clone o repositório [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) do GitHub.

## <a name="create-an-oauth-app"></a>Crie um aplicativo OAuth

1. Abra um navegador da web e navegue até `https://github.com` e entre em sua conta.

2. Sua conta, navegue até **Configurações** > **Configurações do desenvolvedor** e clique em **Registrar um novo aplicativo**, ou **novo OAuth App** nos aplicativos *OAuth*.

3. Use as seguintes configurações para o novo aplicativo do OAuth e clique em **registrar aplicativo**:

    | Nome da configuração | Valor sugerido | Descrição |
    | ------------ | --------------- | ----------- |
    | Nome do aplicativo | *Azure SignalR Chat* | O usuário do GitHub deve ser capaz de reconhecer e confiar no aplicativo com o qual está autenticando.   |
    | URL da homepage | `http://localhost:5000/home` | |
    | Descrição do aplicativo | *Um exemplo de sala de chat usando o Serviço do Azure SignalR com a autenticação do GitHub* | Uma descrição útil do aplicativo que ajuda os usuários de aplicativos a compreender o contexto de autenticação que está sendo usado. |
    | URL de retorno de chamada da autorização | `http://localhost:5000/signin-github` | Essa configuração é a configuração mais importante para seu aplicativo OAuth. É a URL de retorno de chamada GitHub retorna ao usuário após uma autenticação bem-sucedida. Neste tutorial, você deve usar a URL de retorno de chamada padrão para o *pacote AspNet.Security.OAuth.GitHub* pacote, */signin-github*.  |

4. Quando o novo registro de aplicativo OAuth for concluído, adicione a *ID do cliente* e *Segredo do cliente* ao Gerenciador de segredo usando os seguintes comandos. Substituir *Your_GitHub_Client_Id* e *Your_GitHub_Client_Secret* pelos valores para seu aplicativo OAuth.

    ```dotnetcli
    dotnet user-secrets set GitHubClientId Your_GitHub_Client_Id
    dotnet user-secrets set GitHubClientSecret Your_GitHub_Client_Secret
    ```

## <a name="implement-the-oauth-flow"></a>Implementar o fluxo de OAuth

### <a name="update-the-startup-class-to-support-github-authentication"></a>Atualizar a classe de inicialização para oferecer suporte à autenticação do GitHub

1. Adicione uma referência aos pacotes mais recentes do *Microsoft.AspNetCore.Authentication.Cookies* e do *AspNet.Security.OAuth.GitHub* e restaure todos os pacotes.

    ```dotnetcli
    dotnet add package Microsoft.AspNetCore.Authentication.Cookies -v 2.1.0-rc1-30656
    dotnet add package AspNet.Security.OAuth.GitHub -v 2.0.0-rc2-final
    dotnet restore
    ```

1. Abra *Startup.cs* e adicione `using` instruções para os namespaces a seguir:

    ```csharp
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Security.Claims;
    using Microsoft.AspNetCore.Authentication.Cookies;
    using Microsoft.AspNetCore.Authentication.OAuth;
    using Newtonsoft.Json.Linq;
    ```

2. Na parte superior da `Startup` classe, adicione constantes para as chaves de segredo Manager que mantenha os segredos do aplicativo GitHub OAuth.

    ```csharp
    private const string GitHubClientId = "GitHubClientId";
    private const string GitHubClientSecret = "GitHubClientSecret";
    ```

3. Adicione o seguinte código para o `ConfigureServices` método para oferecer suporte à autenticação com o aplicativo GitHub OAuth:

    ```csharp
    services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
        .AddCookie()
        .AddGitHub(options =>
        {
            options.ClientId = Configuration[GitHubClientId];
            options.ClientSecret = Configuration[GitHubClientSecret];
            options.Scope.Add("user:email");
            options.Events = new OAuthEvents
            {
                OnCreatingTicket = GetUserCompanyInfoAsync
            };
        });
    ```

4. Adicione o método `GetUserCompanyInfoAsync` auxiliar à `Startup` classe.

    ```csharp
    private static async Task GetUserCompanyInfoAsync(OAuthCreatingTicketContext context)
    {
        var request = new HttpRequestMessage(HttpMethod.Get, context.Options.UserInformationEndpoint);
        request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", context.AccessToken);

        var response = await context.Backchannel.SendAsync(request,
            HttpCompletionOption.ResponseHeadersRead, context.HttpContext.RequestAborted);

        var user = JObject.Parse(await response.Content.ReadAsStringAsync());
        if (user.ContainsKey("company"))
        {
            var company = user["company"].ToString();
            var companyIdentity = new ClaimsIdentity(new[]
            {
                new Claim("Company", company)
            });
            context.Principal.AddIdentity(companyIdentity);
        }
    }
    ```

5. Atualize o `Configure` método da inicialização de classe com a seguinte linha de código e salve o arquivo.

    ```csharp
    app.UseAuthentication();
    ```

### <a name="add-an-authentication-controller"></a>Adicione um controlador de autenticação

Nesta seção, você irá implementar um `Login` API que autentica clientes usando o aplicativo GitHub OAuth. Uma vez autenticada, a API adicionará um cookie para a resposta de cliente da web antes de redirecionar o cliente de volta para o aplicativo de chat. Esse cookie será usado para identificar o cliente.

1. Adicionar um novo arquivo de código do controlador ao diretório *chattest\controladores*. Nomeie o arquivo *AuthController.cs*.

2. Adicione o seguinte código para o controlador de autenticação. Certifique-se de atualizar o namespace, se o diretório do projeto não foi *chattest*:

    ```csharp
    using AspNet.Security.OAuth.GitHub;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.AspNetCore.Mvc;

    namespace chattest.Controllers
    {
        [Route("/")]
        public class AuthController : Controller
        {
            [HttpGet("login")]
            public IActionResult Login()
            {
                if (!User.Identity.IsAuthenticated)
                {
                    return Challenge(GitHubAuthenticationDefaults.AuthenticationScheme);
                }

                HttpContext.Response.Cookies.Append("githubchat_username", User.Identity.Name);
                HttpContext.SignInAsync(User);
                return Redirect("/");
            }
        }
    }
    ```

3. Salve suas alterações.

### <a name="update-the-hub-class"></a>Atualizar a classe de Hub

Por padrão quando um cliente web tenta se conectar ao SignalR Service, a conexão é concedida com base em um token de acesso que é fornecido internamente. Esse token de acesso não está associado a uma identidade autenticada. Esse acesso é acesso realmente anônimo.

Nesta seção, você ativará autenticação real, adicionando o `Authorize` atributo à classe hub e atualizando os métodos de hub para ler o nome de usuário de declaração do usuário autenticado.

1. Abra *Hub\Chat.cs* e adicione referências para esses namespaces:

    ```csharp
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    ```

2. Atualize o código de hub, conforme mostrado abaixo. Esse código adiciona a `Authorize` de atributo para a `Chat` classe e usa a identidade do usuário autenticado nos métodos de hub. Além disso, o `OnConnectedAsync` método é adicionado, o que registrará uma mensagem do sistema na sala de chat sempre que um novo cliente se conecta.

    ```csharp
    [Authorize]
    public class Chat : Hub
    {
        public override Task OnConnectedAsync()
        {
            return Clients.All.SendAsync("broadcastMessage", "_SYSTEM_", $"{Context.User.Identity.Name} JOINED");
        }

        // Uncomment this line to only allow user in Microsoft to send message
        //[Authorize(Policy = "Microsoft_Only")]
        public void BroadcastMessage(string message)
        {
            Clients.All.SendAsync("broadcastMessage", Context.User.Identity.Name, message);
        }

        public void Echo(string message)
        {
            var echoMessage = $"{message} (echo from server)";
            Clients.Client(Context.ConnectionId).SendAsync("echo", Context.User.Identity.Name, echoMessage);
        }
    }
    ```

3. Salve suas alterações.

### <a name="update-the-web-client-code"></a>Atualize o código de cliente da web

1. Abra *wwwroot\index.html* e substitua o código que solicita o nome de usuário com o código para usar o cookie retornado pelo controlador de autenticação.

    Remova o seguinte código de *index.html*:

    ```javascript
    // Get the user name and store it to prepend to messages.
    var username = generateRandomName();
    var promptMessage = 'Enter your name:';
    do {
        username = prompt(promptMessage, username);
        if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
            username = '';
            promptMessage = 'Invalid input. Enter your name:';
        }
    } while(!username)
    ```

    Adicione o seguinte código em vez do código acima para usar o cookie:

    ```javascript
    // Get the user name cookie.
    function getCookie(key) {
        var cookies = document.cookie.split(';').map(c => c.trim());
        for (var i = 0; i < cookies.length; i++) {
            if (cookies[i].startsWith(key + '=')) return unescape(cookies[i].slice(key.length + 1));
        }
        return '';
    }
    var username = getCookie('githubchat_username');
    ```

2. Apenas sob a linha de código que você adicionou ao usar o cookie, adicione a seguinte definição para o `appendMessage` função:

    ```javascript
    function appendMessage(encodedName, encodedMsg) {
        var messageEntry = createMessageEntry(encodedName, encodedMsg);
        var messageBox = document.getElementById('messages');
        messageBox.appendChild(messageEntry);
        messageBox.scrollTop = messageBox.scrollHeight;
    }
    ```

3. Atualização as funções de `bindConnectionMessage` e `onConnected` com o código a seguir para usar `appendMessage`.

    ```javascript
    function bindConnectionMessage(connection) {
        var messageCallback = function(name, message) {
            if (!message) return;
            // Html encode display name and message.
            var encodedName = name;
            var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
            appendMessage(encodedName, encodedMsg);
        };
        // Create a function that the hub can call to broadcast messages.
        connection.on('broadcastMessage', messageCallback);
        connection.on('echo', messageCallback);
        connection.onclose(onConnectionError);
    }

    function onConnected(connection) {
        console.log('connection started');
        document.getElementById('sendmessage').addEventListener('click', function (event) {
            // Call the broadcastMessage method on the hub.
            if (messageInput.value) {
                connection
                    .invoke('broadcastMessage', messageInput.value)
                    .catch(e => appendMessage('_BROADCAST_', e.message));
            }

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
        document.getElementById('message').addEventListener('keypress', function (event) {
            if (event.keyCode === 13) {
                event.preventDefault();
                document.getElementById('sendmessage').click();
                return false;
            }
        });
        document.getElementById('echo').addEventListener('click', function (event) {
            // Call the echo method on the hub.
            connection.send('echo', messageInput.value);

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
    }
    ```

4. Na parte inferior de *index.html*, atualize o manipulador de erro para `connection.start()` conforme mostrado abaixo para solicitar ao usuário para fazer logon.

    ```javascript
    connection.start()
        .then(function () {
            onConnected(connection);
        })
        .catch(function (error) {
            if (error) {
                if (error.message) {
                    console.error(error.message);
                }
                if (error.statusCode && error.statusCode === 401) {
                    appendMessage('_BROADCAST_', 'You\'re not logged in. Click <a href="/login">here</a> to login with GitHub.');
                }
            }
        });
    ```

5. Salve suas alterações.

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Salve as alterações em todos os arquivos.

2. Compilar o aplicativo usando a CLI do .NET Core, execute o seguinte comando no shell de comando:

    ```dotnetcli
    dotnet build
    ```

3. Após a compilação, execute o seguinte comando para executar o aplicativo Web localmente:

    ```dotnetcli
    dotnet run
    ```

    Por padrão, o aplicativo será hospedado localmente na porta 5000:

    ```output
    E:\Testing\chattest>dotnet run
    Hosting environment: Production
    Content root path: E:\Testing\chattest
    Now listening on: http://localhost:5000
                    Application started. Press Ctrl+C to shut down.
    ```

4. Inicie uma janela de navegador e navegue até `http://localhost:5000`. Clique no link **aqui** na parte superior para fazer logon com GitHub.

    ![OAuth Complete hospedado no Azure](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

    Você será solicitado a autorizar o acesso do aplicativo chat para sua conta do GitHub. Clique no botão **Autorizar**.

    ![Autorizar o aplicativo OAuth](media/signalr-concept-authenticate-oauth/signalr-authorize-oauth-app.png)

    Você será redirecionado de volta para o aplicativo de chat e conectado com seu nome de conta do GitHub. O aplicativo web determinou o nome da conta ao autenticar usando a nova autenticação que você adicionou.

    ![Conta identificada](media/signalr-concept-authenticate-oauth/signalr-oauth-account-identified.png)

    Agora que o aplicativo de chat realiza a autenticação com o GitHub e armazena as informações de autenticação como cookies, você deve implantá-lo no Azure para que outros usuários possam autenticar com suas contas e comunicar-se a partir de outras estações de trabalho.

## <a name="deploy-the-app-to-azure"></a>Implantar o aplicativo no Azure

Preparar o ambiente para a CLI do Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Nesta seção, você usará a CLI (interface de linha de comando) do Azure para criar um novo aplicativo Web no [serviço Azure app](../app-service/index.yml) para hospedar seu aplicativo ASP.net no Azure. O aplicativo web será configurado para usar a implantação local do Git. O aplicativo web também será configurado com a cadeia de caracteres de conexão do SignalR, segredos do aplicativo GitHub OAuth e um usuário de implantação.

Ao criar os recursos a seguir, certifique-se de usar o mesmo grupo de recursos que o recurso SignalR Service reside. Essa abordagem fará limpar muito mais tarde quando você desejar remover todos os recursos. Os exemplos fornecidos supõe que você usou o nome de grupo nos tutorais anteriores, *SignalRTestResources*.

### <a name="create-the-web-app-and-plan"></a>Criar o aplicativo web e o plano

Copie o texto para os comandos abaixo e atualize os parâmetros. Cole o script atualizado no Azure Cloud Shell e pressione **Enter** para criar um novo aplicativo de web e de plano de serviço de aplicativo.

```azurecli-interactive
#========================================================================
#=== Update these variable for your resource group name.              ===
#========================================================================
ResourceGroupName=SignalRTestResources

#========================================================================
#=== Update these variable for your web app.                          ===
#========================================================================
WebAppName=myWebAppName
WebAppPlan=myAppServicePlanName

# Create an App Service plan.
az appservice plan create --name $WebAppPlan --resource-group $ResourceGroupName \
    --sku FREE

# Create the new Web App
az webapp create --name $WebAppName --resource-group $ResourceGroupName \
    --plan $WebAppPlan
```

| Parâmetro | Descrição |
| -------------------- | --------------- |
| ResourceGroupName | Este nome de grupo de recursos foi sugerido nos tutoriais anteriores. É recomendável manter todos os recursos de tutorial agrupados juntos. Use o mesmo nome de grupo de recursos que é usado na seção anterior. |
| WebAppPlan | Insira um nome de Plano do Serviço de Aplicativo nova e exclusivo. |
| WebAppName | Esse será o nome do novo aplicativo web e parte da URL. Use um nome exclusivo. Por exemplo, signalrtestwebapp22665120.   |

### <a name="add-app-settings-to-the-web-app"></a>Adicionar configurações de aplicativo para o aplicativo Web

Nesta seção, você irá adicionar configurações de aplicativo para os seguintes componentes:

* Cadeia de caracteres de conexão do recurso SignalR Service
* ID do cliente de aplicativo GitHub OAuth
* Segredo do cliente do aplicativo GitHub OAuth

Copie o texto para os comandos abaixo e atualize os parâmetros. Cole o script atualizado no Azure Cloud Shell e aperte **Enter** para adicionar as configurações do aplicativo:

```azurecli-interactive
#========================================================================
#=== Update these variables for your GitHub OAuth App.                ===
#========================================================================
GitHubClientId=1234567890
GitHubClientSecret=1234567890

#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
SignalRServiceResource=mySignalRresourcename
WebAppName=myWebAppName

# Get the SignalR primary connection string 
primaryConnectionString=$(az signalr key list --name $SignalRServiceResource \
  --resource-group $ResourceGroupName --query primaryConnectionString -o tsv)

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "Azure__SignalR__ConnectionString=$primaryConnectionString"

#Add the app settings to use with GitHub authentication
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientId=$GitHubClientId"
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientSecret=$GitHubClientSecret"
```

| Parâmetro | Descrição |
| -------------------- | --------------- |
| GitHubClientId | Atribua a Id do cliente secreto dessa variável para o aplicativo GitHub OAuth. |
| GitHubClientSecret | Atribua a esta variável a senha secreta para o seu aplicativo GitHub OAuth. |
| ResourceGroupName | Atualize esta variável para ser o mesmo nome do grupo de recurso usado na seção anterior. |
| SignalRServiceResource | Atualize essa variável com o nome do recurso de serviço SignalR criado no início rápido. Por exemplo, signalrtestsvc48778624. |
| WebAppName | Atualize essa variável com o nome do novo aplicativo web que você criou na seção anterior. |

### <a name="configure-the-web-app-for-local-git-deployment"></a>Configurar o aplicativo Web para implantação local do Git

No Azure Cloud Shell, cole o script a seguir. Esse script cria um novo nome de usuário de implantação e uma senha que será usada ao implantar seu código para o aplicativo Web com o Git. O script também configura o aplicativo Web para implantação com um repositório Git local e retorna a URL de implantação do Git.

```azurecli-interactive
#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
WebAppName=myWebAppName

#========================================================================
#=== Update these variables for your deployment user.                 ===
#========================================================================
DeploymentUserName=myUserName
DeploymentUserPassword=myPassword

# Add the desired deployment user name and password
az webapp deployment user set --user-name $DeploymentUserName \
    --password $DeploymentUserPassword

# Configure Git deployment and note the deployment URL in the output
az webapp deployment source config-local-git --name $WebAppName \
    --resource-group $ResourceGroupName \
    --query [url] -o tsv
```

| Parâmetro | Descrição |
| -------------------- | --------------- |
| DeploymentUserName | Escolha um novo nome de usuário de implantação. |
| DeploymentUserPassword | Escolha uma senha para o novo usuário de implantação. |
| ResourceGroupName | Use o mesmo nome de grupo de recursos que é usado na seção anterior. |
| WebAppName | Esse será o nome do novo aplicativo web criado anteriormente. |

Anote a URL de implantação do Git retornada deste comando. Você usará essa URL mais tarde.

### <a name="deploy-your-code-to-the-azure-web-app"></a>Implantar seu código no novo aplicativo web do Azure

Para implantar seu código, execute os seguintes comandos em um shell do Git.

1. Navegue até raiz do diretório do projeto. Se você não tiver o projeto inicializado com um repositório Git, execute o seguinte comando:

    ```bash
    git init
    ```

2. Adicione um controle remoto para a URL de implantação do Git que você anotou anteriormente:

    ```bash
    git remote add Azure <your git deployment url>
    ```

3. Prepare todos os arquivos no repositório inicializado e adicione uma confirmação.

    ```bash
    git add -A
    git commit -m "init commit"
    ```

4. Implante seu código no novo aplicativo Web do Azure.

    ```bash
    git push Azure main
    ```

    Você deverá se autenticar para implantar o código no Azure. Insira o nome de usuário e a senha do usuário de implantação criado acima.

### <a name="update-the-github-oauth-app"></a>Atualizar o aplicativo GitHub OAuth

A última coisa que você precisa fazer é atualizar a **URL da home page** e a **URL de retorno de chamada de autorização** do aplicativo GitHub OAuth para apontar para o ponto do novo aplicativo hospedado.

1. Abra [https://github.com](https://github.com) em um navegador e navegue até as **configurações da Conta** > **Configurações do desenvolvedor** > **Aplicativos Oauth**.

2. Clique no aplicativo de autenticação e atualize a **URL da home page** e a **URL de retorno de chamada de autorização** conforme mostrado abaixo:

    | Configuração | Exemplo |
    | ------- | ------- |
    | URL da homepage | `https://signalrtestwebapp22665120.azurewebsites.net/home` |
    | URL de retorno de chamada da autorização | `https://signalrtestwebapp22665120.azurewebsites.net/signin-github` |

3. Navegue até a URL do aplicativo web e teste o aplicativo.

    ![OAuth Complete hospedado no Azure](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se você for continuar no próximo tutorial, mantenha os recursos criados neste início rápido e reutilize-os no tutorial a seguir.

Caso contrário, se você não for mais usar o aplicativo de exemplo do início rápido, exclua os recursos do Azure criados neste início rápido para evitar encargos.

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível, e o grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. Se tiver criado os recursos para hospedar este exemplo dentro de um grupo de recursos existente que contém recursos que você quer manter, exclua cada recurso individualmente de suas respectivas folhas, em vez de excluir o grupo de recursos.

Entre no [portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

Na caixa de texto **Filtrar por nome...** , digite o nome do seu grupo de recursos. As instruções deste artigo usaram um grupo de recursos chamado *SignalRTestResources*. Em seu grupo de recursos, na lista de resultados, clique em **...** , depois em **Excluir grupo de recursos**.

![Excluir](./media/signalr-concept-authenticate-oauth/signalr-delete-resource-group.png)

Você receberá uma solicitação para confirmar a exclusão do grupo de recursos. Digite o nome do grupo de recursos para confirmar e clique em **Excluir**.

Após alguns instantes, o grupo de recursos, e todos os recursos contidos nele, serão excluídos.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você adicionou a autenticação com o OAuth para fornecer uma abordagem melhor para autenticação com o serviço do Azure SignalR. Para saber mais sobre como usar o Servidor do Azure SignalR, continue para os exemplos de CLI do Azure para o Serviço SignalR.

> [!div class="nextstepaction"]
> [Exemplos de CLI do Azure SignalR](./signalr-reference-cli.md)