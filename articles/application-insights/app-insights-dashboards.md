---
title: "Painéis e navegação no Azure Application Insights | Microsoft Docs"
description: "Crie exibições de suas consultas e gráficos principais do APM."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 39b0701b-2fec-4683-842a-8a19424f67bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: 9987f04e7e71df5fe10c8bc209a390cb940ec4f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="navigation-and-dashboards-in-the-application-insights-portal"></a>Navegação e painéis no portal do Application Insights
Após de ter [Configurado o Application Insights no seu projeto](app-insights-overview.md), os dados de telemetria sobre desempenho e uso do aplicativo aparecerá no recurso do Application Insights do projeto no [portal do Azure](https://portal.azure.com).

## <a name="find-your-telemetry"></a>Encontrar sua telemetria
Entre no [portal do Azure](https://portal.azure.com) e navegue até o recurso do Application Insights que você criou para seu aplicativo.

![Clique em Procurar, selecione Application Insights e seu aplicativo.](./media/app-insights-dashboards/00-start.png)

A folha (página) de visão geral para seu aplicativo mostra um resumo das principais métricas de diagnóstico de seu aplicativo, e um gateway para outros recursos do portal.

![Rotas principais para exibir sua telemetria](./media/app-insights-dashboards/010-oview.png)

Você pode personalizar qualquer um dos gráficos e grades e fixá-los em um painel. Dessa forma, você pode reunir a telemetria da chave de aplicativos diferentes em um painel central.

## <a name="dashboards"></a>Painéis
A primeira coisa que você vê depois de entrar no [Portal do Microsoft Azure](https://portal.azure.com) é um painel. Aqui, você pode reunir os gráficos que são mais importantes para você em todos os seus recursos do Azure, incluindo a telemetria do [Azure Application Insights](app-insights-overview.md).

![Um painel personalizado.](./media/app-insights-dashboards/31.png)

1. **Navegar até recursos específicos** como seu aplicativo no Application Insights: use a barra à esquerda.
2. **Voltar para o painel atual** ou alternar para outros modos recentes: use o menu suspenso na parte superior esquerda.
3. **Alternar painéis**: use o menu suspenso no título do painel
4. **Criar, editar e compartilhar painéis** na barra de ferramentas do painel.
5. **Editar o painel**: passe o mouse sobre um bloco e use sua barra superior para movê-lo, personalizá-lo ou removê-lo.

## <a name="add-to-a-dashboard"></a>Adicionar a um painel
Quando estiver vendo uma folha ou conjunto de gráficos que é particularmente interessante, você poderá fixá-los no painel. Você o verá da próxima vez que retornar.

![Para fixar um gráfico, passe o mouse sobre ele e clique em "…" no cabeçalho.](./media/app-insights-dashboards/33.png)

1. Fixe um gráfico no painel. Uma cópia do gráfico é exibida no painel.
2. Fixe a folha inteira no painel - ela aparecerá no painel como um bloco em que você poderá clicar.
3. Clique o canto superior esquerdo para retornar ao painel atual. Em seguida, você poderá usar o menu suspenso para retornar ao modo de exibição atual.

Observe que os gráficos são agrupados em blocos: um bloco pode conter mais de um gráfico. O bloco inteiro é fixado no painel.

O gráfico é atualizado automaticamente com uma frequência que depende do intervalo de tempo do gráfico:

* Intervalo de tempo até 1 hora: atualizar a cada 5 minutos
* Intervalo de tempo de 1 a 24 horas: atualizar a cada 15 minutos
* Intervalo de tempo acima de 24 horas: (intervalo de tempo)/60.

### <a name="pin-any-query-in-analytics"></a>Fixar qualquer consulta no Analytics
Você também pode [fixar gráficos do Analytics](app-insights-analytics-using.md#pin-to-dashboard) a um painel [compartilhado](#share-dashboards-with-your-team). Isso permite que você adicione gráficos de qualquer consulta arbitrária junto com as métricas padrão. 

Os resultados são automaticamente recalculados a cada hora. Clique no ícone Atualizar no gráfico para recalcular imediatamente. (Atualizar do navegador não é recalculado.)

## <a name="adjust-a-tile-on-the-dashboard"></a>Ajustar um bloco no painel
Quando um bloco estiver no painel, você poderá ajustá-lo.

![Passe o mouse sobre um gráfico para editá-lo.](./media/app-insights-dashboards/36.png)

1. Adicione um gráfico ao bloco.
2. Defina a métrica, a dimensão de grupo e o estilo (tabela, gráfico) de um diagrama.
3. Percorra o diagrama para aplicar zoom; clique no botão de desfazer para redefinir o período de tempo; defina propriedades de filtro para os gráficos no bloco.
4. Defina o título do bloco.

Blocos fixados de folhas do Metric Explorer têm mais opções de edição que aqueles fixados de uma folha de Visão Geral.

O bloco original que você fixou não é afetado por suas edições.

## <a name="switch-between-dashboards"></a>Alternar entre painéis
É possível salvar mais de um painel e alternar entre eles. Quando você fixa um gráfico ou folha, ele é adicionado ao painel atual.

![Para alternar entre os painéis, clique em Painel e selecione um painel salvo. Para criar e salvar um novo painel, clique em Novo. Para reorganizar, clique em Editar.](./media/app-insights-dashboards/32.png)

Por exemplo, você pode ter um painel para exibir em tela inteira na sala da equipe e outro para desenvolvimento geral.

No painel, uma folha é exibida como um bloco: clique nele para ir para a folha. Um gráfico replica o gráfico em seu local original.

![Clique em um bloco para abrir a folha que ele representa](./media/app-insights-dashboards/35.png)

## <a name="share-dashboards"></a>Compartilhar painéis
Quando você tiver criado um painel, poderá compartilhá-lo com outros usuários.

![No cabeçalho do painel de controle, clique em Compartilhar](./media/app-insights-dashboards/41.png)

Saiba mais sobre [Funções e controle de acesso](app-insights-resources-roles-access-control.md).

## <a name="app-navigation"></a>Navegação do aplicativo
A folha de visão geral é o gateway para obter mais informações sobre seu aplicativo.

* **Qualquer gráfico ou bloco** - clique em qualquer bloco ou gráfico para ver mais detalhes sobre o que ele exibe.

### <a name="overview-blade-buttons"></a>Botões da folha Visão geral
![Barra de navegação superior da folha Visão geral](./media/app-insights-dashboards/app-overview-top-nav.png)

* [**Metrics Explorer**](app-insights-metrics-explorer.md) -crie seus próprios gráficos de desempenho e de uso.
* [**Pesquisa**](app-insights-diagnostic-search.md) - para investigar instâncias específicas de eventos, como solicitações, exceções ou log de rastreamento.
* [**Analytics**](app-insights-analytics.md) - consultas poderosas em sua telemetria.
* **Intervalo de tempo** -ajuste o intervalo exibido por todos os gráficos na folha.
* **Excluir** -exclua o recurso Application Insights para esse aplicativo. Você deve também remover os pacotes do Application Insights do código do seu aplicativo, ou editar a [chave de instrumentação](app-insights-create-new-resource.md#copy-the-instrumentation-key) em seu aplicativo para direcionar a telemetria para um recurso diferente do Application Insights.

### <a name="essentials-tab"></a>Guia Conceitos Básicos
* [Chave de instrumentação](app-insights-create-new-resource.md#copy-the-instrumentation-key) -identifica esse recurso de aplicativo.
* Preço - disponibilize as funcionalidades disponíveis e defina volumes.

### <a name="app-navigation-bar"></a>Barra de navegação do aplicativo
![Barra de navegação à esquerda](./media/app-insights-dashboards/app-left-nav-bar.png)

* **Visão geral** -volte para a folha de visão geral do aplicativo.
* **Log de atividades** -alertas e eventos administrativos do Azure.
* [**Controle de acesso**](app-insights-resources-roles-access-control.md) -forneça acesso a membros da equipe e outras pessoas.
* [**Marcas**](../azure-resource-manager/resource-group-using-tags.md) -use marcas para agrupar seu aplicativo com outros.

INVESTIGAR

* [**Mapa de aplicativos**](app-insights-app-map.md) - mapa ativo mostrando os componentes do aplicativo, derivado das informações de dependência.
* [**Detecção Inteligente**](app-insights-proactive-diagnostics.md) - Examine os alertas de desempenho recentes.
* [**Live Stream**](app-insights-live-stream.md) - um conjunto fixo de métricas quase instantâneas, úteis ao implantar um novo build ou depurar.
* [**Disponibilidade/testes da Web**](app-insights-monitor-web-app-availability.md) -envie solicitações regulares ao seu aplicativo Web no mundo inteiro.*
* [**Falhas, Desempenho**](app-insights-web-monitor-performance.md) -exceções, taxas de falha e tempos de resposta para solicitações ao seu aplicativo e para solicitações de seu aplicativo para [dependências](app-insights-asp-net-dependencies.md).
* [**Desempenho**](app-insights-web-monitor-performance.md) - tempo de resposta, tempos de resposta de dependência.
* [Servidores](app-insights-web-monitor-performance.md) - contadores de desempenho. Disponível se você [instalar o Status Monitor](app-insights-monitor-performance-live-website-now.md).
* **Navegador** - exibição de página e desempenho do AJAX. Disponível se você [instrumentar suas páginas da Web](app-insights-javascript.md).
* **Uso** - contagens de sessão, usuário e exibição de página. Disponível se você [instrumentar suas páginas da Web](app-insights-javascript.md).

CONFIGURAR

* **Introdução** - tutorial embutido.
* **Propriedades** - chave de instrumentação, assinatura e ID de recurso.
* [Alertas](app-insights-alerts.md) - configuração do alerta de métrica.
* [Exportação contínua](app-insights-export-telemetry.md) - configure a exportação de telemetria no armazenamento do Azure.
* [Teste de desempenho](app-insights-monitor-web-app-availability.md#performance-tests) - configure uma carga sintética no seu site.
* [Cota e preço](app-insights-pricing.md) e [amostragem de ingestão](app-insights-sampling.md).
* **Acesso à API** - atualmente usado para criar [anotações de versão](app-insights-annotations.md) e para a API de Acesso a Dados.
* [**Itens de Trabalho**](app-insights-diagnostic-search.md#create-work-item) - conecte-se a um sistema de controle do trabalho de forma que você possa criar bugs enquanto inspeciona a telemetria.

CONFIGURAÇÕES

* [**Bloqueios**](../azure-resource-manager/resource-group-lock-resources.md) - bloqueie recursos do Azure
* [**Script de automação**](app-insights-powershell.md) - exporte uma definição do recurso do Azure para que você possa usá-la como modelo para criar novos recursos.


## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Próximas etapas

|  |  |
| --- | --- |
| [Metrics explorer](app-insights-metrics-explorer.md)<br/>Métricas de filtro e de segmento |![Exemplo de pesquisa](./media/app-insights-dashboards/64.png) |
| [Pesquisa de diagnóstico](app-insights-diagnostic-search.md)<br/>Localize e inspecione eventos, eventos relacionados e crie bugs |![Exemplo de pesquisa](./media/app-insights-dashboards/61.png) |
| [Analytics](app-insights-analytics.md)<br/>Linguagem de consulta poderosa |![Exemplo de pesquisa](./media/app-insights-dashboards/63.png) |
