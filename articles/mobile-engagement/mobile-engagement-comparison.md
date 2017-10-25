---
title: "Comparando o Azure Mobile Engagement a outros serviços parecidos do Azure"
description: "Comparando o Azure Mobile Engagement a outros serviços parecidos do Azure - HockeyApp, AppInsights, Hubs de Notificação"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 1f114775-3a9a-4dd4-8d59-b10d1da9a68b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 7df2eb9ecebe3313dad9c15171552a084787f6b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="comparing-azure-mobile-engagement-with-other-similar-azure-services"></a>Comparando o Azure Mobile Engagement a outros serviços parecidos do Azure
A lista de serviços oferecidos pelo Microsoft Azure está sempre em expansão e, às vezes, você pode se perguntar qual é a diferença do Azure Mobile Engagement desse outro serviço que você acabou de ler ou sobre o qual ouvir falar. Este artigo tenta esclarecer a confusão e direciona você a escolher o Azure Mobile Engagement quando ele for mais adequado para seu uso. 

O Azure Mobile Engagement é um serviço direcionado especificamente **aos comerciantes digitais/CMOs**, mas pode ser usado por qualquer **proprietário de aplicativo móvel ou editor** que quer aumentar o uso, a retenção e a monetização de seus aplicativos móveis. 

*Ele é uma plataforma SaaS (software como serviço) de envolvimento com o usuário que fornece informações orientadas aos dados sobre uso do aplicativo, segmentação do usuário em tempo real, além de habilitar notificações por push com reconhecimento contextual e mensagens no aplicativo.* 

Detalhando essa definição, temos as seguintes características que também destacam sua proposta de valor exclusivo:

1. **Notificações por push e mensagens no aplicativo com reconhecimento de contexto**
   
   Esse é o foco principal do produto: executar notificações por push direcionadas e personalizadas. E, para que isso aconteça, coletamos muitos dados de análise de comportamento. 
2. **Informações controladas por dados sobre o uso do aplicativo**
   
   Fornecemos SDKs entre plataformas para coletar análises comportamentais sobre os usuários do aplicativo. Observe o termo análise comportamental (ao contrário de análise de desempenho), pois vamos nos concentrar em como os usuários do aplicativo estão usando o aplicativo. Coletamos dados básicos de análise do desempenho sobre erros, falhas etc, mas esse não é o foco principal do produto. 
3. **Segmentação de usuários em tempo real**
   
   Depois de coletar os dados de análise comportamental dos usuários do aplicativo, permitimos que você segmente seu público com base em diversos parâmetros e dados coletados a fim de permitir que você execute campanhas por push direcionadas. 
4. **Software como um serviço (SaaS):**
   
   Temos um portal separado do Portal de Gerenciamento do Azure que é otimizado para interagir e exibir a análise comportamental avançada sobre os usuários do aplicativo e executar campanhas de marketing por push. O produto tem como objetivo ajudar você a começar a trabalhar rapidamente!   

Com esse conjunto de diferenciação em mãos, veja como podemos comparar com outras ofertas do Azure aparentemente semelhantes. Observe que o artigo não realiza uma comparação no nível dos recursos, mas usa uma abordagem baseada em cenário para definir qual produto funciona melhor:

1. [HockeyApp](https://azure.microsoft.com/services/hockeyapp/) é a solução móvel de DevOps da Microsoft. Com ele, você pode distribuir compilações para testadores beta, coletar dados de falha e obter comentários dos usuários. Ele também é integrado ao Visual Studio Team Services, permitindo a compilação fácil de implantações e a integração de itens de trabalho. 
   
   O foco aqui sobre o DevOps é a coleta de dados de análise de desempenho sobre os aplicativos móveis. Você pode acabar integrando o HockeyApps e o Mobile Engagement a seu aplicativo, e isso não será incomum, pois mesmo que haja alguma sobreposição nos dados coletados, o foco principal dos produtos é diferente e eles ajudam você a atingir objetivos diferentes.  
2. [Application Insights](../application-insights/app-insights-overview.md) Se o seu aplicativo móvel tiver um lado de servidor, você usará o Application Insights para monitorar o lado do servidor Web de seu aplicativo, mas para os aplicativos móveis no lado do cliente, você deverá usar o HockeyApp. 
3. Os [Hubs de Notificação](https://azure.microsoft.com/services/notification-hubs/) fornecem um serviço leve, pois não é necessário ter um SDK integrado ao aplicativo móvel. Além disso, você pode ter o controle total sobre seu aplicativo móvel e ele permite o envio de notificações por push com os recursos básicos de marcação. Isso é ótimo para qualquer proprietário de aplicativo que se preocupa menos com direcionamento e mais com o envio/comunicação instantânea de informações para os usuários do aplicativo (difusão para uma população grande). 
   
   Observe que o foco está no envio incrivelmente rápido de notificações com o recurso básico de segmentação. 

Vamos analisar alguns cenários:

1. Paulo faz parte da equipe de marketing digital na loja Adventure Works que publica os aplicativos móveis para os usuários. O objetivo de Paulo é garantir que os usuários que baixam seus aplicativos móveis continuem a usá-los e com frequência. Isso não apenas aumenta a fixação da marca nos usuários do aplicativo, mas também aumenta a monetização quando os usuários do aplicativo fazem compras usando o aplicativo móvel. Para isso, Paulo precisará enviar notificações direcionadas aos usuários do aplicativo, que eles consideram úteis, a fim de incentivá-los a abrir o aplicativo e voltar a ele com frequência. É nessa situação que Paulo considerará o Mobile Engagement bastante útil. 
2. Clara faz parte da equipe de desenvolvimento de aplicativos móveis da Adventure Works e está procurando um produto para registrar detalhes sobre falhas, exceções e para obter a telemetria de desempenho de um aplicativo. É nessa situação que Clara considerará o HockeyApp bastante útil. Clara pode integrar o HockeyApp para seus cenários focados no desenvolvedor e o Azure Mobile Engagement para Paulo no mesmo aplicativo, a fim de obter o melhor dos dois mundos. 
3. Laura faz parte da equipe de desenvolvimento de aplicativos móveis da rede Contoso News, e tudo o que ela quer é enviar alertas de notícias recentes para todos os usuários sem muita segmentação e assim que o alerta de notícias é disparado. É nessa situação que Laura considerará o Hubs de Notificação bastante útil. 
   Nesse cenário, é possível que, à medida que o aplicativo móvel amadurece, ocorra uma necessidade de realizar uma segmentação muito mais detalhada e de obter detalhes sobre o comportamento do usuário do aplicativo. Neste momento, Laura precisará avaliar o Azure Mobile Engagement. 

Para recapitular, a finalidade do Mobile Engagement não é apenas coletar análises. Ele não é "outro produto de análise da Microsoft". Trata-se de enviar notificações direcionadas por push e, para esse direcionamento, coletamos dados de análise comportamental, mas o foco permanece no envio de notificações que façam mais sentido para os usuários do aplicativo, para elas não pareçam spam. 

Para obter mais detalhes, confira este [vídeo rápido](mobile-engagement-overview.md) sobre o Mobile Engagement. 

