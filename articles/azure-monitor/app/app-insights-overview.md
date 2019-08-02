---
title: O que é o Application Insights do Azure? | Microsoft Docs
description: Gerenciamento de desempenho de aplicativo e acompanhamento de uso do seu aplicativo web online.  Detectar, realizar triagem e diagnosticar problemas, entender como as pessoas usam o seu aplicativo.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 379721d1-0f82-445a-b416-45b94cb969ec
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: overview
ms.custom: mvc
ms.date: 06/03/2019
ms.author: mbullwin
ms.openlocfilehash: 5e42c36e6a321775e6017a2318857e20b7dcaf0d
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876456"
---
# <a name="what-is-application-insights"></a>O que é o Application Insights?
O Application Insights é um serviço de gerenciamento de desempenho de aplicativo (APM) extensível para desenvolvedores da Web em várias plataformas. Use-o para monitorar seu aplicativo Web online. Ele detectará anomalias de desempenho automaticamente. Ele inclui ferramentas de análise avançadas para ajudar você a diagnosticar problemas e entender o que os usuários realmente fazem com seu aplicativo.  Ele foi projetado para ajudar você a aprimorar continuamente o desempenho e a usabilidade do seu aplicativo. Ele funciona com aplicativos em uma ampla variedade de plataformas, incluindo .NET, Node.js e Java EE, hospedadas localmente, de maneira híbrida ou em qualquer nuvem pública. Ele integra-se ao seu processo DevOps e tem pontos de conexão para uma ampla variedade de ferramentas de desenvolvimento. Ele pode monitorar e analisar a telemetria de aplicativos móveis integrando-se ao Visual Studio App Center.

## <a name="how-does-application-insights-work"></a>Como funciona o Application Insights?
Você instala um pacote de instrumentação pequeno em seu aplicativo e configura um recurso Application Insights no portal do Microsoft Azure. A instrumentação monitora seu aplicativo e envia os dados de telemetria ao portal. (O aplicativo pode ser executado em qualquer lugar — ele não precisa ser hospedado no Azure.)

Você pode instrumentar não apenas o aplicativo de serviço web, mas também todos os componentes em segundo plano e o JavaScript nas próprias páginas da web. 

![A instrumentação do Application Insights no seu aplicativo envia telemetria ao recurso do Application Insights.](./media/app-insights-overview/01-scheme.png)

Além disso, você pode efetuar pull de telemetria dos ambientes de host, como contadores de desempenho, diagnóstico do Azure, ou logs de Docker. Você também pode configurar testes da web que enviam periodicamente solicitações sintéticas para o serviço web.

Todos esses fluxos de telemetria estão integrados no portal do Azure, onde é possível aplicar ferramentas de pesquisa e analíticas poderosas para os dados brutos.


### <a name="whats-the-overhead"></a>Qual é a sobrecarga?
O impacto sobre o desempenho do aplicativo é muito pequeno. As chamadas de acompanhamento não são bloqueadas, além de serem colocadas em lote e enviadas em um thread separado.

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

|  |  |
| --- | --- |
| [**Detecção inteligente e alertas manuais**](../../azure-monitor/app/proactive-diagnostics.md)<br/>alertas automáticos se adaptam aos padrões normais de telemetria do seu aplicativo e são disparados quando há algo fora do padrão normal. Você também pode [definir alertas](../../azure-monitor/app/alerts.md) em níveis específicos de métricas padrão ou personalizadas. |![Exemplo de alerta](./media/app-insights-overview/alerts-tn.png) |
| [**Mapa do aplicativo**](../../azure-monitor/app/app-map.md)<br/>Os componentes de seu aplicativo, com as principais métricas e alertas. |![Mapa do aplicativo](./media/app-insights-overview/appmap-tn.png)  |
| [**Criador de perfil**](../../azure-monitor/app/profiler.md)<br/>Inspecione os perfis de execução de solicitações de amostras. |![Criador de perfil](./media/app-insights-overview/profiler.png) |
| [**Análise de uso**](../../azure-monitor/app/usage-overview.md)<br/>Analise a retenção e a segmentação de usuários.|![Ferramenta de retenção](./media/app-insights-overview/retention.png) |
| [**Pesquisa de diagnóstico para dados da instância**](../../azure-monitor/app/diagnostic-search.md)<br/>pesquise e filtre eventos como solicitações, exceções, chamadas de dependência, rastreamentos de log e exibições de página.  |![Como pesquisar telemetria](./media/app-insights-overview/search-tn.png) |
| [**Metrics Explorer para os dados agregados**](../../azure-monitor/app/metrics-explorer.md)<br/>explore, filtre e segmente dados agregados, como taxas de solicitações, falhas e exceções; tempos de resposta e tempos de carregamento de página. |![Métricas](./media/app-insights-overview/metrics-tn.png) |
| [**Painéis**](../../azure-monitor/app/overview-dashboard.md)<br/>faça um mashup de dados de vários recursos e compartilhe com outras pessoas. Excelente para aplicativos com vários componentes e para exibição contínua no ambiente de equipe. |![Exemplos de painéis](./media/app-insights-overview/dashboard-tn.png) |
| [**Live Metrics Stream**](../../azure-monitor/app/live-stream.md)<br/>quando implantar um novo build, acompanhe esses indicadores de desempenho quase em tempo real para verificar se tudo está funcionando conforme esperado. |![Exemplo de métricas ao vivo](./media/app-insights-overview/live-metrics-tn.png) |
| [**Analytics**](../../azure-monitor/app/analytics.md)<br/>responda perguntas difíceis sobre o desempenho e o uso do seu aplicativo usando essa poderosa linguagem de consulta. |![Exemplo de análise](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](../../azure-monitor/app/visual-studio.md)<br/>Veja dados de desempenho no código. Vá até o código dos rastreamentos de pilha.|![Visual Studio](./media/app-insights-overview/visual-studio-tn.png) |
| [**Depurador de instantâneo**](../../azure-monitor/app/snapshot-debugger.md)<br/>Depure instantâneos tirado como exemplo de operações ao vivo, com valores de parâmetro.|![Visual Studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](../../azure-monitor/app/export-power-bi.md )<br/>Integre as métricas de uso com outro business intelligence.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**REST API**](https://dev.applicationinsights.io/)<br/>Escreva o código para executar consultas em suas métricas e dados brutos.| ![API REST](./media/app-insights-overview/rest-tn.png) |
| [**Exportação contínua**](../../azure-monitor/app/export-telemetry.md)<br/>Exportação em massa de dados brutos para armazenamento assim que eles chegam. |![Exportação](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Como usar o Application Insights?

### <a name="monitor"></a>Monitoramento
Instale o Application Insights no seu aplicativo, configure os [testes de disponibilidade da web](../../azure-monitor/app/monitor-web-app-availability.md) e:

* Faça o check-out do [painel do aplicativo](../../azure-monitor/app/overview-dashboard.md) padrão para sua sala da equipe para ficar atento à carga, à capacidade de resposta e ao desempenho de suas dependências, aos carregamentos de página e às chamadas AJAX.
* Descubra quais são as solicitações mais lentas e com mais falhas.
* Assista ao [Live Stream](../../azure-monitor/app/live-stream.md) quando implantar uma nova versão, para identificar imediatamente uma degradação.

### <a name="detect-diagnose"></a>Detectar, diagnosticar
Quando você recebe um alerta ou descobre um problema:

* Avalie quantos usuários são afetados.
* Correlacione falhas a exceções, chamadas de dependência e rastreamentos.
* Examine o criador de perfil, instantâneos, despejos de pilha e logs de rastreamento.

### <a name="build-measure-learn"></a>Compilar, medir, aprender
[Meça a eficácia](../../azure-monitor/app/usage-overview.md) de cada novo recurso que você implanta.

* Planeje medir como os clientes usam a nova experiência do usuário ou as funcionalidades de negócios.
* Escreva a telemetria personalizada em seu código.
* Baseie o próximo ciclo de desenvolvimento em evidências da telemetria.

## <a name="get-started"></a>Introdução
Application Insights é um dos muitos serviços hospedados no Microsoft Azure e a telemetria é enviada para ele para análise e apresentação. Portanto, antes de fazer qualquer outra coisa, você precisará de uma assinatura do [Microsoft Azure](https://azure.com). A inscrição é gratuita, e se você optar pelo [plano de preços](https://azure.microsoft.com/pricing/details/application-insights/) básico do Application Insights, não haverá cobrança até que seu aplicativo apresente uso substancial. Se sua organização já tiver uma assinatura, sua conta da Microsoft poderá ser adicionada a ela.

Existem várias maneiras de começar. Comece com o que funciona melhor para você. Você pode adicionar outras posteriormente.

* **Em tempo de execução: instrumente seu aplicativo Web no servidor.** Ideal para aplicativos já implantados. Evita qualquer atualização no código.
  * [**Aplicativos ASP.NET ou ASP.NET Core hospedados nos Aplicativos Web do Azure**](../../azure-monitor/app/azure-web-apps.md)
  * [**Aplicativos ASP.NET hospedados no IIS na VM do Azure ou no conjunto de dimensionamento de máquinas virtuais do Azure**](../../azure-monitor/app/azure-vm-vmss-apps.md)
  * [**Aplicativos ASP.NET hospedados na VM local do IIS**](../../azure-monitor/app/monitor-performance-live-website-now.md)
* **Em tempo de desenvolvimento: adicione o Application Insights ao seu código.** Permite personalizar a coleta de telemetria e enviar telemetria adicional.
  * [Aplicativos ASP.NET](../../azure-monitor/app/asp-net.md)
  * [Aplicativo ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
  * [Aplicativos de Console .NET](../../azure-monitor/app/console.md)
  * [Java](../../azure-monitor/app/java-get-started.md)
  * [Node.js](../../azure-monitor/app/nodejs.md)
  * [Outras plataformas](../../azure-monitor/app/platforms.md)
* **[Instrumentar suas páginas da Web](../../azure-monitor/app/javascript.md)** para a exibição de página, o AJAX e outros tipos de telemetria do lado do cliente.
* **[Analisar o uso do aplicativo móvel](../../azure-monitor/learn/mobile-center-quickstart.md)** pela integração com o Visual Studio App Center.
* **[Testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)** - execute o ping de seu site regularmente de nossos servidores.

## <a name="next-steps"></a>Próximas etapas
Introdução ao tempo de execução com:

* [Aplicativos hospedados no IIS na VM do Azure e no conjunto de dimensionamento de máquinas virtuais do Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Servidor IIS](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Aplicativos Web do Azure](../../azure-monitor/app/azure-web-apps.md)

Introdução ao tempo de desenvolvimento com:

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)

## <a name="support-and-feedback"></a>Suporte e comentários
* Perguntas e problemas:
  * [Solução de problemas][qna]
  * [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
  * [StackOverflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* Suas sugestões:
  * [UserVoice](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* Blog:
  * [Blog do Application Insights](https://azure.microsoft.com/blog/tag/application-insights)

## <a name="videos"></a>vídeos

- Vídeo externo: [Configuração do Application Insights com um aplicativo ASP.NET](https://www.youtube.com/watch?v=blnGAVgMAfA).
- Vídeo externo: [Configuração do Application Insights com um o ASP.NET Core e Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t).
- Vídeo externo: [Configuração do Application Insights com um o ASP.NET Core e Visual Studio Code](https://youtu.be/ygGt84GDync).

<!--Link references-->

[android]: ../../azure-monitor/learn/mobile-center-quickstart.md
[azure]: ../../insights-perf-analytics.md
[client]: ../../azure-monitor/app/javascript.md
[desktop]: ../../azure-monitor/app/windows-desktop.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[ios]: ../../azure-monitor/learn/mobile-center-quickstart.md
[java]: ../../azure-monitor/app/java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: ../../azure-monitor/app/platforms.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
