
Nesta seção, você envia as últimas notícias como notificações de modelo marcadas, de um aplicativo de console do .NET.

Se estiver usando o recurso Aplicativos Móveis do Serviço de Aplicativo do Microsoft Azure, veja o tutorial [Adicionar notificações push aos Aplicativos Móveis] e selecione a plataforma na parte superior.

Se desejar usar Java ou PHP, consulte [Como utilizar Hubs de Notificação a partir de Java ou PHP]. Você pode enviar notificações de qualquer back-end usando a [interface REST de Hubs de Notificação].

Se você tiver criado um aplicativo de console para enviar notificações, quando concluiu a [Introdução aos Hubs de Notificação], ignore as etapas de 1 a 3.

1. No Visual Studio, crie um novo aplicativo de console em Visual C#:
   
      ![O link de Aplicativo de Console][13]

2. No menu principal do Visual Studio, selecione **Ferramentas** > **Gerenciador de Pacotes de Biblioteca** > **Console do Gerenciador de pacotes** e, em seguida, no console do janela, insira a cadeia de caracteres a seguir:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
3. Selecione **Inserir**.  
    Essa ação adiciona uma referência ao SDK dos Hubs de Notificação do Azure usando o [pacote NuGet Microsoft.Azure.Notification Hubs].

4. Abra o arquivo Program.cs e adicione a seguinte instrução `using`:
   
        using Microsoft.Azure.NotificationHubs;

5. Na classe `Program` , adicione o seguinte método ou substitua-o, se ele já existir:
   
        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub =
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");
   
            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business",
                                            "Technology", "Science", "Sports"};
   
            // Send the notification as a template notification. All template registrations that contain
            // "messageParam" and the proper tags will receive the notifications.
            // This includes APNS, GCM, WNS, and MPNS template registrations.
   
            Dictionary<string, string> templateParams = new Dictionary<string, string>();
   
            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";
                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
         }
   
    Esse código envia uma notificação de modelo para cada uma das seis marcas na matriz de cadeia de caracteres. O uso de marcas garante que os dispositivos recebam notificações somente para as categorias de marcas.

5. No código anterior, substitua os espaços reservados `<hub name>` e `<connection string with full access>` pelo nome do hub de notificação e a cadeia de conexão por *DefaultFullSharedAccessSignature* do painel do hub de notificação.

6. No método **Main**, adicione as seguintes linhas:
   
         SendTemplateNotificationAsync();
         Console.ReadLine();

7. Compile o aplicativo de console.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Introdução aos Hubs de Notificação]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[interface REST de Hubs de Notificação]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Adicionar notificações push aos Aplicativos Móveis]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[Como utilizar Hubs de Notificação a partir de Java ou PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[pacote NuGet Microsoft.Azure.Notification Hubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
