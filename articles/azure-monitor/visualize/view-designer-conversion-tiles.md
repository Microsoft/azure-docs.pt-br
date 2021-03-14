---
title: Conversões de bloco do designer de exibição para pastas de trabalho Azure Monitor
description: Detalhes da conversão de blocos em pastas de trabalho ao fazer a transição de exibições no Azure Monitor.
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 9b8d1840c0b1cea4c03312d38ed892fde94faa4c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043330"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Azure Monitor conversões de bloco do designer de exibição
O [Designer de exibição](view-designer.md) é um recurso do Azure monitor que permite que você crie modos de exibição personalizados para ajudá-lo a Visualizar dados em seu espaço de trabalho do log Analytics, com gráficos, listas e linhas do tempo. Eles estão sendo desativados e substituídos por pastas de trabalho que fornecem funcionalidade adicional. Este artigo fornece detalhes para converter blocos diferentes em pastas de trabalho.

## <a name="donut--list-tile"></a>Bloco de lista de & de rosca

![Lista de rosca](media/view-designer-conversion-tiles/donut-list.png)

Recriar o bloco de lista de & de rosca em pastas de trabalho envolve duas visualizações separadas. Para a parte de rosca, há duas opções.
Para ambos, comece selecionando **Adicionar consulta** e cole a consulta original do designer de exibição na célula.

**Opção 1:** Selecione o **gráfico de pizza** na lista suspensa **Visualização** : menu de visualização de ![ gráfico de pizza](media/view-designer-conversion-tiles/pie-chart.png)

**Opção 2:** Selecione **definir por consulta** na lista suspensa **Visualização** e adicione `| render piechart` à consulta:

 ![Menu de visualização](media/view-designer-conversion-tiles/set-by-query.png)

**Exemplo**

Consulta original
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

Consulta atualizada
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

Para criar uma lista e habilitar minigráficos, consulte o artigo sobre [tarefas comuns](view-designer-conversion-tasks.md).

Veja a seguir um exemplo de como o bloco de lista de & de rosca pode ser reinterpretado em pastas de trabalho:

![Pastas de trabalho da lista de rosca](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>Bloco de lista & do gráfico de linhas
![Lista de gráficos de linhas](media/view-designer-conversion-tiles/line-list.png) 

Para recriar a parte do gráfico de linhas, atualize a consulta da seguinte maneira:

Consulta original
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Consulta atualizada
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

Há duas opções para visualizar o gráfico de linhas

**Opção 1:** Selecione o **gráfico de linhas** na lista suspensa **Visualização** :
 
 ![Menu de gráfico de linhas](media/view-designer-conversion-tiles/line-visualization.png)

**Opção 2:** Selecione **definir por consulta** na lista suspensa **Visualização** e adicione `| render linechart` à consulta:

 ![Menu de visualização](media/view-designer-conversion-tiles/set-by-query.png)

**Exemplo**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

Para criar uma lista e habilitar minigráficos, consulte o artigo sobre [tarefas comuns](view-designer-conversion-tasks.md).

Veja a seguir um exemplo de como o gráfico de linhas & bloco de lista pode ser reinterpretado em pastas de trabalho:

![Planilhas de lista de gráfico de linhas](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>Número de & bloco de lista

 ![Lista de blocos](media/view-designer-conversion-tiles/tile-list-example.png)

Para o bloco número, atualize a consulta da seguinte maneira:

Consulta original
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

Consulta atualizada
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

Altere a lista suspensa visualização para **blocos** e, em seguida, selecione **configurações de bloco**.
 ![Visualização de bloco](media/view-designer-conversion-tiles/tile-visualization.png)

Deixe a seção **título** em branco e selecione **esquerda**. Altere o valor para **usar coluna:** para **contagem** e **renderizador de coluna** para **número grande**:

![Configurações de bloco](media/view-designer-conversion-tiles/tile-settings.png)

 
Para criar uma lista e habilitar minigráficos, consulte o artigo sobre [tarefas comuns](view-designer-conversion-tasks.md).

Veja a seguir um exemplo de como o número de & bloco de lista pode ser reinterpretado em pastas de trabalho:

![Listar pastas de trabalho](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>Linha do tempo e lista

 ![Lista de linha do tempo](media/view-designer-conversion-tiles/time-list.png)

Para a linha do tempo, atualize sua consulta da seguinte maneira:

Consulta original
```KQL
search * 
| sort by TimeGenerated desc
```

Consulta atualizada
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

Há duas opções para visualizar a consulta como um gráfico de barras:

**Opção 1:** Selecione o **gráfico de barras** na lista suspensa **Visualização** : visualização de ![ barChart](media/view-designer-conversion-tiles/bar-visualization.png)
 
**Opção 2:** Selecione **definir por consulta** na lista suspensa **Visualização** e adicione `| render barchart` à consulta:

 ![Menu de visualização](media/view-designer-conversion-tiles/set-by-query.png)

 
Para criar uma lista e habilitar minigráficos, consulte o artigo sobre [tarefas comuns](view-designer-conversion-tasks.md).

Veja a seguir um exemplo de como a linha do tempo & bloco de lista pode ser reinterpretado em pastas de trabalho:

![Lista de tempo das pastas de trabalho](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da transição do designer de exibição para pastas de trabalho](view-designer-conversion-overview.md)
