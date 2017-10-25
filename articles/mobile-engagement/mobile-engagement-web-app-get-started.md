---
title: "Introdução ao Azure Mobile Engagement para os Aplicativos Web | Microsoft Docs"
description: "Saiba como usar o Azure Mobile Engagement com a análise e as notificações por push para os Aplicativos Web."
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 04afe53a-4caf-4c80-bd75-20cc630cd75c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: hero-article
ms.date: 06/01/2016
ms.author: piyushjo
ms.openlocfilehash: abcb04e4e0a3ae4fdba3a4ded20b3846ac3b21e6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-web-apps"></a>Introdução ao Azure Mobile Engagement para Aplicativos Web
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile Engagement para entender o uso do aplicativo Web.

> [!NOTE]
> O serviço Azure Mobile Engagement será desativado em março de 2018 e, no momento, está disponível somente para os clientes existentes. Para saber mais, confira [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Este tutorial exige o seguinte:

* Visual Studio 2015 ou qualquer outro editor de sua escolha
* [SDK da Web](http://aka.ms/P7b453)

Esse SDK da Web está na Visualização, só dá suporte à Análise no momento e ainda não suporta enviar notificações por push no aplicativo ou no navegador. 

> [!NOTE]
> Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started).
> 
> 

## <a name="setup-mobile-engagement-for-your-web-app"></a>Configurar o Mobile Engagement para seu aplicativo Web
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Conecte o seu aplicativo ao back-end do Mobile Engagement
Este tutorial apresenta uma "integração básica", que é o conjunto mínimo exigido para coletar dados.

Criaremos um aplicativo Web básico com o Visual Studio para demonstrar a integração, embora você possa seguir as etapas com qualquer aplicativo Web criado fora do Visual Studio também. 

### <a name="create-a-new-web-app"></a>Criar um novo aplicativo Web
As etapas a seguir pressupõem o uso do Visual Studio 2015, embora as etapas sejam semelhantes em versões anteriores do Visual Studio. 

1. Inicie o Visual Studio e na tela **Início**, selecione **Novo Projeto**.
2. No menu pop-up, selecione **Web** -> **Aplicativo Web do ASP.Net**. Preencha o **Nome** do aplicativo, **Local** e **Nome da solução**, em seguida, clique em **OK**.
3. No pop-up **Selecionar um modelo**, selecione **Vazio** em **Modelos do ASP.Net 4.5** e clique em **OK**. 

Agora, você criou um novo projeto de Aplicativo Web em branco no qual integraremos o SDK do Azure Mobile Engagement da Web.

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Conecte seu aplicativo ao back-end do Mobile Engagement
1. Crie uma nova pasta denominada **javascript** em sua solução e adicione o arquivo JS SDK da Web **azure-engagement.js** a ela. 
2. Adicione um novo arquivo denominado **main.js** a essa pasta javascript com o código a seguir. Atualize a cadeia de conexão. Este objeto `azureEngagement` será usado para acessar os métodos SDK da Web. 
   
        var azureEngagement = {
            debug: true,
            connectionString: 'xxxxx'
        };
   
    ![Visual Studio com arquivos js][1]

## <a name="enable-real-time-monitoring"></a>Habilitar o monitoramento em tempo real
Para começar a enviar dados e assegurar que os usuários estejam ativos, você deve enviar, pelo menos, uma Atividade para o back-end do Mobile Engagement. Uma atividade no contexto de um aplicativo Web é uma página da Web. 

1. Crie uma nova página denominada **home.html** em sua solução e configure-a como a página inicial de seu aplicativo Web. 
2. Inclua os dois javascripts adicionados anteriormente nesta página acrescentando o seguinte na marcação body. 
   
        <script type="text/javascript" src="javascript/main.js"></script>
        <script type="text/javascript" src="javascript/azure-engagement.js"></script>
3. Atualize a marcação body para chamar o método `startActivity` do EngagementAgent
   
        <body onload="engagement.agent.startActivity('Home')">
4. Sua **home.html** ficará assim
   
        <html>
        <head>
            ...
        </head>
        <body onload="engagement.agent.startActivity('Home')">
            <script type="text/javascript" src="javascript/main.js"></script>
            <script type="text/javascript" src="javascript/azure-engagement.js"></script>
        </body>
        </html>

## <a name="connect-app-with-real-time-monitoring"></a>Conectar o aplicativo com monitoramento em tempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

  ![][2]

## <a name="extend-analytics"></a>Estender a análise
Aqui estão todos os métodos disponíveis no momento com o SDK da Web que você pode usar para a análise:

1. Páginas de atividades/Web:
   
        engagement.agent.startActivity(name);
        engagement.agent.endActivity();
2. Eventos
   
        engagement.agent.sendEvent(name, extras);
3. Erros
   
        engagement.agent.sendError(name, extras);
4. Trabalhos
   
        engagement.agent.startJob(name);
        engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png

