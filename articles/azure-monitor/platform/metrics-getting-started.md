---
title: Introdução ao Gerenciador de métricas do Azure
description: Saiba como criar seu primeiro gráfico de métricas com o Azure Metrics Explorer.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e31eb8deb3102ab03809f01e33b1e6548113dae4
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736382"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Introdução ao Azure Metrics Explorer

## <a name="where-do-i-start"></a>Por onde começar
O Azure Monitor Metrics Explorer é um componente do portal do Microsoft Azure que permite plotar gráficos, correlacionar tendências visualmente e investigar picos e quedas nos valores das métricas. Use o Metrics Explorer para investigar a integridade e o uso dos recursos. Iniciar na seguinte ordem:

1. [Escolha um recurso e uma métrica](#create-your-first-metric-chart) e você verá um gráfico básico. Em seguida, [Selecione um intervalo de tempo](#select-a-time-range) que seja relevante para sua investigação.

1. Tente [aplicar filtros de dimensão e divisão](#apply-dimension-filters-and-splitting). Os filtros e a divisão permitem que você analise quais segmentos da métrica contribuem para o valor de métrica geral e identificam possíveis exceções.

1. Use [as configurações avançadas](#advanced-chart-settings) para personalizar o gráfico antes de fixar os painéis. [Configure alertas](alerts-metric-overview.md) para receber notificações quando o valor da métrica exceder ou cair abaixo de um limite.

## <a name="create-your-first-metric-chart"></a>Criar seu primeiro gráfico de métrica

Para criar um gráfico de métricas, em seu recurso, grupo de recursos, assinatura ou Azure Monitor exibição, abra a guia **métricas** e siga estas etapas:

1. Clique no botão "selecionar um escopo" para abrir o seletor de escopo de recurso. Isso permitirá que você selecione os recursos para os quais deseja ver as métricas. O recurso já deve estar preenchido se você tiver aberto o Metrics Explorer no menu do recurso. Para saber como exibir as métricas em vários recursos, [Leia este artigo](./metrics-dynamic-scope.md).
    > ![Selecionar um recurso](./media/metrics-getting-started/scope-picker.png)

2. Para alguns recursos, você deve escolher um namespace. O namespace é apenas uma maneira de organizar as métricas para que você possa encontrá-las com facilidade. Por exemplo, as contas de armazenamento têm namespaces separados para armazenar métricas de arquivos, tabelas, blobs e filas. Muitos tipos de recursos têm apenas um namespace.

3. Selecione uma métrica em uma lista de métricas disponíveis.

    > ![Selecione uma métrica](./media/metrics-getting-started/metrics-dropdown.png)

4. Opcionalmente, você pode [alterar a agregação de métrica](metrics-charts.md#aggregation). Por exemplo, talvez você queira que o gráfico mostre os valores mínimo, máximo ou médio da métrica.

> [!TIP]
> Use o botão **Adicionar métrica** e repita essas etapas se quiser ver várias métricas plotadas no mesmo gráfico. Para vários gráficos em uma exibição, selecione o botão **Adicionar gráfico** na parte superior.

## <a name="select-a-time-range"></a>Selecione um intervalo de tempo

> [!WARNING]
> [A maioria das métricas no Azure é armazenada por 93 dias](data-platform-metrics.md#retention-of-metrics). No entanto, você pode consultar no máximo 30 dias de dados em qualquer gráfico individual. Essa limitação não se aplica a [métricas baseadas em log](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

Por padrão, o gráfico mostra as últimas 24 horas de dados de métricas. Use o painel **seletor de tempo** para alterar o intervalo de tempo, ampliar ou reduzir seu gráfico. 

![Alterar o painel de intervalo de tempo](./media/metrics-getting-started/time.png)

> [!TIP]
> Use o **pincel de tempo** para investigar uma área interessante do gráfico (pico ou DIP). Coloque o ponteiro do mouse no início da área, clique e mantenha o botão esquerdo do mouse, arraste para o outro lado da área e, em seguida, solte o botão. O gráfico será ampliado naquele intervalo de tempo. 

## <a name="apply-dimension-filters-and-splitting"></a>Aplicar filtros de dimensão e divisão

[Filtragem](metrics-charts.md#filters) e [divisão](metrics-charts.md#apply-splitting) são ferramentas de diagnóstico poderosas para as métricas que têm dimensões. Esses recursos mostram como vários segmentos de métrica ("valores de dimensão") impactam o valor geral da métrica e permitem que você identifique possíveis exceções.

- A **filtragem** permite que você escolha quais valores de dimensão serão incluídos no gráfico. Por exemplo, talvez você queira mostrar solicitações bem-sucedidas ao representar o gráfico da métrica de *tempo de resposta do servidor* . Você precisaria aplicar o filtro no *sucesso da dimensão de solicitação* . 

- A **divisão** controla se o gráfico exibe linhas separadas para cada valor de uma dimensão ou se agrega os valores em uma única linha. Por exemplo, você pode ver uma linha para um tempo médio de resposta em todas as instâncias de servidor ou ver linhas separadas para cada servidor. Você precisaria aplicar a divisão na dimensão de *instância do servidor* para ver linhas separadas.

Confira [exemplos de gráficos](metric-chart-samples.md) que têm a filtragem e a divisão aplicadas. O artigo mostra as etapas usadas para configurar os gráficos.

## <a name="advanced-chart-settings"></a>Configurações de gráfico avançadas

Você pode personalizar o estilo do gráfico, o título e modificar as configurações avançadas do gráfico. Quando terminar a personalização, fixe-a em um painel para salvar seu trabalho. Você também pode configurar alertas de métricas. Siga a [documentação do produto](metrics-charts.md) para saber mais sobre esses e outros recursos avançados do Azure monitor métricas Explorer.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os recursos avançados do Metrics Explorer](metrics-charts.md)
* [Exibindo vários recursos no Metrics Explorer](metrics-dynamic-scope.md)
* [Solução de problemas do Metrics Explorer](metrics-troubleshoot.md)
* [Veja uma lista das métricas disponíveis para os serviços do Azure](metrics-supported.md)
* [Veja exemplos de gráficos configurados](metric-chart-samples.md)