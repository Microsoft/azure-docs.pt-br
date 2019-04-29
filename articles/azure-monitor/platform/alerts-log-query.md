---
title: Consultas de alertas de log no Azure Monitor | Microsoft Docs
description: Fornece recomendações sobre como escrever consultas eficientes para alertas de log em atualizações do Azure Monitor e um processo de conversão de consultas existentes.
author: yossi-y
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: bwren
ms.subservice: alerts
ms.openlocfilehash: 429770b7651a93473c03f5e386d8f7b72692c161
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995863"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Consultas de alertas de log no Azure Monitor
[As regras de alerta baseadas em logs do Azure Monitor](alerts-unified-log.md) são executadas em intervalos regulares. Portanto, você deve garantir que sejam escritas para minimizar a sobrecarga e a latência. Este artigo fornece recomendações sobre como escrever consultas eficientes para alertas de log e um processo de conversão de consultas existentes. 

## <a name="types-of-log-queries"></a>Tipos de consultas de log
[Log de consultas no Azure Monitor](../log-query/log-query-overview.md) começar com uma tabela ou uma [search](/azure/kusto/query/searchoperator) ou [união](/azure/kusto/query/unionoperator) operador.

Por exemplo, o escopo da consulta a seguir é a tabela _SecurityEvent_ e pesquisa a ID do evento específico. É a única tabela que a consulta deve processar.

``` Kusto
SecurityEvent | where EventID == 4624 
```

As consultas que começam com `search` ou `union` permitem pesquisar em várias colunas em uma tabela ou até mesmo várias tabelas. Os exemplos a seguir mostram vários métodos para pesquisar o termo _Memória_:

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

Embora `search` e `union` sejam úteis durante a exploração de dados, pesquisando termos no modelo de dados inteiro, são menos eficientes do que o uso de uma tabela, pois devem examinar várias tabelas. Como as consultas nas regras de alerta são executadas em intervalos regulares, isso pode resultar em sobrecarga excessiva, acrescentando latência ao alerta. Devido a essa sobrecarga, as consultas de regras de alerta de log no Azure sempre devem começar com uma tabela para definir um escopo evidente, o que melhora o desempenho da consulta e a relevância dos resultados.

## <a name="unsupported-queries"></a>Consultas sem suporte
A partir de 11 de janeiro de 2019, a criação ou modificação de regras de alerta de log que usam os operadores `search` ou `union` não terá suporte no portal do Azure. O uso desses operadores em uma regra de alerta retornará uma mensagem de erro. As regras de alerta existentes e as regras de alerta criadas e editadas com a API Log Analytics não são afetadas por essa alteração. Você ainda deve considerar a possibilidade de alterar as regras de alerta que usam esses tipos de consultas para aumentar a eficiência.  

As regras de alerta de log que usam [consultas de recursos cruzados](../log-query/cross-workspace-query.md) não são afetadas por essa alteração, pois as consultas de recursos cruzados usam `union`, o que limita o escopo da consulta a recursos específicos. Isso não é o equivalente de `union *` que não pode ser usado.  O exemplo a seguir seria válido em uma regra de alerta de log:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>A [consulta de recursos cruzados](../log-query/cross-workspace-query.md) nos alertas de log é suportada na nova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Por padrão, o Azure Monitor usa a [API herdada de alertas do Log Analytics](api-alerts.md) para a criação de novas regras de alertas de log do portal do Azure, mas você pode mudar para a [API herdada de alertas de log](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Após a mudança, a nova API torna-se o padrão para novas regras de alerta no portal do Azure e permite criar regras de alertas de log de consulta de recursos cruzados. Você pode criar regras de alerta de log de [consulta de recursos cruzados](../log-query/cross-workspace-query.md) sem fazer a mudança usando o [modelo do ARM para a API scheduledQueryRules](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) – mas essa regra de alerta é gerenciável na [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e não no portal do Azure.

## <a name="examples"></a>Exemplos
Os exemplos a seguir incluem consultas de log que usam `search` e `union` e fornecem etapas que podem ser usadas para modificar essas consultas para uso com as regras de alerta.

### <a name="example-1"></a>Exemplo 1
Crie uma regra de alerta de log usando a consulta a seguir, que recupera informações de desempenho usando `search`: 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

Para modificar essa consulta, comece usando a consulta a seguir para identificar a tabela à que as propriedades pertencem:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

O resultado dessa consulta mostraria que a propriedade _CounterName_ veio da tabela _Perf_. 

Você pode usar esse resultado para criar a consulta a seguir que deseja usar para a regra de alerta:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>Exemplo 2
Crie uma regra de alerta de log usando a consulta a seguir, que recupera informações de desempenho usando `search`: 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

Para modificar essa consulta, comece usando a consulta a seguir para identificar a tabela à que as propriedades pertencem:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

O resultado dessa consulta mostraria que a propriedade _ObjectName_ e _CounterName_ veio da tabela _Perf_. 

Você pode usar esse resultado para criar a consulta a seguir que deseja usar para a regra de alerta:

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>Exemplo 3

Crie uma regra de alerta de log usando a consulta a seguir, que usa `search` e `union` para recuperar informações de desempenho: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

Para modificar essa consulta, comece usando a consulta a seguir para identificar a tabela à que as propriedades na primeira parte da consulta pertencem: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

O resultado dessa consulta mostraria que todas as propriedades vieram da tabela _Perf_. 

Agora, use `union` com o comando `withsource` para identificar qual tabela de origem contribuiu com cada linha.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

O resultado dessa consulta mostraria que essas propriedades também vieram da tabela _Perf_. 

Você pode usar esses resultados para criar a consulta a seguir que deseja usar para a regra de alerta: 

``` Kusto
Perf 
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total" 
| where Computer !in ( 
    (Perf 
    | where CounterName == "% Processor Utility" 
    | summarize by Computer)) 
| summarize Avg_Idle_Time = avg(CounterValue) by Computer 
| count 
``` 

### <a name="example-4"></a>Exemplo 4
Crie uma regra de alerta de log usando a consulta a seguir, que adiciona os resultados de duas consultas `search`:

```Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by Computer, Hour = bin(TimeGenerated, 1h) 
| join kind = leftouter ( 
    search in (Heartbeat) OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
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

 
O resultado indica que as propriedades no lado esquerdo da junção pertencem à tabela Pulsação. 

Você pode usar esses resultados para criar a consulta a seguir que deseja usar para a regra de alerta: 


``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat  
    | where OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre os [alertas de log](alerts-log.md) no Azure Monitor.
- Saiba mais sobre [consultas de log](../log-query/log-query-overview.md).

