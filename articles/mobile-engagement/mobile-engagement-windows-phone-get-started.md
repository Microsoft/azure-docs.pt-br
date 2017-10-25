---
title: "Introdução ao Azure Mobile Engagement para aplicativos do Windows Phone Silverlight"
description: "Saiba como usar o Azure Mobile Engagement com análises e notificações por push para aplicativos do Windows Phone Silverlight."
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: erikre
editor: 
ms.assetid: aa34692f-87f7-47c6-a20c-a1972750bc25
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: d2334a59d83c90bdd02c4fa29261d36aad292892
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-windows-phone-silverlight-apps"></a>Introdução ao Azure Mobile Engagement para aplicativos do Windows Phone Silverlight
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile Engagement para entender o uso do aplicativo e enviar notificações por push para usuários segmentados de um aplicativo do Windows Phone Silverlight.
Esse tutorial demonstra um cenário de transmissão simples usando o Mobile Engagement. Nele, você cria um aplicativo do Windows Phone Silverlight em branco que coleta dados básicos e recebe notificações por push usando o Serviço de Notificação por Push da Microsoft (MPNS).

> [!NOTE]
> O serviço Azure Mobile Engagement será desativado em março de 2018 e, no momento, está disponível somente para os clientes existentes. Para saber mais, confira [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

> [!NOTE]
> Não há suporte para projetos do Windows Phone 8.1 e versões anteriores no Visual Studio 2017.  Para saber mais, confira [Direcionamento e compatibilidade da plataforma Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

> [!NOTE]
> Se você estiver almejando o Windows Phone 8.1 (não Silverlight), consulte o [tutorial do Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md).
> 
> 

Este tutorial exige o seguinte:

* Visual Studio 2013
* [MicrosoftAzure.MobileEngagement] 

> [!NOTE]
> Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started).
> 
> 

## <a id="setup-azme"></a>Configurar o Mobile Engagement para aplicativos do Windows Phone
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Conecte o seu aplicativo ao back-end do Mobile Engagement
Este tutorial apresenta uma "integração básica" que é o conjunto mínimo exigido para coletar dados e enviar uma notificação por push. A documentação de integração completa pode ser encontrada na [integração do SDK do Windows Phone para o Mobile Engagement](mobile-engagement-windows-phone-sdk-overview.md)

Criaremos um aplicativo básico com o Visual Studio para demonstrar a integração.

### <a name="create-a-new-windows-phone-silverlight-project"></a>Criação de um novo projeto do Windows Phone Silverlight
As etapas a seguir pressupõem o uso do Visual Studio 2015, embora as etapas sejam semelhantes em versões anteriores do Visual Studio. 

1. Inicie o Visual Studio e na tela **Início**, selecione **Novo Projeto**.
2. No menu pop-up, selecione **Windows 8** -> **Windows Phone** -> **Aplicativo em Branco (Windows Phone Silverlight)**. Preencha o **Nome** e o **Nome da solução** do aplicativo, em seguida, clique em **OK**.
   
    ![][1]
3. Você pode escolher como destino **Windows Phone 8.0** ou **Windows Phone 8.1**.

Você agora criou um novo aplicativo do Windows Phone Silverlight no qual integraremos o SDK do Azure Mobile Engagement.

### <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Conecte o seu aplicativo ao back-end do Mobile Engagement
1. Instale o pacote do nuget [MicrosoftAzure.MobileEngagement] em seu projeto.
2. Abra `WMAppManifest.xml` (na pasta Propriedades) e verifique se os itens a seguir estão declarados (adicione caso não estejam) na marca `<Capabilities />`:
   
        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />
   
    ![][2]
3. Agora cole a cadeia de conexão que você copiou anteriormente para o seu aplicativo do Mobile Engagement e cole no arquivo `Resources\EngagementConfiguration.xml` entre as marcas `<connectionString>` e `</connectionString>`:
   
    ![][3]
4. No arquivo `App.xaml.cs`:
   
    a. Adicione a instrução `using`:
   
            using Microsoft.Azure.Engagement;
   
    b. Inicializar o SDK no método `Application_Launching`:
   
            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }
   
    c. Insira o seguinte em `Application_Activated`:
   
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }

## <a id="monitor"></a>Habilitar monitoramento em tempo real
Para iniciar o envio de dados e assegurar que os usuários estejam ativos, você deve enviar pelo menos uma tela (Atividade) para o back-end do Mobile Engagement.

1. Em MainPage.xaml.cs, adicione a instrução `using`:
   
        using Microsoft.Azure.Engagement;
2. Substitua a classe base **MainPage**, que fica antes de **PhoneApplicationPage**, por **EngagementPage**.
   
        class MainPage : EngagementPage 
3. No arquivo `MainPage.xml`:
   
    a. Adicione às suas declarações de namespaces:
   
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
   
    b. Substitua `phone:PhoneApplicationPage` no nome da marca XML por `engagement:EngagementPage`.

## <a id="monitor"></a>Conectar o aplicativo com monitoramento em tempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Habilitar notificações por push e mensagens no aplicativo
O Mobile Engagement permite interagir e entrar em contato com usuários com notificações por push e mensagens no aplicativo no contexto de campanhas. Esse módulo é chamado de REACH no portal do Mobile Engagement.
As seções a seguir configuram seu aplicativo para recebê-las.

### <a name="enable-your-app-to-receive-mpns-push-notifications"></a>Habilitar o aplicativo para receber Notificações por Push do MPNS
Adicionar novos recursos ao arquivo `WMAppManifest.xml` :

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

### <a name="initialize-the-reach-sdk"></a>Inicializar o SDK do REACH
1. Em `App.xaml.cs`, chame `EngagementReach.Instance.Init();` na função **Application_Launching**, logo após a inicialização do agente:
   
        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }
2. Em `App.xaml.cs`, chame `EngagementReach.Instance.OnActivated(e);` na função **Application_Activated** logo após a inicialização do agente:
   
        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

Você está pronto. Agora vamos verificar se você criou corretamente essa integração básica.

## <a id="send"></a>Envie uma notificação para seu aplicativo
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Você agora verá uma notificação em seu dispositivo que será exibida como uma notificação no aplicativo, se o aplicativo estiver aberto como uma notificação do sistema semelhante à seguinte: 

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png
