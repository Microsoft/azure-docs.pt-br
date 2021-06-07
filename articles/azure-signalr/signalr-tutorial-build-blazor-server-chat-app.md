---
title: 'Tutorial: Criar um aplicativo de chat do Blazor Server – Azure SignalR'
description: Neste tutorial, você aprenderá a criar e modificar um aplicativo Blazor Server com o Serviço do Azure SignalR
author: JialinXin
ms.service: signalr
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jixin
ms.openlocfilehash: a1423e8aaf4b50db94cda0b935a7b1658249893e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726338"
---
# <a name="tutorial-build-a-blazor-server-chat-app"></a>Tutorial: Criar um aplicativo de chat do Blazor Server

Este tutorial mostra como criar e modificar um aplicativo Blazor Server. Você aprenderá a:

> [!div class="checklist"]
> * Criar uma sala de chat simples com um aplicativo Blazor Server.
> * Modificar os componentes do Razor.
> * Usar a manipulação de eventos e a associação de dados em componentes.
> * Implantar rapidamente o Serviço de Aplicativo do Azure no Visual Studio.
> * Migrar o SignalR local para o Serviço do Azure SignalR.

## <a name="prerequisites"></a>Pré-requisitos
* Instalar o [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) (Versão >= 3.0.100)
* Instalar o [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) (Versão >= 16.3)
> A versão prévia do Visual Studio 2019 também funciona e está sendo lançada com o mais recente modelo de aplicativo Blazor Server voltado à versão mais recente do .Net Core.

[Está com problemas? Fale conosco.](https://aka.ms/asrs/qsblazor)

## <a name="build-a-local-chat-room-in-blazor-server-app"></a>Criar uma sala de chat local no aplicativo Blazor Server

No Visual Studio 2019 versão 16.2.0, o Serviço do Azure SignalR é o processo integrado de publicação de aplicativo Web e gerencia as dependências entre o aplicativo Web e o Serviço do SignalR que seria mais conveniente. Você pode experimentar trabalhar no SignalR local no ambiente local de desenvolvimento e, ao mesmo tempo, no Serviço do Azure SignalR para o Serviço de Aplicativo do Azure sem nenhuma alteração de código.

1. Criar um aplicativo Blazor de chat
   
   No Visual Studio, escolha Criar um projeto -> Aplicativo Blazor -> (nomeie o aplicativo e escolha uma pasta) -> Aplicativo Blazor Server. Verifique se você já instalou o SDK do .NET Core 3.0 ou posterior a fim de habilitar o Visual Studio para reconhecer corretamente a estrutura de destino.

   [ ![Em Criar um projeto, os modelos do Aplicativo Blazor são selecionados.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png#lightbox)
   
   Ou execute cmd
   ```dotnetcli
   dotnet new blazorserver -o BlazorChat
   ```
   
1. Adicione um arquivo `BlazorChatSampleHub.cs` para implementar o `Hub` para o chat.
   
   ```cs
   using System;
   using System.Threading.Tasks;
   using Microsoft.AspNetCore.SignalR;
   
   namespace BlazorChat
   {
       public class BlazorChatSampleHub : Hub
       {
           public const string HubUrl = "/chat";
   
           public async Task Broadcast(string username, string message)
           {
               await Clients.All.SendAsync("Broadcast", username, message);
           }
   
           public override Task OnConnectedAsync()
           {
               Console.WriteLine($"{Context.ConnectionId} connected");
               return base.OnConnectedAsync();
           }
   
           public override async Task OnDisconnectedAsync(Exception e)
           {
               Console.WriteLine($"Disconnected {e?.Message} {Context.ConnectionId}");
               await base.OnDisconnectedAsync(e);
           }
       }
   }
   ```
   
1. Adicione um ponto de extremidade para o hub em `Startup.Configure()`.
   
   ```cs
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapBlazorHub();
       endpoints.MapFallbackToPage("/_Host");
       endpoints.MapHub<BlazorChatSampleHub>(BlazorChatSampleHub.HubUrl);
   });
   ```
   
1. Instale o pacote `Microsoft.AspNetCore.SignalR.Client` para usar o cliente SignalR.

   ```dotnetcli
   dotnet add package Microsoft.AspNetCore.SignalR.Client --version 3.1.7
   ```

1. Crie `ChatRoom.razor` na pasta `Pages` para implementar o cliente SignalR. Siga as etapas abaixo ou simplesmente copie o [ChatRoom.razor](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BlazorChat/Pages/ChatRoom.razor).

   1. Adicione o link e a referência da página.
      
      ```razor
      @page "/chatroom"
      @inject NavigationManager navigationManager
      @using Microsoft.AspNetCore.SignalR.Client;
      ```

   1. Adicione o código ao novo cliente SignalR para enviar e receber mensagens.
      
      ```razor
      @code {
          // flag to indicate chat status
          private bool _isChatting = false;
          
          // name of the user who will be chatting
          private string _username;
      
          // on-screen message
          private string _message;
          
          // new message input
          private string _newMessage;
          
          // list of messages in chat
          private List<Message> _messages = new List<Message>();
          
          private string _hubUrl;
          private HubConnection _hubConnection;
      
          public async Task Chat()
          {
              // check username is valid
              if (string.IsNullOrWhiteSpace(_username))
              {
                  _message = "Please enter a name";
                  return;
              };
      
              try
              {
                  // Start chatting and force refresh UI.
                  _isChatting = true;
                  await Task.Delay(1);

                  // remove old messages if any
                  _messages.Clear();
         
                  // Create the chat client
                  string baseUrl = navigationManager.BaseUri;
      
                  _hubUrl = baseUrl.TrimEnd('/') + BlazorChatSampleHub.HubUrl;
      
                  _hubConnection = new HubConnectionBuilder()
                      .WithUrl(_hubUrl)
                      .Build();
      
                  _hubConnection.On<string, string>("Broadcast", BroadcastMessage);
      
                  await _hubConnection.StartAsync();
      
                  await SendAsync($"[Notice] {_username} joined chat room.");
              }
              catch (Exception e)
              {
                  _message = $"ERROR: Failed to start chat client: {e.Message}";
                  _isChatting = false;
              }
          }
      
          private void BroadcastMessage(string name, string message)
          {
              bool isMine = name.Equals(_username, StringComparison.OrdinalIgnoreCase);
      
              _messages.Add(new Message(name, message, isMine));
      
              // Inform blazor the UI needs updating
              StateHasChanged();
          }
      
          private async Task DisconnectAsync()
          {
              if (_isChatting)
              {
                  await SendAsync($"[Notice] {_username} left chat room.");
      
                  await _hubConnection.StopAsync();
                  await _hubConnection.DisposeAsync();
      
                  _hubConnection = null;
                  _isChatting = false;
              }
          }
      
          private async Task SendAsync(string message)
          {
              if (_isChatting && !string.IsNullOrWhiteSpace(message))
              {
                  await _hubConnection.SendAsync("Broadcast", _username, message);
      
                  _newMessage = string.Empty;
              }
          }
      
          private class Message
          {
              public Message(string username, string body, bool mine)
              {
                  Username = username;
                  Body = body;
                  Mine = mine;
              }
      
              public string Username { get; set; }
              public string Body { get; set; }
              public bool Mine { get; set; }
      
              public bool IsNotice => Body.StartsWith("[Notice]");
      
              public string CSS => Mine ? "sent" : "received";
          }
      }
      ```

   1. Adicione parte da renderização antes de `@code` para a interface do usuário interagir com o cliente SignalR.
      
      ```razor
      <h1>Blazor SignalR Chat Sample</h1>
      <hr />
      
      @if (!_isChatting)
      {
          <p>
              Enter your name to start chatting:
          </p>
      
          <input type="text" maxlength="32" @bind="@_username" />
          <button type="button" @onclick="@Chat"><span class="oi oi-chat" aria-hidden="true"></span> Chat!</button>
      
          // Error messages
          @if (_message != null)
          {
              <div class="invalid-feedback">@_message</div>
              <small id="emailHelp" class="form-text text-muted">@_message</small>
          }
      }
      else
      {
          // banner to show current user
          <div class="alert alert-secondary mt-4" role="alert">
              <span class="oi oi-person mr-2" aria-hidden="true"></span>
              <span>You are connected as <b>@_username</b></span>
              <button class="btn btn-sm btn-warning ml-md-auto" @onclick="@DisconnectAsync">Disconnect</button>
          </div>
          // display messages
          <div id="scrollbox">
              @foreach (var item in _messages)
              {
                  @if (item.IsNotice)
                  {
                      <div class="alert alert-info">@item.Body</div>
                  }
                  else
                  {
                      <div class="@item.CSS">
                          <div class="user">@item.Username</div>
                          <div class="msg">@item.Body</div>
                      </div>
                  }
              }
              <hr />
              <textarea class="input-lg" placeholder="enter your comment" @bind="@_newMessage"></textarea>
              <button class="btn btn-default" @onclick="@(() => SendAsync(_newMessage))">Send</button>
          </div>
      }
      ```

1. Atualize `NavMenu.razor` para inserir um menu de entrada para a sala de chat em `NavMenuCssClass` como REST.

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="chatroom">
           <span class="oi oi-chat" aria-hidden="true"></span> Chat room
       </NavLink>
   </li>
   ```
   
1. Atualize `site.css` para otimizá-lo para exibições de bolhas da área de chat. Acrescente o código abaixo ao final.

   ```css
   /* improved for chat text box */
   textarea {
       border: 1px dashed #888;
       border-radius: 5px;
       width: 80%;
       overflow: auto;
       background: #f7f7f7
   }
   
   /* improved for speech bubbles */
   .received, .sent {
       position: relative;
       font-family: arial;
       font-size: 1.1em;
       border-radius: 10px;
       padding: 20px;
       margin-bottom: 20px;
   }
   
   .received:after, .sent:after {
       content: '';
       border: 20px solid transparent;
       position: absolute;
       margin-top: -30px;
   }
   
   .sent {
       background: #03a9f4;
       color: #fff;
       margin-left: 10%;
       top: 50%;
       text-align: right;
   }
   
   .received {
       background: #4CAF50;
       color: #fff;
       margin-left: 10px;
       margin-right: 10%;
   }
   
   .sent:after {
       border-left-color: #03a9f4;
       border-right: 0;
       right: -20px;
   }
   
   .received:after {
       border-right-color: #4CAF50;
       border-left: 0;
       left: -20px;
   }
   
   /* div within bubble for name */
   .user {
       font-size: 0.8em;
       font-weight: bold;
       color: #000;
   }
   
   .msg {
       /*display: inline;*/
   }
   ```

1. Clique em <kbd>F5</kbd> para executar o aplicativo. Você poderá participar do chat como mostrado a seguir.

   [ ![Um chat animado entre Bob e Alice é exibido. Alice diz Olá, Bob diz Oi.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif#lightbox)
   
[Está com problemas? Fale conosco.](https://aka.ms/asrs/qsblazor)

## <a name="publish-to-azure"></a>Publicar no Azure

   Até agora, o Aplicativo Blazor está funcionando no SignalR local e, ao implantar no Serviço de Aplicativo do Azure, é recomendável usar o [Serviço do Azure SignalR](/aspnet/core/signalr/scale#azure-signalr-service), que permite escalar verticalmente um aplicativo Blazor Server para um grande número de conexões simultâneas do SignalR. Além disso, o alcance global do serviço SignalR e os data centers de alto desempenho ajudam significativamente a reduzir a latência devido à geografia.

> [!IMPORTANT]
> No aplicativo Blazor Server, os estados da interface do usuário são mantidos no lado do servidor, o que significa que ela deve aderir ao servidor, nesse caso. Se houver um único servidor de aplicativos, a aderência ao servidor será garantida por design. No entanto, se houver vários servidores de aplicativos, haverá uma chance de que a negociação e a conexão do cliente possam ir para servidores diferentes, o que levaria a erros de interface do usuário no aplicativo Blazor. Portanto, você precisa habilitar a aderência ao servidor, como mostrado abaixo, em `appsettings.json`:
> ```json
> "Azure:SignalR:ServerStickyMode": "Required"
> ```

1. Clique com o botão direito do mouse no projeto e navegue até `Publish`.

   * Destino: Azure
   * Destino específico: há suporte para todos os tipos de **Serviço de Aplicativo do Azure**.
   * Serviço de Aplicativo: crie um serviço de aplicativo ou selecione um existente.

   [ ![A animação mostra a seleção do Azure como o Destino e o Serviço de Aplicativo do Azure como o Destino específico.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif#lightbox)

1. Adicionar dependência do Serviço do Azure SignalR

   Após a criação do perfil de publicação, você poderá ver uma mensagem recomendada em **Dependências do Serviço**. Clique em **Configurar** para criar ou selecionar um Serviço do Azure SignalR existente no painel.

   [ ![Em Publicar, o link para a opção Configurar é realçado.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png#lightbox)

   A dependência do serviço realizará as ações abaixo a fim de permitir que seu aplicativo seja alternado automaticamente para o Serviço do Azure SignalR quando estiver no Azure.

   * Atualizar [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration) para usar o Serviço do Azure SignalR
   * Adicionar a referência do pacote NuGet do Serviço do Azure SignalR.
   * Atualizar as propriedades do perfil para salvar as configurações de dependência.
   * A configuração do repositório de segredos depende de sua escolha.
   * Adicionar a configuração `appsettings` para fazer com que seu aplicativo seja direcionado ao Serviço do Azure SignalR selecionado.

   [ ![Em Resumo das alterações, as caixas de seleção são usadas para selecionar todas as dependências.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png#lightbox)

1. Publicar o aplicativo

   Agora ele está pronto para ser publicado. E pesquisará automaticamente a página após a publicação ser concluída. 
   > [!NOTE]
   > Ele pode não funcionar imediatamente ao visitar a página pela primeira vez devido à latência de inicialização de implantação do Serviço de Aplicativo do Azure. Tente atualizar a página para dar algum tempo.
   > Além disso, você pode usar o modo de depurador do navegador com <kbd>F12</kbd> para validar se o tráfego já foi redirecionado ao Serviço do Azure SignalR.

   [ ![O Exemplo de Chat do SignalR do Blazor tem uma caixa de texto para seu nome e um botão Chat! para iniciar um chat.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png#lightbox)
   
[Está com problemas? Fale conosco.](https://aka.ms/asrs/qsblazor)

## <a name="further-topic-enable-azure-signalr-service-in-local-development"></a>Tópico adicional: habilitar o Serviço do Azure SignalR no desenvolvimento local

1. Adicione referência ao SDK do Azure SignalR.

   ```dotnetcli
   dotnet add package Microsoft.Azure.SignalR --version 1.5.1
   ```

1. Adicione uma chamada para o Serviço do Azure SignalR em `Startup.ConfigureServices()`.

   ```cs
   public void ConfigureServices(IServiceCollection services)
   {
       ...
       services.AddSignalR().AddAzureSignalR();
       ...
   }
   ```

1. Configure `ConnectionString` do Serviço do Azure SignalR no `appsetting.json` ou com a ferramenta de [Gerenciador de Segredos](/aspnet/core/security/app-secrets?tabs=visual-studio#secret-manager).

> [!NOTE]
> A etapa 2 pode ser substituída usando [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration) no SDK do SignalR.
> 
> 1. Adicione configuração para ativar o Serviço do Azure SignalR no `appsetting.json`.
>    ```js
>    "Azure": {
>      "SignalR": {
>        "Enabled": true,
>        "ConnectionString": <your-connection-string>
>      }
>    }
>    ```
> 
> 1. Atribua o assembly de inicialização de hospedagem para usar o SDK do Azure SignalR. Edite `launchSettings.json` e adicione uma configuração como abaixo em `environmentVariables`.
>    ```js
>    "environmentVariables": {
>        ...,
>        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.Azure.SignalR"
>      }
>    ```

[Está com problemas? Fale conosco.](https://aka.ms/asrs/qsblazor)

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar os recursos criados neste tutorial, exclua o grupo de recursos usando o portal do Azure.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma sala de chat simples com um aplicativo Blazor Server.
> * Modificar os componentes do Razor.
> * Usar a manipulação de eventos e a associação de dados em componentes.
> * Implantar rapidamente o Serviço de Aplicativo do Azure no Visual Studio.
> * Migrar o SignalR local para o Serviço do Azure SignalR.

Saiba mais sobre a alta disponibilidade.
> [!div class="nextstepaction"]
> [Resiliência e recuperação de desastre](signalr-concept-disaster-recovery.md)

## <a name="additional-resources"></a>Recursos adicionais

* [ASP.NET Core Blazor](/aspnet/core/blazor)
