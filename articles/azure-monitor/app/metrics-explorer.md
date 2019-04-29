---
title: Explorando métricas no Azure Application Insights | Microsoft Docs
description: Como interpretar os gráficos no gerenciador de métricas e como personalizar as folhas do gerenciador de métricas.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: mbullwin
ms.openlocfilehash: 5c659ca2f40d47450227d16963499a6b27c9e313
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60700565"
---
# <a name="exploring-metrics-in-application-insights"></a>Explorar métricas no Application Insights
Métricas no [Application Insights][start] são contagens e valores medidos de eventos enviados em telemetria do seu aplicativo. Eles ajudam você a detectar problemas de desempenho e observar as tendências referentes a como seu aplicativo está sendo usado. Há uma grande variedade de métricas padrão, e você também pode criar suas próprias métricas e eventos personalizados.

> [!NOTE]
> Este artigo descreve a experiência do Metrics Explorer clássico que, atualmente, foi preterido e será desativado no futuro. É recomendável verificar a nova experiência descrita [neste artigo](../platform/metrics-charts.md).

Contagens de métricas e eventos são exibidas em gráficos de valores agregados como somas, médias ou contagens.

Aqui está um exemplo de conjunto de gráficos:

![](./media/metrics-explorer/01-overview.png)

No portal do Application Insights, você encontra gráficos de métricas em todos os lugares. Na maioria dos casos, eles podem ser personalizados e você pode adicionar mais gráficos à folha. Na folha visão geral, clique para gráficos mais detalhados (que têm títulos como "Servidor") ou clique em **Metrics Explorer** para abrir uma nova folha em que você possa criar gráficos personalizados.

## <a name="time-range"></a>Intervalo de tempo
Você pode alterar o intervalo de tempo coberto pelos gráficos ou grades em qualquer folha.

![Abrir a lâmina de visão geral do seu aplicativo no portal do Azure](./media/metrics-explorer/03-range.png)

Se você estiver esperando dados que não apareceram ainda, clique em Atualizar. Os gráficos são atualizados em intervalos, mas os intervalos são mais longos para intervalos de tempo maiores. Pode levar algum tempo para que dados passem pelo pipeline de análise e sejam representados em um gráfico.

Para ampliar parte de um gráfico, arraste sobre ele:

![Arraste por parte de um gráfico.](./media/metrics-explorer/12-drag.png)

Clique no botão Desfazer Zoom para restaurá-lo.

## <a name="granularity-and-point-values"></a>Valores de granularidade e ponto
Passe o mouse sobre o gráfico para exibir os valores das métricas nesse determinado ponto.

![Passar o ponteiro do mouse sobre um gráfico](./media/metrics-explorer/02-focus.png)

O valor de métrica em um ponto específico é agregado durante o intervalo de amostragem anterior.

O intervalo de amostragem ou "granularidade" é mostrado na parte superior da folha.

![O cabeçalho de uma folha.](./media/metrics-explorer/11-grain.png)

Você pode ajustar a granularidade na folha Intervalo de tempo:

![O cabeçalho de uma folha.](./media/metrics-explorer/grain.png)

As granularidades disponíveis dependem do intervalo de tempo selecionado. As granularidades explícitas são alternativas à granularidade "automática" para o intervalo de tempo.


## <a name="editing-charts-and-grids"></a>Edição de gráficos e grades
Para adicionar um novo gráfico à folha:

![No Metrics Explorer, escolher Adicionar Gráfico](./media/metrics-explorer/04-add.png)

Escolha **Editar** em um gráfico novo ou existente para editar o conteúdo exibido por ele:

![Selecionar uma ou mais métricas](./media/metrics-explorer/08-select.png)

Você pode exibir mais de uma métrica em um gráfico, porém há restrições sobre as combinações que podem ser exibidas em conjunto. Assim que você escolher uma métrica, algumas das outras serão desabilitadas.

Se você tiver codificado [métricas personalizadas][track] em seu aplicativo (chamadas para TrackMetric e TrackEvent), elas serão listadas aqui.

## <a name="segment-your-data"></a>Segmentar os dados
Você pode dividir uma métrica por propriedade - por exemplo, para comparar exibições de página em clientes com sistemas operacionais diferentes.

Selecione um gráfico ou uma grade, ative o agrupamento e escolha uma propriedade pela qual agrupar:

![Selecionar Agrupamento Ativo, então selecionar uma propriedade em Agrupar Por](./media/metrics-explorer/15-segment.png)

> [!NOTE]
> Quando você usa o agrupamento, os tipos de gráfico de Barras e de Área fornecem uma exibição empilhada. Isso é adequado quando o método de Agregação é Soma. Mas onde o tipo de agregação é Média, escolha os tipos de exibição de Linha ou Grade.
>
>

Se você tiver codificado [métricas personalizadas][track] em seu aplicativo e elas incluírem valores de propriedade, você poderá selecionar a propriedade na lista.

O gráfico é muito pequeno para dados segmentados? Ajuste sua altura:

![Ajustar a barra de controle deslizante](./media/metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Tipos de agregação
A legenda na lateral normalmente mostra, por padrão, o valor agregado durante o período do gráfico. Se você passar o mouse sobre o gráfico, ele mostra o valor nesse ponto.

Cada ponto de dados no gráfico é um acumulado dos valores de dados recebidos no intervalo de amostragem anterior, ou "granularidade". A granularidade é mostrada na parte superior da folha e varia de acordo com a escala de tempo total do gráfico.

Métricas podem ser agregadas de maneiras diferentes:

* **Contagem** é uma contagem de eventos recebidos no intervalo de amostragem. Ela é usada para eventos como solicitações. Variações na altura do gráfico indicam variações na taxa em que os eventos ocorrem. Mas observe que o valor numérico é alterado quando você altera o intervalo de amostragem.
* **Soma** adiciona os valores de todos os pontos de dados recebidos no intervalo de amostragem ou no período do gráfico.
* **Média** divide a Soma pelo número de pontos de dados recebidos durante o intervalo.
* **Únicas** são usadas para contagens de usuários e contas. Durante o intervalo de amostragem, ou durante o período do gráfico, a figura mostra a contagem de diferentes usuários vistos no momento.
* **%** – versões de percentual de cada agregação são usadas somente com gráficos segmentados. O total sempre resulta em 100% e o gráfico mostra a contribuição relativa de diferentes componentes de um total.

    ![Agregação de percentual](./media/metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Alterar o tipo de agregação

![Editar o gráfico e selecionar agregação](./media/metrics-explorer/05-aggregation.png)

O método padrão para cada métrica é mostrado quando você cria um novo gráfico ou quando todas as métricas são desmarcadas:

![Desmarque a seleção de todas as métricas para ver os padrões](./media/metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Eixo Y do PIN 
Por padrão, um gráfico mostra valores do eixo Y a partir do zero até os valores máximos no intervalo de dados, para fornecer uma representação visual do quantum dos valores. Mas, em alguns casos, mais do que o quantum, pode ser interessante inspecionar visualmente pequenas alterações nos valores. Para personalizações como essa, use o recurso de edição de intervalo do eixo Y para fixar o valor mínimo ou máximo do eixo Y no local desejado.
Clique na caixa de seleção "Configurações Avançadas" para exibir as configurações do intervalo do eixo Y

![Clique em Configurações Avançadas, selecione Intervalo personalizado e especifique os valores mín. e máx.](./media/metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Filtrar seus dados
Para ver apenas as métricas para um conjunto selecionado de valores de propriedade:

![Clicar em Filtro, expandir uma propriedade e verificar alguns valores](./media/metrics-explorer/19-filter.png)

Se você não selecionar nenhum valor para uma determinada propriedade, será o mesmo que selecionar todas elas: não há nenhum filtro para essa propriedade.

Observe as contagens de eventos junto a cada valor da propriedade. Quando você seleciona valores de uma propriedade, as contagens junto a outros valores de propriedade são ajustadas.

Os filtros se aplicam a todos os gráficos em uma folha. Se você quiser filtros diferentes aplicados a gráficos diferentes, crie e salve folhas de métricas diferentes. Se desejar, você pode fixar gráficos de diferentes folhas ao painel para vê-los lado a lado.

### <a name="remove-bot-and-web-test-traffic"></a>Remover o tráfego de testes da Web e de bot
Use o filtro **Tráfego real ou sintético** e marque **Real**.

Você também pode filtrar por **Origem do tráfego sintético**.

### <a name="to-add-properties-to-the-filter-list"></a>Para adicionar propriedades à lista de filtros
Você deseja filtrar a telemetria em uma categoria de sua escolha? Por exemplo, talvez você divida seus usuários em categorias diferentes e queira segmentar os dados segundo essas categorias.

[Crie sua própria propriedade](../../azure-monitor/app/api-custom-events-metrics.md#properties). Defina-a em um [Inicializador de Telemetria](../../azure-monitor/app/api-custom-events-metrics.md#defaults) para que ela apareça em toda a telemetria, incluindo a telemetria padrão enviada por diferentes módulos do SDK.

## <a name="edit-the-chart-type"></a>Editar o tipo de gráfico
Observe que você pode alternar entre grafos e grades:

![Selecionar uma grade ou um gráfico e escolher um tipo de grafo](./media/metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Salve sua folha de métricas
Quando você tiver criado alguns gráficos, salve-os como favoritos. Se você utiliza uma conta organizacional, você pode escolher entre compartilhá-la ou não com outros membros da equipe.

![Escolher Favorito](./media/metrics-explorer/21-favorite-save.png)

Para ver a folha novamente, **vá até a folha de visão geral** e abra Favoritos:

![Na folha Visão Geral, selecionar Favoritos](./media/metrics-explorer/22-favorite-get.png)

Se você escolheu o intervalo de tempo Relativo quando salvou, a folha será atualizada com as métricas mais recentes. Se você escolheu o intervalo de tempo Absoluto, ele mostrará sempre os mesmos dados.

## <a name="reset-the-blade"></a>Redefinir a folha
Se você editar uma folha mas em seguida decidir voltar ao conjunto original salvo, clique em Redefinir.

![Nos botões na parte superior do Metrics Explorer](./media/metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Live Metrics Stream

Para obter uma exibição imediata da sua telemetria, abra [Live Stream](live-stream.md). A maioria das métricas leva alguns minutos para aparecer devido ao processo de agregação. Por outro lado, as métricas em tempo real são otimizadas para baixa latência. 

## <a name="set-alerts"></a>Definir alertas
Para ser notificado por email sobre valores incomuns de qualquer métrica, adicione um alerta. Você pode escolher para enviar o email para os administradores de conta ou para endereços de email específicos.

![No Metrics Explorer, escolher Regras de Alerta, Adicionar Alerta](./media/metrics-explorer/appinsights-413setMetricAlert.png)

[Saiba mais sobre alertas][alerts].


## <a name="continuous-export"></a>Exportação Contínua
Se desejar que os dados sejam exportados de forma contínua para que você possa processá-los externamente, considere usar a [Exportação contínua](../../azure-monitor/app/export-telemetry.md).

### <a name="power-bi"></a>Power BI
Se desejar obter exibições ainda mais avançadas dos seus dados, você poderá [exportar para o Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Análise
[Análise](../../azure-monitor/app/analytics.md) é uma maneira mais versátil de analisar a telemetria usando uma linguagem de consulta eficiente. Use-a se quiser combinar ou calcular resultados de métricas ou executar uma exploração detalhada do desempenho recente de seu aplicativo. 

Em um gráfico de métricas, clique no ícone do Analytics para ir diretamente à consulta do Analytics equivalente.

## <a name="troubleshooting"></a>solução de problemas
*Não vejo dados no gráfico.*

* Os filtros se aplicam a todos os gráficos da folha. Verifique se, ao se concentrar em um gráfico, não definiu um filtro que excluía todos os dados em outro.

    Se quiser definir filtros diferentes em gráficos diferentes, crie-os em folhas diferentes e os salve como favoritos separados. Se desejar, você poderá fixá-los ao painel para que eles sejam exibidos lado a lado.
* Se você agrupar um gráfico por uma propriedade que não esteja definida na métrica, o gráfico ficará vazio. Tente limpar “agrupar por” ou escolha uma propriedade de agrupamento diferente.
* Haverá dados de desempenho (CPU, taxa de E/S, etc.) disponíveis para serviços Web Java, aplicativos da área de trabalho do Windows, [aplicativos Web e serviços do IIS se você instalar o Status Monitor](../../azure-monitor/app/monitor-performance-live-website-now.md) e os [Serviços de Nuvem do Azure](../../azure-monitor/app/app-insights-overview.md). Esses dados não estão disponíveis para sites do Azure.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Próximas etapas
* [Monitorando o uso com o Application Insights](../../azure-monitor/app/usage-overview.md)
* [Usando a Pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md)

<!--Link references-->

[alerts]: ../../azure-monitor/app/alerts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
