---
title: "Introdução ao Azure Mobile Engagement para Xamarin.iOS"
description: "Aprenda a usar o Azure Mobile Engagement com Análises e Notificações por Push para Aplicativos Xamarin.iOS."
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: 
ms.assetid: 0448209e-fff6-47bd-985c-2cf074bac12f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9938c3e994acf31244825b1afb347f8c9f90ebe3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-xamarinios-apps"></a>Introdução ao Azure Mobile Engagement para aplicativos Xamarin.iOS
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile Engagement para entender o uso do aplicativo e enviar notificações por push para usuários segmentados em um aplicativo Xamarin.iOS.
Neste tutorial, você cria um aplicativo Xamarin.iOS em branco que coleta dados básicos e recebe notificações por push usando o Sistema de Notificação por Push da Apple (APNS).

> [!NOTE]
> O serviço Azure Mobile Engagement será desativado em março de 2018 e, no momento, está disponível somente para os clientes existentes. Para saber mais, confira [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Este tutorial exige o seguinte:

* [Xamarin Studio](http://xamarin.com/studio). Você também pode usar o Visual Studio com Xamarin, mas este tutorial usa o Xamarin Studio. Para obter instruções de instalação, confira [Instalar e configurar para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). 
* [SDK do Xamarin do Mobile Engagement](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started).
> 
> 

## <a id="setup-azme"></a>Configurar o Mobile Engagement para seu aplicativo iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Conecte o seu aplicativo ao back-end do Mobile Engagement
Este tutorial apresenta uma "integração básica" que é o conjunto mínimo exigido para coletar dados e enviar uma notificação por push.

Criaremos um aplicativo básico com o Xamarin.iOS para demonstrar a integração:

### <a name="create-a-new-xamarinios-project"></a>Criar um novo projeto do Xamarin.iOS
1. Inicie o Xamarin Studio. Vá para **Arquivo** -> **Novo** -> **Solução** 
   
    ![][1]
2. Selecione **Aplicativo de Exibição Única**, verifique se a linguagem selecionada é **C#** e clique em **Avançar**.
   
    ![][2]
3. Preencha o **Nome do Aplicativo** e o **Identificador da Organização** e clique em **Avançar**. 
   
    ![][3]
   
   > [!IMPORTANT]
   > Verifique se o perfil de publicação que você eventualmente usará para implantar o aplicativo iOS está usando uma ID de Aplicativo que corresponde exatamente ao Identificador de Pacote aqui. 
   > 
   > 
4. Atualize o **Nome do Projeto**, o **Nome da Solução** e o **Localização**, se necessário, e clique em **Criar**.
   
    ![][4]

O Xamarin Studio criará o aplicativo de demonstração, ao qual integraremos o Mobile Engagement. 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Conecte seu aplicativo ao back-end do Mobile Engagement
1. Clique com o botão direito do mouse na pasta **Pacotes** na janela Solução e escolha **Adicionar Pacotes...**
   
    ![][5]
2. Pesquise o **SDK do Xamarin do Microsoft Azure Mobile Engagement** e adicione-o à solução.  
   
    ![][6]
3. Abra **AppDelegate.cs** e adicione a seguinte instrução using:
   
        using Microsoft.Azure.Engagement.Xamarin;
4. No método **FinishedLaunching** , adicione o seguinte para inicializar a conexão com o back-end do Mobile Engagement. Adicione a **ConnectionString**. Esse código também usa um **NotificationIcon** fictício que é adicionado pelo SDK do Mobile Engagement, que convém substituir. 
   
        EngagementConfiguration config = new EngagementConfiguration {
                        ConnectionString = "YourConnectionStringFromAzurePortal",
                        NotificationIcon = UIImage.FromBundle("close")
                    };
        EngagementAgent.Init (config);

## <a id="monitor"></a>Habilitar monitoramento em tempo real
Para iniciar o envio de dados e assegurar que os usuários estejam ativos, você deve enviar pelo menos uma tela ao back-end do Mobile Engagement.

1. Abra **ViewController.cs** e adicione a seguinte instrução using:
   
        using Microsoft.Azure.Engagement.Xamarin;
2. Substitua a classe da qual `ViewController` herda de `UIViewController` para `EngagementViewController`. 

## <a id="monitor"></a>Conectar o aplicativo com monitoramento em tempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Habilitar notificações por push e mensagens no aplicativo
O Mobile Engagement permite interagir com seus usuários e o REACH com notificações por push e mensagens no aplicativo no contexto das campanhas. Esse módulo é chamado de REACH no portal do Mobile Engagement.
As seções a seguir configuram seu aplicativo para recebê-las.

### <a name="modify-your-application-delegate"></a>Modifique seu Representante do Aplicativo
1. Abra **AppDelegate.cs** e adicione a seguinte instrução using:
   
        using System; 
2. Agora, no método `FinishedLaunching`, adicione o seguinte para se registrar para mensagens de envio por push após `EngagementAgent.init(...)`
   
        if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }
3. Por fim, atualize ou adicione os seguintes métodos:
   
        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }
   
        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }
   
        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }
4. No arquivo **Info.plist** da solução, confirme se o **Identificador de Pacote** corresponde à **ID do Aplicativo** que você tem em seu perfil de provisionamento no Centro de Desenvolvimento da Apple. 
   
    ![][7]
5. No mesmo arquivo **Info.plist**, verifique se você marcou **Habilitar Modos de Segundo Plano** e **Notificações Remotas**. 
   
     ![][8]
6. Execute o aplicativo no dispositivo que você associou a esse perfil de publicação. 

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png
