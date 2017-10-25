---
title: "Notificar usuários nos Hubs de Notificação do Azure com o back-end do .NET"
description: "Saiba como enviar notificações por push seguro no Azure. Amostras de código escrito em C# usando a API .NET."
documentationcenter: windows
author: ysxu
manager: erikre
services: notification-hubs
editor: 
ms.assetid: 012529f2-fdbc-43c4-8634-2698164b5880
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: c0b963ef661612b1a176dd8e5f01d56e61eb5acb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-notification-hubs-notify-users-with-net-backend"></a>Notificar usuários nos Hubs de Notificação do Azure com o back-end do .NET
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Visão geral
O suporte à notificação por push no Azure permite que você acesse uma infraestrutura de envio por push fácil de usar, multiplataforma e expansível que simplifica em muito a implementação de notificações por push para aplicativos de consumidor e empresariais para plataformas móveis. Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push a um usuário específico do aplicativo em um dispositivo específico. Um back-end de WebAPI do ASP.NET é usado para autenticar clientes. Usando o usuário cliente autenticado e a marca será automaticamente adicionada pelo back-end para o registro de notificação. Essa marca será usada para enviar pelo back-end para gerar notificações para um usuário específico. Para obter mais informações sobre como se registrar para receber notificações usando um back-end do aplicativo, consulte o tópico de diretrizes [Registrando-se por meio do back-end do aplicativo](http://msdn.microsoft.com/library/dn743807.aspx). Este tutorial baseia-se no hub de notificação que você criou no tutorial [Introdução aos Hubs de Notificação] .

Este tutorial também é um pré-requisito para o tutorial [Push Seguro] . Depois de concluir as etapas deste tutorial, você pode prosseguir para o tutorial [Push Seguro] , que mostra como modificar o código neste tutorial para enviar uma notificação por push com segurança.

## <a name="before-you-begin"></a>Antes de começar
Levamos seus comentários a sério. Se você tiver alguma dificuldade para concluir este tópico ou recomendações para melhorar este conteúdo, apreciaremos seus comentários na parte inferior da página.

O código completo para este tutorial pode ser encontrado no GitHub [aqui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). 

## <a name="prerequisites"></a>Pré-requisitos
Antes de iniciar este tutorial, você já deve ter concluído estes tutoriais dos Serviços Móveis:

* [Introdução aos Hubs de Notificação]<br/>Você cria seu hub de notificação, reserva o nome do aplicativo e se registra para receber notificações nesse tutorial. Este tutorial presume que você já concluiu essas etapas. Do contrário, siga as etapas descritas em [Introdução aos Hubs de Notificação (Windows Store)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md); especificamente, as seções [Registrar seu aplicativo para a Windows Store](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#register-your-app-for-the-windows-store) e [Configurar seu Hub de Notificação](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#configure-your-notification-hub). Especificamente, não se esqueça de inserir os valores de **SID do Pacote** e **Segredo do Cliente** no portal, na guia **Configurar** de seu hub de notificação. Esse procedimento de configuração é descrito na seção [Configurar seu Hub de Notificação](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#configure-your-notification-hub). Esta é uma etapa importante: se as credenciais no portal não corresponderem àquelas especificadas para o nome do aplicativo escolhido, a notificação por push não será bem-sucedida.

> [!NOTE]
> Se você estiver usando Aplicativos Móveis no Serviço de Aplicativo do Azure como serviço de back-end, consulte a [versão dos Aplicativos Móveis](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md) deste tutorial.
> 
> 

&nbsp;

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="update-the-code-for-the-client-project"></a>Atualizar o código para o projeto cliente
Nesta seção, você atualiza o código no projeto concluído no tutorial [Introdução aos Hubs de Notificação] . Ele já deve estar associado à loja e configurado para o hub de notificação. Nesta seção, você adicionará código para chamar o novo back-end da WebAPI e o usará para se registrar e enviar notificações.

1. No Visual Studio, abra a solução criada no tutorial [Introdução aos Hubs de Notificação] .
2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **(Windows 8.1)** e clique em **Gerenciar Pacotes NuGet**.
3. No lado esquerdo, clique em **Online**.
4. Na caixa **Pesquisar**, digite **Http Client**.
5. Na lista de resultados, clique em **Bibliotecas de Cliente HTTP da Microsoft** e em **Instalar**. Conclua a instalação.
6. De volta à caixa **Pesquisar** do NuGet, digite **Json.net**. Instale o pacote **Json.NET** e feche a janela do Gerenciador de Pacotes NuGet.
7. Repita as etapas acima para o projeto **(Windows Phone 8.1)** para instalar o pacote NuGet **JSON.NET** para o projeto do Windows Phone.
8. No Gerenciador de Soluções, no projeto **(Windows Phone 8.1)**, clique duas vezes em **MainPage.xaml** para abri-lo no editor do Visual Studio.
9. No código de XML de **MainPage.xaml**, substitua a seção `<Grid>` pelo código a seguir. Este código adiciona uma caixa de texto de nome de usuário e de senha com as quais o usuário irá autenticar. Ele também adiciona caixas de texto para a mensagem de notificação e a marca de nome de usuário que deve receber a notificação:
   
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
   
            <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>
   
            <StackPanel Grid.Row="1" VerticalAlignment="Center">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                    </Grid.ColumnDefinitions>
                    <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                    <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
   
                    <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                                Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>
   
                    <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                    <ToggleButton Name="toggleGCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="GCM" />
                    <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />
   
                    <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
                </Grid>
            </StackPanel>
        </Grid>
10. No Gerenciador de Soluções, no projeto **(Windows Phone 8.1)**, abra **MainPage.xaml** e substitua a seção Windows Phone 8.1 `<Grid>` com esse mesmo código acima. A interface deve ser semelhante ao que está mostrado a seguir.
    
    ![][13]
11. No Gerenciador de Soluções, abra o arquivo **MainPage.xaml.cs** para os projetos **(Windows 8.1)** e **(Windows Phone 8.1)**. Adicione as seguintes instruções `using` na parte superior dos dois arquivos:
    
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;
        using System.Threading.Tasks;
12. Em **MainPage.xaml.cs** para os projetos **(Windows 8.1)** e **(Windows Phone 8.1)**, adicione o membro a seguir para a classe `MainPage`. Substitua `<Enter Your Backend Endpoint>` pelo ponto de extremidade de back-end real obtido anteriormente. Por exemplo: `http://mybackend.azurewebsites.net`.
    
        private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
13. Adicione o código a seguir à classe MainPage em **MainPage.xaml.cs** para os projetos **(Windows 8.1)** e **(Windows Phone 8.1)**.
    
    O método `PushClick` é o manipulador de cliques para o botão **Enviar por Push** . Ele chama o back-end para disparar uma notificação para todos os dispositivos com uma marca de nome de usuário que corresponde ao parâmetro `to_tag` . A mensagem de notificação é enviada como conteúdo JSON no corpo da solicitação.
    
    O método `LoginAndRegisterClick` é o manipulador de cliques para o botão **Fazer logon e registrar** . Ele armazena o token de autenticação básica localmente (observe que isso representa qualquer token usado pelo seu esquema de autenticação) e utiliza `RegisterClient` para o registro de notificações usando o back-end.

        private async void PushClick(object sender, RoutedEventArgs e)
        {
            if (toggleWNS.IsChecked.Value)
            {
                await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleGCM.IsChecked.Value)
            {
                await sendPush("gcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleAPNS.IsChecked.Value)
            {
                await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

            }
        }

        private async Task sendPush(string pns, string userTag, string message)
        {
            var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
                pns + "&to_tag=" + userTag;

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                try
                {
                    await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                        System.Text.Encoding.UTF8, "application/json"));
                }
                catch (Exception ex)
                {
                    MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                    alert.ShowAsync();
                }
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();

            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            // The "username:<user name>" tag gets automatically added by the message handler in the backend.
            // The tag passed here can be whatever other tags you may want to use.
            try
            {
                // The device handle used will be different depending on the device and PNS. 
                // Windows devices use the channel uri as the PNS handle.
                await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

                var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
                SendPushButton.IsEnabled = true;
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
                alert.ShowAsync();
            }
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;

            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }



1. No Gerenciador de Soluções, sob o projeto **Compartilhado**, abra o arquivo **App.xaml.cs**. Localize a chamada para `InitNotificationsAsync()` in the `OnLaunched()` . Comentar ou excluir a chamada para `InitNotificationsAsync()`. O manipulador do botão adicionado acima irá inicializar os registros de notificação.

        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
            //InitNotificationsAsync();


1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **Compartilhado**, clique em **Adicionar** e em **Classe**. Nomeie a classe como **RegisterClient.cs** e clique em **OK** para gerar a classe.
   
   Essa classe irá encapsular as chamadas do REST necessárias para entrar em contato com o back-end do aplicativo de modo a se registrar para as notificações por push. Ele também armazena localmente os *registrationIds* criados pelo Hub de Notificação, conforme detalhado em [Registrando-se por meio do back-end do aplicativo](http://msdn.microsoft.com/library/dn743807.aspx). Observe que ele usa um token de autorização armazenado localmente quando você clica no botão **Fazer logon e registrar-se** .
2. Adicione as seguintes instruções `using` à parte superior do arquivo RegisterClient.cs:
   
       using Windows.Storage;
       using System.Net;
       using System.Net.Http;
       using System.Net.Http.Headers;
       using Newtonsoft.Json;
       using System.Threading.Tasks;
       using System.Linq;
3. Adicione o código a seguir à definição de classe `RegisterClient` .
   
       private string POST_URL;
   
       private class DeviceRegistration
       {
           public string Platform { get; set; }
           public string Handle { get; set; }
           public string[] Tags { get; set; }
       }
   
       public RegisterClient(string backendEndpoint)
       {
           POST_URL = backendEndpoint + "/api/register";
       }
   
       public async Task RegisterAsync(string handle, IEnumerable<string> tags)
       {
           var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
   
           var deviceRegistration = new DeviceRegistration
           {
               Platform = "wns",
               Handle = handle,
               Tags = tags.ToArray<string>()
           };
   
           var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
   
           if (statusCode == HttpStatusCode.Gone)
           {
               // regId is expired, deleting from local storage & recreating
               var settings = ApplicationData.Current.LocalSettings.Values;
               settings.Remove("__NHRegistrationId");
               regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
               statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
           }
   
           if (statusCode != HttpStatusCode.Accepted)
           {
               // log or throw
               throw new System.Net.WebException(statusCode.ToString());
           }
       }
   
       private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
       {
           using (var httpClient = new HttpClient())
           {
               var settings = ApplicationData.Current.LocalSettings.Values;
               httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);
   
               var putUri = POST_URL + "/" + regId;
   
               string json = JsonConvert.SerializeObject(deviceRegistration);
                               var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
               return response.StatusCode;
           }
       }
   
       private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
       {
           var settings = ApplicationData.Current.LocalSettings.Values;
           if (!settings.ContainsKey("__NHRegistrationId"))
           {
               using (var httpClient = new HttpClient())
               {
                   httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);
   
                   var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                   if (response.IsSuccessStatusCode)
                   {
                       string regId = await response.Content.ReadAsStringAsync();
                       regId = regId.Substring(1, regId.Length - 2);
                       settings.Add("__NHRegistrationId", regId);
                   }
                   else
                   {
                       throw new System.Net.WebException(response.StatusCode.ToString());
                   }
               }
           }
           return (string)settings["__NHRegistrationId"];
   
       }
4. Salve todas as alterações.

## <a name="testing-the-application"></a>Testando o aplicativo
1. Inicie o aplicativo no Windows 8.1 e no Windows Phone 8.1. Para o Windows Phone 8.1, você pode executar a instância no emulador ou em um dispositivo real.
2. Na instância Windows 8.1 do aplicativo, insira um **Nome de Usuário** e **Senha** conforme mostrado na tela abaixo. Eles devem diferir do nome de usuário e senha que você insere no Windows Phone.
3. Clique em **Fazer logon e registrar** e verifique se um diálogo mostra que você fez logon. Isso também habilitará o botão **Enviar por Push** .
   
    ![][14]
4. Na instância do Windows Phone 8.1, insira uma cadeia de caracteres de nome de usuário nos campos **nome de usuário** e **senha** e clique em **Fazer logon e registrar**.
5. Em seguida, no campo **Marca de Nome de Usuário do Destinatário** , insira o nome de usuário registrado no Windows 8.1. Digite uma mensagem de notificação e clique em **Enviar notificação por push**.
   
    ![][16]
6. Apenas os dispositivos que foram registrados com o nome de usuário correspondente recebem a mensagem de notificação.
   
    ![][15]

## <a name="next-steps"></a>Próximas etapas
* Se desejar segmentar os usuários por grupos de interesse, você poderá ler [Usar Hubs de Notificação para enviar notícias mais recentes].
* Para saber mais sobre como usar Hubs de Notificação, consulte [Diretrizes dos Hubs de Notificação].

[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png



<!-- URLs. -->
[Introdução aos Hubs de Notificação]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Push Seguro]: notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md
[Usar Hubs de Notificação para enviar notícias mais recentes]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
