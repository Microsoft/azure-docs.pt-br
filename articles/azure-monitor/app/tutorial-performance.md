---
title: Diagnosticar problemas de desempenho usando o Azure Application Insights | Microsoft Docs
description: Tutorial para localizar e diagnosticar problemas de desempenho no seu aplicativo usando o Azure Application Insights.
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 06/15/2020
ms.custom: mvc
ms.openlocfilehash: 36a581cb958639f4e9d88a8828b341ef1a1919d7
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383211"
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

- Instale o [Visual Studio 2019](https://www.visualstudio.com/downloads/) com as cargas de trabalho a seguir:
    - Desenvolvimento Web e ASP.NET
    - Desenvolvimento do Azure
- Implante um aplicativo .NET para Azure e [habilite o SDK do Application Insights](../app/asp-net.md).
- [Habilitar o Application Insights Profiler](../app/profiler.md#installation) para seu aplicativo.

## <a name="log-in-to-azure"></a>Fazer logon no Azure
Faça logon no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="identify-slow-server-operations"></a>Identificar operações lentas do servidor
O Application Insights coleta detalhes de desempenho para as diferentes operações em seu aplicativo. Ao identificar as operações de maior duração, você pode diagnosticar problemas em potencial ou direcionar melhor seu desenvolvimento contínuo para aprimorar o desempenho geral do aplicativo.

1. Selecione **Application Insights** e, em seguida, selecione sua assinatura.  
1. Para abrir o painel **Desempenho**, selecione **Desempenho** no menu **Investigar** ou clique no grafo **Tempo de Resposta do Servidor**.

    ![Desempenho](media/tutorial-performance/1-overview.png)

2. O painel **Desempenho** mostra a contagem e a duração média de cada operação para o aplicativo.  Você pode usar essas informações para identificar as operações que têm mais impacto para os usuários. Neste exemplo, **GET Customers/Details** e **GET Home/Index** são candidatos prováveis à investigação devido à duração e número de chamadas relativamente grandes.  Outras operações podem ter uma duração maior, mas raramente foram chamadas, assim, o efeito de sua aperfeiçoá-las seria mínimo.  

    ![Painel do servidor de desempenho](media/tutorial-performance/2-server-operations.png)

3. Atualmente, o gráfico mostra a duração média de todas as operações ao longo do tempo. Você pode alternar para o 95º percentil para encontrar os problemas de desempenho. Adicione as operações em que você está interessado em fixando-as ao grafo.  Isso mostra que há alguns picos que valem a pena investigar.  Isole isso ainda mais tarde reduzindo a janela de tempo do grafo.

    ![Fixar operações](media/tutorial-performance/3-server-operations-95th.png)

4.  O painel de desempenho à direita mostra a distribuição de durações para diferentes solicitações da operação selecionada.  Reduza a janela para iniciar em torno do 95º percentil. O cartão de insights “Três dependências principais” pode informar rapidamente que as dependências externas provavelmente estão contribuindo para as transações lentas.  Clique no botão com o número de amostras para ver uma lista dos exemplos. Em seguida, é possível selecionar qualquer amostra para ver os detalhes da transação.

5.  Você pode ver rapidamente que a chamada para a Tabela Fabrikamaccount do Azure está contribuindo principalmente para a duração total da transação. Também é possível ver que uma exceção causou a falha. É possível clicar em qualquer item na lista para ver seus detalhes no lado direito. [Saiba mais sobre a experiência de diagnóstico de transação](../app/transaction-diagnostics.md)

    ![Detalhes de ponta a ponta da operação](media/tutorial-performance/4-end-to-end.png)
    

6.  O [**Criador de perfil**](../app/profiler-overview.md) ajuda a ir além com o diagnóstico de nível de código, mostrando o código real que executou a operação e o tempo necessário para cada etapa. Algumas operações podem não ter um rastreamento, já que o criador de perfil é executado periodicamente.  Ao longo do tempo, mais operações devem ter rastreamentos.  Para iniciar o criador de perfil para a operação, clique em **Rastreamentos do criador de perfil**.
5.  O rastreamento mostra os eventos individuais para cada operação para que você possa diagnosticar a causa raiz para a duração da operação geral.  Clique em um dos exemplos principais, que têm a duração mais longa.
6.  Clique em **Afunilamento** para realçar o caminho específico de eventos que mais contribuem para a duração total da operação.  Neste exemplo, você pode ver que a chamada mais lenta é a do método *FabrikamFiberAzureStorage.GetStorageTableData*. A parte que usa a maior parte do tempo é o método *CloudTable.CreateIfNotExist*. Se esta linha de código for executada sempre que a função for chamada, serão consumidos recursos de CPU e de chamada de rede desnecessários. A melhor maneira de corrigir seu código é colocar essa linha em algum método de inicialização que seja executado somente uma vez.

    ![Detalhes do criador de perfil](media/tutorial-performance/5-hot-path.png)

7.  A **Dica de Desempenho** na parte superior da tela dá suporte à avaliação de que a duração excessiva se deve à espera.  Clique no link **esperando** para obter a documentação sobre como interpretar os diferentes tipos de eventos.

    ![Dica de desempenho](media/tutorial-performance/6-perf-tip.png)

8.   Para análise adicional, você pode clicar em **Baixar rastreamento** para baixar o rastreamento. Você pode exibir esses dados usando [PerfView](https://github.com/Microsoft/perfview#perfview-overview).

## <a name="use-logs-data-for-server"></a>Usar dados de logs para o servidor
 Os logs oferecem uma linguagem de consulta avançada que permite analisar todos os dados coletados pelo Application Insights. Você pode usar isso para executar a análise detalhada nos dados de solicitação e desempenho.

1. Retorne ao painel de detalhes da operação e clique no ![ícone Logs](media/tutorial-performance/app-viewinlogs-icon.png)**Exibir em Logs (Analytics)**

2. Os logs são abertos com uma consulta para cada uma das exibições no painel.  Você pode executar essas consultas como elas estão ou modificá-las conforme suas necessidades.  A primeira consulta mostra a duração para essa operação ao longo do tempo.

    ![consulta de logs](media/tutorial-performance/7-request-time-logs.png)


## <a name="identify-slow-client-operations"></a>Identificar operações lentas do cliente
Além de identificar os processos do servidor a otimizar, o Application Insights pode analisar a perspectiva de navegadores cliente.  Isso pode ajudá-lo a identificar possíveis aprimoramentos aos componentes do cliente e até mesmo identificar problemas com navegadores diferentes ou locais diferentes.

1. Selecione **Navegador** em **Investigar** e clique em **Desempenho do Navegador** ou selecione **Desempenho** em **Investigar** e alterne para a guia **Navegador** clicando no botão de alternância de servidor/navegador na parte superior direita para abrir o resumo do desempenho do navegador. Isso fornece um resumo visual de várias telemetrias do seu aplicativo da perspectiva do navegador.

    ![Resumo do navegador](media/tutorial-performance/8-browser.png)

2. Selecione um dos nomes de operação e clique no botão amostras azul na parte inferior direita e selecione uma operação. Isso abrirá os detalhes da transação de ponta a ponta e, no lado direito, você poderá exibir as **Propriedades de Exibição de Página**. Isso permite que você exiba detalhes do cliente que está solicitando a página, incluindo o tipo de navegador e sua localização. Essas informações podem ajudá-lo a determinar se há problemas de desempenho relacionados a determinado tipos de clientes.

    ![Exibição de página](media/tutorial-performance/9-page-view-properties.png)

## <a name="use-logs-data-for-client"></a>Usar dados de logs para o cliente
Como os dados coletados para o desempenho do servidor, o Application Insights disponibiliza todos os dados do cliente para uma análise profunda usando logs.

1. Volte para o resumo do navegador e clique no ![ícone Logs](media/tutorial-performance/app-viewinlogs-icon.png) **Exibir em Logs (Analytics)**

2. Os logs são abertos com uma consulta para cada uma das exibições no painel. A primeira consulta mostra a duração para diferentes exibições de página ao longo do tempo.

    ![Consulta de logs](media/tutorial-performance/10-page-view-logs.png)

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a identificar exceções de tempo de execução, avance para o próximo tutorial para aprender a criar alertas em resposta a falhas.

> [!div class="nextstepaction"]
> [Alerta sobre a integridade do aplicativo](./tutorial-alert.md)

