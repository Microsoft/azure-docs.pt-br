---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: b150cad22528234286fa7939bf7055e8312ed361
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229171"
---
## <a name="webapi-project"></a>Projeto WebAPI
1. No Visual Studio, abra o projeto **AppBackend** que você criou no tutorial **Notificar Usuários** .
2. Em Notifications.cs, substitua toda a classe **Notifications** pelo código a seguir. Certifique-se de substituir os espaços reservados por sua cadeia de conexão (com acesso completo) para seu hub de notificação e pelo nome do hub. Você pode obter esses valores do [Portal do Azure](https://portal.azure.com). Esse módulo agora representa as diferentes notificações seguras que serão enviadas. Em uma implementação completa, as notificações são armazenadas em um banco de dados; por questão de simplicidade, neste caso as armazenaremos na memória.
   
        public class Notification
        {
            public int Id { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public class Notifications
        {
            public static Notifications Instance = new Notifications();

            private List<Notification> notifications = new List<Notification>();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",     "{hub name}");
            }

            public Notification CreateNotification(string payload)
            {
                var notification = new Notification() {
                Id = notifications.Count,
                Payload = payload,
                Read = false
                };

                notifications.Add(notification);

                return notification;
            }

            public Notification ReadNotification(int id)
            {
                return notifications.ElementAt(id);
            }
        }

1. Em NotificationsController.cs, substitua o código dentro da definição de classe **NotificationsController** pelo código a seguir. Este componente implementa um modo para o dispositivo obter a notificação com segurança, além de também fornecer um modo (para os fins deste tutorial) para disparar uma notificação por push segura para seus dispositivos. Observe que, ao enviar a notificação ao Hub de Notificação, enviamos somente uma notificação bruta com a ID da notificação (sem a mensagem em si):
   
       public NotificationsController()
       {
           Notifications.Instance.CreateNotification("This is a secure notification!");
       }
   
       // GET api/notifications/id
       public Notification Get(int id)
       {
           return Notifications.Instance.ReadNotification(id);
       }
   
       public async Task<HttpResponseMessage> Post()
       {
           var secureNotificationInTheBackend = Notifications.Instance.CreateNotification("Secure confirmation.");
           var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;
   
           // windows
           var rawNotificationToBeSent = new Microsoft.Azure.NotificationHubs.WindowsNotification(secureNotificationInTheBackend.Id.ToString(),
                           new Dictionary<string, string> {
                               {"X-WNS-Type", "wns/raw"}
                           });
           await Notifications.Instance.Hub.SendNotificationAsync(rawNotificationToBeSent, usernameTag);
   
           // apns
           await Notifications.Instance.Hub.SendAppleNativeNotificationAsync("{\"aps\": {\"content-available\": 1}, \"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}", usernameTag);
   
           // gcm
           await Notifications.Instance.Hub.SendGcmNativeNotificationAsync("{\"data\": {\"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}}", usernameTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }


Observe que agora o método `Post` não envia uma notificação de sistema. Ele envia uma notificação bruta, que contém somente a ID de notificação, sem conteúdo sensível. Além disso, certifique-se de comentar a operação de envio para as plataformas para as quais você não tem credenciais configuradas em seu hub de notificação, já que elas resultarão em erros.

1. Agora, reimplantaremos esse aplicativo em um Site do Azure para torná-lo acessível a partir de todos os dispositivos. Clique com o botão direito do mouse no projeto **AppBackend** e selecione **Publicar**.
2. Selecione o Site do Azure como seu destino de publicação. Entre em sua conta do Azure e selecione um site novo ou existente, então anote a propriedade **URL de destino** na guia **Conexão**. Iremos nos referir a essa URL, posteriormente neste tutorial, como seu *ponto de extremidade de back-end* . Clique em **Publicar**.

