---
title: Push Avançado dos Hubs de Notificação do Azure
description: Saiba como enviar notificações por push avançado para um aplicativo iOS do Azure. Exemplos de códigos escritos em Objective-C e c#.
documentationcenter: ios
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/17/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 33626b7aee615d07ef88dd9fbca46e6512e2cafc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90090356"
---
# <a name="azure-notification-hubs-rich-push"></a>Push Avançado dos Hubs de Notificação do Azure

## <a name="overview"></a>Visão geral

Para atrair os usuários com conteúdo elaborado instantâneo, um aplicativo talvez queira enviar por push além do texto sem formatação. Essas notificações promovem interações do usuário e apresentam conteúdo como URLs, sons, imagens/cupons e muito mais. Este tutorial se baseia no tutorial [notificar usuários](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) e mostra como enviar notificações por push que incorporam cargas (por exemplo, imagens).

Este tutorial é compatível com iOS 7 e 8.

  ![Três instantâneos: uma tela de aplicativo com um botão enviar por push, uma tela inicial em um dispositivo e um logotipo do Windows com um botão voltar.][IOS1]

Em um alto nível:

1. O back-end do aplicativo:
   * Armazena a carga avançada (nesse caso, imagem) no banco de dados de back-end/armazenamento local.
   * Envia a ID dessa notificação avançada para o dispositivo.
2. Aplicativo no dispositivo:
   * Entra em contato com o back-end solicitando a carga avançada com a ID que recebe.
   * Envia notificações de usuários no dispositivo quando a recuperação de dados é concluída e mostra a carga imediatamente quando os usuários tocam para saber mais.

## <a name="webapi-project"></a>Projeto WebAPI

1. No Visual Studio, abra o projeto **AppBackend** que você criou no tutorial [Notificar Usuários](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .
2. Obter uma imagem para notificar usuários e colocá-la em uma pasta **img** no diretório do projeto.
3. Clique em **Mostrar Todos os Arquivos** no Gerenciador de Soluções e clique com o botão direito na pasta para **Incluir no Projeto**.
4. Com a imagem selecionada, altere sua **ação de Build** na janela **Propriedades** para **recurso incorporado**.

    ![Captura de tela de Gerenciador de Soluções. O arquivo de imagem é selecionado e, em seu painel de propriedades, recurso incorporado é listado como a ação de compilação.][IOS2]
5. No `Notifications.cs` , adicione a seguinte `using` instrução:

    ```csharp
    using System.Reflection;
    ```

6. Substitua a `Notifications` classe pelo código a seguir. Certifique-se de substituir os espaços reservados pelas suas credenciais do hub de notificação e o nome do arquivo de imagem:

    ```csharp
    public class Notification {
        public int Id { get; set; }
        // Initial notification message to display to users
        public string Message { get; set; }
        // Type of rich payload (developer-defined)
        public string RichType { get; set; }
        public string Payload { get; set; }
        public bool Read { get; set; }
    }

    public class Notifications {
        public static Notifications Instance = new Notifications();

        private List<Notification> notifications = new List<Notification>();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
        }

        public Notification CreateNotification(string message, string richType, string payload) {
            var notification = new Notification() {
                Id = notifications.Count,
                Message = message,
                RichType = richType,
                Payload = payload,
                Read = false
            };

            notifications.Add(notification);

            return notification;
        }

        public Stream ReadImage(int id) {
            var assembly = Assembly.GetExecutingAssembly();
            // Placeholder: image file name (for example, logo.png).
            return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
        }
    }
    ```

7. No `NotificationsController.cs` , redefina `NotificationsController` com o código a seguir. Isso envia uma ID de notificação avançada silenciosa inicial para o dispositivo e permite a recuperação do lado do cliente da imagem:

    ```csharp
    // Return http response with image binary
    public HttpResponseMessage Get(int id) {
        var stream = Notifications.Instance.ReadImage(id);

        var result = new HttpResponseMessage(HttpStatusCode.OK);
        result.Content = new StreamContent(stream);
        // Switch in your image extension for "png"
        result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");

        return result;
    }

    // Create rich notification and send initial silent notification (containing id) to client
    public async Task<HttpResponseMessage> Post() {
        // Replace the placeholder with image file name
        var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");

        var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

        // Silent notification with content available
        var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";

        // Send notification to apns
        await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

8. Agora, implante novamente esse aplicativo em um site do Azure para torná-lo acessível de todos os dispositivos. Clique com o botão direito do mouse no projeto **AppBackend** e selecione **Publicar**.
9. Selecione **site do Azure** como seu destino de publicação. Entre com sua conta do Azure e selecione um site novo ou existente e anote a propriedade **URL de destino** na guia **conexão** . Nós nos referimos a essa URL como *ponto de extremidade de back-end* posteriormente neste tutorial. Selecione **Publicar**.

## <a name="modify-the-ios-project"></a>Modificar o projeto iOS

Agora que você modificou o back-end do aplicativo para enviar apenas a *ID* de uma notificação, altere seu aplicativo IOS para lidar com essa ID e recupere a mensagem avançada do seu back-end:

1. Abra o projeto do iOS e habilite notificações remotas indo para o destino do aplicativo principal na seção **Destinos** .
2. Selecione **recursos**, habilitar **modos de segundo plano** e marque a caixa de seleção **notificações remotas** .

    ![Captura de tela do projeto do iOS que mostra as telas de recursos. Os modos de segundo plano são ativados e a caixa de seleção notificações remotas é marcada.][IOS3]
3. Abra `Main.storyboard` o e verifique se você tem um controlador de exibição (conhecido como controlador de exibição de página inicial neste tutorial) no tutorial [notificar usuário](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .
4. Adicione um **controlador de navegação** ao storyboard e arraste o controle de página inicial para torná-lo a **exibição raiz** da navegação. Verifique se que **é o controlador de exibição inicial** no Inspetor de atributos está selecionado apenas para o controlador de navegação.
5. Adicione um **controlador de exibição** ao storyboard e adicione uma **exibição de imagem**. Esta é a página que os usuários verão quando desejarem saber mais e clicarem na notificação. O storyboard deve ter a seguinte aparência:

    ![Captura de tela de um Storyboard. Três telas de aplicativo são visíveis: um modo de exibição de navegação, uma exibição inicial e uma exibição de imagem.][IOS4]
6. Clique no **controlador de exibição de página inicial** no storyboard e verifique se ele tem **HomeViewController** como sua **classe personalizada** e a **ID do storyboard** no Inspetor de identidade.
7. Faça o mesmo para o controlador de exibição de imagem, como **imageViewController**.
8. Em seguida, crie uma nova classe de controlador de exibição chamada **imageViewController** para manipular a interface do usuário que você acabou de criar.
9. Em **imageViewController. h**, adicione o código a seguir às declarações de interface do controlador. Certifique-se de manter Control pressionado e arrastar da exibição de imagem do storyboard para essas propriedades para vincular as duas:

    ```objc
    @property (weak, nonatomic) IBOutlet UIImageView *myImage;
    @property (strong) UIImage* imagePayload;
    ```

10. Em `imageViewController.m`, adicione o seguinte ao final de `viewDidload`:

    ```objc
    // Display the UI Image in UI Image View
    [self.myImage setImage:self.imagePayload];
    ```

11. Em `AppDelegate.m`, importe o controlador de imagem criado:

    ```objc
    #import "imageViewController.h"
    ```

12. Adicione uma seção de interface com a seguinte declaração:

    ```objc
    @interface AppDelegate ()

    @property UIImage* imagePayload;
    @property NSDictionary* userInfo;
    @property BOOL iOS8;

    // Obtain content from backend with notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

    // Redirect to Image View Controller after notification interaction
    - (void)redirectToImageViewWithImage: (UIImage *)img;

    @end
    ```

13. Em `AppDelegate`, verifique se o aplicativo está registrado para notificações silenciosas em `application: didFinishLaunchingWithOptions`:

    ```objc
    // Software version
    self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];

    // Register for remote notifications for iOS8 and previous versions
    if (self.iOS8) {
        NSLog(@"This device is running with iOS8.");

        // Action
        UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
        richPushAction.identifier = @"richPushMore";
        richPushAction.activationMode = UIUserNotificationActivationModeForeground;
        richPushAction.authenticationRequired = NO;
        richPushAction.title = @"More";

        // Notification category
        UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
        richPushCategory.identifier = @"richPush";
        [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];

        // Notification categories
        NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];

        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert |
                                                UIUserNotificationTypeBadge
                                                                                    categories:richPushCategories];

        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

    }
    else {
        // Previous iOS versions
        NSLog(@"This device is running with iOS7 or earlier versions.");

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    }

    return YES;
    ```

14. Substitua a seguinte implementação para `application:didRegisterForRemoteNotificationsWithDeviceToken` que as alterações da interface do usuário do storyboard sejam alteradas em conta:

    ```objc
    // Access navigation controller which is at the root of window
    UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
    // Get home view controller from stack on navigation controller
    homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
    hvc.deviceToken = deviceToken;
    ```

15. Em seguida, adicione os métodos a seguir a `AppDelegate.m` para recuperar a imagem do ponto de extremidade e enviar uma notificação local quando a recuperação estiver concluída. Não deixe de substituir o espaço reservado `{backend endpoint}` pelo seu ponto de extremidade do back-end:

    ```objc
    NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";

    // Helper: retrieve notification content from backend with rich notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
        // Check if authenticated
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                delegate:nil
                                delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {

            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200) {
                // From NSData to UIImage
                self.imagePayload = [UIImage imageWithData:data];

                completion(nil);
            }
            else {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(error);
                else {
                    completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    // Handle silent push notifications when id is sent from backend
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
        self.userInfo = userInfo;
        int richId = [[self.userInfo objectForKey:@"richId"] intValue];
        NSString* richType = [self.userInfo objectForKey:@"richType"];

        // Retrieve image data
        if ([richType isEqualToString:@"img"]) {  
            [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                if (!error){
                    // Send local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];

                    // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                    localNotification.userInfo = self.userInfo;
                    localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];

                    // iOS8 categories
                    if (self.iOS8) {
                        localNotification.category = @"richPush";
                    }

                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    handler(UIBackgroundFetchResultNewData);
                }
                else{
                    handler(UIBackgroundFetchResultFailed);
                }
            }];
        }
        // Add "else if" here to handle more types of rich content such as url, sound files, etc.
    }
    ```

16. Manipule a notificação local anterior abrindo o controlador de exibição de imagem no `AppDelegate.m` com os seguintes métodos:

    ```objc
    // Helper: redirect users to image view controller
    - (void)redirectToImageViewWithImage: (UIImage *)img {
        UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
        UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main" bundle: nil];
        imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
        // Pass data/image to image view controller
        imgViewController.imagePayload = img;

        // Redirect
        [navigationController pushViewController:imgViewController animated:YES];
    }

    // Handle local notification sent above in didReceiveRemoteNotification
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
        if (application.applicationState == UIApplicationStateActive) {
            // Show in-app alert with an extra "more" button
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];
            [alert show];
        }
        // App becomes active from user's tap on notification
        else {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
    }

    // Handle buttons in in-app alerts and redirect with data/image
    - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
        // Handle "more" button
        if (buttonIndex == 1)
        {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        // Add "else if" here to handle more buttons
    }

    // Handle notification setting actions in iOS8
    - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
        // Handle richPush related buttons
        if ([identifier isEqualToString:@"richPushMore"]) {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        completionHandler();
    }
    ```

## <a name="run-the-application"></a>Executar o aplicativo

1. No XCode, execute o aplicativo em um dispositivo iOS físico (as notificações por push não funcionarão no simulador).
2. Na IU de aplicativo do iOS, insira um nome de usuário e senha do mesmo valor para a autenticação e clique em **Fazer Logon**.
3. Clique em **Enviar por push** e você verá um alerta no aplicativo. Se você clicar em **Mais**, você será levado à imagem que optou por incluir no back-end do aplicativo.
4. Você pode também clicar em **Enviar por push** e imediatamente pressionar o botão Início do seu dispositivo. Em alguns momentos, você receberá uma notificação por push. Se você tocar nela ou clicar em Mais, será levado para seu aplicativo e o conteúdo da imagem avançado.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
