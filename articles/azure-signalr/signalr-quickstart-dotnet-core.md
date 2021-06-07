---
title: Início Rápido para aprender a usar o Serviço do Azure SignalR
description: Um início rápido sobre como usar o Serviço Azure SignalR para criar uma sala de chat com aplicativos MVC do ASP.NET Core.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: 3cfcf57f455a5c3b17b794acf2ded66ed2285eff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94873498"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Início Rápido: Criar uma sala de chat usando o Serviço do SignalR

O Serviço Azure SignalR é um serviço do Azure que ajuda os desenvolvedores a criarem facilmente aplicativos Web com recursos em tempo real. Esse serviço foi originalmente baseado em [SignalR para ASP.NET Core 2.1](/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1), mas agora dá suporte a versões posteriores.

Este artigo mostra como usar o Serviço Azure SignalR. Neste início rápido, você criará um aplicativo de chat usando um aplicativo web MVC do ASP.NET Core. Este aplicativo faz uma conexão com o recurso do Serviço Azure SignalR a fim de habilitar atualizações de conteúdo em tempo real. Você hospedará o aplicativo Web localmente e se conectará com vários clientes do navegador. Cada cliente poderá enviar por push as atualizações de conteúdo para todos os outros clientes. 

Você pode usar qualquer editor de código para concluir as etapas deste início rápido. O [Visual Studio Code](https://code.visualstudio.com/) é uma opção disponível nas plataformas Windows, macOS e Linux.

O código para este tutorial está disponível para download no [repositório AzureSignalR-samples do GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Além disso, você pode criar recursos do Azure usados neste início rápido com [Criar um script do Serviço do SignalR](scripts/signalr-cli-create-service.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Instale o [SDK do .NET Core](https://www.microsoft.com/net/download/windows).
* Faça o download ou clone o repositório [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) do GitHub. 

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsnetcore).

## <a name="create-an-azure-signalr-resource"></a>Criar um recurso do Azure SignalR

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsnetcore).

## <a name="create-an-aspnet-core-web-app"></a>Criar um aplicativo Web ASP.NET Core

Nesta seção, você usará a [CLI (interface de linha de comando) do .NET Core](/dotnet/core/tools/) para criar um projeto de aplicativo Web MVC do ASP.NET Core. A vantagem de usar a CLI do .NET Core em relação ao Visual Studio é que ela está disponível nas plataformas Windows, macOS e Linux. 

1. Crie uma pasta para seu projeto. Este início rápido usa a pasta *E:\Testing\chattest*.

2. Na nova pasta, execute o seguinte comando para criar o projeto:

    ```dotnetcli
    dotnet new mvc
    ```

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsnetcore).

## <a name="add-secret-manager-to-the-project"></a>Adicionar o Gerenciador de Segredos ao projeto

Nesta seção, você adicionará a [ferramenta Gerenciador de Segredos](/aspnet/core/security/app-secrets) ao projeto. A ferramenta Gerenciador de Segredos armazena dados confidenciais para o trabalho de desenvolvimento fora da árvore do projeto. Essa abordagem ajuda a impedir o compartilhamento acidental de segredos do aplicativo no código-fonte.

1. Abra seu arquivo *.csproj*. Adicione um elemento `DotNetCliToolReference` para incluir *Microsoft.Extensions.SecretManager.Tools*. Adicione também um elemento `UserSecretsId`, conforme mostrado no código a seguir para *chattest.csproj*, e salve o arquivo.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.2" />
    </ItemGroup>

    </Project>
    ```

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsnetcore).

## <a name="add-azure-signalr-to-the-web-app"></a>Adicionar o Azure SignalR ao aplicativo Web

1. Adicione uma referência ao pacote NuGet `Microsoft.Azure.SignalR`, executando o seguinte comando:

    ```dotnetcli
    dotnet add package Microsoft.Azure.SignalR
    ```

2. Execute o seguinte comando para restaurar pacotes do projeto:

    ```dotnetcli
    dotnet restore
    ```

3. Adicione um segredo chamado *Azure:SignalR:ConnectionString* ao Gerenciador de Segredos. 

    Esse segredo conterá a cadeia de conexão para acessar seu recurso SignalR Service. *Azure:SignalR:ConnectionString* é a chave de configuração padrão que o SignalR procura para estabelecer uma conexão. Substitua o valor no comando a seguir pela cadeia de conexão do recurso Serviço do SignalR.

    Este comando deve ser executado no mesmo diretório do arquivo *.csproj*.

    ```dotnetcli
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"
    ```

    O Gerenciador de Segredos só será usado para testar o aplicativo Web enquanto ele estiver hospedado localmente. Em um tutorial posterior, você implantará o aplicativo Web de chat no Azure. Após a implantação do aplicativo Web no Azure, você usará uma configuração de aplicativo em vez de armazenar a cadeia de conexão com o Gerenciador de Segredos.

    Esse segredo é acessado com a API de Configuração. Um sinal de dois pontos (:) funciona no nome da configuração com a API de Configuração em todas as plataformas com suporte. Consulte [Configuração por ambiente](/dotnet/core/extensions/configuration-providers#environment-variable-configuration-provider).


4. Abra *Startup.cs* e atualize o método `ConfigureServices` para usar o Serviço do Azure SignalR chamando os métodos `AddSignalR()` e `AddAzureSignalR()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR()
                .AddAzureSignalR();
    }
    ```

    Ao não passar um parâmetro para `AddAzureSignalR()`, esse código usa a chave de configuração padrão para a cadeia de conexão do recurso Serviço do SignalR. A chave de configuração padrão é *Azure:SignalR:ConnectionString*.

5. Em *Startup.cs*, atualize o método `Configure` substituindo ele pelo código a seguir.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseRouting();
        app.UseFileServer();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ChatHub>("/chat");
        });
    }
    ```

### <a name="add-a-hub-class"></a>Adicionar uma classe de hub

No SignalR, um hub é um componente fundamental que expõe um conjunto de métodos que pode ser chamado do cliente. Nesta seção, você definirá uma classe de hub com dois métodos:

* `Broadcast`: este método transmite uma mensagem a todos os clientes.
* `Echo`: este método envia uma mensagem de volta para o chamador.

Os dois métodos usam a interface `Clients` fornecida pelo SDK do SignalR do ASP.NET Core. Essa interface fornece acesso a todos os clientes conectados, permitindo que você envie o conteúdo por push aos clientes.

1. No diretório do projeto, adicione uma nova pasta chamada *Hub*. Adicione um novo arquivo de código de hub chamado *ChatHub.cs* à nova pasta.

2. Adicione o código a seguir a *ChatHub.cs* para definir a classe de hub e salve o arquivo.

    Atualize o namespace dessa classe se você usou um nome de projeto diferente de *SignalR.Mvc*.

    ```csharp
    using Microsoft.AspNetCore.SignalR;
    using System.Threading.Tasks;
    
    namespace SignalR.Mvc
    {
        public class ChatHub : Hub
        {
            public Task BroadcastMessage(string name, string message) =>
                Clients.All.SendAsync("broadcastMessage", name, message);
    
            public Task Echo(string name, string message) =>
                Clients.Client(Context.ConnectionId)
                       .SendAsync("echo", name, $"{message} (echo from server)");
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>Adicionar a interface de cliente para o aplicativo Web

A interface do usuário de cliente para este aplicativo de sala de chat será composta por HTML e JavaScript em um arquivo chamado *index.html* no diretório *wwwroot*.

Copie o arquivo *css/site.css* da pasta *wwwroot* do [repositório de exemplos](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot). Substitua o *css/site.css* do seu projeto pelo o que você copiou.

Veja o código principal de *index.html*:

Crie um arquivo no diretório *wwwroot* chamado *index.html*. Em seguida, copie e cole o seguinte HTML no arquivo recém-criado:

```html
<!DOCTYPE html>
<html>
<head>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" rel="stylesheet" />
    <link href="css/site.css" rel="stylesheet" />
    <title>Azure SignalR Group Chat</title>
</head>
<body>
    <h2 class="text-center" style="margin-top: 0; padding-top: 30px; padding-bottom: 30px;">Azure SignalR Group Chat</h2>
    <div class="container" style="height: calc(100% - 110px);">
        <div id="messages" style="background-color: whitesmoke; "></div>
        <div style="width: 100%; border-left-style: ridge; border-right-style: ridge;">
            <textarea id="message"
                      style="width: 100%; padding: 5px 10px; border-style: hidden;"
                      placeholder="Type message and press Enter to send..."></textarea>
        </div>
        <div style="overflow: auto; border-style: ridge; border-top-style: hidden;">
            <button class="btn-warning pull-right" id="echo">Echo</button>
            <button class="btn-success pull-right" id="sendmessage">Send</button>
        </div>
    </div>
    <div class="modal alert alert-danger fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <div>Connection Error...</div>
                    <div><strong style="font-size: 1.5em;">Hit Refresh/F5</strong> to rejoin. ;)</div>
                </div>
            </div>
        </div>
    </div>

    <!--Reference the SignalR library. -->
    <script src="https://cdn.jsdelivr.net/npm/@microsoft/signalr@3.1.8/dist/browser/signalr.min.js"></script>

    <!--Add script to update the page and send messages.-->
    <script type="text/javascript">
        document.addEventListener('DOMContentLoaded', function () {

            const generateRandomName = () =>
                Math.random().toString(36).substring(2, 10);

            let username = generateRandomName();
            const promptMessage = 'Enter your name:';
            do {
                username = prompt(promptMessage, username);
                if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
                    username = '';
                    promptMessage = 'Invalid input. Enter your name:';
                }
            } while (!username)

            const messageInput = document.getElementById('message');
            messageInput.focus();

            function createMessageEntry(encodedName, encodedMsg) {
                var entry = document.createElement('div');
                entry.classList.add("message-entry");
                if (encodedName === "_SYSTEM_") {
                    entry.innerHTML = encodedMsg;
                    entry.classList.add("text-center");
                    entry.classList.add("system-message");
                } else if (encodedName === "_BROADCAST_") {
                    entry.classList.add("text-center");
                    entry.innerHTML = `<div class="text-center broadcast-message">${encodedMsg}</div>`;
                } else if (encodedName === username) {
                    entry.innerHTML = `<div class="message-avatar pull-right">${encodedName}</div>` +
                        `<div class="message-content pull-right">${encodedMsg}<div>`;
                } else {
                    entry.innerHTML = `<div class="message-avatar pull-left">${encodedName}</div>` +
                        `<div class="message-content pull-left">${encodedMsg}<div>`;
                }
                return entry;
            }

            function bindConnectionMessage(connection) {
                var messageCallback = function (name, message) {
                    if (!message) return;
                    var encodedName = name;
                    var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
                    var messageEntry = createMessageEntry(encodedName, encodedMsg);

                    var messageBox = document.getElementById('messages');
                    messageBox.appendChild(messageEntry);
                    messageBox.scrollTop = messageBox.scrollHeight;
                };
                connection.on('broadcastMessage', messageCallback);
                connection.on('echo', messageCallback);
                connection.onclose(onConnectionError);
            }

            function onConnected(connection) {
                console.log('connection started');
                connection.send('broadcastMessage', '_SYSTEM_', username + ' JOINED');
                document.getElementById('sendmessage').addEventListener('click', function (event) {
                    if (messageInput.value) {
                        connection.send('broadcastMessage', username, messageInput.value);
                    }

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
                    connection.send('echo', username, messageInput.value);

                    messageInput.value = '';
                    messageInput.focus();
                    event.preventDefault();
                });
            }

            function onConnectionError(error) {
                if (error && error.message) {
                    console.error(error.message);
                }
                var modal = document.getElementById('myModal');
                modal.classList.add('in');
                modal.style = 'display: block;';
            }

            const connection = new signalR.HubConnectionBuilder()
                .withUrl('/chat')
                .build();
            bindConnectionMessage(connection);
            connection.start()
                .then(() => onConnected(connection))
                .catch(error => console.error(error.message));
        });
    </script>
</body>
</html>
```

O código em *index.html*, chama `HubConnectionBuilder.build()` para fazer uma conexão HTTP com o recurso Azure SignalR.

Se a conexão for bem-sucedida, essa conexão será passada para `bindConnectionMessage`, o que adiciona manipuladores de eventos aos envios por push do conteúdo de entrada para o cliente. 

`HubConnection.start()` inicia a comunicação com o hub. Em seguida, o `onConnected()` adiciona os manipuladores de evento do botão. Esses manipuladores usam a conexão para permitir que esse cliente envie por push as atualizações de conteúdo a todos os clientes conectados.

## <a name="add-a-development-runtime-profile"></a>Adicionar um perfil de runtime de desenvolvimento

Nesta seção, você adicionará um ambiente de runtime de desenvolvimento para o ASP.NET Core. Para saber mais, confira [Trabalhar com vários ambientes no ASP.NET Core](/aspnet/core/fundamentals/environments).

1. Crie uma pasta chamada *Propriedades* em seu projeto.

2. Adicione um novo arquivo chamado *launchSettings.json* à pasta com o conteúdo a seguir e salve o arquivo.

    ```json
    {
        "profiles" : {
            "ChatRoom": {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsnetcore).

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Para criar o aplicativo usando a CLI do .NET Core, execute o seguinte comando no shell de comando:

    ```dotnetcli
    dotnet build
    ```

1. Depois que a compilação for concluída com êxito, execute o seguinte comando para executar o aplicativo Web localmente:

    ```dotnetcli
    dotnet run
    ```

    O aplicativo será hospedado localmente na porta 5000, conforme configurado em nosso perfil de runtime de desenvolvimento:

    ```output
    info: Microsoft.Hosting.Lifetime[0]
          Now listening on: https://localhost:5001
    info: Microsoft.Hosting.Lifetime[0]
          Now listening on: http://localhost:5000
    info: Microsoft.Hosting.Lifetime[0]
          Application started. Press Ctrl+C to shut down.
    info: Microsoft.Hosting.Lifetime[0]
          Hosting environment: Development
    info: Microsoft.Hosting.Lifetime[0]
          Content root path: E:\Testing\chattest
    ```

1. Abra duas janelas do navegador. Em cada navegador, acesse `http://localhost:5000`. Você receberá uma solicitação para inserir seu nome. Insira um nome de cliente para os dois clientes e teste o envio por push do conteúdo da mensagem entre eles usando o botão **Enviar**.

    ![Exemplo de um chat de grupo do Azure SignalR](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsnetcore).

## <a name="clean-up-resources"></a>Limpar os recursos

Se for prosseguir para o próximo tutorial, mantenha os recursos criados neste início rápido e reutilize-os.

Se já não for usar o aplicativo de exemplo do início rápido, exclua os recursos do Azure criados neste início rápido para evitar encargos. 

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível e incluirá todos os recursos no grupo. Não exclua acidentalmente grupo de recursos ou recursos incorretos. Se tiver criado os recursos para hospedar este exemplo dentro de um grupo de recursos existente que contém recursos que você quer manter, exclua cada recurso individualmente de suas folhas, em vez de excluir o grupo de recursos.

Entre no [portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.

Na caixa de texto **Filtrar por nome**, digite o nome de seu grupo de recursos. As instruções deste início rápido usaram um grupo de recursos chamado *SignalRTestResources*. Em seu grupo de recursos, na lista de resultados, escolha as reticências ( **...** ) > **Excluir grupo de recursos**.

![Seleções para excluir um grupo de recursos](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Você receberá uma solicitação para confirmar a exclusão do grupo de recursos. Insira o nome do grupo de recursos para confirmar e selecione **Excluir**.

Após alguns instantes, o grupo de recursos, e todos os recursos nele são excluídos.

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsnetcore).

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um novo recurso do Serviço Azure SignalR. Em seguida, usou esse recurso com um aplicativo Web do ASP.NET Core para enviar por push as atualizações de conteúdo em tempo real para vários clientes conectados. Para saber mais sobre como usar o Serviço Azure SignalR, continue no tutorial, que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Autenticação do Serviço Azure SignalR](./signalr-concept-authenticate-oauth.md)
