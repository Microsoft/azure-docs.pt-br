---
title: Visão geral de diagnóstico do Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como você pode solucionar problemas com seu aplicativo com o diagnóstico do serviço de aplicativo.
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
ms.openlocfilehash: f2f798be85e9c3aeb8d4b54cba89d8be059427e0
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147324"
---
# <a name="azure-app-service-diagnostics-overview"></a>Visão geral de diagnóstico do Serviço de Aplicativo do Azure

Quando você estiver executando um aplicativo Web, você deseja estar preparado para os problemas que podem surgir, desde os erros 500 até os usuários informando que seu site está inoperante. Diagnóstico do serviço de aplicativo é uma experiência interativa e inteligente para ajudá-lo a solucionar problemas do aplicativo sem nenhuma configuração necessária. Quando você enfrentar problemas com seu aplicativo, o diagnóstico do serviço de aplicativo aponta o que está errado para guiar você até as informações certas para mais fácil e rápida solução de problemas e resolver o problema.

Embora essa experiência é mais útil quando você estiver tendo problemas com seu aplicativo nas últimas 24 horas, todos os gráficos de diagnóstico estão sempre disponíveis para você analisar.

O diagnóstico do Serviço de Aplicativo funciona não para apenas o aplicativo no Windows, mas também aplicativos em [Linux/contêineres](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), [Ambiente do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/environment/intro) e [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).

## <a name="open-app-service-diagnostics"></a>Abra o diagnóstico do Serviço de Aplicativo

Para acessar o diagnóstico do serviço de aplicativo, navegue até seu aplicativo web do serviço de aplicativo ou o ambiente de serviço de aplicativo na [portal do Azure](https://portal.azure.com). No painel de navegação esquerdo, clique em **Diagnosticar e resolver problemas**.

Para funções do Azure, navegue até seu aplicativo de funções e, no painel de navegação superior, clique em **recursos da plataforma**e selecione **diagnosticar e resolver problemas** do **gerenciamentoderecursos** seção.

Na home page diagnóstico do serviço de aplicativo, você pode escolher a categoria que melhor descreva o problema com seu aplicativo usando as palavras-chave em cada bloco de home page. Além disso, esta página é onde você pode encontrar **ferramentas de diagnóstico** para aplicativos do Windows. Ver [ferramentas de diagnóstico (somente para o aplicativo do Windows)](#diagnostic-tools-only-for-windows-app).

![Home page](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>Interface interativa

Depois de selecionar uma categoria de home page que se alinha melhor com o problema do seu aplicativo, interface interativa dos diagnósticos do serviço de aplicativo, o gênio, pode orientá-lo por meio de diagnosticar e resolver o problema com seu aplicativo. Você pode usar os atalhos de bloco fornecidos pelo gênio para exibir o relatório de diagnóstico completo da categoria de problema que você está interessado. Os atalhos de bloco fornecem uma maneira direta de acessar suas métricas de diagnóstico.

![Atalhos de bloco](./media/app-service-diagnostics/tile-shortcuts-2.png)

Depois de clicar em um desses blocos, você pode ver uma lista de tópicos relacionados ao problema descrito no bloco. Estes tópicos fornecem os trechos de informações importantes de todo o relatório. Você pode clicar em qualquer um desses tópicos para investigar os problemas ainda mais. Além disso, você pode clicar em **exibir o relatório completo** para explorar todos os tópicos em uma única página.

![Tópicos](./media/app-service-diagnostics/application-logs-insights-3.png)

![Exibir o relatório completo](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Relatório de diagnóstico

Depois que você escolher investigar o problema clicando em um tópico, você pode exibir mais detalhes sobre o tópico geralmente complementado com grafos e markdowns. Relatório de diagnóstico pode ser uma ferramenta poderosa para identificar o problema com seu aplicativo.

![Relatório de diagnóstico](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Verificação de integridade

Se você não souber o que há de errado com seu aplicativo ou não souber por onde começar a solucionar seus problemas, a verificação de integridade é um bom lugar para começar. A verificação de integridade analisa seus aplicativos para dar a você uma visão geral rápida e interativa que destaca o que está íntegro e o que está errado, informando a você onde pesquisar para investigar o problema. Sua interface inteligente e interativa fornece orientações durante o processo de solução de problemas. Verificação de integridade é integrada com a experiência de gênio para aplicativos do Windows e o aplicativo web faz uma busca diagnóstico relatório para aplicativos do Linux.

### <a name="health-checkup-graphs"></a>Gráficos de verificação de integridade

Há quatro gráficos diferentes a verificação de integridade.

- **solicitações e erros:** Um gráfico que mostra o número de solicitações feitas durante as últimas 24 horas, juntamente com os erros do servidor HTTP.
- **desempenho do aplicativo:** Um gráfico que mostra o tempo de resposta nas últimas 24 horas para vários grupos de percentil.
- **Uso da CPU:** Um gráfico que mostra o uso da CPU porcentagem geral por instância nas últimas 24 horas.  
- **uso de memória:** Um gráfico que mostra o uso geral de porcentagem de memória física por instância nas últimas 24 horas.

![Verificação de integridade](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Investigar problemas de código do aplicativo (somente para o aplicativo do Windows)

Como muitos problemas de aplicativo estão relacionados a problemas no código do aplicativo, o diagnóstico do Serviço de Aplicativo integra-se ao [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) para realçar exceções e problemas de dependência para correlacionar com o tempo de inatividade selecionado. Application Insights deve ser habilitado separadamente.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Para exibir as dependências e exceções de Application Insights, selecione a **aplicativo web para baixo** ou **aplicativo web lento** atalhos de bloco.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Etapas de solução de problemas (somente para o aplicativo do Windows)

Se for detectado um problema com uma categoria de problema específico nas últimas 24 horas, você pode exibir o relatório de diagnóstico completo e o diagnóstico do serviço de aplicativo pode solicitar que você exiba o conselho mais solução de problemas e as próximas etapas para uma experiência mais guiada.

![O Application Insights e solução de problemas e próximas etapas](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Ferramentas de diagnóstico (somente para o aplicativo do Windows)

Ferramentas de diagnóstico incluem ferramentas de diagnóstico mais avançadas, que ajuda você investigue aplicativos de código de problemas de lentidão, cadeias de caracteres de conexão e muito mais. e proativas ferramentas que ajudam você a atenuar os problemas com o uso de CPU, solicitações e memória.

### <a name="proactive-cpu-monitoring"></a>O monitoramento proativo da CPU

O monitoramento proativo da CPU fornece uma maneira fácil para executar uma ação quando o processo de aplicativo ou filho para seu aplicativo está consumindo recursos de CPU alta proativa. Você pode definir suas próprias regras de limite de CPU para atenuar temporariamente um problema de CPU alta, até encontra a causa real do problema inesperado.

![O monitoramento proativo da CPU](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="proactive-auto-healing"></a>Recuperação automática proativa

Como o monitoramento proativo de CPU, a recuperação automática proativa oferece uma abordagem proativa fácil à redução de um comportamento inesperado do seu aplicativo. Você pode definir suas próprias regras com base em contagem de solicitações, solicitação lenta, limite de memória e o código de status HTTP para disparar ações de mitigação. Essa ferramenta pode ser usada para reduzir temporariamente um comportamento inesperado, até encontra a causa real do problema. Para obter mais informações sobre a recuperação automática proativa, visite [anunciando o novo automático reparo de experiência no diagnóstico do serviço de aplicativo](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html).

![Recuperação automática proativa](./media/app-service-diagnostics/proactive-auto-healing-10.png)

## <a name="change-analysis-only-for-windows-app"></a>Análise de alterações (somente para o aplicativo do Windows)

Em um ambiente de desenvolvimento rápido, às vezes, pode ser difícil manter o controle de todas as alterações feitas ao seu aplicativo e deixar que pinpoint sozinho em uma alteração que causou um comportamento não íntegro. Análise de alterações pode ajudar a refinar sobre as alterações feitas ao seu aplicativo para facilitar a experiência de solução de problemas. Análise de alterações for encontrado na **alterações de aplicativo** e também inserido em um relatório de diagnóstico, como **aplicativo travar** para que você pode usá-lo simultaneamente com outras métricas.

Análise de alterações deve ser habilitado antes de usar o recurso. Para obter mais informações sobre análise de alterações, visite [anunciando a nova experiência de análise de alteração no diagnóstico do serviço de aplicativo](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).

![Página de padrão de análise de alterações](./media/app-service-diagnostics/change-analysis-default-page-11.png)

![Modo de exibição de comparação](./media/app-service-diagnostics/diff-view-12.png)