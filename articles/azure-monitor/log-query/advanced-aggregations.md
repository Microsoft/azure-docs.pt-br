---
title: Agregações avançadas nas consultas de log do Azure Monitor | Microsoft Docs
description: Descreve algumas das opções de agregação mais avançadas disponíveis para as consultas de log do Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 56e87da0353a41504035a070d4c10bab0dda2279
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551746"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Agregações avançadas nas consultas de log do Azure Monitor

> [!NOTE]
> Você deve concluir as [ Agregações nas consultas do Azure Monitor](./aggregations.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve algumas das opções de agregação mais avançadas disponíveis para as consultas do Azure Monitor.

## <a name="generating-lists-and-sets"></a>Geração de listas e conjuntos
Você pode usar `makelist` para dinamizar dados pela ordem de valores em uma coluna específica. Por exemplo, você pode querer explorar os eventos de pedido mais comuns em suas máquinas. Você pode essencialmente girar os dados pela ordem de EventIDs em cada máquina. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Computador|list_EventID|
|---|---|
| computador1 | [704,701,1501,1500,1085,704,704,701] |
| computador2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` gera uma lista na ordem em que os dados foram passados para ela. Para classificar os eventos do mais antigo para o mais recente, use `asc`na declaração do pedido em vez de`desc`. 

Também é útil criar uma lista de valores distintos apenas. Isso é chamado de um _definir_ e pode ser gerado com `makeset`:

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Computador|list_EventID|
|---|---|
| computador1 | [704,701,1501,1500,1085] |
| computador2 | [326,105,302,301,300,102] |
| ... | ... |

Como `makelist`, `makeset` também trabalha com dados ordenados e gera os arrays com base na ordem das linhas que são passadas para ele.

## <a name="expanding-lists"></a>Expansão de listas
A operação inversa de `makelist` ou `makeset` é `mvexpand`, o que expande uma lista de valores para linhas separadas. Ele pode se expandir em qualquer número de colunas dinâmicas, tanto JSON quanto array. Por exemplo, você pode verificar a *pulsação* tabela para soluções que enviam dados de computadores que enviou uma pulsação na última hora:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Computador | Soluções | 
|--------------|----------------------|
| computador1 | "segurança", "atualizações", "changeTracking" |
| computador2 | "segurança", "atualizações" |
| Computador3 | "antiMalware", "controle de alterações" |
| ... | ... |

Use `mvexpand` para mostrar cada valor em uma linha separada em vez de uma lista separada por vírgula:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Computador | Soluções | 
|--------------|----------------------|
| computador1 | "segurança" |
| computador1 | "atualizações" |
| computador1 | "controle de alterações" |
| computador2 | "segurança" |
| computador2 | "atualizações" |
| Computador3 | "antiMalware" |
| Computador3 | "controle de alterações" |
| ... | ... |


Você pode usar `makelist` novamente agrupar itens juntos e, desta vez ver a lista de computadores por solução:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Soluções | list_Computer |
|--------------|----------------------|
| "segurança" | ["Computador1", "computador2"] |
| "atualizações" | ["Computador1", "computador2"] |
| "controle de alterações" | ["Computador1", "Computador3"] |
| "antiMalware" | ["Computador3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>Handling missing bins
A useful application of `mvexpand` is the need to fill default values in for missing bins. Por exemplo, suponha que você esteja procurando o tempo de atividade de uma determinada máquina, explorando sua pulsação. Você também deseja ver a origem da pulsação que está na coluna _categoria_. Normalmente, usaríamos um simples resumir instrução da seguinte maneira:

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| Category | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Agente direto | 2017-06-06T17:00:00Z | 15 |
| Agente direto | 2017-06-06T18:00:00Z | 60 |
| Agente direto | 2017-06-06T20:00:00Z | 55 |
| Agente direto | 2017-06-06T21:00:00Z | 57 |
| Agente direto | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

Nesses resultados, o bucket associado a "2017-06-06T19: 00: 00Z" está ausente porque não há dados de pulsação para essa hora. Use a função `make-series` para atribuir um valor padrão a depósitos vazios. Isso gerará uma linha para cada categoria com duas colunas de matriz extras, uma para valores e outra para correspondência de intervalos de tempo:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Category | count_ | TimeGenerated |
|---|---|---|
| Agente direto | [15,60,0,55,60,57,60...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

O terceiro elemento da matriz *count_* é 0 como esperado e há um registro de data e hora correspondente de "2017-06-06T19: 00: 00.0000000Z" na matriz _TimeGenerated_. Esse formato de matriz é difícil de ler, no entanto. Use `mvexpand` para expandir as matrizes e produzem o mesmo formato de saída gerada pelo `summarize`:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| Category | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Agente direto | 2017-06-06T17:00:00Z | 15 |
| Agente direto | 2017-06-06T18:00:00Z | 60 |
| Agente direto | 2017-06-06T19:00:00Z | 0 |
| Agente direto | 2017-06-06T20:00:00Z | 55 |
| Agente direto | 2017-06-06T21:00:00Z | 57 |
| Agente direto | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Estreitando os resultados para um conjunto de elementos: `let`, `makeset`, `toscalar`, `in`
Um cenário comum é selecionar os nomes de algumas entidades específicas com base em um conjunto de critérios e, em seguida, filtrar um conjunto de dados diferente para esse conjunto de entidades. Por exemplo, você pode encontrar computadores que são conhecidos por ter atualizações ausentes e identificar os IPs que esses computadores chamavam:


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="next-steps"></a>Próximas etapas

Consulte outras lições para usar a [linguagem de consulta Kusto](/azure/kusto/query/) com os dados de log do Azure Monitor:

- [Operações de cadeia de caracteres](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [JSON e estruturas de dados](json-data-structures.md)
- [Junções](joins.md)
- [Gráficos](charts.md)