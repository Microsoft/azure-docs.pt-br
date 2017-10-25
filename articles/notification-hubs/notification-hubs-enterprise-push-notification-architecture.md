---
title: "Hubs de notificação - Arquitetura de Push Corporativo"
description: "Orientação sobre como usar os Hubs de Notificação do Azure em um ambiente corporativo"
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: d71c706a7db570e88339c4ff7af05a48c05df65b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="enterprise-push-architectural-guidance"></a>Orientação arquitetural do push corporativo
As empresas hoje estão gradualmente migrando para a criação de aplicativos móveis para os usuários finais (externos) ou para os funcionários (internos). Eles têm sistemas de back-end no local como mainframes ou alguns aplicativos LoB que devem ser integrados na arquitetura de aplicativos móveis. Este guia falará sobre a melhor maneira de fazer essa integração recomendado a melhor solução para cenários comuns.

Um requisito frequente é enviar notificação por push para os usuários através de seus aplicativos móveis quando ocorre um evento de interesse nos sistemas de back-end. Por exemplo Um cliente bancário que tenha o aplicativo de serviços bancários do banco em seu iPhone deseja ser notificado quando um débito fica acima de um determinado valor de sua conta ou um cenário de intranet em que um funcionário do departamento financeiro com um aplicativo de aprovação de orçamento em seu Windows Phone deseja ser notificado quando ele recebe uma solicitação de aprovação.

A conta bancária ou o processamento de aprovação provavelmente pode ser feito em algum sistema back-end que deve iniciar um envio por push para o usuário. Poderá haver vários sistemas de back-end e todos deverão compilar o mesmo tipo de lógica para implementar push quando um evento disparar uma notificação. A complexidade aqui reside na integração de vários sistemas de back-end com sistemas individuais de envio por push, nos quais os usuários finais podem se inscrever para diferentes notificações e pode até mesmo haver vários aplicativos móveis, por exemplo, no caso de aplicativos móveis de intranet nos quais um aplicativo móvel talvez queira receber notificações de vários sistemas de back-end. Os sistemas de back-end não sabem nem precisam saber de tecnologia/semântica de push para que uma solução comum aqui tem sido tradicionalmente para introduzir um componente que controla os sistemas de back-end para todos os eventos de interesse e é responsável por enviar as mensagens por push para o cliente.
Aqui falaremos sobre uma solução ainda melhor usando o Barramento de Serviço do Azure - modelo de Tópico/Assinatura que reduzirá a complexidade, tornando a solução escalonável.

Esta é a arquitetura geral da solução (generalizado com vários aplicativos móveis, mas igualmente aplicável quando há apenas um aplicativo móvel)

## <a name="architecture"></a>Arquitetura
![][1]

A parte mais importante neste diagrama de arquitetura é o Barramento de Serviço do Azure que fornece um modelo de programação de tópicos/assinaturas (falaremos mais sobre isso em [Programação Pub/Sub do Barramento de Serviço]). O receptor, que nesse caso, é o back-end móvel (normalment, [Serviço Móvel do Azure], que iniciará um envio por push para os aplicativos móveis) não recebe mensagens diretamente dos sistemas de back-end, mas em vez disso, temos uma camada de abstração intermediária fornecida pelo [Barramento de Serviço do Azure], que permite que o back-end móvel receba mensagens de um ou mais sistemas de back-end. Um Tópico do Barramento de Serviço precisa ser criada para cada um dos sistemas de back-end, por exemplo, Conta, RH, Finanças, que são basicamente "tópicos" de interesse que iniciarão o envio de mensagens como notificação por push. Os sistemas de back-end enviarão mensagens para esses tópicos. Um Back-end Móvel pode assinar um ou mais tópicos criando uma assinatura do Barramento de Serviço. Isso permitirá que o back-end móvel receba uma notificação do sistema de back-end correspondente. O back-end móvel continua a escutar mensagens em suas assinaturas e, assim que uma mensagem chega, ela volta e é enviada como notificação para seu hub de notificação. Os hubs de notificação eventualmente entregam a mensagem para o aplicativo móvel. Portanto, para resumir os principais componentes, nós temos:

1. Sistemas de back-end (sistemas de LoB/herdados)
   * Cria um tópico do barramento de serviço
   * Envia mensagem
2. Back-end móvel
   * Cria a assinatura do serviço
   * Recebe uma mensagem (do sistema de back-end)
   * Envia uma notificação para os clientes (via Hub de Notificação do Azure)
3. Aplicativo Móvel
   * Recebe e exibe a notificação

### <a name="benefits"></a>Benefícios:
1. A separação entre o receptor (aplicativo/serviço móvel via Hub de Notificação) e o remetente (sistemas de back-end) permite que os sistemas de back-end adicionais sejam integrados com alterações mínimas.
2. Isso também torna o cenário de vários aplicativos móveis, sendo capaz de receber eventos de um ou mais sistemas de back-end.  

## <a name="sample"></a>Exemplo:
### <a name="prerequisites"></a>Pré-requisitos
Você deve concluir os tutoriais a seguir para se familiarizar com os conceitos, bem como etapas de criação e configuração comuns:

1. [Programação Pub/Sub do Barramento de Serviço] - explica os detalhes de como trabalhar com Tópicos/Assinaturas do Barramento de Serviço, como criar um namespace para conter tópicos/assinaturas e como enviar e receber mensagens deles.
2. [Hubs de Notificação - tutorial do Windows Universal] - isso explica como configurar um aplicativo da Windows Store e usar Hubs de Notificação para se registrar e receber notificações.

### <a name="sample-code"></a>Exemplo de código
O código de exemplo completo está disponível em [Exemplos do Hub de Notificação]. Ele é dividido em três componentes:

1. **EnterprisePushBackendSystem**
   
    a. Esse projeto usa o pacote *WindowsAzure.ServiceBus* do Nuget e é baseado na [Programação Pub/Sub do Barramento de Serviço].
   
    b. Isso é um console de aplicativo em C# simples para simular um sistema LoB que inicia a mensagem a ser entregue ao aplicativo móvel.
   
        static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the topic where we will send notifications
            CreateTopic(connectionString);
   
            // Send message
            SendMessage(connectionString);
        }
   
    c. `CreateTopic` é usado para criar o tópico do Barramento de Serviço, no qual poderemos enviar mensagens.
   
        public static void CreateTopic(string connectionString)
        {
            // Create the topic if it does not exist already
   
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);
   
            if (!namespaceManager.TopicExists(sampleTopic))
            {
                namespaceManager.CreateTopic(sampleTopic);
            }
        }
   
    d. `SendMessage` é usado para enviar as mensagens para esse Tópico do Barramento de Serviço. Aqui podemos simplesmente enviar um conjunto de mensagens aleatórias para o tópico periodicamente para fins de exemplo. Normalmente haverá um sistema de back-end que enviará mensagens quando ocorre um evento.
   
        public static void SendMessage(string connectionString)
        {
            TopicClient client =
                TopicClient.CreateFromConnectionString(connectionString, sampleTopic);
   
            // Sends random messages every 10 seconds to the topic
            string[] messages =
            {
                "Employee Id '{0}' has joined.",
                "Employee Id '{0}' has left.",
                "Employee Id '{0}' has switched to a different team."
            };
   
            while (true)
            {
                Random rnd = new Random();
                string employeeId = rnd.Next(10000, 99999).ToString();
                string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);
   
                // Send Notification
                BrokeredMessage message = new BrokeredMessage(notification);
                client.Send(message);
   
                Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);
   
                System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
            }
        }
2. **ReceiveAndSendNotification**
   
    a. Esse projeto usa os pacotes *WindowsAzure.ServiceBus* e *Microsoft.Web.WebJobs.Publish* do Nuget e se baseia na [Programação Pub/Sub do Barramento de Serviço].
   
    b. Esse é outro console aplicativo em C# que podemos executar como um [Trabalho Web do Azure] porque ele precisa ser executado continuamente para ouvir mensagens dos sistemas LoB/back-end. Isso fará parte do back-end do celular.
   
        static void Main(string[] args)
        {
            string connectionString =
                     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the subscription which will receive messages
            CreateSubscription(connectionString);
   
            // Receive message
            ReceiveMessageAndSendNotification(connectionString);
        }
   
    c. `CreateSubscription` é usado para criar uma assinatura do Barramento de Serviço para o tópico onde o sistema de back-end enviará mensagens. Dependendo do cenário de negócios, esse componente criará uma ou mais assinaturas para tópicos correspondentes (por exemplo, alguns podem estar recebendo mensagens do sistema de RH, parte do sistema de Finanças e assim por diante)
   
        static void CreateSubscription(string connectionString)
        {
            // Create the subscription if it does not exist already
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);
   
            if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
            {
                namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
            }
        }
   
    d. ReceiveMessageAndSendNotification é usado para ler a mensagem do tópico usando sua assinatura e se a leitura for bem-sucedida, em seguida, criar uma notificação (no cenário de exemplo uma notificação nativa do Windows) para ser enviado para o aplicativo móvel usando os Hubs de Notificação do Azure.
   
        static void ReceiveMessageAndSendNotification(string connectionString)
        {
            // Initialize the Notification Hub
            string hubConnectionString = CloudConfigurationManager.GetSetting
                    ("Microsoft.NotificationHub.ConnectionString");
            hub = NotificationHubClient.CreateClientFromConnectionString
                    (hubConnectionString, "enterprisepushservicehub");
   
            SubscriptionClient Client =
                SubscriptionClient.CreateFromConnectionString
                        (connectionString, sampleTopic, sampleSubscription);
   
            Client.Receive();
   
            // Continuously process messages received from the subscription
            while (true)
            {
                BrokeredMessage message = Client.Receive();
                var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";
   
                if (message != null)
                {
                    try
                    {
                        Console.WriteLine(message.MessageId);
                        Console.WriteLine(message.SequenceNumber);
                        string messageBody = message.GetBody<string>();
                        Console.WriteLine("Body: " + messageBody + "\n");
   
                        toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                        SendNotificationAsync(toastMessage);
   
                        // Remove message from subscription
                        message.Complete();
                    }
                    catch (Exception)
                    {
                        // Indicate a problem, unlock message in subscription
                        message.Abandon();
                    }
                }
            }
        }
        static async void SendNotificationAsync(string message)
        {
            await hub.SendWindowsNativeNotificationAsync(message);
        }
   
    e. Para publicar isso como um **Trabalho Web**, clique com o botão direito na solução no Visual Studio e selecione **Publicar como Trabalho Web**
   
    ![][2]
   
    f. Selecione o perfil de publicação e crie um novo site do Azure, se ele ainda não existir, que hospedará esse Trabalho Web e, quando tiver o site, **Publicar**.
   
    ![][3]
   
    g. Configure o trabalho para ser “Executado Continuamente” para que, quando fizer logon no [Portal Clássico do Azure] , você veja algo semelhante ao seguinte:
   
    ![][4]
3. **EnterprisePushMobileApp**
   
    a. Isso é um aplicativo da Windows Store que receberá notificações do WebJob em execução como parte do back-end Móvel e exibi-lo. Isso se baseia em [Hubs de Notificação - tutorial do Windows Universal].  
   
    b. Certifique-se de que seu aplicativo está habilitado para receber notificações do sistema.
   
    c. Verifique se o seguinte código de registro de Hubs de Notificação está sendo chamado no aplicativo de inicialização (depois de substituir *HubName* e *DefaultListenSharedAccessSignature*:
   
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
            var result = await hub.RegisterNativeAsync(channel.Uri);
   
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

### <a name="running-sample"></a>Exemplo de execução:
1. Certifique-se de que seu WebJob está em execução com êxito e programado para "Executar Continuamente".
2. Execute o **EnterprisePushMobileApp** que iniciará o aplicativo da Windows Store.
3. Execute o aplicativo de console **EnterprisePushBackendSystem** que simulará o back-end do LoB e começará a enviar mensagens e você deverá ver notificações do sistema que aparecem como o seguinte:
   
    ![][5]
4. Originalmente, as mensagens foram enviadas para os tópicos do Barramento de Serviço que estava sendo monitorado por assinaturas do Barramento de Serviço no seu WebJob. Depois que uma mensagem foi recebida, uma notificação foi criada e enviada ao aplicativo móvel. Você pode verificar os logs do WebJob para confirmar o processamento quando for para o link Logs no [Portal Clássico do Azure] para seu WebJob:
   
    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Exemplos do Hub de Notificação]: https://github.com/Azure/azure-notificationhubs-samples
[Serviço Móvel do Azure]: http://azure.microsoft.com/documentation/services/mobile-services/
[Barramento de Serviço do Azure]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Programação Pub/Sub do Barramento de Serviço]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Trabalho Web do Azure]: ../app-service/web-sites-create-web-jobs.md
[Hubs de Notificação - tutorial do Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Portal Clássico do Azure]: https://manage.windowsazure.com/
