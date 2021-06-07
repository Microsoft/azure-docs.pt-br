---
title: O que é o Application Insights do Azure? | Microsoft Docs
description: Gerenciamento de desempenho de aplicativo e acompanhamento de uso do seu aplicativo web online.  Detectar, realizar triagem e diagnosticar problemas, entender como as pessoas usam o seu aplicativo.
ms.topic: overview
ms.date: 06/03/2019
ms.custom: mvc
ms.openlocfilehash: f25a53cb227d6894aaabaf82033178f15f61c173
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076200"
---
# <a name="what-is-application-insights"></a>O que é o Application Insights?
O Application Insights, um recurso do [Azure Monitor](../overview.md), é um serviço de APM (Gerenciamento de Desempenho de Aplicativo) para desenvolvedores e profissionais de DevOps. Use-o para monitorar seus aplicativos ativos. Ele detecta automaticamente anomalias de desempenho e inclui ferramentas de análise avançadas para ajudar a diagnosticar problemas e entender o que os usuários realmente fazem com seu aplicativo.  Ele foi projetado para ajudar você a aprimorar continuamente o desempenho e a usabilidade do seu aplicativo. Ele funciona com aplicativos em uma ampla variedade de plataformas, incluindo .NET, Node.js, Java e Python, hospedadas localmente, de maneira híbrida ou em qualquer nuvem pública. Ele integra-se ao seu processo DevOps e tem pontos de conexão para uma ampla variedade de ferramentas de desenvolvimento. Ele pode monitorar e analisar a telemetria de aplicativos móveis integrando-se ao Visual Studio App Center.

## <a name="how-does-application-insights-work"></a>Como funciona o Application Insights?
Instale um pacote de instrumentação pequeno (SDK) no seu aplicativo ou habilite o Application Insights usando o Agente do Application Insights quando essa opção for [compatível](./platforms.md). A instrumentação monitora seu aplicativo e direciona os dados de telemetria para um recurso do Azure Application Insights usando um GUID exclusivo ao qual nos referimos como uma chave de instrumentação.

Você pode instrumentar não apenas o aplicativo de serviço web, mas também todos os componentes em segundo plano e o JavaScript nas próprias páginas da web. O aplicativo e os componentes dele podem ser executados em qualquer lugar, ele não precisa ser hospedado no Azure.

![A instrumentação do Application Insights no seu aplicativo envia telemetria ao recurso do Application Insights.](./media/app-insights-overview/diagram.png)

Além disso, você pode efetuar pull de telemetria dos ambientes de host, como contadores de desempenho, diagnóstico do Azure, ou logs de Docker. Você também pode configurar testes da web que enviam periodicamente solicitações sintéticas para o serviço web.

Todos esses fluxos de telemetria são integrados ao Azure Monitor. No portal do Azure, é possível aplicar ferramentas de pesquisa e análise poderosas aos dados brutos.

### <a name="whats-the-overhead"></a>Qual é a sobrecarga?
O impacto sobre o desempenho do aplicativo é pequeno. As chamadas de acompanhamento não são bloqueadas, além de serem colocadas em lote e enviadas em um thread separado.

## <a name="what-does-application-insights-monitor"></a>O que o Application Insights monitora?

O Application Insights é indicado para a equipe de desenvolvimento, para ajudá-lo a compreender como está o desempenho de seu aplicativo e como ele está sendo usado. Ele monitora:

* **Taxas de solicitação, tempos de resposta e taxas de falha** - descubra quais páginas estão mais populares, em que momentos do dia, e onde os usuários estão. Confira as páginas que têm melhor desempenho. Se as taxas de falha e os tempos de resposta ficam altos quando há mais solicitações, possivelmente você tem um problema de alocação de recursos. 
* **Taxas de dependência, tempos de resposta e taxas de falha** - descubra se os serviços externos estão atrasando você.
* **Exceções** – analise as estatísticas agregadas ou escolha instâncias específicas e faça uma busca detalhada no rastreamento de pilha e nas solicitações relacionadas. A maioria das exceções de navegador e servidor são relatadas.
* **Exibições de página e o desempenho de carregamento** - relatados por navegadores dos usuários.
* **Chamadas AJAX** de páginas da web - taxas, tempos de resposta e taxas de falha.
* **Contagens de seção e usuários**.
* **Contadores de desempenho** de suas máquinas de servidor Linux ou Windows server, como CPU, memória e uso da rede. 
* **Diagnósticos de host** do Docker ou do Azure. 
* **Logs de rastreamento de diagnóstico** do seu aplicativo - para que você possa correlacionar eventos de rastreamento com solicitações.
* **Métricas e eventos personalizados** que você escreve em código de cliente ou servidor, para acompanhar os eventos de negócios, como itens vendidos ou vitórias.

## <a name="where-do-i-see-my-telemetry"></a>Onde posso encontrar minha telemetria?

Há várias maneiras de explorar seus dados. Confira estes artigos:

| Descrição do artigo   | Imagem |
| --- | --- |
| [**Detecção inteligente e alertas manuais**](./proactive-diagnostics.md)<br/>Defina alertas automáticos que se adaptem aos padrões normais de telemetria do seu aplicativo e são disparados quando há algo fora do padrão normal. Você também pode [definir alertas](../alerts/alerts-log.md) em níveis específicos de métricas padrão ou personalizadas. |![Exemplo de alerta](./media/app-insights-overview/alerts-tn.png) |
| [**Mapa do aplicativo**](./app-map.md)<br/>Explore os componentes de seu aplicativo, com as principais métricas e alertas. |![Mapa do aplicativo](./media/app-insights-overview/appmap-tn.png)  |
| [**Criador de perfil**](./profiler.md)<br/>Inspecione os perfis de execução de solicitações de amostras. |![A captura de tela mostra perfis de execução de solicitações de amostras.](./media/app-insights-overview/profiler.png) |
| [**Análise de uso**](./usage-overview.md)<br/>Analise a retenção e a segmentação de usuários.|![Ferramenta de retenção](./media/app-insights-overview/retention.png) |
| [**Pesquisa de diagnóstico para dados da instância**](./diagnostic-search.md)<br/>pesquise e filtre eventos como solicitações, exceções, chamadas de dependência, rastreamentos de log e exibições de página.  |![Como pesquisar telemetria](./media/app-insights-overview/search-tn.png) |
| [**Metrics Explorer para os dados agregados**](../essentials/metrics-charts.md)<br/>explore, filtre e segmente dados agregados, como taxas de solicitações, falhas e exceções; tempos de resposta e tempos de carregamento de página. |![Métricas](./media/app-insights-overview/metrics-tn.png) |
| [**Painéis**](./overview-dashboard.md)<br/>faça um mashup de dados de vários recursos e compartilhe com outras pessoas. Excelente para aplicativos com vários componentes e para exibição contínua no ambiente de equipe. |![Exemplos de painéis](./media/app-insights-overview/dashboard-tn.png) |
| [**Live Metrics Stream**](./live-stream.md)<br/>quando implantar um novo build, acompanhe esses indicadores de desempenho quase em tempo real para verificar se tudo está funcionando conforme esperado. |![Exemplo de métricas ao vivo](./media/app-insights-overview/live-metrics-tn.png) |
| [**Analytics**](../logs/log-query-overview.md)<br/>responda perguntas difíceis sobre o desempenho e o uso do seu aplicativo usando essa poderosa linguagem de consulta. |![Exemplo de análise](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](./visual-studio.md)<br/>Veja dados de desempenho no código. Vá até o código dos rastreamentos de pilha.|![A captura de tela mostra Detalhes da Exceção no Visual Studio e um exemplo de como fazer a codificação de rastreamentos de pilha.](./media/app-insights-overview/visual-studio-tn.png) |
| [**Depurador de instantâneo**](./snapshot-debugger.md)<br/>Depure instantâneos tirado como exemplo de operações ao vivo, com valores de parâmetro.|![Visual Studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](./export-power-bi.md)<br/>Integre as métricas de uso com outro business intelligence.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**REST API**](https://dev.applicationinsights.io/)<br/>Escreva o código para executar consultas em suas métricas e dados brutos.| ![API REST](./media/app-insights-overview/rest-tn.png) |
| [**Exportação contínua**](./export-telemetry.md)<br/>Exportação em massa de dados brutos para armazenamento assim que eles chegam. |![Exportação](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Como usar o Application Insights?

### <a name="monitor"></a>Monitoramento
Instale o Application Insights no seu aplicativo, configure os [testes de disponibilidade da web](./monitor-web-app-availability.md) e:

* Confira o [painel do aplicativo](./overview-dashboard.md) padrão da sala da equipe para acompanhar a carga, a capacidade de resposta e o desempenho das dependências, dos carregamentos de página e das chamadas AJAX.
* Descubra quais são as solicitações mais lentas e com mais falhas.
* Assista ao [Live Stream](./live-stream.md) quando implantar uma nova versão, para identificar imediatamente uma degradação.

### <a name="detect-diagnose"></a>Detectar, diagnosticar
Quando você recebe um alerta ou descobre um problema:

* Avalie quantos usuários são afetados.
* Correlacione falhas a exceções, chamadas de dependência e rastreamentos.
* Examine o criador de perfil, instantâneos, despejos de pilha e logs de rastreamento.

### <a name="build-measure-learn"></a>Compilar, medir, aprender
[Meça a eficácia](./usage-overview.md) de cada novo recurso que você implanta.

* Planeje medir como os clientes usam a nova experiência do usuário ou as funcionalidades de negócios.
* Escreva a telemetria personalizada em seu código.
* Baseie o próximo ciclo de desenvolvimento em evidências da telemetria.

## <a name="get-started"></a>Introdução
Application Insights é um dos muitos serviços hospedados no Microsoft Azure e a telemetria é enviada para ele para análise e apresentação. Portanto, antes de fazer qualquer outra coisa, você precisará de uma assinatura do [Microsoft Azure](https://azure.com). A inscrição é gratuita, e se você optar pelo [plano de preços](https://azure.microsoft.com/pricing/details/application-insights/) básico do Application Insights, não haverá cobrança até que seu aplicativo apresente uso substancial. Se sua organização já tiver uma assinatura, sua conta da Microsoft poderá ser adicionada a ela.

Existem várias maneiras de começar. Comece com o que funciona melhor para você. Você pode adicionar outras posteriormente.

* **Em tempo de execução: instrumente seu aplicativo Web no servidor.** Ideal para aplicativos já implantados. Evita qualquer atualização no código.
  * [**Aplicativos ASP.NET ou ASP.NET Core hospedados nos Aplicativos Web do Azure**](./azure-web-apps.md)
  * [**Aplicativos ASP.NET hospedados no IIS na VM do Azure ou no conjunto de dimensionamento de máquinas virtuais do Azure**](./azure-vm-vmss-apps.md)
  * [**Aplicativos ASP.NET hospedados na VM local do IIS**](./monitor-performance-live-website-now.md)
* **Em tempo de desenvolvimento: adicione o Application Insights ao seu código.** Permite personalizar a coleta de telemetria e enviar telemetria adicional.
  * [Aplicativos ASP.NET](./asp-net.md)
  * [Aplicativo ASP.NET Core](./asp-net-core.md)
  * [Aplicativos de Console .NET](./console.md)
  * [Java](./java-get-started.md)
  * [Node.js](./nodejs.md)
  * [Python](./opencensus-python.md)
  * [Outras plataformas](./platforms.md)
* **[Instrumentar suas páginas da Web](./javascript.md)** para a exibição de página, o AJAX e outros tipos de telemetria do lado do cliente.
* **[Analisar o uso do aplicativo móvel](../app/mobile-center-quickstart.md)** pela integração com o Visual Studio App Center.
* **[Testes de disponibilidade](./monitor-web-app-availability.md)** - execute o ping de seu site regularmente de nossos servidores.

## <a name="next-steps"></a>Próximas etapas
Introdução ao runtime com:

* [Aplicativos hospedados no IIS na VM do Azure e no conjunto de dimensionamento de máquinas virtuais do Azure](./azure-vm-vmss-apps.md)
* [Servidor IIS](./monitor-performance-live-website-now.md)
* [Aplicativos Web do Azure](./azure-web-apps.md)

Introdução ao tempo de desenvolvimento com:

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)
* [JavaScript](./javascript.md)


## <a name="support-and-feedback"></a>Suporte e comentários
* Perguntas e problemas:
  * [Solução de problemas][qna]
  * [Página de P e R da Microsoft](/answers/topics/azure-monitor.html)
  * [StackOverflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* Suas sugestões:
  * [UserVoice](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* Blog:
  * [Blog do Application Insights](https://azure.microsoft.com/blog/tag/application-insights)

<!--Link references-->

[android]: ../app/mobile-center-quickstart.md
[azure]: ../../insights-perf-analytics.md
[client]: ./javascript.md
[desktop]: ./windows-desktop.md
[greenbrown]: ./asp-net.md
[ios]: ../app/mobile-center-quickstart.md
[java]: ./java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: ./platforms.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md

