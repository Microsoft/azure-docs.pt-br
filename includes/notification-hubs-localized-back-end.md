



Ao enviar notificações de modelos, você só precisa fornecer um conjunto de propriedades. No nosso caso, enviaremos o conjunto de propriedades contendo a versão localizada das notícias atuais, por exemplo:

    {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin": "World News in Mandarin!"
    }


Esta seção mostra como enviar notificações usando um aplicativo do console

O código incluído transmite a dispositivos Windows Store e iOS, como o back-end pode transmitir para qualquer um dos dispositivos com suporte.

### <a name="to-send-notifications-using-a-c-console-app"></a>Para enviar notificações usando um aplicativo de console C#
Modifique o método `SendTemplateNotificationAsync` no aplicativo de console que você criou anteriormente, com o código a seguir. Observe como nesse caso não é necessário enviar várias notificações para localidades e plataformas diferentes.

        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub = 
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");

            // Sending the notification as a template notification. All template registrations that contain 
            // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
            // This includes APNS, GCM, WNS, and MPNS template registrations.
            Dictionary<string, string> templateParams = new Dictionary<string, string>();

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
            var locales = new string[] { "English", "French", "Mandarin" };

            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";

                // Sending localized News for each tag too...
                foreach( var locale in locales)
                {
                    string key = "News_" + locale;

                    // Your real localized news content would go here.
                    templateParams[key] = "Breaking " + category + " News in " + locale + "!";
                }

                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
        }


Observe que esta simples chamada entregará a notícia correta localizada a **todos** os dispositivos, independentemente da plataforma, enquanto o Hub de Notificação cria e entrega a carga de trabalho nativa correta a todos os dispositivos inscritos em uma marca específica.

### <a name="sending-the-notification-with-mobile-services"></a>Enviando a notificação com Serviços Móveis
No Agendador de serviço móvel, você pode usar o script a seguir:

    var azure = require('azure');
    var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
    var notification = {
            "News_English": "World News in English!",
            "News_French": "World News in French!",
            "News_Mandarin", "World News in Mandarin!"
    }
    notificationHubService.send('World', notification, function(error) {
        if (!error) {
            console.warn("Notification successful");
        }
    });


