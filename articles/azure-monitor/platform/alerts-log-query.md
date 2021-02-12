---
title: Otimizando consultas de alerta de log | Microsoft Docs
description: Recomendações para escrever consultas de alerta eficientes
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: e7c9c76816b5d1ee2eedfb7e54645e056906feef
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186619"
---
# <a name="optimizing-log-alert-queries"></a>Otimizando consultas de alerta de log
Este artigo descreve como gravar e converter consultas de [alerta de log](alerts-unified-log.md) para obter o desempenho ideal. Consultas otimizadas reduzem a latência e a carga de alertas, que são executados com frequência.

## <a name="how-to-start-writing-an-alert-log-query"></a>Como iniciar a gravação de uma consulta de log de alertas

As consultas de alerta começam com [a consulta dos dados de log em log Analytics](alerts-log.md#create-a-log-alert-rule-with-the-azure-portal) que indica o problema. Você pode usar o [tópico Exemplos de consulta de alerta](../log-query/example-queries.md) para entender o que você pode descobrir. Você também pode começar [a escrever sua própria consulta](../log-query/log-analytics-tutorial.md). 

### <a name="queries-that-indicate-the-issue-and-not-the-alert"></a>Consultas que indicam o problema e não o alerta

O fluxo de alertas foi criado para transformar os resultados que indicam o problema para um alerta. Por exemplo, em um caso de uma consulta como:

``` Kusto
SecurityEvent
| where EventID == 4624
```

Se a intenção do usuário for alertar, quando esse tipo de evento ocorrer, a lógica de alerta acrescentará `count` à consulta. A consulta que será executada será:

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

Não há necessidade de adicionar lógica de alerta à consulta e fazer isso pode até mesmo causar problemas. No exemplo acima, se você incluir `count` em sua consulta, ela sempre resultará no valor 1, pois o serviço de alerta fará `count` do `count` .

### <a name="avoid-limit-and-take-operators"></a>Evite `limit` `take` operadores e

O uso de `limit` e `take` em consultas pode aumentar a latência e a carga de alertas, pois os resultados não são consistentes ao longo do tempo. É preferível usá-lo somente se necessário.

## <a name="log-query-constraints"></a>Restrições de consulta de log
As [consultas de log no Azure monitor](../log-query/log-query-overview.md) começam com uma tabela, [`search`](/azure/kusto/query/searchoperator) ou [`union`](/azure/kusto/query/unionoperator) operador.

As consultas de regras de alerta de log sempre devem começar com uma tabela para definir um escopo claro, o que melhora o desempenho da consulta e a relevância dos resultados. As consultas em regras de alerta são executadas com frequência, portanto usar `search` e `union` pode resultar na sobrecarga excessiva de adição de latência ao alerta, pois ele requer a verificação em várias tabelas. Esses operadores também reduzem a capacidade do serviço de alerta de otimizar a consulta.

Não há suporte para criar ou modificar regras de alerta de log que usam `search` `union` operadores ou, espera para consultas entre recursos.

Por exemplo, a consulta de alerta a seguir tem como escopo a tabela _SecurityEvent_ e procura por uma ID de evento específica. É a única tabela que a consulta deve processar.

``` Kusto
SecurityEvent
| where EventID == 4624
```

As regras de alerta de log usando [consultas entre recursos](../log-query/cross-workspace-query.md) não são afetadas por essa alteração, pois as consultas entre recursos usam um tipo de `union` , que limita o escopo da consulta a recursos específicos. O exemplo a seguir seria uma consulta de alerta de log válida:

```Kusto
union
app('Contoso-app1').requests,
app('Contoso-app2').requests,
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
> Há suporte para [consultas entre recursos](../log-query/cross-workspace-query.md) na nova [API do scheduledQueryRules](/rest/api/monitor/scheduledqueryrules). Se você ainda usa a [API de alerta log Analytics herdada](api-alerts.md) para criar alertas de log, você pode aprender a alternar [aqui](alerts-log-api-switch.md).

## <a name="examples"></a>Exemplos
Os exemplos a seguir incluem consultas de log que usam `search` e `union` fornecem etapas que você pode usar para modificar essas consultas para uso em regras de alerta.

### <a name="example-1"></a>Exemplo 1
Você deseja criar uma regra de alerta de log usando a seguinte consulta que recupera informações de desempenho usando `search` : 

``` Kusto
search *
| where Type == 'Perf' and CounterName == '% Free Space'
| where CounterValue < 30
```

Para modificar essa consulta, comece usando a consulta a seguir para identificar a tabela à que as propriedades pertencem:

``` Kusto
search *
| where CounterName == '% Free Space'
| summarize by $table
```

O resultado dessa consulta mostraria que a propriedade _CounterName_ veio da tabela _Perf_.

Você pode usar esse resultado para criar a seguinte consulta que você usaria para a regra de alerta:

``` Kusto
Perf
| where CounterName == '% Free Space'
| where CounterValue < 30
```

### <a name="example-2"></a>Exemplo 2
Você deseja criar uma regra de alerta de log usando a seguinte consulta que recupera informações de desempenho usando `search` : 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)  
```

Para modificar essa consulta, comece usando a consulta a seguir para identificar a tabela à que as propriedades pertencem:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use"
| summarize by $table
```

O resultado dessa consulta mostraria que a propriedade _ObjectName_ e _CounterName_ veio da tabela _Perf_.

Você pode usar esse resultado para criar a seguinte consulta que você usaria para a regra de alerta:

``` Kusto
Perf
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)
```

### <a name="example-3"></a>Exemplo 3

Você deseja criar uma regra de alerta de log usando a seguinte consulta que usa `search` e `union` para recuperar informações de desempenho: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| where Computer !in (
    union *
    | where CounterName == "% Processor Utility"
    | summarize by Computer)
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
```

Para modificar essa consulta, comece usando a consulta a seguir para identificar a tabela à que as propriedades na primeira parte da consulta pertencem: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| summarize by $table
```

O resultado dessa consulta mostraria que todas as propriedades vieram da tabela _Perf_.

Agora, use `union` com o comando `withsource` para identificar qual tabela de origem contribuiu com cada linha.

``` Kusto
union withsource=table *
| where CounterName == "% Processor Utility"
| summarize by table
```

O resultado dessa consulta mostraria que essas propriedades também vieram da tabela _Perf_.

Você pode usar esses resultados para criar a seguinte consulta que você usaria para a regra de alerta: 

``` Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total"
| where Computer !in (
    (Perf
    | where CounterName == "% Processor Utility"
    | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
``` 

### <a name="example-4"></a>Exemplo 4
Você deseja criar uma regra de alerta de log usando a seguinte consulta que une os resultados de duas `search` consultas:

```Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    search in (Heartbeat) OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

Para modificar a consulta, comece usando a consulta a seguir para identificar a tabela que contém as propriedades no lado esquerdo da junção: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by $table
```

O resultado indica que as propriedades no lado esquerdo da junção pertencem à tabela _SecurityEvent_. 

Agora, use a consulta a seguir para identificar a tabela que contém as propriedades no lado direito da junção: 
 
``` Kusto
search in (Heartbeat) OSType == 'Windows'
| summarize by $table
```
 
O resultado indica que as propriedades no lado direito da junção pertencem à tabela de _pulsação_ .

Você pode usar esses resultados para criar a seguinte consulta que você usaria para a regra de alerta: 

``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat
    | where OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre os [alertas de log](alerts-log.md) no Azure Monitor.
- Saiba mais sobre [consultas de log](../log-query/log-query-overview.md).