

As notificações por push normalmente são enviadas em um serviço de back-end como Aplicativos Móveis ou ASP.NET usando uma biblioteca compatível. Você também pode usar a API REST diretamente para enviar mensagens de notificação se uma biblioteca não está disponível para o back-end. 

Aqui está uma lista de alguns outros tutoriais que talvez você queira examinar para o envio de notificações:

* Aplicativos Móveis do Azure: para obter um exemplo de como enviar notificações por push de um back-end de Aplicativos Móveis integrado com Hubs de Notificação, veja [Adicionar notificações por push ao seu aplicativo iOS](../articles/app-service-mobile/app-service-mobile-ios-get-started-push.md).  
* ASP.NET: [Usar hubs de notificação para enviar notificações por push aos usuários](../articles/notification-hubs/notification-hubs-aspnet-backend-ios-apple-apns-notification.md).
* SDK Java do Hub de Notificação do Azure: consulte [Como usar os Hubs de Notificação do Java](../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md) para enviar notificações do Java. Isso foi testado no Eclipse para desenvolvimento no Android.
* PHP: [como usar Hubs de Notificação do PHP](../articles/notification-hubs/notification-hubs-php-push-notification-tutorial.md).

Na próxima seção do tutorial, você aprenderá a usar a [interface REST do Hub de Notificação](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx) para enviar a mensagem de notificação diretamente para seu aplicativo. Todos os dispositivos registrados recebem a notificação enviada por qualquer dispositivo.  

