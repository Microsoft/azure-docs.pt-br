---
title: Arquivo de inclusão
description: Inclua o arquivo que contém o código para criar um projeto de back-end WebAPI ASP .NET.
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 590ba4b7a61fa437767d99ac6b9ae3e0fa94edc3
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227784"
---
## <a name="create-the-webapi-project"></a>Criar o projeto WebAPI

As seções a seguir abordam a criação de um novo back-end WebAPI ASP.NET. Esse processo tem três objetivos principais:

- **Autenticar clientes**: adicione um manipulador de mensagens para autenticar solicitações de clientes e associar o usuário à solicitação.
- **Registrar para receber notificações usando o back-end da WebAPI**: adicione um controlador para manipular novos registros para um dispositivo cliente receber notificações. O nome de usuário autenticado é automaticamente adicionado ao registro como uma [marca](../articles/notification-hubs/notification-hubs-tags-segment-push-message.md).
- **Enviar notificações aos clientes**: adicione um controlador para permitir aos usuários disparar um envio por push seguro para dispositivos e clientes associados à marca.

Crie um novo back-end WebAPI ASP.NET executando estas ações:

> [!IMPORTANT]
> Se você estiver usando o Visual Studio 2015 ou anterior, antes de iniciar este tutorial, instale a versão mais recente do Gerenciador de Pacotes NuGet para Visual Studio.
>
>Para verificar, inicie o Visual Studio. No menu **Ferramentas**, selecione **Extensões e Atualizações**. Pesquise por **Gerenciador de Pacotes NuGet** na sua versão do Visual Studio e verifique se a versão mais recente está instalada. Se a versão não for a versão mais recente, desinstale-a e reinstale o Gerenciador de Pacotes NuGet.

![][B4]

> [!NOTE]
> Verifique se você instalou o [SDK do Azure](https://azure.microsoft.com/downloads/) do Visual Studio para implantação de site.

1. Inicie o Visual Studio ou o Visual Studio Express.

2. Selecione **Gerenciador de Servidores** e entre na sua conta do Azure. Para criar os recursos de site na sua conta, você precisará estar conectado.

3. No Visual Studio, clique com o botão direito na solução do Visual Studio, aponte para **Adicionar** e clique em **Novo Projeto**.
4. Expanda **Visual C#** , selecione **Web** e clique em **Aplicativo Web do ASP.NET**.

5. Na caixa **Nome** , digite **AppBackend**e selecione **OK**.

    ![A janela Novo Projeto][B1]

6. Na janela **Novo Projeto ASP.NET**, marque a caixa de seleção **Web API** e selecione **OK**.

    ![A janela Novo Projeto ASP.NET][B2]

7. Na janela **Configurar o Aplicativo Web do Microsoft Azure**, selecione uma assinatura e, na lista **Plano do Serviço de Aplicativo**, execute uma destas ações:

    * Selecione um plano do serviço de aplicativo que você criou.
    * Selecione **Criar um novo plano do serviço de aplicativo** para criar um.

   Não é necessário um banco de dados para este tutorial. Depois que você tiver selecionado o seu plano de serviço de aplicativo, selecione **OK** para criar o projeto.

    ![A janela do aplicativo Web do Microsoft Azure][B5]

    Se você não vir essa página para configurar o plano do serviço de aplicativo, continue com o tutorial. Você pode configurá-la ao publicar o aplicativo mais tarde. 

## <a name="authenticate-clients-to-the-webapi-backend"></a>Autenticar clientes para o back-end da WebAPI

Nesta seção, você cria uma nova classe de manipulador de mensagens denominada **AuthenticationTestHandler** para o novo back-end. Essa classe é derivada de [DelegatingHandler](https://msdn.microsoft.com/library/system.net.http.delegatinghandler.aspx) e adicionada como um manipulador de mensagens para poder processar todas as solicitações que chegam ao back-end.

1. No Gerenciador de Soluções, clique com botão direito do mouse no projeto **AppBackend**, selecione **Adicionar**e selecione **Classe**.
2. Nomeie a nova classe **AuthenticationTestHandler.cs** e selecione **Adicionar** para gerar a classe. Essa classe usa *Autenticação Básica* para manter a simplicidade na autenticação dos usuários. Seu aplicativo pode utilizar qualquer esquema de autenticação.
3. Em AuthenticationTestHandler.cs, adicione as seguintes instruções `using`:

    ```csharp
    using System.Net.Http;
    using System.Threading;
    using System.Security.Principal;
    using System.Net;
    using System.Text;
    using System.Threading.Tasks;
    ```

4. Em AuthenticationTestHandler.cs, substitua a definição da classe `AuthenticationTestHandler` pelo código a seguir:

    Esse manipulador autoriza a solicitação quando as três seguintes condições a seguir forem verdadeiras:

   * A solicitação inclui um cabeçalho de *Autorização*.
   * A solicitação usa a autenticação *básica* .
   * A cadeia de caracteres de nome de usuário e a cadeia de caracteres de senha são iguais.

   Caso contrário, a solicitação é rejeitada. Essa não é uma abordagem de autenticação e autorização verdadeira. É apenas um exemplo simples para este tutorial.

   Se a mensagem de solicitação for autenticada e autorizada pelo `AuthenticationTestHandler`, o usuário de autenticação básica será anexado à solicitação atual no [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.current.aspx). As informações do usuário no HttpContext serão usadas por outro controlador (RegisterController) posteriormente para adicionar uma [marca](https://msdn.microsoft.com/library/azure/dn530749.aspx) à solicitação de registro de notificação.

    ```csharp
    public class AuthenticationTestHandler : DelegatingHandler
    {
        protected override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            var authorizationHeader = request.Headers.GetValues("Authorization").First();

            if (authorizationHeader != null && authorizationHeader
                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
            {
                string authorizationUserAndPwdBase64 =
                    authorizationHeader.Substring("Basic ".Length);
                string authorizationUserAndPwd = Encoding.Default
                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
                string user = authorizationUserAndPwd.Split(':')[0];
                string password = authorizationUserAndPwd.Split(':')[1];

                if (verifyUserAndPwd(user, password))
                {
                    // Attach the new principal object to the current HttpContext object
                    HttpContext.Current.User =
                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
                    System.Threading.Thread.CurrentPrincipal =
                        System.Web.HttpContext.Current.User;
                }
                else return Unauthorized();
            }
            else return Unauthorized();

            return base.SendAsync(request, cancellationToken);
        }

        private bool verifyUserAndPwd(string user, string password)
        {
            // This is not a real authentication scheme.
            return user == password;
        }

        private Task<HttpResponseMessage> Unauthorized()
        {
            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
            var tsc = new TaskCompletionSource<HttpResponseMessage>();
            tsc.SetResult(response);
            return tsc.Task;
        }
    }
    ```

    > [!NOTE]
    > Observação de segurança: a classe `AuthenticationTestHandler` não oferece autenticação verdadeira. Ela é usada somente para imitar a autenticação básica e não é segura. Você deve implementar um mecanismo de autenticação seguro em seus aplicativos e serviços de produção.
5. Para registrar o manipulador de mensagens:, adicione o seguinte código ao final do método `Register` na classe **App_Start/WebApiConfig.cs**:

    ```csharp
    config.MessageHandlers.Add(new AuthenticationTestHandler());
    ```
6. Salve suas alterações.

## <a name="register-for-notifications-by-using-the-webapi-backend"></a>Registrar para receber notificações usando o back-end da WebAPI

Nesta seção, você adiciona um novo controlador ao back-end WebAPI para manipular solicitações e registrar um usuário e um dispositivo para notificações usando a biblioteca de cliente dos hubs de notificação. O controlador adiciona uma marca de usuário ao usuário que foi autenticado e anexado a HttpContext pelo `AuthenticationTestHandler`. A marca tem o formato de cadeia de caracteres, `"username:<actual username>"`.

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **AppBackend** e selecione **Gerenciar Pacotes NuGet**.

2. No painel esquerdo, selecione **Online** e, na caixa **Pesquisa**, digite **Microsoft.Azure.NotificationHubs**.

3. Na lista de resultados, selecione **Hubs de Notificação do Microsoft Azure** e selecione **Instalar**. Conclua a instalação e, por fim, feche a janela Gerenciador de Pacotes NuGet.

    Essa ação adiciona uma referência ao SDK dos Hubs de Notificação do Azure usando o [pacote NuGet Microsoft.Azure.Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

4. Crie um novo arquivo de classe que representa a conexão com o hub de notificação usado para enviar notificações. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Modelos**, selecione **Adicionar** e Classe. Nomeie a nova classe como **Notifications.cs** e selecione **Adicionar** para gerar a classe.

    ![A janela Adicionar Novo Item][B6]

5. Em Notifications.cs, adicione a seguinte instrução `using` à parte superior do arquivo:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

6. Substitua a definição da classe `Notifications` pelo seguinte e substitua os dois espaços reservados pela cadeia de conexão (com acesso completo) para o hub de notificação e o nome do hub (disponível no [portal do Azure](https://portal.azure.com)):

    ```csharp
    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>",
                                                                            "<hub name>");
        }
    }
    ```
7. Em seguida, crie um novo controlador chamado **RegisterController**. No Gerenciador de Soluções, clique com o botão direito do mouse na pasta **Controladores**, selecione **Adicionar** e **Controlador**.

8. Selecione **Controlador da API Web 2 - Vazio**e selecione **Adicionar**.

    ![A janela Adicionar Scaffold][B7]

9. Na caixa **Nome do controlador**, digite **RegisterController** para nomear a nova classe e selecione **Adicionar**.

    ![A janela Adicionar Controlador][B8]

10. Em RegisterController.cs, adicione as seguintes instruções `using` :

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.NotificationHubs.Messaging;
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
11. Adicione o código a seguir à definição de classe `RegisterController` . Nesse código, você adiciona uma marca de usuário para o usuário anexado a HttpContext. O usuário foi autenticado e anexado a HttpContext pelo filtro de mensagens que você adicionou, `AuthenticationTestHandler`. Você também pode adicionar verificações opcionais para conferir se o usuário tem direitos para registro das tags requeridas.

    ```csharp
    private NotificationHubClient hub;

    public RegisterController()
    {
        hub = Notifications.Instance.Hub;
    }

    public class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    // POST api/register
    // This creates a registration id
    public async Task<string> Post(string handle = null)
    {
        string newRegistrationId = null;

        // make sure there are no existing registrations for this push handle (used for iOS and Android)
        if (handle != null)
        {
            var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

            foreach (RegistrationDescription registration in registrations)
            {
                if (newRegistrationId == null)
                {
                    newRegistrationId = registration.RegistrationId;
                }
                else
                {
                    await hub.DeleteRegistrationAsync(registration);
                }
            }
        }

        if (newRegistrationId == null) 
            newRegistrationId = await hub.CreateRegistrationIdAsync();

        return newRegistrationId;
    }

    // PUT api/register/5
    // This creates or updates a registration (with provided channelURI) at the specified id
    public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
    {
        RegistrationDescription registration = null;
        switch (deviceUpdate.Platform)
        {
            case "mpns":
                registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "wns":
                registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "apns":
                registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                break;
            case "fcm":
                registration = new FcmRegistrationDescription(deviceUpdate.Handle);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

        registration.RegistrationId = id;
        var username = HttpContext.Current.User.Identity.Name;

        // add check if user is allowed to add these tags
        registration.Tags = new HashSet<string>(deviceUpdate.Tags);
        registration.Tags.Add("username:" + username);

        try
        {
            await hub.CreateOrUpdateRegistrationAsync(registration);
        }
        catch (MessagingException e)
        {
            ReturnGoneIfHubResponseIsGone(e);
        }

        return Request.CreateResponse(HttpStatusCode.OK);
    }

    // DELETE api/register/5
    public async Task<HttpResponseMessage> Delete(string id)
    {
        await hub.DeleteRegistrationAsync(id);
        return Request.CreateResponse(HttpStatusCode.OK);
    }

    private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
    {
        var webex = e.InnerException as WebException;
        if (webex.Status == WebExceptionStatus.ProtocolError)
        {
            var response = (HttpWebResponse)webex.Response;
            if (response.StatusCode == HttpStatusCode.Gone)
                throw new HttpRequestException(HttpStatusCode.Gone.ToString());
        }
    }
    ```
12. Salve suas alterações.

## <a name="send-notifications-from-the-webapi-backend"></a>Enviar notificações do back-end da WebAPI

Nesta seção, você adiciona um novo controlador que expõe uma maneira de os dispositivos clientes enviarem uma notificação. A notificação se baseia na marca de nome de usuário que usa a Biblioteca .NET dos Hubs de Notificação do Azure no back-end WebAPI ASP.NET.

1. Crie outro novo controlador chamado **NotificationsController** da mesma maneira que você criou **RegisterController** na seção anterior.

2. Em NotificationsController.cs, adicione as seguintes instruções `using` :

    ```csharp
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
3. Adicione o seguinte método à classe **NotificationsController**:

    Esse código envia um tipo de notificação com base no parâmetro `pns` do PNS (Platform Notification Service). O valor de `to_tag` é usado para definir a marca *username* na mensagem. Essa marca deve corresponder a uma marca de nome de usuário de um registro de hub de notificação ativo. A mensagem de notificação é recuperada do corpo da solicitação POST e formatada para o PNS de destino.

    Dependendo do PNS que seus dispositivos com suporte usam para receber notificações, as notificações têm suporte por vários formatos diferentes. Por exemplo, em dispositivos do Windows, você pode usar uma [notificação do sistema com WNS](https://msdn.microsoft.com/library/windows/apps/br230849.aspx) que não tenha suporte direto de outro PNS. Nesse caso, o back-end precisa formatar a notificação em uma notificação com suporte para o PNS de dispositivos aos quais você planeja dar suporte. Em seguida, use a API de envio apropriada na [classe NotificationHubClient](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.notificationhubclient_methods.aspx).

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
    {
        var user = HttpContext.Current.User.Identity.Name;
        string[] userTag = new string[2];
        userTag[0] = "username:" + to_tag;
        userTag[1] = "from:" + user;

        Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
        HttpStatusCode ret = HttpStatusCode.InternalServerError;

        switch (pns.ToLower())
        {
            case "wns":
                // Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                break;
            case "apns":
                // iOS
                var alert = "{\"aps\":{\"alert\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                break;
            case "fcm":
                // Android
                var notif = "{ \"data\" : {\"message\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendFcmNativeNotificationAsync(notif, userTag);
                break;
        }

        if (outcome != null)
        {
            if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
            {
                ret = HttpStatusCode.OK;
            }
        }

        return Request.CreateResponse(ret);
    }
    ```
4. Para executar o aplicativo e garantir a precisão de seu trabalho até aqui, selecione a tecla **F5**. O aplicativo abre um navegador da Web e será exibido na home page do ASP.NET.

## <a name="publish-the-new-webapi-backend"></a>Publicar o novo back-end da API Web

Em seguida, implante o aplicativo em um site do Azure para poder ser acessado por todos os dispositivos.

1. Clique com o botão direito do mouse no projeto **AppBackend** e selecione **Publicar**.

2. Escolha **Serviço de Aplicativo do Microsoft Azure** como destino de publicação e selecione **Publicar. A janela Criar Serviço de Aplicativo é aberta. Aqui, você pode criar todos os recursos do Azure necessários para executar o aplicativo Web ASP.NET no Azure.

    ![O bloco Serviço de Aplicativo do Azure][B15]

3. Na janela **Criar Serviço de Aplicativo**, selecione sua conta do Azure. Selecione **Alterar Tipo** > **Aplicativo Web**. Mantenha o **Nome do Aplicativo Web** padrão e selecione a **Assinatura**, o **Grupo de Recursos** e o **Plano do Serviço de Aplicativo**.

4. Selecione **Criar**.

5. Anote a propriedade **URL do Site** na seção **Resumo**. Essa URL será seu *ponto de extremidade de back-end* mais adiante no tutorial.

6. Selecione **Publicar**.

Depois de concluir o assistente, ele publica o aplicativo Web ASP.NET no Azure e abre o aplicativo no navegador padrão.  Seu aplicativo pode ser exibido nos Serviços de Aplicativo do Azure.

A URL usa o nome do aplicativo Web especificado anteriormente, com o formato http://<app_name>.azurewebsites.net.

[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/publish-to-app-service.png
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG
