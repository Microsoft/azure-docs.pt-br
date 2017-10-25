---
title: "Introdução ao Azure Mobile Engagement para implantação do Unity para iOS"
description: "Saiba como usar o Azure Mobile Engagement com Análises e Notificações por Push para aplicativos Unity implantados em dispositivos iOS."
services: mobile-engagement
documentationcenter: unity
author: piyushjo
manager: erikre
editor: 
ms.assetid: 7ddfbac3-8d13-4ebe-b061-c865f357297f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-unity-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: c8f50404771965ec636065346ac04e059d264c3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-unity-ios-deployment"></a>Introdução ao Azure Mobile Engagement para implantação do Unity para iOS
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile Engagement para entender o uso de aplicativos e como enviar notificações por push a usuários segmentados de um aplicativo do Unity durante a implantação em um dispositivo iOS.
Este tutorial usa o tutorial clássico Roll a Ball do Unity como ponto de partida. Você deve seguir as etapas deste [tutorial](mobile-engagement-unity-roll-a-ball.md) antes de prosseguir com a integração do Mobile Engagement que demonstramos no tutorial abaixo. 

Este tutorial exige o seguinte:

* [Editor do Unity](http://unity3d.com/get-unity)
* [SDK do Unity do Mobile Engagement](https://aka.ms/azmeunitysdk)
* Editor do XCode

> [!NOTE]
> Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started).
> 
> 

## <a id="setup-azme"></a>Configurar o Mobile Engagement para seu aplicativo iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Conecte o seu aplicativo ao back-end do Mobile Engagement
### <a name="import-the-unity-package"></a>Importar o pacote do Unity
1. Baixe o [pacote do Unity do Mobile Engagement](https://aka.ms/azmeunitysdk) e salve-o em seu computador local. 
2. Vá para **Ativos -> Importar Pacote -> Pacote Personalizado** e selecione o pacote que você baixou na etapa anterior. 
   
    ![][70] 
3. Verifique se todos os arquivos estão selecionados e clique no botão **Importar** . 
   
    ![][71] 
4. Depois que a importação for bem-sucedida, você verá os arquivos importados do SDK em seu projeto.  
   
    ![][72] 

### <a name="update-the-engagementconfiguration"></a>Atualizar o EngagementConfiguration
1. Abra o arquivo de script **EngagementConfiguration** na pasta SDK e atualize **IOS\_CONNECTION\_STRING** com a cadeia de conexão obtida anteriormente no portal do Azure.  
   
    ![][73]
2. Salve o arquivo. 

### <a name="configure-the-app-for-basic-tracking"></a>Configurar o aplicativo para o 	acompanhamento básico
1. Abra o script **PlayerController** anexado ao objeto Player para edição. 
2. Adicione a seguinte instrução usando:
   
        using Microsoft.Azure.Engagement.Unity;
3. Adicione o seguinte ao método `Start()`
   
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

### <a name="deploy-and-run-the-app"></a>Implantar e executar o aplicativo
1. Conecte um dispositivo iOS a seu computador. 
2. Abra **Arquivo -> Configurações de Compilação** 
   
    ![][40]
3. Selecione **iOS**, em seguida, clique em **Alternar Plataforma**
   
    ![][41]
   
    ![][42]
4. Clique em **Configurações do jogador** e forneça um Identificador de Pacote válido. 
   
    ![][53]
5. Finalmente, clique em **Compilar e Executar**
   
    ![][54]
6. Você poderá ser solicitado a fornecer um nome de pasta para armazenar o pacote do iOS. 
   
    ![][43]
7. Se tudo correr bem, o projeto será compilado e você deverá abri-lo em seu aplicativo do XCode. 
8. Verifique se o **Identificador de pacote** está correto no projeto.  
   
    ![][75]
9. Agora execute o aplicativo no XCode para que o pacote seja implantado em seu dispositivo conectado e você verá o jogo Unity em seu telefone! 

## <a id="monitor"></a>Conectar o aplicativo com monitoramento em tempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Habilitar notificações por push e mensagens no aplicativo
O Mobile Engagement permite interagir com seus usuários e o REACH com notificações por push e mensagens no aplicativo no contexto das campanhas. Esse módulo é chamado REACH no portal do Mobile Engagement.
Você não precisa fazer qualquer configuração adicional no aplicativo para receber notificações, e ele já está configurado para isso.

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png
