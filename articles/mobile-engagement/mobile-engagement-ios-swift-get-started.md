---
title: "Introdução ao Azure Mobile Engagement para iOS em Swift | Microsoft Docs"
description: "Aprenda a usar o Azure Mobile Engagement com Análises e Notificações por Push para Aplicativos iOS."
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 196c282d-6f2f-4cbc-aeee-6517c5ad866d
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: swift
ms.topic: hero-article
ms.date: 09/20/2016
ms.author: piyushjo
ms.openlocfilehash: 1011b9823333e79a52cd2d187df4f8d063b1f799
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-swift"></a>Introdução ao Azure Mobile Engagement para Aplicativos iOS em Swift
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile Engagement para entender o uso do aplicativo e enviar notificações por push para usuários segmentados para um aplicativo iOS.
Neste tutorial, você cria um aplicativo iOS em branco que coleta dados básicos e recebe notificações por push usando o Sistema de Notificação por Push da Apple (APNS).

Este tutorial exige o seguinte:

* XCode 8, que pode ser instalado a partir da MAC App Store
* o [SKD do Mobile Engagement iOS]
* Certificado de notificação por push (. p12), que pode ser obtido no centro de desenvolvimento da Apple

> [!NOTE]
> Este tutorial usa o Swift versão 3.0. 
> 
> 

A conclusão desse tutorial é um pré-requisito para todos os outros tutoriais do Mobile Engagement para os aplicativos iOS.

> [!NOTE]
> Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started).
> 
> 

## <a id="setup-azme"></a>Configurar o Mobile Engagement para seu aplicativo iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Conecte o seu aplicativo ao back-end do Mobile Engagement
Este tutorial apresenta uma "integração básica" que é o conjunto mínimo exigido para coletar dados e enviar uma notificação por push. A documentação de integração completa pode ser encontrada na [Integração do SDK do iOS no Mobile Engagement](mobile-engagement-ios-sdk-overview.md)

Criaremos um aplicativo básico com XCode para demonstrar a integração:

### <a name="create-a-new-ios-project"></a>Crie um novo projeto iOS
[!INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Conecte seu aplicativo ao back-end do Mobile Engagement
1. Faça o download do [SKD do Mobile Engagement iOS]
2. Extraia arquivo o arquivo .tar.gz para uma pasta no seu computador
3. Clique com botão direito no projeto e selecione "Adicionar arquivos a..."
   
    ![][1]
4. Navegue até a pasta onde você extraiu o SDK e selecione a `EngagementSDK` pasta, então, pressione OK.
   
    ![][2]
5. Abra a guia `Build Phases` e no menu `Link Binary With Libraries`, adicione as estruturas, como mostrado abaixo:
   
    ![][3]
6. Crie um cabeçalho Bridging para poder usar as APIs do Objective C do SDK escolhendo Arquivo > Novo > Arquivo > iOS > Fonte > Arquivo de Cabeçalho.
   
    ![][4]
7. Edite o arquivo de cabeçalho ponte para expor o código em Objective-C do Mobile Engagement para o código Swift e adicione as seguintes importações:
   
        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEUserNotificationHandler.h"
        #import "AEIdfaProvider.h"
8. Em Configurações de Compilação, verifique se a definição da compilação do Bridging Header do Objective C em Compilador Swift - Geração de Código tem um caminho para esse cabeçalho. Aqui está um exemplo de caminho: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (dependendo do caminho)**
   
   ![][6]
9. Volte para o portal do Azure na página *Informações de Conexão* de seu aplicativo e copie a Cadeia de Conexão
   
   ![][5]
10. Agora, cole a cadeia de conexão em `didFinishLaunchingWithOptions` delegar
    
        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
        {
              [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
              [...]
        }

## <a id="monitor"></a>Habilitar monitoramento em tempo real
Para iniciar o envio de dados e assegurar que os usuários estejam ativos, você deve enviar pelo menos uma tela (Atividade) para o back-end do Mobile Engagement.

1. Abra o arquivo **ViewController.swift** e substitua a classe base **ViewController** para ser **EngagementViewController**:
   
    `class ViewController : EngagementViewController {`

## <a id="monitor"></a>Conectar o aplicativo com monitoramento em tempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Habilitar Notificações por Push e mensagens no aplicativo
O Mobile Engagement permite interagir e ENTRAR EM CONTATO com seus usuários com Notificações por Push e Mensagens no Aplicativo no contexto de campanhas. Esse módulo é chamado de REACH no portal do Mobile Engagement.
As seções a seguir irão configurar o aplicativo para recebê-los.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Habilitar seu aplicativo para receber Notificações por push silenciosas
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>Adicionar a biblioteca Reach ao seu projeto
1. Clique com botão direito no projeto
2. Selecione `Add file to ...`
3. Navegue até a pasta onde você extraiu o SDK
4. Selecione a pasta `EngagementReach`
5. Clique em Adicionar
6. Edite o arquivo de cabeçalho ponte para expor os cabeçalhos de Acesso em Objective-C do Mobile Engagement e adicione as seguintes importações:
   
        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### <a name="modify-your-application-delegate"></a>Modifique seu Representante do Aplicativo
1. Em `didFinishLaunchingWithOptions` - crie um módulo de alcance e transmita-o para a linha de inicialização do Engagement existente:
   
        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool 
        {
            let reach = AEReachModule.module(withNotificationIcon: UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

### <a name="enable-your-app-to-receive-apns-push-notifications"></a>Habilite seu aplicativo para receber Notificações por Push do APNS
1. Adicione a seguinte linha ao método `didFinishLaunchingWithOptions`:
   
        if #available(iOS 8.0, *)
        {
            if #available(iOS 10.0, *)
            {
                UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in }
            }else
            {
                let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
                application.registerUserNotificationSettings(settings)
            }
            application.registerForRemoteNotifications()
        }
        else
        {
            application.registerForRemoteNotifications(matching: [.alert, .badge, .sound])
        }
2. Adicione o método `didRegisterForRemoteNotificationsWithDeviceToken` da seguinte maneira:
   
        func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }
3. Adicione o método `didReceiveRemoteNotification:fetchCompletionHandler:` da seguinte maneira:
   
        func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[SKD do Mobile Engagement iOS]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png
