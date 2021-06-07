---
title: Azure Monitor visualizações de gráfico de pasta de trabalho
description: Saiba mais sobre todas as visualizações de gráfico de pasta de trabalho Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 139c8cdb1a227595d728d2acc0b09cf1eb210715
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100606442"
---
# <a name="chart-visualizations"></a>Visualizações de gráfico

As pastas de trabalho permitem que os dados de monitoramento sejam apresentados como gráficos. Os tipos de gráfico com suporte incluem linha, barra, categórica, área, gráficos de dispersão, pizza e hora. Os autores podem optar por personalizar a altura, a largura, a paleta de cores, a legenda, os títulos, as mensagens sem dados, etc. do gráfico, e personalizar os tipos de eixo e as cores da série usando as configurações do gráfico.

As pastas de trabalho dão suporte a gráficos para logs e fontes de dados de métrica.

## <a name="log-charts"></a>Gráficos de log

Os logs de Azure Monitor fornecem aos proprietários de recursos informações detalhadas sobre o funcionamento de seus aplicativos e sua infraestrutura. Diferentemente das métricas, as informações de log não são coletadas por padrão e exigem algum tipo de integração de coleção. No entanto, quando os logs presentes fornecem muitas informações sobre o estado do recurso e os dados são úteis para o diagnóstico. As pastas de trabalho permitem apresentar dados de log como gráficos visuais para análise de usuário.

### <a name="adding-a-log-chart"></a>Adicionando um gráfico de log

O exemplo a seguir mostra a tendência de solicitações para um aplicativo nos dias anteriores.

1. Alterne a pasta de trabalho para o modo de edição selecionando o item **Editar** barra de ferramentas.
2. Use o link **Adicionar consulta** para adicionar um controle de consulta de log à pasta de trabalho.
3. Selecione o tipo de consulta como **log**, tipo de recurso (por exemplo, Application insights) e os recursos a serem direcionados.
4. Use o editor de consultas para inserir o [KQL](/azure/kusto/query/) para sua análise (por exemplo, tendência de solicitações).
5. Defina a visualização como uma das: **área**, **barra**, **barra (categórico)**, **linha**, **pizza**, **dispersão** ou **hora**.
6. Defina outros parâmetros, se necessário, como intervalo de tempo, visualização, tamanho, paleta de cores e legenda.

[![Captura de tela do gráfico de log no modo de edição](./media/workbooks-chart-visualizations/log-chart.png)](./media/workbooks-chart-visualizations/log-chart.png#lightbox)

### <a name="log-chart-parameters"></a>Parâmetros do gráfico de log

| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `Query Type` | O tipo de consulta a ser usado. | Log, grafo de recursos do Azure, etc. |
| `Resource Type` | O tipo de recurso para o destino. | Application Insights, Log Analytics ou Azure-First |
| `Resources` | Um conjunto de recursos do qual obter o valor de métricas. | MyApp1 |
| `Time Range` | A janela de tempo para exibir o gráfico de log. | Última hora, últimas 24 horas, etc. |
| `Visualization` | A visualização a ser usada. | Área, barra, linha, pizza, dispersão, hora, categórica da barra |
| `Size` | O tamanho vertical do controle. | Pequeno, médio, grande ou completo |
| `Color palette` | A paleta de cores a ser usada no gráfico. Ignorado no modo de várias métricas ou segmentado. | Azul, verde, vermelho etc. |
| `Legend` | A função de agregação a ser usada para a legenda. | Soma ou média de valores ou máximo, mínimo, primeiro, último valor |
| `Query` | Qualquer consulta KQL que retorna dados no formato esperado pela visualização de gráfico. | _solicitações \| Make-Series requests = Count () padrão = 0 no timestamp de atrás (1D) para Now () etapa 1h_ |

### <a name="time-series-charts"></a>Gráficos de série temporal

Gráficos de série temporal, como área, barra, linha, dispersão e hora, podem ser facilmente criados usando o controle de consulta em pastas de trabalho. A chave está tendo informações de tempo e métrica no conjunto de resultados.

#### <a name="simple-time-series"></a>Série temporal simples

A consulta abaixo retorna uma tabela com duas colunas: *timestamp* e *solicitações*. O controle de consulta usa *carimbo de data/hora* para o eixo X e *as solicitações* para o eixo Y.

```kusto
requests
| summarize Requests = count() by bin(timestamp, 1h)
```

[![Captura de tela de um gráfico simples de linha de log de série temporal.](./media/workbooks-chart-visualizations/log-chart-line-simple.png)](./media/workbooks-chart-visualizations/log-chart-line-simple.png#lightbox)

#### <a name="time-series-with-multiple-metrics"></a>Série temporal com várias métricas

A consulta abaixo retorna uma tabela com três colunas: *timestamp*, *solicitações* e *usuários*. O controle de consulta usa *carimbo de data/hora* para o eixo X e *solicita*  &  *os usuários* como uma série separada no eixo Y.

```kusto
requests
| summarize Requests = count(), Users = dcount(user_Id) by bin(timestamp, 1h)
```

[![Captura de tela de uma série temporal com o gráfico de linhas de log de várias métricas.](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png)](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png#lightbox)

#### <a name="segmented-time-series"></a>Série temporal segmentada

A consulta abaixo retorna uma tabela com três colunas: *timestamp*, *solicitações* e *RequestName* , em que *RequestName* é uma coluna categórica com os nomes das solicitações. O controle de consulta aqui usa *carimbo de data/hora* para o eixo X e adiciona uma série por valor de *RequestName*.

```
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

[![Captura de tela de um gráfico de linhas de log de série temporal segmentada.](./media/workbooks-chart-visualizations/log-chart-line-segmented.png)](./media/workbooks-chart-visualizations/log-chart-line-segmented.png#lightbox)

### <a name="summarize-vs-make-series"></a>Resumir versus criar série

Os exemplos na seção anterior usam o `summarize` operador porque é mais fácil de entender. No entanto, o resumo tem uma limitação importante, pois omite a linha de resultados se não houver nenhum item no Bucket. Ele pode ter o efeito de deslocar a janela de tempo do gráfico dependendo se os buckets vazios estão na frente ou no verso do intervalo de tempo.

Geralmente, é melhor usar o `make-series` operador para criar dados de série temporal, que tem a opção de fornecer valores padrão para buckets vazios.

A consulta a seguir usa o `make-series` operador.

```kusto
requests
| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h by RequestName = name
```

A consulta abaixo mostra um gráfico semelhante com o `summarize` operador

```kusto
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

Embora o conjunto de resultados subjacente seja diferente. Tudo o que um usuário precisa fazer é definir a visualização como área, linha, barra ou hora e pastas de trabalho cuidam do restante.

[![Captura de tela de um gráfico de linha de log criado por meio de uma consulta Make-Series](./media/workbooks-chart-visualizations/log-chart-line-make-series.png)](./media/workbooks-chart-visualizations/log-chart-line-make-series.png#lightbox)

### <a name="categorical-bar-chart-or-histogram"></a>Gráfico de barras categóricas ou histograma

Gráficos categóricos permitem que os usuários representem uma dimensão ou coluna no eixo X de um gráfico, isso é especialmente útil em histogramas. O exemplo a seguir mostra a distribuição de solicitações por seu código de resultado.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

A consulta retorna duas colunas: *solicitação* de métrica e categoria de *resultado* . Cada valor da coluna de *resultados* receberá sua própria barra no gráfico com altura proporcional à *métrica de solicitações*.

[![Captura de tela de um gráfico de barras categórica para solicitações por código de resultado](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png)](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png#lightbox)

### <a name="pie-charts"></a>Gráficos de pizza

Os gráficos de pizza permitem a visualização da proporção numérica. O exemplo a seguir mostra a proporção de solicitações por seu código de resultado.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

A consulta retorna duas colunas: *solicitação* de métrica e categoria de *resultado* . Cada valor da coluna de *resultados* receberá sua própria fatia na pizza com tamanho proporcional à métrica de *solicitações* .

[![Captura de tela de um gráfico de pizza com fatias representando o código de resultado](./media/workbooks-chart-visualizations/log-chart-pie-chart.png)](./media/workbooks-chart-visualizations/log-chart-pie-chart.png#lightbox)

## <a name="metric-charts"></a>Gráficos de métricas

A maioria dos recursos do Azure emite dados de métrica sobre estado e integridade (por exemplo, utilização da CPU, disponibilidade de armazenamento, contagem de transações de banco de dados, solicitações de aplicativos com falha etc.). As pastas de trabalho permitem a visualização desses dados como gráficos de série temporal.)

### <a name="adding-a-metric-chart"></a>Adicionando um gráfico de métrica

O exemplo a seguir mostrará o número de transações em uma conta de armazenamento na hora anterior. Isso permite que o proprietário do armazenamento Veja a tendência da transação e Procure anomalias no comportamento.

1. Alterne a pasta de trabalho para o modo de edição selecionando o item **Editar** barra de ferramentas.
2. Use o link **Adicionar métrica** para adicionar um controle de métrica à pasta de trabalho.
3. Selecione um tipo de recurso (por exemplo, conta de armazenamento), os recursos a serem direcionados, o namespace e o nome da métrica e a agregação a ser usada.
4. Defina outros parâmetros, se necessário, como intervalo de tempo, divisão por, visualização, tamanho e paleta de cores.

[![Captura de tela do gráfico de métrica no modo de edição](./media/workbooks-chart-visualizations/metric-chart.png)](./media/workbooks-chart-visualizations/metric-chart.png#lightbox)

### <a name="metric-chart-parameters"></a>Parâmetros do gráfico de métrica

| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `Resource Type` | O tipo de recurso para o destino. | Armazenamento ou máquina virtual. |
| `Resources` | Um conjunto de recursos do qual obter o valor de métricas. | MyStorage1 |
| `Namespace` | O namespace com a métrica. | Blob de > de armazenamento |
| `Metric` | A métrica a ser visualizada. | Armazenamento > blob > transações |
| `Aggregation` | A função de agregação a ser aplicada à métrica. | Soma, contagem, média, etc. |
| `Time Range` | A janela de tempo para a qual exibir a métrica. | Última hora, últimas 24 horas, etc. |
| `Visualization` | A visualização a ser usada. | Área, barra, linha, dispersão, grade |
| `Split By` | Opcionalmente, divida a métrica em uma dimensão. | Transações por tipo geográfico |
| `Size` | O tamanho vertical do controle. | Pequeno, médio ou grande |
| `Color palette` | A paleta de cores a ser usada no gráfico. Ignorado se o `Split by` parâmetro for usado. | Azul, verde, vermelho etc. |

### <a name="examples"></a>Exemplos

Transações divididas pelo nome da API como um gráfico de linhas:

[![Captura de tela de um gráfico de linhas de métrica para transações de armazenamento divididas pelo nome da API](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png)](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png#lightbox)

Transações divididas por tipo de resposta como um gráfico de barras grande:

[![Captura de tela de um gráfico de barras de métrica grande para transações de armazenamento divididas por tipo de resposta](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png)](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png#lightbox)

Latência média como um gráfico de dispersão:

[![Captura de tela de um gráfico de dispersão de métrica para latência de armazenamento](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png)](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png#lightbox)

## <a name="chart-settings"></a>Configurações do gráfico

Os autores podem usar as configurações de gráfico para personalizar quais campos são usados nos eixos do gráfico, as unidades do eixo, a formatação personalizada, os intervalos, os comportamentos de agrupamento, as legendas e as cores da série.

### <a name="the-settings-tab"></a>Guia de configurações

A guia Configurações controla:

- As configurações de eixo, incluindo quais campos, formatação personalizada, permite aos usuários definir a formatação de número para os valores de eixo e os intervalos personalizados.
- Configurações de agrupamento, incluindo qual campo, os limites antes de um grupo de "outros" é criado.
- Configurações de legenda, incluindo a exibição de métricas (nome da série, cores e números) na parte inferior e/ou uma legenda (nomes e cores da série).

![Captura de tela das configurações do gráfico.](./media/workbooks-chart-visualizations/chart-settings.png)

#### <a name="custom-formatting"></a>Formatação personalizada

As opções de formatação de número incluem:

| Opção de formatação             | Explicação                                                                                           |
|:---------------------------- |:-------------------------------------------------------------------------------------------------------|
| `Units`                      | As unidades da coluna-várias opções de porcentagem, contagens, tempo, byte, contagem/tempo, bytes/tempo, etc. Por exemplo, a unidade de um valor de 1234 pode ser definida como milissegundos e é renderizada como 1,234 s.                                  |
| `Style`                      | O formato para renderizá-lo como-Decimal, moeda, porcentagem.                                               |
| `Show group separator`       | Caixa de seleção para mostrar separadores de grupo. Renderiza 1234 como 1.234 nos EUA.                                    |
| `Minimum integer digits`     | Número mínimo de dígitos inteiros a serem usados (padrão 1).                                                   |
| `Minimum fractional digits`  | Número mínimo de dígitos fracionários a serem usados (padrão 0).                                                |
| `Maximum fractional digits`  | Número máximo de dígitos fracionários a serem usados.                                                            |
| `Minimum significant digits` | Número mínimo de dígitos significativos a serem usados (padrão 1).                                               |
| `Maximum significant digits` | Número máximo de dígitos significativos a serem usados.                                                           |

![Captura de tela das configurações do eixo X.](./media/workbooks-chart-visualizations/number-format-settings.png)

### <a name="the-series-tab"></a>A guia série

A guia Configuração da série permite ajustar os rótulos e as cores mostradas para a série no gráfico.

- O `Series name` campo é usado para corresponder a uma série nos dados e, se for correspondido, o rótulo e a cor de exibição serão exibidos.
- O `Comment` campo é útil para autores de modelo, pois esse comentário pode ser usado por tradutores para localizar os rótulos de exibição.

![Captura de tela das configurações da série.](./media/workbooks-chart-visualizations/series-settings.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como criar um [bloco em pastas de trabalho](workbooks-tile-visualizations.md).
- Saiba como criar [pastas de trabalho interativas](workbooks-interactive.md).