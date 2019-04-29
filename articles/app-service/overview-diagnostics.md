---
title: Visão Geral de Diagnóstico - Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como solucionar problemas com seu aplicativo Web com o diagnóstico do Serviço de Aplicativo.
keywords: serviço de aplicativo, serviço de aplicativo do azure, diagnósticos, suporte, aplicativo web, solução de problemas, autoajuda
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: a8b256f43d8e4103404ab4276431ceb06d9ed36a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60839333"
---
# <a name="azure-app-service-diagnostics-overview"></a>Visão geral de diagnóstico do Serviço de Aplicativo do Azure 

Quando você estiver executando um aplicativo Web, você deseja estar preparado para os problemas que podem surgir, desde os erros 500 até os usuários informando que seu site está inoperante. O diagnóstico do Serviço de Aplicativo é uma experiência interativa e inteligente para ajudá-lo a solucionar problemas de seu aplicativo Web sem configuração necessária. Quando você tiver problemas com seu aplicativo Web, o diagnóstico do Serviço de Aplicativo apontará o que está errado para guiar você até as informações adequadas para a forma mais fácil e rápida de solucionar problemas. 
 
Embora essa experiência seja útil quando você estiver tendo problemas com seu aplicativo Web nas últimas 24 horas, todos os gráficos de diagnóstico estarão disponíveis para você analisar o tempo todo. Ferramentas e links de solução de problemas adicionais para documentação e fóruns úteis localizados na coluna à direita.

O diagnóstico do Serviço de Aplicativo funciona não para apenas o aplicativo no Windows, mas também aplicativos em [Linux/contêineres](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), [Ambiente do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/environment/intro) e [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview). 

## <a name="open-app-service-diagnostics"></a>Abra o diagnóstico do Serviço de Aplicativo

Para acessar o diagnóstico do Serviço de Aplicativo, navegue até seu aplicativo Serviço de Aplicativo ou Ambiente do Serviço de Aplicativo no [Portal do Azure](https://portal.azure.com). No painel de navegação esquerdo, clique em **Diagnosticar e resolver problemas**. 

Para Azure Functions, navegue até seu aplicativo de função e, no painel de navegação superior, clique em **Recursos da plataforma** e selecione **Diagnosticar e resolver problemas** na seção **Monitoramento**. 

![Home page](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>Verificação de integridade

Se você não souber o que há de errado com seu aplicativo Web ou não souber por onde começar a solucionar seus problemas, a verificação de integridade é um bom lugar para começar. A verificação de integridade analisará seus aplicativos Web para lhe dar uma visão geral rápida e interativa que indique se o que está íntegro e o que está errado, informando a você onde pesquisar para investigar o problema. Sua interface inteligente e interativa fornece orientações durante o processo de solução de problemas.  

![Verificação de integridade](./media/app-service-diagnostics/HealthCheckup2.png)

Se for detectado um problema com uma categoria de problema específico nas últimas 24 horas, você poderá exibir o relatório de diagnóstico completo e o diagnóstico do Serviço de Aplicativo pode solicitar que você exiba mais conselhos de solução de problemas e as próximas etapas para uma experiência mais guiada.

![Solução de problemas e próximas etapas](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>Atalhos de bloco

Se você souber exatamente qual tipo de solução de problemas de informações está procurando, os atalhos de bloco levarão você diretamente para o relatório de diagnóstico completo da categoria de problema em que você está interessado. Em comparação à verificação de integridade, os atalhos de bloco são mais diretos, mas sã uma maneira menos guiada de acessar suas métricas de diagnóstico. Como parte dos blocos de atalho, aqui também é onde você encontrará **Ferramentas de Diagnóstico** que são ferramentas mais avançadas que ajudam a investigar problemas relacionados a problemas de código do aplicativo, lentidão, cadeias de conexão e muito mais. 

![Atalhos de bloco](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>Relatório de diagnóstico

Se você deseja obter mais informações depois de executar uma [verificação de integridade](#health-checkup) ou se tiver clicado em um dos [atalhos de bloco](#tile-shortcuts), o relatório de diagnóstico completo mostrará as métricas gráficas relevantes das últimas 24 horas. Se seu aplicativo tiver qualquer tempo de inatividade, ele será representado por uma barra laranja abaixo da linha do tempo. Você pode selecionar uma das barras laranjas para selecionar o tempo de inatividade para ver as observações sobre esse tempo de inatividade e as etapas de solução de problemas sugeridas. 

![Relatório de diagnóstico](./media/app-service-diagnostics/DiagnosticReport5.png)


## <a name="investigating-application-code-issues"></a>Investigando problemas de código do aplicativo

Como muitos problemas de aplicativo estão relacionados a problemas no código do aplicativo, o diagnóstico do Serviço de Aplicativo integra-se ao [Application Insights](https://azure.microsoft.com/services/application-insights/) para realçar exceções e problemas de dependência para correlacionar com o tempo de inatividade selecionado. O Application Insights precisa ser habilitado separadamente. 

Para exibir as exceções e dependências do Application Insights, selecione os blocos de atalho **Aplicativo Web inoperante** ou **Aplicativo Web lento**. 

![Application insights](./media/app-service-diagnostics/AppInsights6.png)

