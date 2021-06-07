---
title: Enviar notificações por push para o iOS usando os Hubs de Notificação do Azure e o SDK do iOS versão 3.0.0 versão prévia 1
description: Neste tutorial, você aprenderá a usar os Hubs de Notificação do Azure e o serviço Apple Push Notification para enviar notificações por push para dispositivos iOS específicos (versão 3.0.0-preview1).
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: f905bfa830bfc78caa6ebb02ae49d4839168367b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100597176"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs-sdk-for-apple"></a>Tutorial: Enviar notificações por push a aplicativos iOS usando o SDK dos Hubs de Notificação do Azure para Apple

Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo iOS usando o SDK dos Hubs de Notificação do Azure para Apple.

Este tutorial cobre as seguintes etapas:

- Criar um aplicativo iOS de exemplo.
- Conectar seu aplicativo iOS aos Hubs de Notificação do Azure.
- Enviar notificações por push de teste.
- Verificar se seu aplicativo recebe notificações.

Baixe o código completo deste tutorial [no GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/main/SampleNHAppObjC).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

- Um Mac executando [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), juntamente com um certificado de desenvolvedor válido instalado em seu conjunto de chaves.
- Um iPhone ou iPad executando o iOS versão 10 ou posterior.
- Seu dispositivo físico registrado no [portal da Apple](https://developer.apple.com/) e associado ao seu certificado.

Antes de prosseguir, siga o tutorial anterior sobre como começar a usar os [Hubs de Notificação do Azure para aplicativos iOS](ios-sdk-get-started.md) para então preparar e configurar as credenciais de push em seu hub de notificação. Mesmo que você não tenha nenhuma experiência anterior com o desenvolvimento do iOS, você será capaz de seguir estas etapas.

> [!NOTE]
> Devido aos requisitos de configuração das notificações por push, você precisa implantá-las e testá-las em um dispositivo iOS físico (iPhone ou iPad), em vez de usar o emulador de iOS.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Conectar seu aplicativo do iOS aos Hubs de Notificação

1. No Xcode, crie um novo projeto do iOS e selecione o modelo **Aplicativo de Modo de Exibição Único** .

   :::image type="content" source="media/ios-sdk/image1.png" alt-text="Selecionar modelo":::

2. Ao definir as opções para o novo projeto, lembre-se de usar os mesmos **Nome do Produto** e **Identificador Organizacional** que você usou quando configurou o identificador do pacote no Portal do Desenvolvedor da Apple.

3. Em Navegador de Projeto, selecione o nome do projeto em **Destinos** e selecione a guia **Assinatura e Funcionalidades**. Verifique se você selecionou a **Equipe** apropriada para sua conta de Desenvolvedor da Apple. O deverá automaticamente efetuar pull do perfil de provisionamento que você criou anteriormente com base em seu identificador de pacote.

   Se você não vir o novo perfil de provisionamento que você criou no Xcode, tente atualizar os perfis da sua identidade de assinatura. Clique em **Xcode** na barra de menus, em **Preferências**, na guia **Conta**, no botão **Exibir Detalhes**, em sua identidade de assinatura e depois clique no botão Atualizar no canto inferior direito.

   :::image type="content" source="media/ios-sdk/image2.png" alt-text="Exibir detalhes":::

4. Na guia **Assinatura e Funcionalidades**, selecione **+ Funcionalidade**. Clique duas vezes em **Notificações por Push** para habilitá-la.

   :::image type="content" source="media/ios-sdk/image3.png" alt-text="Recurso":::

5. Adicione os módulos do SDK dos Hubs de Notificação do Microsoft Azure.

   Você pode integrar o SDK dos Hubs de Notificação do Azure ao seu aplicativo usando o [CocoaPods](https://cocoapods.org/) ou adicionando manualmente os binários ao seu projeto.

   - Integração por meio de Cocoapods: Adicione as seguintes dependências ao seu podfile para incluir o SDK dos Hubs de Notificação do Azure em seu aplicativo:

      ```ruby
      pod 'AzureNotificationHubs-iOS'
      ```

      - Execute pod install para instalar seu pod recém-definido e abrir seu .xcworkspace.

         Se um erro como **Não é possível encontrar uma especificação para AzureNotificationHubs-iOS** for exibido durante a execução de pod install, execute `pod repo update` para obter os pods mais recentes do repositório Cocoapods e execute pod install.

   - Integração por meio do Carthage: Adicione as seguintes dependências ao seu Cartfile para incluir o SDK dos Hubs de Notificação do Azure em seu aplicativo:

      ```ruby
      github "Azure/azure-notificationhubs-ios"
      ```

      - Em seguida, atualize as dependências de build:

      ```shell
      $ carthage update
      ```

      Para obter mais informações sobre como usar o Carthage, confira o [repositório GitHub do Carthage](https://github.com/Carthage/Carthage).

   - Integração copiando os binários em seu projeto:

      Você pode realizar a integração copiando os binários para seu projeto, da seguinte maneira:

        - Baixe a estrutura do [SDK dos Hubs de Notificação do Azure](https://github.com/Azure/azure-notificationhubs-iOS/releases/) fornecida como um arquivo zip e descompacte-o.

        - No Xcode, clique com o botão direito do mouse no projeto e clique na opção **Adicionar Arquivos a** para adicionar a pasta **WindowsAzureMessaging.framework** ao seu projeto do Xcode. Selecione **Opções** e verifique se a opção **Copiar itens se necessário** está selecionada e, em seguida, clique em **Adicionar**.

          :::image type="content" source="media/ios-sdk/image4.png" alt-text="Adicionar estrutura":::

6. Adicione ou edite um arquivo chamado **DevSettings.plist** que contém duas propriedades, `CONNECTION_STRING` para a cadeia de conexão do Hub de Notificações do Azure e `HUB_NAME` para o nome do Hub de Notificações do Azure.

7. Adicione as informações para se conectar aos Hubs de Notificação do Azure na seção `<string></string>` apropriada.  Substitua os espaços reservados de literal de cadeia de caracteres `--HUB-NAME--` e `--CONNECTION-STRING--` pelo nome do hub e a **DefaultListenSharedAccessSignature**, respectivamente, como você obteve anteriormente do portal:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
        <key>HUB_NAME</key>
        <string>--HUB-NAME--</string>
        <key>CONNECTION_STRING</key>
        <string>--CONNECTION-STRING--</string>
    </dict>
    </plist>
    ```

8. No mesmo arquivo **AppDelegate.m**, substitua todo o código após `didFinishLaunchingWithOptions` pelo código a seguir:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>

    // Extend the AppDelegate to listen for messages using MSNotificationHubDelegate and User Notification Center
    @interface AppDelegate () <MSNotificationHubDelegate>

    @end

    @implementation AppDelegate
    
    @synthesize notificationPresentationCompletionHandler;
    @synthesize notificationResponseCompletionHandler;

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

        NSString *path = [[NSBundle mainBundle] pathForResource:@"DevSettings" ofType:@"plist"];
        NSDictionary *configValues = [NSDictionary dictionaryWithContentsOfFile:path];
        
        NSString *connectionString = [configValues objectForKey:@"CONNECTION_STRING"];
        NSString *hubName = [configValues objectForKey:@"HUB_NAME"];

        if([connectionString length] != 0 && [hubName length] != 0) {
            [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
            [MSNotificationHub setDelegate:self];
            [MSNotificationHub initWithConnectionString:connectionString withHubName:hubName];
    
            return YES;
        }

        NSLog(@"Please setup CONNECTION_STRING and HUB_NAME in DevSettings.plist and restart application");

        exit(-1);
    }

    - (void)notificationHub:(MSNotificationHub *)notificationHub didReceivePushNotification:(MSNotificationHubMessage *)message {
        // Send message using NSNotificationCenter with the message
        NSDictionary *userInfo = [NSDictionary dictionaryWithObject:message forKey:@"message"];
        [[NSNotificationCenter defaultCenter] postNotificationName:@"MessageReceived" object:nil userInfo:userInfo];
    }

    @end
    ```

    Esse código conecta-se ao hub de notificações usando as informações de conexão especificadas em **DevSettings.plist**. Esse código fornece o token do dispositivo ao hub de notificação para que o hub possa enviar notificações.

### <a name="create-notificationdetailviewcontroller-header-file"></a>Criar arquivo de cabeçalho de NotificationDetailViewController

1. Assim como nas instruções anteriores, adicione outro arquivo de cabeçalho chamado **SetupViewController.h**. Substitua o conteúdo do novo arquivo de cabeçalho pelo seguinte código:

   ```objc
   #import <UIKit/UIKit.h>

   NS_ASSUME_NONNULL_BEGIN

   @interface SetupViewController : UIViewController

   @end

   NS_ASSUME_NONNULL_END
   ```

2. Adicione o arquivo de implementação **SetupViewController.m**. Substitua o conteúdo do arquivo pelo seguinte código, que implementa os métodos UIViewController:

   ```objc
   #import "SetupViewController.h"

    static NSString *const kNHMessageReceived = @"MessageReceived";
    
    @interface SetupViewController ()
    
    @end

    @implementation SetupViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        
        // Listen for messages using NSNotificationCenter
        [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(didReceivePushNotification:) name:kNHMessageReceived object:nil];
    }

    - (void)dealloc {
        // Clean up subscription to NSNotificationCenter
        [[NSNotificationCenter defaultCenter] removeObserver:self name:kNHMessageReceived object:nil];
    }

    - (void)didReceivePushNotification:(NSNotification *)notification {
        MSNotificationHubMessage *message = [notification.userInfo objectForKey:@"message"];

        // Create UI Alert controller with message title and body
        UIAlertController *alertController = [UIAlertController alertControllerWithTitle:message.title
                             message:message.body
                      preferredStyle:UIAlertControllerStyleAlert];
        [alertController addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleCancel handler:nil]];
        [self presentViewController:alertController animated:YES completion:nil];
        
        // Dismiss after 2 seconds
        dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
            [alertController dismissViewControllerAnimated:YES completion: nil];
        });

    }

    @end
   ```

3. Compile e execute o aplicativo no dispositivo para verificar se não há falhas.

## <a name="send-test-push-notifications"></a>Enviar notificações por push de teste

Você pode testar o recebimento de notificações no aplicativo com a opção **Envio de Teste** no [Azure portal](https://portal.azure.com/). Isso envia uma notificação por push de teste para seu dispositivo.

:::image type="content" source="media/ios-sdk/image6.png" alt-text="Envio de teste":::

As notificações por push normalmente são enviadas em um serviço de back-end como Aplicativos Móveis ou ASP.NET usando uma biblioteca compatível. Se uma biblioteca não estiver disponível para o seu back-end, você também poderá usar a API REST diretamente para enviar mensagens de notificação.

Aqui está uma lista de alguns outros tutoriais que talvez você queira examinar para o envio de notificações:

- Aplicativos Móveis do Azure: para obter um exemplo de como enviar notificações de um back-end de Aplicativos Móveis integrado aos Hubs de Notificação, veja [Adicionar notificações por push ao seu aplicativo iOS](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).
- ASP.NET: [use os Hubs de Notificação para enviar notificações por push aos usuários](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).
- SDK do Java para Hubs de Notificação do Azure: confira [Como usar os Hubs de Notificação do Java](notification-hubs-java-push-notification-tutorial.md) para enviar notificações do Java. Isso foi testado no Eclipse para desenvolvimento no Android.
- PHP: [como usar Hubs de Notificação do PHP](notification-hubs-php-push-notification-tutorial.md).

## <a name="verify-that-your-app-receives-push-notifications"></a>Verifique se seu aplicativo recebe notificações por push

Para testar as notificações por push no iOS, você deve implantar o aplicativo em um dispositivo iOS físico. Não é possível enviar notificações por push da Apple com o iOS Simulator.

1. Execute o aplicativo e verifique se o registro foi bem-sucedido e pressione **OK**.

   :::image type="content" source="media/ios-sdk/image7.png" alt-text="Registrar":::

2. Em seguida, envie uma notificação por push de teste do [portal do Azure](https://portal.azure.com/), conforme descrito na seção anterior.

3. A notificação por push é enviada a todos os dispositivos que estão registrados para receber as notificações do hub de notificação fornecido.

   :::image type="content" source="media/ios-sdk/image8.png" alt-text="Enviar teste":::

## <a name="next-steps"></a>Próximas etapas

Neste exemplo simples, você difunde notificações por push a todos os seus dispositivos iOS registrados. Para saber como enviar notificações por push para dispositivos iOS específicos, prossiga para o seguinte tutorial:

[Tutorial: Enviar notificações por push para dispositivos específicos](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

Para obter mais informações, consulte os seguintes artigos:

- [Visão geral dos Hubs de Notificação do Azure](notification-hubs-push-notification-overview.md)
- [APIs REST dos Hubs de Notificação](/rest/api/notificationhubs/)
- [SDK dos Hubs de Notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [SDKs dos Hubs de Notificação do GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registrar com o back-end de aplicativo](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Gerenciamento de registros](notification-hubs-push-notification-registration-management.md)
- [Como trabalhar com marcas](notification-hubs-tags-segment-push-message.md)
- [Como trabalhar com modelos personalizados](notification-hubs-templates-cross-platform-push-messages.md)
- [Controle de acesso do Barramento de Serviço com assinaturas de acesso compartilhado](../service-bus-messaging/service-bus-sas.md)
- [Gerar tokens SAS programaticamente](/rest/api/eventhub/generate-sas-token)
- [Segurança da Apple: criptografia comum](https://developer.apple.com/security/)
- [Hora da época do UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)