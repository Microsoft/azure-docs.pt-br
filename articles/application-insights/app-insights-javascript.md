---
title: Application Insights do Azure para aplicativos Web JavaScript | Microsoft Docs
description: "Obter contagens de sessões e exibições de páginas e dados de clientes da Web e acompanhar padrões de uso. Detecte exceções e problemas de desempenho em páginas da Web do JavaScript."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 3b710d09-6ab4-4004-b26a-4fa840039500
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: 4e8a77e3644bb726d1b8e2050dab61893ccfa3c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="application-insights-for-web-pages"></a>Application Insights para páginas da Web
Saiba mais sobre o desempenho e o uso de sua página da Web ou aplicativo. Se adicionar o [Application Insights](app-insights-overview.md) ao script de página, você obterá intervalos de carregamentos de página e chamadas AJAX, contagens e detalhes de exceções de navegador e falhas de AJAX, bem como contagens de usuários e sessões. Todos esses itens podem ser segmentados por página, sistema operacional cliente e versão do navegador, localização geográfica e outras dimensões. Você pode definir alertas para contagens de falhas ou carregamento de páginas lento. E inserindo chamadas de rastreamento em seu código JavaScript, você pode controlar como os diferentes recursos do seu aplicativo de página da Web são usados.

O Application Insights pode ser usado com todas as páginas da Web: basta adicionar um breve trecho de JavaScript. Se o serviço Web for [Java](app-insights-java-get-started.md) ou [ASP.NET](app-insights-asp-net.md), você poderá integrar a telemetria de seu servidor e clientes.

![Em portal.azure.com, abra o recurso do aplicativo e clique em Navegador](./media/app-insights-javascript/03.png)

Você precisa de uma assinatura do [Microsoft Azure](https://azure.com). Se sua equipe tiver uma assinatura organizacional, peça ao proprietário que adicione sua Conta da Microsoft a ela. O desenvolvimento e o uso em pequena escala não custam nada.

## <a name="set-up-application-insights-for-your-web-page"></a>Configurar o Application Insights para sua página da Web
Adicione o trecho de código do carregador para suas páginas da Web, conforme demonstrado a seguir.

### <a name="open-or-create-application-insights-resource"></a>Abrir ou criar um recurso do Application Insights
O recurso Application Insights é onde os dados sobre o desempenho e o uso da página são exibidos. 

Entre no [portal do Azure](https://portal.azure.com).

Se tiver configurado o monitoramento do lado do servidor de seu aplicativo, você já terá um recurso:

![Escolha Procurar, Serviços de Desenvolvedor, Application Insights.](./media/app-insights-javascript/01-find.png)

Se não tiver um, crie-o:

![Escolha Novo, Serviços de Desenvolvedor, Application Insights.](./media/app-insights-javascript/01-create.png)

*Tem dúvidas?* [Mais informações sobre a criação de um recurso](app-insights-create-new-resource.md).

### <a name="add-the-sdk-script-to-your-app-or-web-pages"></a>Adicione o script do SDK a seu aplicativo ou às suas páginas da Web
Em Início Rápido, obtenha o script para páginas da Web:

![Na folha de visão geral de seu aplicativo, escolha Início Rápido, Obter o código para monitorar minhas páginas da Web. Copie o script.](./media/app-insights-javascript/02-monitor-web-page.png)

Insira o script antes da marca `</head>` de cada página que você deseja acompanhar. Se seu site possui uma página mestra, você poderá colocar o script lá. Por exemplo:

* Em um projeto MVC ASP.NET, você deve colocá-lo em `View\Shared\_Layout.cshtml`
* Em um site do SharePoint, no painel de controle, abra [Configurações do Site/Página Mestra](app-insights-sharepoint.md).

O script contém a chave de instrumentação que direciona os dados para o recurso do Application Insights. 

([Explicação mais detalhada do script](http://apmtips.com/blog/2015/03/18/javascript-snippet-explained/)).

*(Se você estiver usando uma estrutura de página da Web conhecida, procure adaptadores do Application Insights. Por exemplo, há [um módulo AngularJS](http://ngmodules.org/modules/angular-appinsights).)*

## <a name="detailed-configuration"></a>Configuração detalhada
Há vários [parâmetros](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) que você pode definir, embora, na maioria dos casos, isso não seja preciso. Por exemplo, você pode desabilitar ou limitar o número de chamadas Ajax relatadas por exibição de página (para reduzir o tráfego). Ou então, você pode definir o modo de depuração para que a telemetria se mova rapidamente pelo pipeline sem sendo colocada em lotes.

Para definir esses parâmetros, procure esta linha no trecho de código e adicione mais itens separados por vírgula depois dela:

    })({
      instrumentationKey: "..."
      // Insert here
    });

Os [parâmetros disponíveis](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) incluem:

    // Send telemetry immediately without batching.
    // Remember to remove this when no longer required, as it
    // can affect browser performance.
    enableDebug: boolean,

    // Don't log browser exceptions.
    disableExceptionTracking: boolean,

    // Don't log ajax calls.
    disableAjaxTracking: boolean,

    // Limit number of Ajax calls logged, to reduce traffic.
    maxAjaxCallsPerView: 10, // default is 500

    // Time page load up to execution of first trackPageView().
    overridePageViewDuration: boolean,

    // Set these dynamically for an authenticated user.
    appUserId: string,
    accountId: string,



## <a name="run"></a>Executar seu aplicativo
Execute seu aplicativo Web, use-o por algum tempo para gerar telemetria e aguarde alguns segundos. Você pode executá-lo usando a tecla **F5** em seu computador de desenvolvimento ou publicá-lo e permitir que os usuários o utilizem.

Se desejar verificar a telemetria que um aplicativo Web está enviando ao Application Insights, use as ferramentas de depuração de seu navegador (**F12** em muitos navegadores). Os dados são enviados a dc.services.visualstudio.com.

## <a name="explore-your-browser-performance-data"></a>Explorar seus dados de desempenho do navegador
Abra a folha Navegador para exibir dados de desempenho agregados dos navegadores dos usuários.

![Em portal.azure.com, abra o recurso do aplicativo e clique em Configurações, Navegador](./media/app-insights-javascript/03.png)

*Não há dados ainda? Clique em **Atualizar** na parte superior da página. Nada mesmo assim? Consulte [Solução de problemas](app-insights-troubleshoot-faq.md).*

A folha Navegadores é uma [folha do Metrics Explorer](app-insights-metrics-explorer.md) com filtros predefinidos e opções de gráfico. Você poderá editar o intervalo de tempo, os filtros e a configuração do gráfico, se desejar, e salvar o resultado como um favorito. Clique em **Restaurar padrões** para voltar para a configuração original da folha.

## <a name="page-load-performance"></a>Desempenho de carregamento de página
Na parte superior, há um gráfico segmentado de tempos de carregamento de página. A altura total do gráfico representa o tempo médio para carregar e exibir páginas de seu aplicativo nos navegadores dos usuários. O tempo é medido desde quando o navegador envia a solicitação HTTP inicial até que os todos os eventos de carregamento síncronos tenham sido processados, incluindo layout e execução de scripts. Ele não inclui tarefas assíncronas, como carregar Web parts de chamadas AJAX.

O gráfico segmenta o tempo de carregamento total da página nos [intervalos padrão definidos pelo W3C](http://www.w3.org/TR/navigation-timing/#processing-model). 

![](./media/app-insights-javascript/08-client-split.png)

Observe que o tempo de *conexão de rede* é frequentemente menor do que o esperado, pois ele é uma média sobre todas as solicitações do navegador para o servidor. Muitas solicitações individuais têm um tempo de conexão igual a 0 porque já existe uma conexão ativa com o servidor.

### <a name="slow-loading"></a>Carregamento lento?
O carregamento de página lento é uma grande fonte de insatisfação para seus usuários. Se o gráfico indicar o carregamento de página lento, será fácil fazer algumas pesquisas de diagnóstico.

O gráfico mostra a média de todos os carregamentos de página em seu aplicativo. Para ver se o problema é restrito a páginas específicas, observe mais adiante na folha, onde há uma grade segmentada por URL de página:

![](./media/app-insights-javascript/09-page-perf.png)

Observe a contagem de exibições de páginas e o desvio padrão. Se a contagem de páginas for muito baixa, o problema não está afetando muito os usuários. Um desvio padrão alto (comparável à média em si) indica muita variação entre medidas individuais.

**Aplicar zoom em uma URL e uma exibição de página.** Clique em qualquer nome de página para ver uma folha de gráficos de navegador filtrados apenas para essa URL e, em seguida, em uma instância de uma exibição de página.

![](./media/app-insights-javascript/35.png)

Clique em `...` para obter uma lista completa das propriedades do evento ou inspecionar as chamadas Ajax e os eventos relacionados. Chamadas Ajax lentas afetam o tempo de carregamento de página geral se são síncronas. Eventos relacionados incluem solicitações do servidor para a mesma URL (se você configurou o Application Insights em seu servidor Web).

**Desempenho da página ao longo do tempo.** De volta à folha Navegadores, altere a grade de Tempo de Carregamento de Exibição de Página para um gráfico de linhas para ver se houve picos em determinados horários:

![Clique no cabeçalho da grade e selecione um novo tipo de gráfico](./media/app-insights-javascript/10-page-perf-area.png)

**Segmentar por outras dimensões.** Será que as páginas têm carregamento mais lento em determinado navegador, sistema operacional cliente ou localidade do usuário? Adicione um novo gráfico e experimente a dimensão **Agrupar por** .

![](./media/app-insights-javascript/21.png)

## <a name="ajax-performance"></a>Desempenho do AJAX
Verifique se chamadas AJAX em páginas da Web estão tendo um bom desempenho. Elas geralmente são usadas para preencher partes da página de forma assíncrona. Embora a página geral possa ser carregada imediatamente, seus usuários podem ficar frustrados ao observar Web parts em branco, aguardando até que os dados apareçam nelas.

As chamadas AJAX feitas de sua página da Web são mostradas na folha Navegadores como dependências.

Há gráficos de resumo na parte superior da folha:

![](./media/app-insights-javascript/31.png)

e grades detalhadas mais abaixo:

![](./media/app-insights-javascript/33.png)

Clique em qualquer linha para obter detalhes específicos.

> [!NOTE]
> Se você excluir o filtro Navegadores na folha, as dependências de servidor e AJAX serão incluídas nesses gráficos. Clique em Restaurar padrões para reconfigurar o filtro.
> 
> 

**Para analisar detalhadamente as falhas de chamadas Ajax** , role para baixo até a grade Falhas de dependência e clique em uma linha para ver as instâncias específicas.

![](./media/app-insights-javascript/37.png)


Clique em `...` para ver a telemetria completa de uma chamada Ajax.

### <a name="no-ajax-calls-reported"></a>Nenhuma chamada Ajax foi relatada?
As chamadas Ajax incluem todas as chamadas HTTP feitas do script da página da Web. Se elas não forem relatadas, verifique se o trecho de código não define os [parâmetros](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) `disableAjaxTracking` ou `maxAjaxCallsPerView`.

## <a name="browser-exceptions"></a>Exceções de navegador
Na folha Navegadores, há um gráfico de resumo de exceções e uma grade dos tipos de exceção mais abaixo na folha.

![](./media/app-insights-javascript/39.png)

Se não forem relatadas exceções de navegador, verifique se o trecho de código não define o [parâmetro](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) `disableExceptionTracking`.

## <a name="inspect-individual-page-view-events"></a>Inspecionar eventos de exibição de páginas individuais

Normalmente a telemetria de exibição da página é analisada pelo Application Insights e você vê somente relatórios cumulativos, calculados sobre todos os seus usuários. Mas para a finalidade de depuração, você também pode olhar para os eventos de exibição da página individua.

Na folha de Pesquisa de Diagnóstico, defina Filtros para Exibição da Página.

![](./media/app-insights-javascript/12-search-pages.png)

Selecione qualquer evento para ver mais detalhes. Na página de detalhes, clique em "..." para ver mais detalhes.

> [!NOTE]
> Se você usar [Pesquisar](app-insights-diagnostic-search.md), observe que precisa fazer a correspondência de palavras inteiras: "Sobr" e "obre" não correspondem a "Sobre".
> 
> 

Você também pode usar a poderosa [linguagem de consulta do Log Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-tour#browser-timings-table) para pesquisar exibições de página.

### <a name="page-view-properties"></a>Propriedades de exibição de página
* **Duração do modo de exibição de página** 
  
  * Por padrão, o tempo necessário para carregar a página, desde a solicitação do cliente até o carregamento completo (incluindo arquivos auxiliares, mas excluindo tarefas assíncronas, como chamadas do Ajax). 
  * Se você definir `overridePageViewDuration` no [configuração de página](#detailed-configuration), o intervalo entre a solicitação do cliente e a execução do primeiro `trackPageView`. Se você moveu trackPageView da sua posição normal após a inicialização do script, ele refletirá um valor diferente.
  * Se `overridePageViewDuration` for definido e um argumento de duração for fornecido na chamada `trackPageView()`, o valor do argumento será usado em vez disso. 

## <a name="custom-page-counts"></a>Contagens da página personalizada
Por padrão, uma contagem de página ocorre todas as vezes que uma nova página carrega no navegador do cliente.  Por exemplo, você talvez queira contar visualizações de página adicionais. Por exemplo, uma página pode exibir seu conteúdo em guias e você quer contar uma página quando o usuário muda as guias. Ou o código do JavaScript na página pode carregar novo conteúdo sem mudar a URL do navegador.

Insira uma chamada do JavaScript como essa no ponto apropriado no código do seu cliente:

    appInsights.trackPageView(myPageName);

O nome da página pode conter os mesmos caracteres da URL, mas nada após o "#" ou "?" é ignorado.

## <a name="usage-tracking"></a>Acompanhamento de uso
Quer saber o que os usuários fazem com seu aplicativo?

* [Saiba mais sobre acompanhamento de uso](app-insights-web-track-usage.md)
* [Saiba mais sobre os eventos e as métricas personalizados de API](app-insights-api-custom-events-metrics.md).

## <a name="video"></a> Vídeo


> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]



## <a name="next"></a> Próximas etapas
* [Acompanhar uso](app-insights-web-track-usage.md)
* [Eventos e métricas personalizados](app-insights-api-custom-events-metrics.md)
* [Build-measure-learn](app-insights-web-track-usage.md)

