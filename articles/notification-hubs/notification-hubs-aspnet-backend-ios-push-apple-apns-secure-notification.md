---
title: Push Seguro dos Hubs de Notificação do Azure
description: Saiba como enviar notificações por push seguro para um aplicativo iOS do Azure. Exemplos de códigos escritos em Objective-C e c#.
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: d88bdb1eaeb95413df84bf69ed4fc763b6d4901f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61458478"
---
# <a name="azure-notification-hubs-secure-push"></a>Push Seguro dos Hubs de Notificação do Azure

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Visão geral

O suporte à notificação por push no Microsoft Azure permite que você acesse uma infraestrutura de envio por push fácil de usar, multiplataforma e expansível que simplifica em muito a implementação de notificações por push para aplicativos de consumidor e empresariais para plataformas móveis.

Devido a restrições regulatórias ou de segurança, às vezes, um aplicativo pode querer incluir algo na notificação que não pode ser transmitido por meio da infraestrutura de notificação por push padrão. Este tutorial descreve como obter a mesma experiência ao enviar informações confidenciais por meio de uma conexão segura e autenticada entre o dispositivo cliente e o back-end do aplicativo.

Em um nível superior, o fluxo é o seguinte:

1. O back-end do aplicativo:
   * Armazena uma carga segura no banco de dados de back-end.
   * Envia a ID dessa notificação ao dispositivo (nenhuma informação segura é enviada).
2. O dispositivo no aplicativo, ao receber a notificação:
   * O dispositivo entra em contato com o back-end solicitando a carga segura.
   * O aplicativo pode mostrar a carga como uma notificação no dispositivo.

É importante observar que no fluxo anterior (e neste tutorial), pressupomos que o dispositivo armazena um token de autenticação no armazenamento local depois que o usuário faz logon. Isso garante uma experiência perfeita já que o dispositivo pode recuperar a carga de segurança da notificação usando este token. Se o seu aplicativo não armazenar tokens de autenticação no dispositivo, ou se esses tokens puderem expirar, o aplicativo do dispositivo, após receber a notificação, deve exibir uma notificação genérica solicitando que o usuário inicie o aplicativo. Dessa forma, o aplicativo autentica o usuário e mostra a carga de notificação.

Este tutorial de Push Seguro mostra como enviar uma notificação por push de maneira segura. O tutorial baseia-se no tutorial [Notificação de usuários](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) , por isso, você deve concluir as etapas nesse tutorial primeiro.

> [!NOTE]
> Este tutorial presume que você criou e configurou o seu hub de notificação conforme descrito em [Introdução aos Hubs de Notificação (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Modificar o projeto iOS

Agora que você modificou o back-end do aplicativo para enviar apenas a *ID* de uma notificação, é preciso alterar o aplicativo iOS para manipular essa notificação e retornar a chamada do back-end para recuperar a mensagem segura a ser exibida.

Para isso, precisamos gravar a lógica para recuperar o conteúdo seguro do back-end do aplicativo.

1. Em `AppDelegate.m`, verifique se o aplicativo registra para notificações silenciosas para processar a ID de notificação enviada do back-end. Adicione a opção `UIRemoteNotificationTypeNewsstandContentAvailability` em didFinishLaunchingWithOptions:

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. Em `AppDelegate.m`, adicione uma seção de implementação no topo, com a seguinte declaração:

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. Em seguida, adicione o código a seguir à seção de implementação, substituindo o espaço reservado `{back-end endpoint}` pelo ponto de extremidade para seu back-end obtido anteriormente:

    ```objc
    NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    {
        // check if authenticated
        ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
        NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                    sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                    delegate:nil
                                    delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                completion([json objectForKey:@"Payload"], nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }
    ```

    Esse método chama o back-end do seu aplicativo para recuperar o conteúdo da notificação usando as credenciais armazenadas nas preferências compartilhadas.

4. Agora, precisamos manipular a notificação de entrada e utilizar o método acima para recuperar o conteúdo a ser exibido. Primeiro, precisamos habilitar o aplicativo iOS para ser executado em segundo plano ao receber uma notificação por push. Em **XCode**, selecione seu projeto de aplicativo no painel à esquerda e depois clique no seu destino de aplicativo principal na seção **Destinos** do painel central.
5. Então, clique na guia **Recursos** na parte superior do painel central e marque a caixa de seleção **Notificações Remotas**.

    ![][IOS1]

6. Em `AppDelegate.m`, adicione o método a seguir para manipular notificações por push:

    ```objc
    -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
        NSLog(@"%@", userInfo);

        [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
            if (!error) {
                // show local notification
                UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                localNotification.alertBody = payload;
                localNotification.timeZone = [NSTimeZone defaultTimeZone];
                [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                completionHandler(UIBackgroundFetchResultNewData);
            } else {
                completionHandler(UIBackgroundFetchResultFailed);
            }
        }];

    }
    ```

    Observe que é preferível manipular os casos de propriedade de cabeçalho de autenticação ausente ou de rejeição por meio do back-end. A manipulação específica desses casos depende em grande parte da sua meta de experiência do usuário. Uma opção é exibir uma notificação com um aviso genérico para que o usuário realize a autenticação para recuperar a notificação real.

## <a name="run-the-application"></a>Executar o aplicativo

Para executar o aplicativo, faça o seguinte:

1. No XCode, execute o aplicativo em um dispositivo iOS físico (as notificações por push não funcionarão no simulador).
2. Na interface do usuário do aplicativo iOS, insira um nome de usuário e senha. Pode ser qualquer cadeia de caracteres, mas elas devem ter o mesmo valor.
3. Na interface do usuário do aplicativo iOS, clique em **Logon**. Em seguida, clique em **Enviar push**. Você deve ver a notificação segura sendo exibida no centro de notificações.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
