---
title: "Integração do Reach SDK do iOS no Azure Mobile Engagement | Microsoft Docs"
description: "Atualizações e procedimentos mais recentes para o SDK do iOS para Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 1f5f5857-867c-40c5-9d76-675a343a0296
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
ms.openlocfilehash: ba74e0c442ac10f096d465f989e03d2ceae8cd88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-integrate-engagement-reach-on-ios"></a>Como integrar o Engagement Reach no iOS
Você deve seguir o procedimento de integração descrito em [Como integrar o Engagement no documento iOS](mobile-engagement-ios-integrate-engagement.md) antes de seguir este guia.

Esta documentação requer o XCode 8. Se você realmente depende do XCode 7, pode usar o [SDK do iOS Engagement v3.2.4](https://aka.ms/r6oouh). Há um bug conhecido nesta versão anterior durante a execução em dispositivos com iOS 10: as notificações de sistema não são acionadas. Para corrigir isso, você terá que implementar a API desaprovada `application:didReceiveRemoteNotification:` no representante do aplicativo da seguinte maneira:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **Não recomendamos essa solução alternativa** pois esse comportamento pode alterar qualquer atualização da versão futura iOS (até mesmo pequenas) porque esta API do iOS foi preterida. Você deve mudar para o XCode 8 assim que possível.
>
>

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Habilitar seu aplicativo para receber Notificações por push silenciosas
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

## <a name="integration-steps"></a>Etapas de integração
### <a name="embed-the-engagement-reach-sdk-into-your-ios-project"></a>Incorporar o SDK do Engagement Reach em seu projeto iOS
* Adicione o sdk Reach em seu projeto Xcode. No Xcode, vá para **Projeto \> Adicionar ao projeto** e escolha a pasta `EngagementReach`.

### <a name="modify-your-application-delegate"></a>Modifique seu Representante do Aplicativo
* Na parte superior do seu arquivo de implementação, importe o módulo do Engagement Reach:

      [...]
      #import "AEReachModule.h"
* Dentro do método `applicationDidFinishLaunching:` ou `application:didFinishLaunchingWithOptions:`, crie um módulo de alcance e passe-o para sua linha de inicialização de Engagement existente:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
        [...]

        return YES;
      }
* Modifique a cadeia de caracteres **'icon.png'** com o nome da imagem que você deseja definir como o ícone de notificação.
* Se você quiser usar a opção *Valor de notificação de atualização* em campanhas Reach ou se quiser usar campanhas de push nativo \</SaaS/Reach API/Campaign format/Native Push\>, você deve deixar o módulo Reach gerenciar o ícone de notificação em si (ele apagará automaticamente a notificação do aplicativo e também redefinirá o valor armazenado pelo Engagement sempre que o aplicativo for iniciado ou colocado em segundo plano). Isso é feito adicionando a seguinte linha após a inicialização do módulo Reach:

      [reach setAutoBadgeEnabled:YES];
* Se você quiser manipular o envio de dados Reach, você deverá permitir que seu Representante de aplicativo em conformidade com o protocolo `AEReachDataPushDelegate` . Adicione a seguinte linha após a inicialização do módulo Reach:

      [reach setDataPushDelegate:self];
* Em seguida, você pode implementar os métodos `onDataPushStringReceived:` e `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` no seu representante de aplicativo:

      -(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
      {
         NSLog(@"String data push message with category <%@> received: %@", category, body);
         return YES;
      }

      -(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
      {
         NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
         // Do something useful with decodedBody like updating an image view
         return YES;
      }

### <a name="category"></a>Categoria
O parâmetro de categoria é opcional quando você criar uma campanha de envio de dados e permitir que você filtre o envio de dados. Isso será útil se você deseja enviar tipos diferentes de dados `Base64` e desejar identificar seu tipo antes da analisá-los.

**Seu aplicativo agora está pronto para receber e exibir o conteúdo do alcance!**

## <a name="how-to-receive-announcements-and-polls-at-any-time"></a>Como receber anúncios e pesquisas de opinião a qualquer momento
O Engagement pode enviar notificações Reach para os usuários finais a qualquer momento usando o Apple Push Notification Service (Serviço de notificações por push da Apple).

Para habilitar essa funcionalidade, você precisará preparar seu aplicativo para notificações de push da Apple e modificar seu representante do aplicativo.

### <a name="prepare-your-application-for-apple-push-notifications"></a>Preparar seu aplicativo para notificações de push da Apple
Siga o guia: [Como preparar seu aplicativo para notificações por push da Apple](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

### <a name="add-the-necessary-client-code"></a>Adicione o código de cliente necessário
*Nesse ponto seu aplicativo deve ter um certificado de push Apple registrado no front-end do Engagement.*

Se ele já não foi feito, você precisa registrar seu aplicativo para receber notificações por push.

* Importe a estrutura `User Notification` :

        #import <UserNotifications/UserNotifications.h>
* Adicione a seguinte linha na inicialização do aplicativo (normalmente em `application:didFinishLaunchingWithOptions:`):

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

Em seguida, você precisa fornecer ao Engagement o token de dispositivo retornado pelos servidores da Apple. Isso é feito no método chamado `application:didRegisterForRemoteNotificationsWithDeviceToken:` no seu representante de aplicativo:

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
        [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

Finalmente, você precisa informar o SDK do Engagement quando seu aplicativo receber uma notificação remota. Para fazer isso, chame o método `applicationDidReceiveRemoteNotification:fetchCompletionHandler:` no seu representante de aplicativo:

    - (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

> [!IMPORTANT]
> Por padrão, Engagement Reach controla o completionHandler. Se você quiser responder manualmente para o bloco `handler`em seu código, passe nulo o argumento `handler` e controle você mesmo o bloco de conclusão. Consulte o tipo `UIBackgroundFetchResult` para obter uma lista de valores possíveis.
>
>

### <a name="full-example"></a>Exemplo completo
Aqui está um exemplo completo de integração:

    #pragma mark -
    #pragma mark Application lifecycle

    - (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
    {
      /* Reach module */
      AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
      [reach setAutoBadgeEnabled:YES];

      /* Engagement initialization */
      [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
      [[EngagementAgent shared] setPushDelegate:self];

      /* Views */
      [window addSubview:[tabBarController view]];
      [window makeKeyAndVisible];

      [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
      return YES;
    }

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
      [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>Resolver conflitos de delegado UNUserNotificationCenter

*Se o aplicativo nem uma das bibliotecas de terceiros implementar um `UNUserNotificationCenterDelegate`, ignore esta parte.*

Um delegado `UNUserNotificationCenter` é usado pelo SDK para monitorar o ciclo de vida das notificações do Engagement em dispositivos que executam o iOS 10 ou superior. O SDK tem sua própria implementação do protocolo `UNUserNotificationCenterDelegate`, mas pode haver apenas um delegado `UNUserNotificationCenter` por aplicativo. Qualquer outro delegado adicionado ao objeto `UNUserNotificationCenter` entrará em conflito com o do Engagement. Se o SDK detectar seu delegado ou qualquer delegado de terceiros, ele não usará sua própria implementação, para lhe dar uma chance para resolver os conflitos. Você precisará adicionar a lógica do Engagement ao seu próprio delegado para resolver os conflitos.

Há duas maneiras de fazer isso.

Proposta 1: apenas encaminhando as chamadas do delegado para o SDK:

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

Ou proposta 2: herdando da classe `AEUserNotificationHandler`

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [!NOTE]
> Você pode determinar se uma notificação vem do Engagement ou não passando seu dicionário `userInfo` para o método da classe `isEngagementPushPayload:` do Agent.

Verifique se o delegado do objeto `UNUserNotificationCenter` é definido como seu delegado no método `application:willFinishLaunchingWithOptions:` ou `application:didFinishLaunchingWithOptions:` do delegado do aplicativo.
Por exemplo, se você implementou a proposta 1 acima:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        // Any other code

        [UNUserNotificationCenter currentNotificationCenter].delegate = self;
        return YES;
      }

## <a name="how-to-customize-campaigns"></a>Como personalizar campanhas
### <a name="notifications"></a>Notificações
Há dois tipos de notificações: notificações de sistema e no aplicativo.

As notificações do sistema são tratadas pelo iOS e não podem ser personalizadas.

Notificações no aplicativo são feitas de uma exibição que é adicionada dinamicamente à janela do aplicativo atual. Isso é chamado uma sobreposição de notificação. Sobreposições de notificação são ótimas para uma integração rápida porque elas não exigem que você modifique qualquer modo de exibição em seu aplicativo.

#### <a name="layout"></a>Layout
Para modificar a aparência de suas notificações no aplicativo, você pode simplesmente modificar o arquivo `AENotificationView.xib` conforme as suas necessidades, desde que você mantenha os valores de marca e tipos das subexibições existentes.

Por padrão, as notificações no aplicativo são apresentadas na parte inferior da tela. Se você preferir exibi-los na parte superior da tela, edite o `AENotificationView.xib` fornecido e altere a propriedade `AutoSizing` do modo de exibição principal para que possa ser mantido na parte superior da sua visão.

#### <a name="categories"></a>Categorias
Quando você modifica o layout fornecido, você pode modificar a aparência de todas as notificações. As categorias permitem que você defina várias aparências direcionadas (possíveis comportamentos) para as notificações. Uma categoria pode ser especificada quando você cria uma campanha de Reach. Tenha em mente que categorias também permitem personalizar anúncios e pesquisas, como está descrito mais adiante neste documento.

Para registrar um manipulador de categoria para as notificações, você precisa adicionar uma chamada depois que o módulo reach for inicializado.

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:@"my_category"];
    ...

`myNotifier` deve ser uma instância de um objeto que está de acordo com o protocolo `AENotifier`.

Você pode implementar os métodos de protocolo por conta própria, ou você pode optar por reimplementar a classe `AEDefaultNotifier` existente que já executa a maior parte do trabalho.

Por exemplo, se você quiser redefinir o modo de exibição de notificação para uma categoria específica, você pode seguir este exemplo:

    #import "AEDefaultNotifier.h"
    #import "AENotificationView.h"
    @interface MyNotifier : AEDefaultNotifier
    @end

    @implementation MyNotifier

    -(NSString*)nibNameForCategory:(NSString*)category
    {
      return "MyNotificationView";
    }

    @end

Esse exemplo simples de categoria pressupõem que você tenha um arquivo chamado `MyNotificationView.xib` no pacote de aplicativo principal. Se o método não for capaz de encontrar um `.xib`correspondente, a notificação não será exibida e o Engagement produzirá uma mensagem no console.

O arquivo nib fornecido deve respeitar as regras a seguir:

* Ele deve conter apenas um modo de exibição.
* Subexibições devem ser dos mesmos tipos que aquelas dentro do arquivo nib fornecido chamado `AENotificationView.xib`
* Subexibições devem ter as mesmas marcas que aqueles fornecidos dentro do arquivo nib chamado `AENotificationView.xib`

> [!TIP]
> Apenas copie o arquivo nib fornecido, chamado `AENotificationView.xib` e comece a trabalhar a partir daí. Mas tenha cuidado, o modo de exibição dentro desse arquivo nib é associado à classe `AENotificationView`. Essa classe redefiniu o método `layoutSubViews` para mover e redimensionar suas subexibições de acordo com o contexto. Você pode substituí-lo por um `UIView` ou classe de modo de exibição personalizada.
>
>

Se precisar de personalização mais profunda das suas notificações (se você quiser, por exemplo para carregar a exibição diretamente do código), é recomendável examinar a documentação de código e a classe de fonte fornecido de `Protocol ReferencesDefaultNotifier` e `AENotifier`.

Observe que você pode usar o mesmo notificador para várias categorias.

Você também pode redefinir a notificação padrão como esta:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### <a name="notification-handling"></a>Tratamento de notificação
Ao usar a categoria padrão, alguns métodos de ciclo de vida são chamados no objeto `AEReachContent` para relatar estatísticas e atualizar o estado de campanha:

* Quando a notificação é exibida no aplicativo, o método `displayNotification` é chamado (que relata estatísticas) por `AEReachModule` se`handleNotification:` retornar `YES`.
* Se a notificação é liberada, o método `exitNotification` é chamado, a estatística é relatada e as próximas campanhas agora podem ser processadas.
* Se a notificação é clicada, `actionNotification` é chamado, a estatística é relatada e a ação associada é executada.

Se sua implementação de `AENotifier` ignora o comportamento padrão, você precisa chamar esses métodos de ciclo de vida por conta própria. Os exemplos a seguir ilustram alguns casos em que o comportamento padrão é ignorado:

* Você não precisa estender `AEDefaultNotifier`, por exemplo, você implementou o tratamento de categoria a partir do zero.
* Você substitui `prepareNotificationView:forContent:`, certifique-se de mapear pelo menos `onNotificationActioned` ou `onNotificationExited` para um dos seus controles de interface de usuário.

> [!WARNING]
> Se `handleNotification:` lança uma exceção, o conteúdo será excluído e `drop` é chamado, isso é informado em estatísticas e as próximas campanhas agora podem ser processadas.
>
>

#### <a name="include-notification-as-part-of-an-existing-view"></a>Incluir notificação como parte de uma exibição existente
Sobreposições são ótimas para uma integração rápida, mas podem não ser convenientes algumas vezes ou podem ter efeitos colaterais indesejados.

Se você não estiver satisfeito com o sistema de sobreposição em algumas de suas exibições, você pode personalizá-lo para esses modos de exibição.

Você pode optar por incluir o layout de notificação em exibições existentes. Para fazer isso, há dois estilos de implementação:

1. Adicionar a exibição de notificação usando o construtor de interface

   * Abra o *Interface Builder*
   * Insira um 320 x 60 (ou 768 x 60 se você estiver no iPad) `UIView` onde você deseja que a notificação seja exibida
   * Defina o valor da marca para esse modo de exibição: **36822491**
2. Adicione o modo de exibição de notificação programaticamente. Basta adicionar o código a seguir ao seu modo de exibição que foi inicializado:

       UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
       notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
       [self.view addSubview:notificationView];

A macro `NOTIFICATION_AREA_VIEW_TAG` pode ser encontrada em `AEDefaultNotifier.h`.

> [!NOTE]
> A notificação padrão automaticamente detecta que o layout de notificação está incluído nessa exibição e não adicionará uma sobreposição para ela.
>
>

### <a name="announcements-and-polls"></a>Anúncios e pesquisas
#### <a name="layouts"></a>Layouts
Você pode modificar os arquivos `AEDefaultAnnouncementView.xib` e `AEDefaultPollView.xib` assim como manter os valores de marca e tipos das subexibições existentes.

#### <a name="categories"></a>Categorias
##### <a name="alternate-layouts"></a>Layouts alternativos
Como as notificações, a categoria de campanha pode ser usada com layouts alternativos para seus anúncios e pesquisas.

Para criar uma categoria para um anúncio, você deve estender **AEAnnouncementViewController** e registrá-lo depois que o módulo reach tiver sido inicializado:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [!NOTE]
> Cada vez que um usuário for clicar em uma notificação para um anúncio com a categoria "my\_category", o controlador de exibição registrado (nesse caso `MyCustomAnnouncementViewController`) será inicializado chamando o método `initWithAnnouncement:` e a exibição será adicionada à janela do aplicativo atual.
>
>

Na implementação da classe `AEAnnouncementViewController` você terá que ler a propriedade `announcement` para inicializar as subexibições. Considere o exemplo a seguir, onde dois rótulos são inicializados usando as propriedades `title` e `body` da classe `AEReachAnnouncement`:

    -(void)loadView
    {
        [super loadView];

        UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        titleLabel.font = [UIFont systemFontOfSize:32.0];
        titleLabel.text = self.announcement.title;

        UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        bodyLabel.font = [UIFont systemFontOfSize:24.0];
        bodyLabel.text = self.announcement.body;

        [self.view addSubview:titleLabel];
        [self.view addSubview:bodyLabel];
    }

Se você não deseja carregar suas exibições por conta própria, mas apenas deseja reutilizar o layout de exibição do anúncio padrão, você pode simplesmente fazer o controlador de exibição personalizada estender a classe `AEDefaultAnnouncementViewController`fornecida. Nesse caso, duplique o arquivo `AEDefaultAnnouncementView.xib` nib e o renomeie para que possa ser carregado pelo controlador de exibição personalizada (para um controlador chamado `CustomAnnouncementViewController`, você deve chamar seu arquivo nib `CustomAnnouncementView.xib`).

Para substituir a categoria padrão de anúncios, basta registrar seu controlador de exibição personalizado para a categoria definida no `kAEReachDefaultCategory`:

    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

Pesquisas podem ser personalizadas da mesma forma:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

Neste momento, os `MyCustomPollViewController` fornecidos devem estender `AEPollViewController`. Ou você pode optar por estender a partir do controlador padrão: `AEDefaultPollViewController`.

> [!IMPORTANT]
> Não se esqueça de chamar `action` (`submitAnswers:` para controladores de exibição de pesquisa personalizada) ou o método `exit` antes do controlador de exibição ser descartado. Caso contrário, as estatísticas não serão enviadas (ou seja, sem análise da campanha) e, mais importante, as próximas campanhas não serão notificadas quando o processo do aplicativo for reiniciado.
>
>

##### <a name="implementation-example"></a>Exemplo de implementação
Nessa implementação a exibição de aviso personalizado é carregada a partir de um arquivo xib externo.

Como para personalização da notificação avançada, é recomendável examinar o código-fonte da implementação do padrão.

`CustomAnnouncementViewController.h`

    //Interface
    @interface CustomAnnouncementViewController : AEAnnouncementViewController {
      UILabel* titleLabel;
      UITextView* descTextView;
      UIWebView* htmlWebView;
      UIButton* okButton;
      UIButton* cancelButton;
    }

    @property (nonatomic, retain) IBOutlet UILabel* titleLabel;
    @property (nonatomic, retain) IBOutlet UITextView* descTextView;
    @property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
    @property (nonatomic, retain) IBOutlet UIButton* okButton;
    @property (nonatomic, retain) IBOutlet UIButton* cancelButton;

    -(IBAction)okButtonClicked:(id)sender;
    -(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

    //Implementation
    @implementation CustomAnnouncementViewController
    @synthesize titleLabel;
    @synthesize descTextView;
    @synthesize htmlWebView;
    @synthesize okButton;
    @synthesize cancelButton;

    -(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
    {
      self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
      if (self != nil) {
        self.announcement = anAnnouncement;
      }
      return self;
    }

    - (void) dealloc
    {
      [titleLabel release];
      [descTextView release];
      [htmlWebView release];
      [okButton release];
      [cancelButton release];
      [super dealloc];
    }

    - (void)viewDidLoad {
      [super viewDidLoad];

      /* Init announcement title */
      titleLabel.text = self.announcement.title;

      /* Init announcement body */
      if(self.announcement.type == AEAnnouncementTypeHtml)
      {
        titleLabel.hidden = YES;
        htmlWebView.hidden = NO;
        [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
      }
      else
      {
        titleLabel.hidden = NO;
        htmlWebView.hidden = YES;
        descTextView.text = self.announcement.body;
      }

      /* Set action button label */
      if([self.announcement.actionLabel length] > 0)
        [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

      /* Set exit button label */
      if([self.announcement.exitLabel length] > 0)
        [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
    }

    #pragma mark Actions

    -(IBAction)okButtonClicked:(id)sender
    {
        [self action];
    }

    -(IBAction)cancelButtonClicked:(id)sender
    {
        [self exit];
    }

    @end
