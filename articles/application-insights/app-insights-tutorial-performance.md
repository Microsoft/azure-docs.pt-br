---
title: Diagnosticar problemas de desempenho usando o Azure Application Insights | Microsoft Docs
description: Tutorial para localizar e diagnosticar problemas de desempenho no seu aplicativo usando o Azure Application Insights.
services: application-insights
keywords: 
author: bwren
ms.author: bwren
ms.date: 09/18/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 411e10367f02846261f9fcc7717b5abb147b2c09
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Localizar e diagnosticar problemas de desempenho com o Azure Application Insights

O Azure Application Insights coleta a telemetria do seu aplicativo para ajudar a analisar suas operações e desempenho.  Você pode usar essas informações para identificar problemas que podem estar ocorrendo ou os aperfeiçoamentos ao aplicativo que mais causariam impacto aos usuários.  Este tutorial o conduz pelo processo de analisar o desempenho dos componentes do servidor do seu aplicativo e a perspectiva do cliente.  Você aprenderá como:

> [!div class="checklist"]
> * Identificar o desempenho de operações no lado do servidor
> * Analisar as operações do servidor para determinar a causa raiz do desempenho lento
> * Identificar as operações mais lentas do lado do cliente
> * Analisar os detalhes de exibições de página usando a linguagem de consulta


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Instale o [Visual Studio 2017](https://www.visualstudio.com/downloads/) com as cargas de trabalho a seguir:
    - Desenvolvimento Web e ASP.NET
    - Desenvolvimento do Azure
- Implante um aplicativo .NET para Azure e [habilite o SDK do Application Insights](app-insights-asp-net.md).
- [Habilitar o Application Insights Profiler](app-insights-profiler.md#enable-the-profiler) para seu aplicativo. 

## <a name="log-in-to-azure"></a>Fazer logon no Azure
Faça logon no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="identify-slow-server-operations"></a>Identificar operações lentas do servidor 
O Application Insights coleta detalhes de desempenho para as diferentes operações em seu aplicativo.  Ao identificar as operações de maior duração, você pode diagnosticar problemas em potencial ou direcionar melhor seu desenvolvimento contínuo para aprimorar o desempenho geral do aplicativo. 

1. Selecione **Application Insights** e, em seguida, selecione sua assinatura.  
1. Para abrir o painel **Desempenho**, selecione **Desempenho** no menu **Investigar** ou clique no gráfico **Tempo de Resposta do Servidor**.

    ![Desempenho](media/app-insights-tutorial-performance/performance.png)

2. O painel **Desempenho** mostra a contagem e a duração média de cada operação para o aplicativo.  Você pode usar essas informações para identificar as operações que têm mais impacto para os usuários. Neste exemplo, **GET Customers/Details** e **GET Home/Index** são candidatos prováveis à investigação devido à duração e número de chamadas relativamente grandes.  Outras operações podem ter uma duração maior, mas raramente foram chamadas, assim, o efeito de sua aperfeiçoá-las seria mínimo.  

    ![Painel de desempenho](media/app-insights-tutorial-performance/performance-blade.png)

3. Atualmente, o gráfico mostra a duração média de todas as operações ao longo do tempo.  Adicione as operações em que você está interessado em fixando-as ao gráfico.  Isso mostra que há alguns picos que valem a pena investigar.  Isole isso ainda mais tarde reduzindo a janela de tempo do gráfico.

    ![Fixar operações](media/app-insights-tutorial-performance/pin-operations.png)

4.  Clique em uma operação para exibir seu painel de desempenho à direita. Isso mostra a distribuição de durações para diferentes solicitações.  Os usuários costumam observar um desempenho lento em cerca de meio segundo, assim, reduza a janela para solicitações acima de 500 milissegundos.  

    ![Distribuição de duração](media/app-insights-tutorial-performance/duration-distribution.png)
  
5.  Neste exemplo, você pode ver que um número significativo de solicitações está levando mais de um segundo para ser processado. Você pode ver os detalhes dessa operação clicando em **Detalhes da operação**.

    ![Detalhes da operação](media/app-insights-tutorial-performance/operation-details.png)
    
6.  As informações que você coletou até agora apenas confirmam que há um desempenho lento, mas não ajuda muito a chegar à causa raiz.  O **Criador de Perfil** ajuda com isso mostrando o código real que executou a operação e o tempo necessário para cada etapa. Algumas operações podem não ter um rastreamento, já que o criador de perfil é executado periodicamente.  Ao longo do tempo, mais operações devem ter rastreamentos.  Para iniciar o criador de perfil para a operação, clique em **Rastreamentos do criador de perfil**.
5.  O rastreamento mostra os eventos individuais para cada operação para que você possa diagnosticar a causa raiz para a duração da operação geral.  Clique em um dos exemplos principais, que têm a duração mais longa.
6.  Clique em **Mostrar Afunilamento** para realçar o caminho específico de eventos que mais contribuem para a duração total da operação.  Neste exemplo, você pode ver que uma exceção foi gerada em dois segundos aguardando um recurso.

    ![Detalhes do criador de perfil](media/app-insights-tutorial-performance/profiler-details.png)

7.  A **Dica de Desempenho** na parte superior da tela dá suporte à avaliação de que a duração excessiva se deve à espera.  Clique no link **esperando** para obter a documentação sobre como interpretar os diferentes tipos de eventos.

    ![Dica de desempenho](media/app-insights-tutorial-performance/performance-tip.png)

8.  Para análise adicional, você pode clicar em **Baixar rastreamento .etl** para baixar o rastreamento para o Visual Studio.

## <a name="use-analytics-data-for-server"></a>Usar dados de análise para o servidor
O Application Insights Analytics oferece uma linguagem de consulta avançada que permite analisar todos os dados coletados pelo Application Insights.  Você pode usar isso para executar a análise detalhada nos dados de solicitação e desempenho.

1. Retorne ao painel de detalhes da operação e clique no botão Analytics.

    ![Botão Analytics](media/app-insights-tutorial-performance/server-analytics-button.png)

2. O Application Insights Analytics é aberto com uma consulta para cada uma das exibições no painel.  Você pode executar essas consultas como elas estão ou modificá-las conforme suas necessidades.  A primeira consulta mostra a duração para essa operação ao longo do tempo.
    
    ![Análise](media/app-insights-tutorial-performance/server-analytics.png)


## <a name="identify-slow-client-operations"></a>Identificar operações lentas do cliente 
Além de identificar os processos do servidor a otimizar, o Application Insights pode analisar a perspectiva de navegadores cliente.  Isso pode ajudá-lo a identificar possíveis aprimoramentos aos componentes do cliente e até mesmo identificar problemas com navegadores diferentes ou locais diferentes. 

1. Selecione **Navegador** em **Investigar** para abrir o resumo do navegador.  Isso fornece um resumo visual de várias telemetrias do seu aplicativo da perspectiva do navegador.

    ![Resumo do navegador](media/app-insights-tutorial-performance/browser-summary.png)
 
2.  Role para baixo até **Quais são minhas páginas mais lentas?**.  Isso mostra uma lista das páginas em seu aplicativo que levaram mais tempo para serem carregadas nos clientes.  Você pode usar essas informações para priorizar as páginas que têm o impacto mais significativo sobre o usuário.
3.  Clique em uma das páginas para abrir o painel **Exibição de página**.  No exemplo, a página **/FabrikamProd** está mostrando uma duração média excessiva.  O painel **Exibição de página** apresenta detalhes sobre essa página, incluindo uma análise de diferentes intervalos de duração.

    ![Exibição de página](media/app-insights-tutorial-performance/page-view.png)
 
4.  Clique na duração mais alta para inspecionar os detalhes dessas solicitações.  Clique na solicitação individual para exibir detalhes do cliente que está solicitando a página, incluindo o tipo de navegador e sua localização.  Essas informações podem ajudá-lo a determinar se há problemas de desempenho relacionados a determinado tipos de clientes.

    ![Detalhes da solicitação](media/app-insights-tutorial-performance/request-details.png) 

## <a name="use-analytics-data-for-client"></a>Usar dados de análise para o cliente
Como os dados coletados para o desempenho do servidor, o Application Insights disponibiliza todos os dados do cliente para uma análise profunda usando o Analytics.

1. Volte para o resumo do navegador e clique no ícone do Analytics.

    ![Ícone do Analytics](media/app-insights-tutorial-performance/client-analytics-icon.png)

2. O Application Insights Analytics é aberto com uma consulta para cada uma das exibições no painel. A primeira consulta mostra a duração para diferentes exibições de página ao longo do tempo.

    ![Análise](media/app-insights-tutorial-performance/client-analytics.png)

3.  Diagnóstico inteligente é um recurso do Application Insights Analytics que identifica padrões exclusivos nos dados.  Quando você clica em um ponto de Diagnóstico Inteligente no gráfico de linhas, a mesma consulta é executada sem os registros que causaram a anomalia.  Detalhes desses registros são mostrados na seção de comentários da consulta para que você possa identificar as propriedades das exibições de página que estão causando a duração excessiva.

    ![Diagnóstico inteligente](media/app-insights-tutorial-performance/client-smart-diagnostics.png)


## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a identificar exceções de tempo de execução, avance para o próximo tutorial para aprender a criar alertas em resposta a falhas.

> [!div class="nextstepaction"]
> [Alerta sobre a integridade do aplicativo](app-insights-tutorial-alert.md)
