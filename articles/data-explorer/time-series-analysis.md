---
title: Analisar dados de série temporal usando o Data Explorer do Azure
description: Saiba como analisar dados de série temporal na nuvem usando o Data Explorer do Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 8492f736e64366802b3601f9b5fc8bd1d9b6ea79
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60827323"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Análise de série temporal no Data Explorer do Azure

O Azure Data Explorer (ADX) executa a coleta contínua de dados de telemetria de serviços de nuvem ou dispositivos IoT. Esses dados podem ser analisados para vários insights, como monitoramento da integridade do serviço, processos de produção física e tendências de uso. A análise é feita em séries temporais de métricas selecionadas para encontrar um desvio no padrão em comparação com seu padrão de linha de base típico.
O ADX contém suporte nativo para criação, manipulação e análise de várias séries temporais. Neste tópico, saiba como o ADX é usado para criar e analisar **milhares de séries temporais em segundos**, permitindo soluções e fluxos de trabalho de monitoramento quase em tempo real.

## <a name="time-series-creation"></a>Criação de série de tempo

Nesta seção, criaremos um conjunto grande de séries temporais regular de forma simples e intuitiva usando o operador `make-series` e preencha os valores ausentes conforme necessário.
A primeira etapa na análise de séries temporais é particionar e transformar a tabela de telemetria original em um conjunto de séries temporais. A tabela geralmente contém uma coluna de registro de data e hora, dimensões contextuais e métricas opcionais. As dimensões são usadas para particionar os dados. O objetivo é criar milhares de série temporal por partição em intervalos regulares.

A tabela de entrada *demo_make_series1* contém 600.000 registros de tráfego de serviço da Web arbitrário. Use o comando a seguir para 10 registros de exemplo:

```kusto
demo_make_series1 | take 10 
```

A tabela resultante contém uma coluna de timestamp, três colunas de dimensões contextuais e nenhuma métrica:

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | TimeStamp | BrowserVer | OsVer | País/Região |
|   | 2016-08-25 09:12:35.4020000 | Chrome 51.0 | Windows 7 | Reino Unido |
|   | 2016-08-25 09:12:41.1120000 | Chrome 52.0 | Windows 10 |   |
|   | 2016-08-25 09:12:46.2300000 | Chrome 52.0 | Windows 7 | Reino Unido |
|   | 2016-08-25 09:12:46.5100000 | Chrome 52.0 | Windows 10 | Reino Unido |
|   | 2016-08-25 09:12:46.5570000 | Chrome 52.0 | Windows 10 | República da Lituânia |
|   | 2016-08-25 09:12:47.0470000 | Chrome 52.0 | Windows 8.1 | Índia |
|   | 2016-08-25 09:12:51.3600000 | Chrome 52.0 | Windows 10 | Reino Unido |
|   | 2016-08-25 09:12:51.6930000 | Chrome 52.0 | Windows 7 | Países Baixos |
|   | 2016-08-25 09:12:56.4240000 | Chrome 52.0 | Windows 10 | Reino Unido |
|   | 2016-08-25 09:13:08.7230000 | Chrome 52.0 | Windows 10 | Índia |

Como não há métricas, só podemos criar um conjunto de séries temporais representando a própria contagem de tráfego, particionada por SO usando a seguinte consulta:

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- Use o operador [`make-series`](/azure/kusto/query/make-seriesoperator) para criar um conjunto de três séries temporais, em que:
    - `num=count()`: série de tráfego temporal
    - `range(min_t, max_t, 1h)`: série temporal é criada em compartimentos de 1 hora no intervalo de tempo (mais antigos e mais recentes carimbos de hora de registros da tabela)
    - `default=0`: especifique o método de preenchimento para a falta de categorias para criar séries temporais regulares. Como alternativa, use [`series_fill_const()`](/azure/kusto/query/series-fill-constfunction), [`series_fill_forward()`](/azure/kusto/query/series-fill-forwardfunction), [`series_fill_backward()`](/azure/kusto/query/series-fill-backwardfunction) e [`series_fill_linear()`](/azure/kusto/query/series-fill-linearfunction) para alterações
    - `byOsVer`: partição de sistema operacional
- A estrutura de dados da série temporal real é uma matriz numérica do valor agregado para cada intervalo de tempo. Podemos usar `render timechart` para visualização.

Na tabela acima, temos três partições. Podemos criar uma série de tempo separada: Windows 10 (vermelho), 7 (azul) e 8.1 (verde) para cada versão do SO, como visto no gráfico:

![Partição de série temporal](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Funções de análise de séries temporais

Nesta seção, executaremos funções típicas de processamento em série.
Depois que um conjunto de séries temporais é criado, o ADX suporta uma lista crescente de funções para processá-las e analisá-las, que podem ser encontradas na [documentação de séries temporais](/azure/kusto/query/machine-learning-and-tsa). Descreveremos algumas funções representativas para processar e analisar séries temporais.

### <a name="filtering"></a>Filtragem

A filtragem é uma prática comum no processamento de sinais e útil para tarefas de processamento de séries temporais (por exemplo, suavizar um sinal com ruído, alterar a detecção).
- Há duas funções genéricas de filtragem:
    - [`series_fir()`](/azure/kusto/query/series-firfunction): Aplicando o filtro FIR. Usado para cálculo simples de média móvel e diferenciação da série temporal para detecção de mudança.
    - [`series_iir()`](/azure/kusto/query/series-iirfunction): Aplicando o filtro IIR. Usado para suavização exponencial e soma cumulativa.
- `Extend` a série temporal definida pela adição de uma nova série de médias móveis de tamanho 5 (denominada *ma_num*) à consulta:

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![Filtragem de série de tempo](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>Análise de regressão

O ADX suporta análise de regressão linear segmentada para estimar a tendência da série temporal.
- Use [series_fit_line()](/azure/kusto/query/series-fit-linefunction) para ajustar a melhor linha a uma série temporal para detecção geral de tendências.
- Use [series_fit_2lines()](/azure/kusto/query/series-fit-2linesfunction) para detectar alterações de tendência, relativas à linha de base, que são úteis em cenários de monitoramento.

Exemplo de funções `series_fit_line()` e `series_fit_2lines()` em uma consulta de série temporal:

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![Regressão de série de tempo](media/time-series-analysis/time-series-regression.png)

- Azul: série temporal original
- Verde: linha de ajustado
- Vermelho: duas linhas de ajustados

> [!NOTE]
> A função detectou com precisão o ponto de salto (mudança de nível).

### <a name="seasonality-detection"></a>Detecção de sazonalidade

Muitas métricas seguem padrões sazonais (periódicos). O tráfego de usuários de serviços em nuvem geralmente contém padrões diários e semanais que são mais altos no meio do dia útil e mais baixos à noite e no fim de semana. Medida de sensores de IoT em intervalos periódicos. Medições físicas como temperatura, pressão ou umidade também podem mostrar um comportamento sazonal.

O exemplo a seguir aplica a detecção de sazonalidade em um tráfego mensal de um serviço da Web (intervalos de 2 horas):

```kusto
demo_series3
| render timechart 
```

![Sazonalidade de série de tempo](media/time-series-analysis/time-series-seasonality.png)

- Use [series_periods_detect()](/azure/kusto/query/series-periods-detectfunction) para detectar automaticamente os períodos na série temporal. 
- Use [series_periods_validate()](/azure/kusto/query/series-periods-validatefunction) se nós sabemos que uma métrica deve ter Extrajudiciais distintos específicos e que queremos verificar se eles existem.

> [!NOTE]
> É uma anomalia se períodos distintos específicos não existe

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mv-expand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | períodos | scores | dias |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 1 |

A função detecta a sazonalidade diária e semanal. As pontuações diárias são menores que as semanais porque os dias de fim de semana são diferentes dos dias da semana.

### <a name="element-wise-functions"></a>Funções elemento a elemento

Operações aritméticas e lógicas podem ser feitas em uma série temporal. Usando [series_subtract()](/azure/kusto/query/series-subtractfunction), podemos calcular uma série temporal residual, ou seja, a diferença entre a métrica original e a suavizada, e procurar por anomalias no sinal residual:

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| extend residual_num=series_subtract(num, ma_num) //to calculate residual time series
| where OsVer == "Windows 10"   // filter on Win 10 to visualize a cleaner chart 
| render timechart
```

![Operações de série de tempo](media/time-series-analysis/time-series-operations.png)

- Azul: série temporal original
- Vermelho: série temporal suavizada
- Verde: série temporal residual

## <a name="time-series-workflow-at-scale"></a>Fluxo de trabalho de série de tempo em escala

O exemplo abaixo mostra como essas funções podem ser executadas em escala em milhares de séries temporais em segundos para detecção de anomalias. Para ver alguns registros de telemetria de amostra da métrica de contagem de leitura de um serviço de DB ao longo de quatro dias, execute a seguinte consulta:

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | TIMESTAMP | Loc | anonOp | DB | DataRead |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 262 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 241 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | -865998331941149874 | 262 | 279862 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 371921734563783410 | 255 | 0 |

E estatísticas simples:

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | num | min\_t | max\_t |
|   | 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

Construir uma série temporal em intervalos de 1 hora da métrica de leitura (total de quatro dias * 24 horas = 96 pontos) resulta em uma flutuação padrão normal:

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![Série temporal em escala](media/time-series-analysis/time-series-at-scale.png)

O comportamento acima é enganoso, uma vez que a única série temporal normal é agregada de milhares de instâncias diferentes que podem ter padrões anormais. Portanto, criamos uma série temporal por instância. Uma instância é definida por Loc (local), anonOp (operação) e DB (máquina específica).

Podemos criar quantas séries de tempo?

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

|   |   |
| --- | --- |
|   | Contagem |
|   | 18339 |

Agora, vamos criar um conjunto de 18339 séries temporais da métrica de contagem de leitura. Adicionamos a cláusula `by` à instrução make-series, aplicamos a regressão linear e selecionamos as duas primeiras séries temporais que apresentaram a tendência decrescente mais significativa:

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![Série temporal principais dois](media/time-series-analysis/time-series-top-2.png)

Exiba as instâncias:

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc
| project Loc, Op, DB, slope 
```

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Loc | Op | DB | slope |
|   | Loc 15 | 37 | 1151 | -102743.910227889 |
|   | Loc 13 | 37 | 1249 | -86303.2334644601 |

Em menos de dois minutos, o ADX analisou cerca de 20.000 séries temporais e detectou duas séries temporais anormais, nas quais a contagem de leitura caiu repentinamente.

Esses recursos avançados combinados com o desempenho rápido do ADX fornecem uma solução exclusiva e poderosa para análise de séries temporais.
