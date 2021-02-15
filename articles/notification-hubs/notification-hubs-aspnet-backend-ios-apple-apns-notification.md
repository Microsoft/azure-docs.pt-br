---
title: Enviar notificações por push para usuários específicos usando hubs de notificação do Azure | Microsoft Docs
description: Saiba como enviar notificações por push para usuários do iOS específicos usando os hubs de notificação do Azure.
documentationcenter: ios
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 167c666c536ee33531fd069dbd1edb530331a9f3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016924"
---
# <a name="tutorial-send-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Tutorial: enviar notificações por push para usuários específicos usando os hubs de notificação do Azure

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push a um usuário específico do aplicativo em um dispositivo específico. Um back-end da API Web ASP.NET é usado para autenticar clientes e gerar notificações, conforme mostrado no tópico de diretrizes [Registrando-se por meio do back-end do aplicativo](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

Neste tutorial, você deve executar as seguintes etapas:

> [!div class="checklist"]
> * Criar o projeto WebAPI
> * Autenticar clientes para o back-end da WebAPI
> * Registrar para receber notificações usando o back-end da WebAPI
> * Enviar notificações do back-end da WebAPI
> * Publicar o novo back-end da API Web
> * Modificar seu aplicativo iOS
> * Testar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você criou e configurou seu hub de notificação conforme descrito em [enviar notificações por push para aplicativos Ios usando os hubs de notificação do Azure](ios-sdk-get-started.md). Este tutorial também é um pré-requisito para o tutorial [Push Seguro (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) .
Se desejar usar os Aplicativos Móveis como seu serviço de back-end, veja [Mobile Apps Get Started with Push](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Modificar seu aplicativo iOS

1. Abra o aplicativo de exibição de página única que você criou no tutorial [enviar notificações por push para aplicativos Ios usando hubs de notificação do Azure](ios-sdk-get-started.md) .

   > [!NOTE]
   > Nesta seção, presumimos que você configurou seu projeto com um nome de organização vazio. Caso contrário, preceda o nome da sua organização para todos os nomes de classe.

2. No arquivo `Main.storyboard`, adicione os componentes mostrados na captura de tela da biblioteca de objetos.

    ![Editar storyboard no Xcode Interface Builder][1]

   * **Nome de usuário**: um UITextField com texto de espaço reservado, *Inserir nome de usuário*, imediatamente abaixo de enviar rótulo de resultados e restrito às margens esquerda e direita e abaixo do rótulo de resultados de envio.
   * **Senha**: um UITextField com texto de espaço reservado, *Digite a senha*, imediatamente abaixo do campo de texto Nome de usuário e restrito às margens esquerda e direita e abaixo do campo de texto Nome de usuário. Verifique a opção **Entrada de texto seguro** no Inspetor de atributo, em *Retornar chave*.
   * **Fazer logon**: um UIButton chamado imediatamente abaixo do campo de texto de senha e desmarque a opção **Habilitado** no Inspetor de atributos, em *Conteúdo de controle*
   * **WNS**: o rótulo e o comutador para habilitar o envio da notificação do Serviço de Notificação do Windows, se ele tiver sido configurado no hub. Confira o tutorial [Introdução ao Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
   * **GCM**: o rótulo e a opção para habilitar o envio da notificação para o Google Cloud Messaging, se ele tiver sido configurado no hub. Consulte o tutorial [Introdução ao Android](notification-hubs-android-push-notification-google-gcm-get-started.md) .
   * **APNS**: o rótulo e a opção para habilitar o envio da notificação ao Apple Platform Notification Service.
   * **Nome do Usuário do Destinatário**: um UITextField com texto de espaço reservado *marca de nome de usuário do destinatário*, imediatamente abaixo do rótulo GCM e restrita às margens esquerda e direita e abaixo do rótulo do GCM.

     Alguns componentes foram adicionados no tutorial [enviar notificações por push para aplicativos Ios usando hubs de notificação do Azure](ios-sdk-get-started.md) .

3. **Ctrl** arraste dos componentes na exibição para `ViewController.h` e adicione essas novas saídas:

    ```objc
    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
    @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
    @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
    @property (weak, nonatomic) IBOutlet UITextField *NotificationField;

    // Used to enable the buttons on the UI
    @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
    @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;

    // Used to enabled sending notifications across platforms
    @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;

    - (IBAction)LogInAction:(id)sender;
    ```

4. No `ViewController.h`, adicione o seguinte `#define` após as instruções de importação. Substitua o espaço reservado `<Your backend endpoint>` pela URL de destino que você usou para implantar o back-end do aplicativo na seção anterior. Por exemplo `http://your_backend.azurewebsites.net`:

    ```objc
    #define BACKEND_ENDPOINT @"<Your backend endpoint>"
    ```

5. Em seu projeto, crie uma nova classe Cocoa Touch chamada `RegisterClient` para fazer a interface com o back-end ASP.NET que você criou. Criar a classe herdeira de `NSObject`. Em seguida, adicione o seguinte código no `RegisterClient.h` :

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. No `RegisterClient.m`, atualize a seção `@interface`:

    ```objc
    @interface RegisterClient ()

    @property (strong, nonatomic) NSURLSession* session;
    @property (strong, nonatomic) NSURLSession* endpoint;

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion;
    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion;
    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;

    @end
    ```

7. Substitua a seção `@implementation` no RegisterClient.m pelo código a seguir:

    ```objc
    @implementation RegisterClient

    // Globals used by RegisterClient
    NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

    -(instancetype) initWithEndpoint:(NSString*)Endpoint
    {
        self = [super init];
        if (self) {
            NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
            _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
            _endpoint = Endpoint;
        }
        return self;
    }

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                andCompletion:(void(^)(NSError*))completion
    {
        [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
    }

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion
    {
        NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

        NSString *deviceTokenString = [[token description]
            stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
        deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                uppercaseString];

        [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
            completion:^(NSString* registrationId, NSError *error) {
            NSLog(@"regId: %@", registrationId);
            if (error) {
                completion(error);
                return;
            }

            [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                if (error) {
                    completion(error);
                    return;
                }

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                if (httpResponse.statusCode == 200) {
                    completion(nil);
                } else if (httpResponse.statusCode == 410 && retry) {
                    [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                } else {
                    NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                    completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }

            }];
        }];
    }

    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
    {
        NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                @"Tags": [tags allObjects]};
        NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                            options:NSJSONWritingPrettyPrinted error:nil];

        NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                            encoding:NSUTF8StringEncoding]);

        NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                registrationId];
        NSURL* requestURL = [NSURL URLWithString:endpoint];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"PUT"];
        [request setHTTPBody:jsonData];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
        [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            if (!error)
            {
                completion(response, error);
            }
            else
            {
                NSLog(@"Error request: %@", error);
                completion(nil, error);
            }
        }];
        [dataTask resume];
    }

    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion
    {
        NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                    objectForKey:RegistrationIdLocalStorageKey];

        if (registrationId)
        {
            completion(registrationId, nil);
            return;
        }

        // request new one & save
        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                _endpoint, token]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSString* registrationId = [[NSString alloc] initWithData:data
                    encoding:NSUTF8StringEncoding];

                // remove quotes
                registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                    [registrationId length]-2)];

                [[NSUserDefaults standardUserDefaults] setObject:registrationId
                    forKey:RegistrationIdLocalStorageKey];
                [[NSUserDefaults standardUserDefaults] synchronize];

                completion(registrationId, nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    @end
    ```

    Este código implementa a lógica explicada no artigo de orientação [Registrando-se a partir do back-end do seu aplicativo](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend), utilizando NSURLSession para realizar chamadas de REST para o back-end do seu aplicativo e NSUserDefaults para armazenar localmente o registrationId retornado pelo hub de notificação.

    Essa classe requer que sua propriedade `authorizationHeader` seja configurada para funcionar adequadamente. Essa propriedade é definida pela classe `ViewController` após o logon.

8. Em `ViewController.h`, adicione uma instrução `#import` a `RegisterClient.h`. Em seguida, adicione uma declaração para o token do dispositivo e faça referência a uma instância `RegisterClient` na seção `@interface`:

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. No ViewController.m, adicione uma declaração de método privado à seção `@interface` :

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > O snippet a seguir não é um esquema de autenticação seguro, você deve substituir a implementação de `createAndSetAuthenticationHeaderWithUsername:AndPassword:` pelo seu mecanismo de autenticação específico que gera um token de autenticação a ser consumido pela classe de cliente do registro, por exemplo, OAuth, Active Directory.

10. Em seguida, na seção `@implementation` do `ViewController.m`, adicione o seguinte código que adiciona a implementação para definir o cabeçalho de autenticação e o token do dispositivo.

    ```objc
    -(void) setDeviceToken: (NSData*) deviceToken
    {
        _deviceToken = deviceToken;
        self.LogInButton.enabled = YES;
    }

    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;
    {
        NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

        NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

        self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                    encoding:NSUTF8StringEncoding];
    }

    -(BOOL)textFieldShouldReturn:(UITextField *)textField
    {
        [textField resignFirstResponder];
        return YES;
    }
    ```

    Observe como a configuração do token do Dispositivo habilita o botão **fazer logon** . Isso ocorre porque, como parte da ação de logon, o controlador de exibição se registra para notificações por push no back-end do aplicativo. Você não deseja que a ação de **logon** seja acessível até que o token do dispositivo tenha sido configurado corretamente. É possível desacoplar o login do registro push desde que a primeira opção ocorra antes da última citada.

11. Em ViewController. m, use os trechos de código a seguir para implementar o método de ação para o botão de **logon** e um método para enviar a mensagem de notificação usando o back-end ASP.net.

    ```objc
    - (IBAction)LogInAction:(id)sender {
        // create authentication header and set it in register client
        NSString* username = self.UsernameField.text;
        NSString* password = self.PasswordField.text;

        [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

        __weak ViewController* selfie = self;
        [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
            andCompletion:^(NSError* error) {
            if (!error) {
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    selfie.SendNotificationButton.enabled = YES;
                    [self MessageBox:@"Success" message:@"Registered successfully!"];
                });
            }
        }];
    }

    - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
                Message:(NSString*)message
    {
        NSURLSession* session = [NSURLSession
            sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
            delegateQueue:nil];

        // Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
        NSURL* requestURL = [NSURL URLWithString:[NSString
            stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
            usernameTag]];

        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];

        // Get the mock authenticationheader from the register client
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
            self.registerClient.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
        [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];

        // Execute the send notification REST API on the ASP.NET Backend
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
                                    pns, httpResponse.statusCode, error];
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    // Append text because all 3 PNS calls may also have information to view
                    [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
                });
                NSLog(status);
            }

            if (data != NULL)
            {
                xmlParser = [[NSXMLParser alloc] initWithData:data];
                [xmlParser setDelegate:self];
                [xmlParser parse];
            }
        }];
        [dataTask resume];
    }
    ```

12. Atualizar a ação para o botão **Enviar notificação** para usar o back-end do ASP.NET e enviar a qualquer PNS habilitado por um comutador.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        //[self SendNotificationRESTAPI];
        [self SendToEnabledPlatforms];
    }

    -(void)SendToEnabledPlatforms
    {
        NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

        [self.sendResults setText:@""];

        if ([self.WNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

        if ([self.GCMSwitch isOn])
            [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

        if ([self.APNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
    }
    ```

13. Na função `ViewDidLoad`, adicione o código a seguir para instanciar a instância `RegisterClient` e definir o delegado para os seus campos de texto.

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. Agora, em `AppDelegate.m`, remova todo o conteúdo do método `application:didRegisterForPushNotificationWithDeviceToken:` e substitua-o pelo seguinte (para se certificar de que o controlador de exibição contenha o token de dispositivo mais recente, recuperado de APNs):

    ```objc
    // Add import to the top of the file
    #import "ViewController.h"

    - (void)application:(UIApplication *)application
                didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;
    }
    ```

15. Por fim, em `AppDelegate.m`, verifique se você tem o seguinte método:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>Testar o aplicativo

1. No XCode, execute o aplicativo em um dispositivo iOS físico (as notificações por push não funcionam no simulador).
2. Na interface do usuário do aplicativo iOS, insira o mesmo valor para o nome de usuário e senha. Em seguida, clique em **fazer logon**.

    ![Aplicativo de teste do iOS][2]

3. Você deverá ver um pop-up informando sobre o sucesso da inscrição. Clique em **OK**.

    ![Notificação de teste do iOS exibida][3]

4. No campo de texto **Marca de nome de usuário do destinatário* , insira a marca de nome de usuário usada com o registro de outro dispositivo.
5. Digite uma mensagem de notificação e clique em **Enviar notificação**. Somente os dispositivos que têm um registro com a marca de nome de usuário do destinatário recebem a mensagem de notificação. Isso é enviado somente aos usuários.

    ![Notificação marcada de teste do iOS][4]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como enviar notificações por push para usuários específicos que têm tags associadas seus registros. Para saber como enviar notificações por push, vá para o tutorial a seguir:

> [!div class="nextstepaction"]
>[Notificações por push com base no local](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
