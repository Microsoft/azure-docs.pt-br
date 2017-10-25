---
title: "Tutorial de últimas notícias de Hubs de Notificação - iOS"
description: "Saiba como usar Hubs de notificação do barramento de serviço do Azure para enviar notificações de últimas notícias para dispositivos iOS."
services: notification-hubs
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
ms.assetid: 6ead4169-deff-4947-858c-8c6cf03cc3b2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: dc47250db6fb3a2853dae24e02bda236154d93fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-notification-hubs-to-send-breaking-news"></a>Usar hubs de notificação para enviar notícias recentes
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Visão geral
Este tópico mostra como usar os Hubs de Notificação do Azure para difundir notificações de últimas notícias a um aplicativo iOS. Ao concluir, você poderá se registrar nas categorias de últimas notícias que desejar e receber notificações por push apenas para essas categorias. Esse cenário é um padrão comum para muitos aplicativos nos quais as notificações precisam ser enviadas para grupos de usuários que tenham anteriormente expressado seu interesse por elas; por ex., leitor de RSS, aplicativos para fãs de música, etc.

Os cenários de transmissão são habilitados por meio da inclusão de uma ou mais *marcas* durante a criação de um registro no hub de notificação. Quando as notificações são enviadas para um rótulo, todos os dispositivos que foram registrados para o rótulo receberão a notificação. Como os rótulos são simplesmente cadeias de caracteres, eles não precisam ser provisionados com antecedência. Para obter mais informações sobre marcas, consulte [Expressões de Marca e Roteamento dos Hubs de Notificação](notification-hubs-tags-segment-push-message.md).

## <a name="prerequisites"></a>Pré-requisitos
Este tópico se baseia no aplicativo criado em [Introdução aos Hubs de Notificação][get-started]. Antes de iniciar o tutorial, você deve primeiro concluir a [Introdução aos Hubs de Notificação][get-started].

## <a name="add-category-selection-to-the-app"></a>Adicionar a seleção de categorias ao aplicativo
A primeira etapa é adicionar os elementos da interface do usuário a seu storyboard existente que permite que o usuário selecione as categorias a serem registradas. As categorias selecionadas por um usuário são armazenadas no dispositivo. Quando o aplicativo é iniciado, o registro do dispositivo é criado no seu hub de notificação com as categorias selecionadas como rótulos.

1. No MainStoryboard_iPhone.storyboard, adicione os seguintes componentes da biblioteca de objetos:
   
   * Um rótulo com o texto "Breaking News",
   * Rótulos com os textos das categorias "World", "Politics", "Business", "Technology", "Science", "Sports",
   * Seis comutadores, um por categoria, defina cada comutador **Estado** para estar **Desligado** por padrão.
   * Um botão rotulado "Assinar"
     
     O storyboard deve ter a seguinte aparência:
     
     ![][3]
2. No editor do assistente, crie saídas para todos os comutadores e chame-os de "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"
3. Crie uma Ação para o botão chamado "subscribe". Seu ViewController.h deve conter o seguinte:
   
        @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;
   
        - (IBAction)subscribe:(id)sender;
4. Criar um novo **Cocoa Touch Class** chamado `Notifications`. Copie o código a seguir na seção da interface do arquivo Notifications.h:
   
        @property NSData* deviceToken;
   
        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;
   
        - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                    completion:(void (^)(NSError* error))completion;
   
        - (NSSet*)retrieveCategories;
   
        - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
5. Adicione a seguinte diretiva de importação a Notifications.m:
   
        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
6. Copie o código a seguir na seção de implementação do arquivo Notifications.m.
   
        SBNotificationHub* hub;
   
        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{
   
            hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                        notificationHubPath:hubName];
   
            return self;
        }
   
        - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
   
            [self subscribeWithCategories:categories completion:completion];
        }

        - (NSSet*)retrieveCategories {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

            if (!categories) return [[NSSet alloc] init];
            return [[NSSet alloc] initWithArray:categories];
        }


        - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
        {
           //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

            NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
                jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
        }



    Essa classe usa o armazenamento local para armazenar as categorias de notícias que esse dispositivo precisa receber. Além disso, ela contém um método para se registrar nessas categorias usando um registro de [Modelo](notification-hubs-templates-cross-platform-push-messages.md) .

1. No arquivo AppDelegate.h, adicione uma instrução de importação para Notifications.h e adicione uma propriedade para instância da classe Notificações:
   
        #import "Notifications.h"
   
        @property (nonatomic) Notifications* notifications;
2. No método **didFinishLaunchingWithOptions** no AppDelegate.m, adicione o código para inicializar a instância de notificações no início do método.  
   
    `HUBNAME` e `HUBLISTENACCESS` (definidos em hubinfo.h) já devem ter os espaços reservados `<hub name>` e `<connection string with listen access>` substituídos pelo nome de seu hub de notificação e a cadeia de conexão para *DefaultListenSharedAccessSignature* obtidos anteriormente
   
        self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
   
   > [!NOTE]
   > Como as credenciais que são distribuídas com um aplicativo cliente não são geralmente seguras, você só deve distribuir a chave para acesso de escuta com o aplicativo cliente. O acesso de escuta permite que seu aplicativo se registre para receber notificações, mas os registros existentes não podem ser modificados e as notificações não podem ser enviadas. A chave de acesso completa é usada em um serviço de back-end seguro para enviar notificações e alterar os registros existentes.
   > 
   > 
3. No método **didRegisterForRemoteNotificationsWithDeviceToken** no AppDelegate.m, substitua o código no método com o código a seguir para passar o token do dispositivo para a classe de notificações. A classe notificações executará o registro para notificações nas categorias. Se o usuário altera as seleções de categoria, podemos chamar o método `subscribeWithCategories` em resposta ao botão **assinar** para atualizá-los.
   
   > [!NOTE]
   > Como o token do dispositivo atribuído pelo APNS (Serviço de Notificação por Push da Apple) pode ser escolhido a qualquer momento, você deve se registrar para receber notificações com frequência para evitar falhas de notificação. Este exemplo registra a notificação a cada vez que o aplicativo é iniciado. Para os aplicativos que são executados com frequência, mais de uma vez por dia, é possível ignorar o registro para preservar a largura de banda se tiver passado menos de um dia desde o registro anterior.
   > 
   > 
   
        self.notifications.deviceToken = deviceToken;
   
        // Retrieves the categories from local storage and requests a registration for these categories
        // each time the app starts and performs a registration.
   
        NSSet* categories = [self.notifications retrieveCategories];
        [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

    Observe que neste ponto não deve haver nenhum outro código no método **didRegisterForRemoteNotificationsWithDeviceToken** .

1. Os métodos a seguir já devem estar presentes no AppDelegate.m com a conclusão do tutorial [Introdução aos Hubs de Notificação][get-started].  Caso contrário, adicione-os.
   
    -(void)MessageBox:(NSString *)title message:(NSString *)messageText  {
   
        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }
   
   * (void)application:(UIApplication *)application didReceiveRemoteNotification:   (NSDictionary *)userInfo {   NSLog(@"%@", userInfo);   [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]]; }
   
   Este método trata as notificações recebidas quando o aplicativo está em execução exibindo um simples **UIAlert**.
2. No ViewController.m, adicione uma instrução de importação para AppDelegate.h e copie o código a seguir no método **assinar** gerado por XCode. Esse código irá atualizar o registro de notificação para usar as novas marcas de categoria que o usuário escolheu na interface do usuário.
   
       ```
       #import "Notifications.h"
       ```
   
       NSMutableArray* categories = [[NSMutableArray alloc] init];
   
       if (self.WorldSwitch.isOn) [categories addObject:@"World"];
       if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
       if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
       if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
       if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
       if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];
   
       Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];
   
       [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
           if (!error) {
               [(AppDelegate*)[[UIApplication sharedApplication]delegate] MessageBox:@"Notification" message:@"Subscribed!"];
           } else {
               NSLog(@"Error subscribing: %@", error);
           }
       }];
   
   Esse método cria um **NSMutableArray** de categorias e usa a classe **Notifications** para armazenar a lista no armazenamento local e registra as marcas correspondentes com seu hub de notificação. Quando as categorias são alteradas, o registro é recriado com as novas categorias.
3. No ViewController.m, adicione o seguinte código ao método **viewDidLoad** para definir a interface do usuário com base nas categorias salvas anteriormente.

        // This updates the UI on startup based on the status of previously saved categories.

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        NSSet* categories = [notifications retrieveCategories];

        if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
        if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
        if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
        if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
        if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
        if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;



Agora, o aplicativo pode armazenar um conjunto de categorias no armazenamento local do dispositivo usado para se registrar no hub de notificação sempre que o aplicativo é iniciado.  O usuário pode alterar a seleção de categorias no tempo de execução e clicar no método **assinar** para atualizar o registro do dispositivo. Em seguida, você irá atualizar o aplicativo para enviar as notificações de últimas notícias diretamente nele.

## <a name="optional-sending-tagged-notifications"></a>(opcional) Enviando notificações marcadas
Se você não tiver acesso ao Visual Studio, você pode pular para a próxima seção e enviar notificações do próprio aplicativo. Você também pode enviar a notificação de modelo apropriada do [Portal Clássico do Azure] usando a guia depurar hub de notificação. 

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>(Opcional) Enviar notificações do dispositivo
Normalmente, as notificações podem ser enviadas por um serviço de back-end, mas você pode enviar notificações de últimas notícias diretamente do aplicativo. Para isso, atualizaremos o método `SendNotificationRESTAPI` que definimos no tutorial [Introdução aos Hubs de Notificação][get-started].

1. No ViewController.m, atualize o método `SendNotificationRESTAPI` como mostrado a seguir, para que ele aceite um parâmetro para a marca de categoria e envie a notificação de [modelo](notification-hubs-templates-cross-platform-push-messages.md) adequada.
   
        - (void)SendNotificationRESTAPI:(NSString*)categoryTag
        {
            NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                     defaultSessionConfiguration] delegate:nil delegateQueue:nil];
   
            NSString *json;
   
            // Construct the messages REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                               HUBNAME, API_VERSION]];
   
            // Generated the token to be used in the authorization header.
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];
   
            //Create the request to add the template notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];
   
            // Add the category as a tag
            [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];
   
            // Template notification
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                    categoryTag, self.notificationMessage.text];
   
            // Signify template notification format
            [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];
   
            // JSON Content-Type
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
   
            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];
   
            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
   
            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                       completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
               {
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                   if (error || httpResponse.statusCode != 200)
                   {
                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                   }
                   if (data != NULL)
                   {
                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
                       //[xmlParser setDelegate:self];
                       //[xmlParser parse];
                   }
               }];
   
            [dataTask resume];
        }
2. No ViewController.m, atualize a ação **Enviar notificação** conforme mostrado no código a seguir. Para que ele envie as notificações usando cada marca individualmente e para várias plataformas.

        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";

            NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                    @"Technology", @"Science", @"Sports", nil];

            // Lets send the message as breaking news for each category to WNS, GCM, and APNS
            // using a template.
            for(NSString* category in categories)
            {
                [self SendNotificationRESTAPI:category];
            }
        }



1. Recrie seu projeto e verifique se não há nenhum erro de compilação.

## <a name="run-the-app-and-generate-notifications"></a>Executar o aplicativo e gerar notificações
1. Pressione o botão Executar para compilar o projeto e iniciar o aplicativo. Selecione algumas opções de notícias recentes para assinar e pressione o botão **Assinar** . Você deve ver uma caixa de diálogo indicando que assinou as notificações.
   
    ![][1]
   
    Quando você escolher **Subscribe**, o aplicativo converterá as categorias selecionadas em marcas e solicitará um novo registro de dispositivo para as marcas selecionadas no hub de notificação.
2. Digite uma mensagem a ser enviada como últimas notícias e pressione o botão **Enviar Notificação** . Como alternativa, execute o aplicativo de console do .NET para gerar notificações.
   
    ![][2]
3. Cada dispositivo que assinou as últimas notícias receberá as notificações de últimas notícias que você acabou de enviar

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, aprendemos a enviar as últimas notícias por categoria. Considere a conclusão de um dos seguintes tutoriais que destacam outros cenários avançados de Hubs de Notificação:

* **[Usar os Hubs de Notificação para transmitir as últimas notícias localizadas]**
  
    Saiba como expandir o aplicativo das últimas notícias para habilitar o envio de notificações localizadas.

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png








<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Usar os Hubs de Notificação para transmitir as últimas notícias localizadas]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/
[Portal Clássico do Azure]: https://manage.windowsazure.com
