---
title: "Como enviar notificações por push para iOS com Hubs de Notificação do Azure | Microsoft Docs"
description: "Neste tutorial, você aprenderá a usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo do iOS."
services: notification-hubs
documentationcenter: ios
keywords: "notificação por push, notificações por push, notificações por push do ios"
author: ysxu
manager: erikre
editor: 
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: ab0777f859e80afcd61e371056b44d018c7b7ab9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sending-push-notifications-to-ios-with-azure-notification-hubs"></a>Como enviar notificações por push para iOS com Hubs de Notificação do Azure
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Visão geral
> [!NOTE]
> Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).
> 
> 

Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo iOS. Você criará um aplicativo do iOS em branco que recebe notificações por push usando o [APNs (Apple Push Notification Service)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html). 

Ao finalizar, você poderá usar seu hub de notificação para transmitir notificações por push a todos os dispositivos que executam seu aplicativo.

## <a name="before-you-begin"></a>Antes de começar
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

O código completo deste tutorial pode ser encontrado [no GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial exige o seguinte:

* [versão 1.2.4 do SDK do iOS dos Serviços Móveis]
* Versão mais recente do [Xcode]
* Um dispositivo compatível com o iOS 8 (ou versão posterior)
* [Programa de Desenvolvedores de iOS](https://developer.apple.com/programs/) 
  
  > [!NOTE]
  > Devido aos requisitos de configuração das notificações por push, você deve implantá-las e testá-las em um dispositivo iOS físico (iPhone ou iPad), em vez de usar o Simulador de iOS.
  > 
  > 

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre os Hubs de Notificação para aplicativos do iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Configurar o Hub de Notificação para notificações por push do iOS
Esta seção mostra a criação de um novo hub de notificação e a configuração da autenticação com APNS usando o certificado push **.p12** que você criou. Se você quiser usar um hub de notificação já criado, ignore a etapa 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p>Clique no botão <b>Serviços de Notificação</b> na folha <b>Configurações</b> e selecione <b>Apple (APNS)</b>. Clique em <b>Carregar certificad</b>o e selecione o arquivo <b>.p12</b> exportado anteriormente. Especifique também a senha correta.</p>

<p>Selecione o modo de <b>Área Restrita</b>, pois se trata de desenvolvimento. Use a <b>Produção</b> apenas se quiser enviar notificações por push aos usuários que adquiriram seu aplicativo na loja.</p>
</li>
</ol>
&emsp;&emsp;&emsp;&emsp;![Configurar o APNS no portal do Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png)

&emsp;&emsp;&emsp;&emsp;![Configurar certificação do APNS no Portal do Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

Seu hub de notificação agora está configurado para funcionar com o APNS e você tem as cadeias de conexão para registrar seu aplicativo e enviar notificações por push.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Conectar seu aplicativo do iOS aos Hubs de Notificação
1. No Xcode, crie um novo projeto do iOS e selecione o modelo **Aplicativo de Modo de Exibição Único** .
   
    ![Xcode - aplicativo de modo de exibição único][8]
    
2. Ao definir as opções para o novo projeto, lembre-se de usar os mesmos **Nome do Produto** e **Identificador Organizacional** que você usou quando configurou a ID do pacote no portal de desenvolvimento da Apple.
   
    ![Xcode - opções de projeto][11]
    
3. Em **Destinos**, clique no nome do projeto, clique na guia **Configurações de Compilação** e expanda **Identidade de Assinatura de Código**. Depois, em **Depurar**, defina sua identidade de assinatura de código. Alterne **Níveis** de **Básico** para **Todos** e defina o **Perfil de Provisionamento** para o perfil de provisionamento que você criou anteriormente.
   
    Se você não vir o novo perfil de provisionamento que você criou no Xcode, tente atualizar os perfis da sua identidade de assinatura. Clique em **Xcode** na barra de menus, em **Preferências**, na guia **Conta**, no botão **Exibir Detalhes**, em sua identidade de assinatura e depois clique no botão Atualizar no canto inferior direito.
   
    ![Xcode - perfil de provisionamento][9]
4. Baixe a [versão 1.2.4 do SDK do iOS dos Serviços Móveis] e descompacte o arquivo. No Xcode, clique com o botão direito do mouse no projeto e clique na opção **Adicionar Arquivos a** para adicionar a pasta **WindowsAzureMessaging.framework** ao seu projeto do Xcode. Selecione **Copiar itens se necessário** e depois clique em **Adicionar**.
   
   > [!NOTE]
   > No momento, o SDK de hubs de notificação não oferece suporte a bitcode em Xcode 7.  Você deve definir **Habilitar Bitcode** como **Não** nas **Opções de Build** para seu projeto.
   > 
   > 
   
    ![Descompactar o SDK do Azure][10]
5. Adicione ao projeto um novo arquivo de cabeçalho chamado `HubInfo.h`. Esse arquivo conterá as constantes para o hub de notificação.  Adicione as seguintes definições e substitua os espaços reservados da cadeia de caracteres literal pelo *nome do hub* e a *DefaultListenSharedAccessSignature* que você anotou anteriormente.
   
        #ifndef HubInfo_h
        #define HubInfo_h
   
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
   
        #endif /* HubInfo_h */
6. Abra o arquivo `AppDelegate.h` e adicione as seguintes diretivas de importação:
   
         #import <WindowsAzureMessaging/WindowsAzureMessaging.h> 
         #import "HubInfo.h"
7. No `AppDelegate.m file`, adicione o código a seguir ao método `didFinishLaunchingWithOptions` de acordo com sua versão do iOS. Esse código registra seu identificador de dispositivo nas APNs:
   
    Para iOS 8:
   
         UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];
   
        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
   
    Para versões do iOS anteriores a 8:
   
         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
8. No mesmo arquivo, adicione os métodos a seguir. Esse código conecta-se ao hub de notificação usando as informações de conexão que você especificou em HubInfo.h. Esse código fornece o token do dispositivo ao hub de notificação para que o hub de notificação possa enviar notificações:
   
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];
   
            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                    [self MessageBox:@"Registration Status" message:@"Registered"];
                }
            }];
        }
   
        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }
9. No mesmo arquivo, adicione o seguinte método para exibir um **UIAlert** caso a notificação seja recebida enquanto o aplicativo estiver ativo:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

1. Compile e execute o aplicativo no dispositivo para verificar se não há falhas.

## <a name="send-test-push-notifications"></a>Enviar notificações por push de teste
Você pode testar o recebimento de notificações no aplicativo enviando notificações por push no [Portal do Azure] por meio da seção **Solução de problemas** na folha de hub (use a opção *Testar Enviar* ).

![Portal do Azure - Testar Enviar][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="optional-send-push-notifications-from-the-app"></a>(Opcional) Enviar notificações por push do aplicativo
> [!IMPORTANT]
> Este exemplo de envio de notificações do aplicativo cliente é fornecido somente para fins de aprendizado. Como o exemplo exigirá a presença da `DefaultFullSharedAccessSignature` no aplicativo cliente, ele expõe seu hub de notificação ao risco de um usuário obter acesso para enviar notificações não autorizadas aos seus clientes.
> 
> 

Se você quer enviar notificações por push de dentro de um aplicativo, esta seção fornece um exemplo de como fazer isso usando a interface REST.

1. No Xcode, abra `Main.storyboard` e adicione os seguintes componentes da interface do usuário da biblioteca de objetos para permitir que o usuário envie notificações por push no aplicativo:
   
   * Um rótulo sem texto de rótulo. Ele será usado para relatar erros no envio de notificações. A propriedade **Lines** deve ser definida como **0** para que ela dimensione automaticamente as margens restritas à direita e à esquerda e na parte superior da exibição.
   * Um campo de texto com o texto de **Espaço Reservado** definido como **Inserir Mensagem de Notificação**. Restrinja o campo logo abaixo do rótulo, conforme mostrado abaixo. Defina o Controlador de Exibição como o delegado de saída.
   * Um botão chamado **Enviar Notificação** restrito logo abaixo do campo de texto e no centro horizontal.
     
     O modo de exibição deve ser semelhante ao seguinte:
     
     ![Designer do Xcode][32]
2. [Adicione saídas](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) para o rótulo e o campo de texto conectados à exibição e atualize a definição `interface` para dar suporte a `UITextFieldDelegate` e a `NSXMLParserDelegate`. Adicione as três declarações de propriedade mostradas abaixo para ajudar a dar suporte a chamar à API REST e fazer a análise da resposta.
   
    O arquivo ViewController.h deve ser semelhante ao seguinte:
   
        #import <UIKit/UIKit.h>
   
        @interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
        {
            NSXMLParser *xmlParser;
        }
   
        // Make sure these outlets are connected to your UI by ctrl+dragging
        @property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
        @property (weak, nonatomic) IBOutlet UILabel *sendResults;
   
        @property (copy, nonatomic) NSString *statusResult;
        @property (copy, nonatomic) NSString *currentElement;
   
        @end
3. Abra `HubInfo.h` e adicione as constantes a seguir, que serão usadas para enviar notificações ao hub. Substitua a cadeia de caracteres literal no espaço reservado pela cadeia de conexão *DefaultFullSharedAccessSignature* real.
   
        #define API_VERSION @"?api-version=2015-01"
        #define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"
4. Adicione as instruções `#import` a seguir ao arquivo `ViewController.h`.
   
        #import <CommonCrypto/CommonHMAC.h>
        #import "HubInfo.h"
5. Em `ViewController.m` , adicione o código a seguir à implementação da interface. Esse código analisará a cadeia de conexão *DefaultFullSharedAccessSignature* . Como mencionado na [referência da API REST](http://msdn.microsoft.com/library/azure/dn495627.aspx), essas informações analisadas serão usadas para gerar um token SaS para o cabeçalho de solicitação da **Autorização** .
   
        NSString *HubEndpoint;
        NSString *HubSasKeyName;
        NSString *HubSasKeyValue;
   
        -(void)ParseConnectionString
        {
            NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
            NSString *part;
   
            if ([parts count] != 3)
            {
                NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
                    reason:@"Invalid full shared access connection string" userInfo:nil];
   
                @throw parseException;
            }
   
            for (part in parts)
            {
                if ([part hasPrefix:@"Endpoint"])
                {
                    HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
                }
                else if ([part hasPrefix:@"SharedAccessKeyName"])
                {
                    HubSasKeyName = [part substringFromIndex:20];
                }
                else if ([part hasPrefix:@"SharedAccessKey"])
                {
                    HubSasKeyValue = [part substringFromIndex:16];
                }
            }
        }
6. Em `ViewController.m`, atualize o método `viewDidLoad` para analisar a cadeia de conexão quando a exibição for carregada. além disso, adicione os métodos de utilitário, mostrados abaixo, à implementação da interface.  

        - (void)viewDidLoad
        {
            [super viewDidLoad];
            [self ParseConnectionString];
            [_notificationMessage setDelegate:self];
        }

        -(NSString *)CF_URLEncodedString:(NSString *)inputString
        {
           return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
                NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }





1. Em `ViewController.m`, adicione o código a seguir à implementação da interface para gerar o token de autorização de SaS que será fornecido no cabeçalho da **Autorização** , como mencionamos na [Referência da API REST](http://msdn.microsoft.com/library/azure/dn495627.aspx).
   
        -(NSString*) generateSasToken:(NSString*)uri
        {
            NSString *targetUri;
            NSString* utf8LowercasedUri = NULL;
            NSString *signature = NULL;
            NSString *token = NULL;
   
            @try
            {
                // Add expiration
                uri = [uri lowercaseString];
                utf8LowercasedUri = [self CF_URLEncodedString:uri];
                targetUri = [utf8LowercasedUri lowercaseString];
                NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60;
                UInt64 expires = trunc(expiresOnDate);
                NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];
   
                // Get an hmac_sha1 Mac instance and initialize with the signing key
                const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
                const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
                unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
                CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
                NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
                signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];
   
                // Construct authorization token string
                token = [NSString stringWithFormat:@"SharedAccessSignature sig=%@&se=%qu&skn=%@&sr=%@",
                    signature, expires, HubSasKeyName, targetUri];
            }
            @catch (NSException *exception)
            {
                [self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
            }
            @finally
            {
                if (utf8LowercasedUri != NULL)
                    CFRelease((CFStringRef)utf8LowercasedUri);
                if (signature != NULL)
                CFRelease((CFStringRef)signature);
            }
   
            return token;
        }
2. Ctrl+arraste do botão **Enviar Notificação** para `ViewController.m` a fim de adicionar uma ação chamada **SendNotificationMessage** ao evento **Touch Down**. Atualize o método com o código a seguir para enviar a notificação usando a API REST.
   
        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";
            [self SendNotificationRESTAPI];
        }
   
        - (void)SendNotificationRESTAPI
        {
            NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];
   
            // Apple Notification format of the notification message
            NSString *json = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"%@\"}}",
                                self.notificationMessage.text];
   
            // Construct the message's REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                                HUBNAME, API_VERSION]];
   
            // Generate the token to be used in the authorization header
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];
   
            //Create the request to add the APNs notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
   
            // Signify Apple notification format
            [request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];
   
            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];
   
            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
   
            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || (httpResponse.statusCode != 200 && httpResponse.statusCode != 201))
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
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
3. Em `ViewController.m`, adicione o método de representante a seguir para dar suporte ao fechamento do teclado para o campo de texto. Use Ctrl + arrastar do campo de texto para o ícone do Controlador de Exibição no designer de interface para definir o controlador de exibição como o representante de saída.
   
        //===[ Implement UITextFieldDelegate methods ]===
   
        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }
4. Em `ViewController.m`, adicione os métodos de representante a seguir para dar suporte à análise da resposta usando `NSXMLParser`.
   
       //===[ Implement NSXMLParserDelegate methods ]===
   
       -(void)parserDidStartDocument:(NSXMLParser *)parser
       {
           self.statusResult = @"";
       }
   
       -(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
           namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
           attributes:(NSDictionary *)attributeDict
       {
           NSString * element = [elementName lowercaseString];
           NSLog(@"*** New element parsed : %@ ***",element);
   
           if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
           {
               self.currentElement = element;
           }
       }
   
       -(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
       {
           self.statusResult = [self.statusResult stringByAppendingString:
               [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
       }
   
       -(void)parserDidEndDocument:(NSXMLParser *)parser
       {
           // Set the status label text on the UI thread
           dispatch_async(dispatch_get_main_queue(),
           ^{
               [self.sendResults setText:self.statusResult];
           });
       }
5. Compile o projeto e verifique se não há erros.

> [!NOTE]
> Se você encontrar um erro de compilação em Xcode7 sobre o suporte de bitcode, altere as **Configurações de Build** > **Habilitar Bitcode (ENABLE_BITCODE)** para **NO** no Xcode. No momento, o SDK de Hubs de Notificação não oferece suporte a bitcode. 
> 
> 

Você encontrará todas as cargas de notificação possíveis no [Guia de programação de notificação local e por push]da Apple.

## <a name="checking-if-your-app-can-receive-push-notifications"></a>Como verificar se o aplicativo pode receber notificações por push
Para testar as notificações por push no iOS, você deve implantar o aplicativo em um dispositivo iOS físico. Não é possível enviar notificações por push da Apple com o Simulador do iOS.

1. Execute o aplicativo e verifique se o registro foi bem-sucedido e pressione **OK**.
   
    ![Teste de registro de notificação por push de aplicativo do iOS][33]
2. Você pode enviar uma notificação por push de teste do [Portal do Azure], conforme descrito acima. Se você adicionou o código para enviar notificações por push no aplicativo, toque dentro do campo de texto para inserir uma mensagem de notificação. Em seguida, pressione o botão **Enviar** no teclado ou o botão **Enviar Notificação** no modo de exibição para enviar a mensagem de notificação.
   
    ![Teste de envio de notificação por push de aplicativo do iOS][34]
3. A notificação por push é enviada a todos os dispositivos que estão registrados para receber as notificações do Hub de Notificação específico.
   
    ![Teste de recebimento de notificação por push de aplicativo do iOS][35]

## <a name="next-steps"></a>Próximas etapas
Neste exemplo simples, você enviou notificações por push a todos os seus dispositivos iOS registrados. Como próxima etapa do aprendizado, sugerimos que você vá para o tutorial [Notificar usuários nos Hubs de Notificação do Azure para iOS com o back-end do .NET] , que o orientará na criação de um back-end para enviar notificações por push usando marcas. 

Se desejar segmentar os usuários por grupos de interesse, você também poderá ir para o tutorial [Usar Hubs de Notificação para enviar as últimas notícias] . 

Para obter informações gerais sobre os Hubs de Notificação, confira [Diretrizes dos Hubs de Notificação].

<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[versão 1.2.4 do SDK do iOS dos Serviços Móveis]: http://aka.ms/kymw2g
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Azure Classic Portal]: https://manage.windowsazure.com/
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Notificar usuários nos Hubs de Notificação do Azure para iOS com o back-end do .NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Usar Hubs de Notificação para enviar as últimas notícias]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Guia de programação de notificação local e por push]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Portal do Azure]: https://portal.azure.com
