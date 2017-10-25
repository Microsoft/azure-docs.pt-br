---
title: Monitorar a integridade e o uso do aplicativo com o Application Insights
description: "Introdução ao Application Insights. Analise o uso, disponibilidade e desempenho de seu local ou aplicativos do Microsoft Azure."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: bwren
ms.openlocfilehash: 9efe10fa35c6a7c84e0d448bbe53127d16d20870
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-performance-in-web-applications"></a>Monitore o desempenho em aplicativos da web


Certifique-se de que seu aplicativo está sendo bem executado, e saiba rapidamente sobre quaisquer falhas. O [Application Insights][start] lhe informará sobre quaisquer problemas de desempenho e exceções e lhe ajudará a localizar e diagnosticar as causas raiz.

O Application Insights pode monitorar serviços e aplicativos Web Java e ASP.NET e serviços WCF. Eles podem ser hospedados localmente, em máquinas virtuais ou como sites do Microsoft Azure. 

No lado do cliente, o Application Insights pode realizar a telemetria de páginas da Web e de uma grande variedade de dispositivos, incluindo iOS, Android e Aplicativos da Windows Store.

>[!Note]
> Disponibilizamos uma nova experiência para encontrar páginas de desempenho lento em seu aplicativo Web. Caso você não tenha acesso a ela, habilite-a configurando as opções de versão prévia na [folha Versão Prévia](app-insights-previews.md). Leia mais sobre essa nova experiência em [Encontrar e corrigir afunilamentos de desempenho com a investigação de Desempenho interativo](#Find-and-fix-performance-bottlenecks-with-an-interactive-Performance-investigation).

## <a name="setup"></a>Configurar o monitoramento de desempenho
Se você ainda não tem o Application Insights adicionado ao seu projeto (ou seja, não tem o ApplicationInsights.config), escolha uma destas formas para começar:

* [Aplicativos Web ASP.NET](app-insights-asp-net.md)
  * [Adicionar monitoramento de exceção](app-insights-asp-net-exceptions.md)
  * [Adicionar monitoramento de dependência](app-insights-monitor-performance-live-website-now.md)
* [Aplicativos Web J2EE](app-insights-java-get-started.md)
  * [Adicionar monitoramento de dependência](app-insights-java-agent.md)

## <a name="view"></a>Explorando métricas de desempenho
No [portal do Azure](https://portal.azure.com), navegue até o recurso do Application Insights que você configurou para seu aplicativo. A folha de visão geral mostra os dados de desempenho básicos:

Clique em qualquer gráfico para ver mais detalhes e para ver os resultados por um período mais longo. Por exemplo, clique no bloco Solicitações e, em seguida, selecione um intervalo de tempo:

![Clique por mais dados e selecione um intervalo de tempo](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

Clique em um gráfico para selecionar outras medidas que são exibidas, ou adicionar um novo gráfico e selecionar a métrica:

![Clique em um gráfico para escolher métricas](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Desmarque todas as métricas** para ver a seleção completa que está disponível. As métricas se enquadram em grupos; quando qualquer membro de um grupo é selecionado, somente os outros membros do grupo aparecem.

## <a name="metrics"></a>O que significa tudo isso? Blocos e relatórios de desempenho
Há várias métricas de desempenho que você pode obter. Vamos começar com estas que aparecem por padrão na folha do aplicativo.

### <a name="requests"></a>Solicitações
O número de solicitações de HTTP receberam em um período especifico. Compare isso com os resultados em outros reatórios para ver como seu aplicativo se comporta conforme a carga varia.

As solicitações HTTP incluem todas as solicitações GET ou POST para páginas, dados e imagens.

Clique no mosaico para obter contagens para URLs específicas.

### <a name="average-response-time"></a>Tempo de resposta média
Mede o tempo entre uma solicitação da web inserindo seu aplicativo e a resposta que está sendo devolvida.

Os pontos mostram uma média da movimentação. Se existe várias solicitações, pode haver agumas que desviam da média sem um pico óbvio ou profundo no gráfico.

Procure por picos incomuns. Em geral, espere o tempo de resposta para eevar com uma elevação nas solicitações. Se a elevação é desproporcional, seu aplicativo pode ser atingido por um limite de recurso como CPU ou a capacidade de um serviço que usa.

Clique no bloco para obter tempos para URLs específicas.

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Solicitações mais lentas
![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

Mostra quais solicitações podem precisar de sintonização de desempenho.

### <a name="failed-requests"></a>Solicitações falhas
![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

Uma contagem de solicitações que gerou exceções não capturadas.

Clique no mosaico para ver os detalhes de falhas específicas, e selecione uma solicitação individual para ver seus detalhes. 

Somente uma amostra representativa de falhas é retida para inspeção individual.

### <a name="other-metrics"></a>Outras métricas
Para ver o que outras métricas que você pode exibir, clique em um gráfico e, em seguida, desmarque a seleção de todas as métricas para ver o conjunto disponível completo. Clique em (i) para ver cada definição da métrica.

![Desmarque a seleção de todas as métricas para ver o conjunto completo](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)

Selecionar uma métrica desabilitará as outras que não podem ser exibidas no mesmo gráfico.

## <a name="set-alerts"></a>Definir alertas
Para ser notificado por email sobre valores incomuns de qualquer métrica, adicione um alerta. Você pode escolher para enviar o email para os administradores de conta ou para endereços de email específicos.

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

Defina o recurso antes de outras propriedades. Não escolha os recursos webtest se você quer definir alertas em métricas de desempenho ou de uso.

Observe as unidades quando você for solicitado para inserir o valor de limite.

*Não vejo o botão Adicionar Alerta.* - Esta é uma conta de grupo para a qual você tem acesso somente leitura? Verifique com o administrador da conta.

## <a name="diagnosis"></a>Diagnosticando problemas
Aqui estão algumas dicas para localizar e diagnosticar problemas de desempenho:

* Configure os [testes da Web][availability] para serem alertados se seu site cair ou responder de forma incorreta ou lenta. 
* Compare a contagem de Solicitação com outras métricas para ver se falhas ou resposta lenta são relatadas ao carregar.
* [Inserir e pesquisar instruções de rastreamento][diagnostic] em seu código para ajudar a detectar problemas.
* Monitore seu aplicativo Web em operação com o [Live Metrics Stream][livestream].
* Capture o estado do aplicativo .Net com o [Depurador de Instantâneo][snapshot].

>[!Note]
> Estamos fazendo a transição da investigação de desempenho do Application Insights para uma experiência interativa de tela inteira. A documentação a seguir aborda a nova experiência primeiro e, em seguida, examina a experiência anterior caso você ainda precise acessá-la, enquanto permanece disponível durante a transição.

## <a name="find-and-fix-performance-bottlenecks-with-an-interactive-full-screen-performance-investigation"></a>Encontrar e corrigir afunilamentos de desempenho com uma investigação de desempenho de tela inteira

Use a nova investigação de desempenho interativo do Application Insights para examinar operações em seu aplicativo Web que apresentam lentidão no desempenho. Você pode selecionar rapidamente uma operação lenta específica e usar o [Criador de Perfil](app-insights-profiler.md) para determinar, em nível de código, a causa raiz da lentidão nessas operações. Usando a nova distribuição de duração mostrada para a operação selecionada, que você pode rapidamente avaliar o quão problemática essa experiência é para seus clientes. Na verdade, para cada operação lenta, você pode ver quantas das interações do usuário foram afetadas. No exemplo a seguir, decidimos examinar mais detalhadamente a experiência para a operação GET Customers/Details. Na distribuição de duração, vemos que há três picos. O pico mais à esquerda é próximo de 400ms e representa uma ótima experiência responsiva. O pico intermediário é próximo de 1,2s e representa uma experiência mediana. Por fim, em 3,6s, temos outro pico pequeno que representa a experiência no 99º percentil, que provavelmente fará com que nossos clientes saiam insatisfeitos. Essa experiência é dez vezes mais lenta do que a experiência ótima para a mesma operação. 

![Três picos de duração de GET Customers/Details](./media/app-insights-web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Para obter uma melhor noção das experiências do usuário para essa operação, podemos selecionar um intervalo de tempo maior. Em seguida, podemos também restringir o tempo para uma janela de tempo específica em que a operação foi especialmente lenta. No exemplo a seguir, mudamos do intervalo de tempo de 24 horas padrão para o intervalo de tempo de 7 dias e então aplicamos zoom na janela de tempo de 9:47 a 12:47 entre terça-feira (dia 12) e quarta-feira (dia 13). Observe que tanto a distribuição de duração quanto o número de amostras e de rastreamentos do criador de perfil foram atualizados no lado direito.

![Três picos de duração de GET Customers/Details num intervalo de 7 dias com uma janela de tempo](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Para restringir para as experiências lentas, aplicamos zoom em seguida às durações que se enquadram entre o 95º e o 99º percentis. Elas representam 4% das interações do usuário que foram especialmente lentas.

![Três picos de duração de GET Customers/Details num intervalo de 7 dias com uma janela de tempo](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Podemos agora examinar os exemplos representativos clicando no botão Amostras ou então os rastreamentos de criador de perfil representativos clicando no botão Rastreamentos do Criador de Perfil. Neste exemplo, há quatro rastreamentos que foram coletados para GET Customers/Details na duração do intervalo e da janela de tempo de interesse.

Às vezes o problema não estará em seu código, mas em vez disso, em uma dependência chamada por ele. Você pode mudar para a guia Dependências na exibição de triagem de desempenho para investigar essas dependências lentas. Observe que, por padrão, a exibição de desempenho é composta de médias de tendências, mas o que realmente lhe interessa é examinar o 95º percentil (ou o 99º, se você estiver monitorando um serviço extremamente maduro). No exemplo a seguir, nos concentramos na dependência de Blob do Azure lenta, em que chamamos PUT fabrikamaccount. As boas experiências estão agrupadas em torno de 40ms, enquanto as chamadas lentas para a mesma dependência são três vezes mais lentas, agrupando-se em aproximadamente 120ms. Não é necessário que muitas dessas chamadas se acumulem para fazer com que a respectiva operação fique perceptivelmente mais lenta. Você pode analisar as amostras e rastreamentos do criador de perfil representativos, assim como você pode fazer com a guia Operações.

![Três picos de duração de GET Customers/Details num intervalo de 7 dias com uma janela de tempo](./media/app-insights-web-monitor-performance/SlowDependencies95thTrend.png)

Outro recurso muito poderoso que é novo na investigação de desempenho de tela cheia interativa é a integração com insights. O Application Insights pode detectar e expor regressões de capacidade de resposta de insights, bem como ajuda você a identificar propriedades comuns do conjunto de amostras no qual você decidiu se concentrar. É a melhor maneira de examinar todas as informações disponíveis é mudar para um intervalo de tempo de 30 dias e, em seguida, selecionar Geral para ver insights de todas as operações do mês anterior.

![Três picos de duração de GET Customers/Details num intervalo de 7 dias com uma janela de tempo](./media/app-insights-web-monitor-performance/Performance30DayOveralllnsights.png)

O Application Insights, na nova exibição de triagem de desempenho, pode literalmente lhe ajudar a localizar as agulhas no palheiro que resultam em experiências ruins para os usuários de aplicativo Web.

## <a name="deprecated-find-and-fix-performance-bottlenecks-with-a-narrow-bladed-legacy-performance-investigation"></a>Preterido: localizar e corrigir afunilamentos de desempenho com uma investigação de desempenho interativo herdada e com folhas restritas

Use a investigação de desempenho herdada com folhas do Application Insights para localizar áreas de seu aplicativo Web que estão causando lentidão no desempenho geral. Você pode encontrar páginas específicas que estão causando lentidão e, em seguida, usar o [Criador de Perfil](app-insights-profiler.md) para rastrear a causa raiz desses problemas em código. 

### <a name="create-a-list-of-slow-performing-pages"></a>Criar uma lista de páginas de desempenho lento 

A primeira etapa para encontrar problemas de desempenho é obter uma lista das páginas de resposta lenta. A captura de tela abaixo demonstra como usar a folha Desempenho para obter uma lista de possíveis páginas para investigação adicional. Você pode ver rapidamente nesta página que houve uma lentidão no tempo de resposta do aplicativo por volta das 18h e novamente por volta das 22h. Você também pode ver que a operação GET de cliente/detalhes teve algumas operações de execução longa, com um tempo de resposta mediano de 507,05 milissegundos. 

![Desempenho interativo do Application Insights](./media/app-insights-web-monitor-performance/performance1.png)

### <a name="drill-down-on-specific-pages"></a>Fazer uma busca detalhada em páginas específicas

Depois de obter um instantâneo do desempenho do aplicativo, você pode obter mais detalhes sobre operações específicas de desempenho lento. Clique em uma operação da lista para ver os detalhes, conforme mostrado abaixo. No gráfico, você pode ver se o desempenho se baseava em uma dependência. Veja também quantos usuários observaram os vários tempos de resposta. 

![Folha Operações do Application Insights](./media/app-insights-web-monitor-performance/performance5.png)

### <a name="drill-down-on-a-specific-time-period"></a>Fazer uma busca detalhada em um período específico

Depois de identificar um ponto no tempo para investigar, faça uma busca detalhada para examinar as operações específicas que podem ter causado a lentidão do desempenho. Ao clicar em um ponto no tempo específico, você obtém os detalhes da página, conforme mostrado abaixo. No exemplo abaixo, você pode ver as operações listadas para determinado período, junto com os códigos de resposta do servidor e a duração da operação. Você também tem a URL para abrir um item de trabalho do TFS, caso precise enviar essas informações para sua equipe de desenvolvimento.

![Fração de tempo do Application Insights](./media/app-insights-web-monitor-performance/performance2.png)

### <a name="drill-down-on-a-specific-operation"></a>Fazer uma busca detalhada em uma operação específica

Depois de identificar um ponto no tempo para investigar, faça uma busca detalhada para examinar as operações específicas que podem ter causado a lentidão do desempenho. Clique em uma operação da lista para ver os detalhes da operação, conforme mostrado abaixo. Neste exemplo, você pode ver que a operação falhou e que o Application Insights forneceu os detalhes da exceção gerada pelo aplicativo. Novamente, é possível criar um item de trabalho do TFS com facilidade nesta folha.

![Folha Operação do Application Insights](./media/app-insights-web-monitor-performance/performance3.png)


## <a name="next"></a>Próximas etapas
[Testes da Web][availability] – faça com que solicitações da Web sejam enviadas ao seu aplicativo em intervalos regulares de todo o mundo.

[Capturar e pesquisar rastreamento de diagnóstico][diagnostic] – Inserir chamadas de rastreamento e separar através dos resultados para problemas de pinpoint.

[Acompanhamento de uso][usage] – Saiba como as pessoas usam seu aplicativo.

[Solução de problemas][qna] – e P e R



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md
[livestream]: app-insights-live-stream.md
[snapshot]: app-insights-snapshot-debugger.md



