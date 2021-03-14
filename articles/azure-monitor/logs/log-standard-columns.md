---
title: Colunas padrão em registros de log Azure Monitor | Microsoft Docs
description: Descreve as colunas que são comuns a vários tipos de dados em logs de Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/25/2021
ms.openlocfilehash: 3e12bef7569110084cd059b0dfde8562bd914823
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030759"
---
# <a name="standard-columns-in-azure-monitor-logs"></a>Colunas padrão em logs de Azure Monitor
Os dados em logs de Azure Monitor são [armazenados como um conjunto de registros em um espaço de trabalho log Analytics ou Application insights aplicativo](../logs/data-platform-logs.md), cada um com um tipo de dados específico que tem um conjunto exclusivo de colunas. Muitos tipos de dados terão colunas padrão comuns em vários tipos. Este artigo descreve essas colunas e fornece exemplos de como você pode usá-las em consultas.

Os aplicativos baseados em espaço de trabalho no Application Insights armazenam seus dados em um espaço de trabalho Log Analytics e usam as mesmas colunas padrão que outras outras tabelas no espaço de trabalho. Os aplicativos clássicos armazenam seus dados separadamente e têm colunas padrão diferentes, conforme especificado neste artigo.

> [!NOTE]
> Algumas das colunas padrão não serão mostradas na exibição de esquema ou no IntelliSense em Log Analytics, e não serão mostradas nos resultados da consulta, a menos que você especifique explicitamente a coluna na saída.
> 

## <a name="tenantid"></a>TenantId
A coluna **tenantid** contém a ID do espaço de trabalho para o espaço de trabalho log Analytics.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated e timestamp
As colunas **TimeGenerated** (log Analytics espaço de trabalho) e **timestamp** (Application insights aplicativo) contêm a data e a hora em que o registro foi criado pela fonte de dados. Confira [Tempo de ingestão de dados de log no Azure Monitor](../logs/data-ingestion-time.md) para obter mais detalhes.

**TimeGenerated** e **timestamp** fornecem uma coluna comum a ser usada para filtrar ou resumir por tempo. Quando você seleciona um intervalo de tempo para uma exibição ou um painel no portal do Azure, ele usa TimeGenerated ou timestamp para filtrar os resultados. 

### <a name="examples"></a>Exemplos

A consulta a seguir retorna o número de eventos de erro criados em cada dia da semana anterior.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

A consulta a seguir retorna o número de exceções criadas em cada dia da semana anterior.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TimeReceived
A coluna de tempo de **\_ recebimento** contém a data e a hora em que o registro foi recebido pelo ponto de ingestão Azure monitor na nuvem do Azure. Isso pode ser útil para identificar problemas de latência entre a fonte de dados e a nuvem. Um exemplo seria um problema de rede causando um atraso no envio dos dados por um agente. Confira [Tempo de ingestão de dados de log no Azure Monitor](../logs/data-ingestion-time.md) para obter mais detalhes.

> [!NOTE]
> A coluna de tempo de **\_ recebimento** é calculada sempre que é usada. Esse processo tem muitos recursos. Refine de usá-lo para filtrar um grande número de registros. Usar essa função recorrentemente pode levar a uma duração de execução de consulta maior.


A consulta a seguir fornece a latência média por hora para registros de eventos de um agente. Isso inclui o tempo do agente para a nuvem e o tempo total para que o registro esteja disponível para consultas de log.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Type e itemType
As colunas **tipo** (log Analytics espaço de trabalho) e **ItemType** (Application insights aplicativo) mantêm o nome da tabela da qual o registro foi recuperado, que também pode ser considerado como o tipo de registro. Essa coluna é útil em consultas que combinam registros de várias tabelas, como aquelas que usam o `search` operador, para distinguir entre os registros de tipos diferentes. **$table** pode ser usado no lugar de **Type** em alguns locais.

### <a name="examples"></a>Exemplos
A consulta a seguir retorna a contagem de registros por tipo coletados na última hora.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
A coluna **\_ ItemId** contém um identificador exclusivo para o registro.


## <a name="_resourceid"></a>\_ResourceId
A coluna **\_ ResourceId** contém um identificador exclusivo para o recurso ao qual o registro está associado. Isso lhe dá uma coluna padrão a ser usada para fazer o escopo da consulta somente para registros de um recurso específico ou para unir dados relacionados em várias tabelas.

Para recursos do Azure, o valor de **_ResourceId** é a [URL de ID de recurso do Azure](../../azure-resource-manager/templates/template-functions-resource.md). A coluna é limitada aos recursos do Azure, incluindo os recursos de [arco do Azure](../../azure-arc/overview.md) ou a logs personalizados que indicaram a ID do recurso durante a ingestão.

> [!NOTE]
> Alguns tipos de dados já têm campos que contêm a ID de recurso do Azure ou, pelo menos, partes dele, como a ID da assinatura. Embora esses campos sejam mantidos para manter a compatibilidade com versões anteriores, é recomendável usar _ResourceId para executar a correlação cruzada, uma vez que ela é mais consistente.

### <a name="examples"></a>Exemplos
A consulta a seguir une dados de desempenho e de eventos de cada computador. Ele mostra todos os eventos com a ID _101_ e utilização do processador acima de 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

A consulta a seguir une registros de _AzureActivity_ com registros de _SecurityEvent_. Ele mostra todas as operações de atividade com usuários que foram registradas nesses computadores.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

A consulta a seguir analisa **_ResourceId** e agrega os volumes de dados cobrados por grupo de recursos do Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by resourceGroup | sort by Bytes nulls last 
```

Use estas consultas `union withsource = tt *` com moderação como verificações em tipos de dados que são caros para executar.

É sempre mais eficiente usar a \_ coluna SubscriptionId do que extraí-la analisando a \_ coluna ResourceId.

## <a name="_substriptionid"></a>\_SubstriptionId
A coluna **\_ SubscriptionId** contém a ID da assinatura do recurso ao qual o registro está associado. Isso lhe dá uma coluna padrão a ser usada para fazer o escopo da consulta somente para registros de uma assinatura específica ou para comparar assinaturas diferentes.

Para recursos do Azure, o valor de **__SubscriptionId** é a parte da assinatura da [URL da ID de recurso do Azure](../../azure-resource-manager/templates/template-functions-resource.md). A coluna é limitada aos recursos do Azure, incluindo os recursos de [arco do Azure](../../azure-arc/overview.md) ou a logs personalizados que indicaram a ID do recurso durante a ingestão.

> [!NOTE]
> Alguns tipos de dados já têm campos que contêm a ID de assinatura do Azure. Embora esses campos sejam mantidos para compatibilidade com versões anteriores, é recomendável usar a \_ coluna SubscriptionId para executar a correlação cruzada, pois ela será mais consistente.
### <a name="examples"></a>Exemplos
A consulta a seguir examina os dados de desempenho para computadores de uma assinatura específica. 

```Kusto
Perf 
| where TimeGenerated > ago(24h) and CounterName == "memoryAllocatableBytes"
| where _SubscriptionId == "57366bcb3-7fde-4caf-8629-41dc15e3b352"
| summarize avgMemoryAllocatableBytes = avg(CounterValue) by Computer
```

A consulta a seguir analisa **_ResourceId** e agrega os volumes de dados cobrados por assinatura do Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _SubscriptionId | sort by Bytes nulls last 
```

Use estas consultas `union withsource = tt *` com moderação como verificações em tipos de dados que são caros para executar.


## <a name="_isbillable"></a>\_IsBillable
A **\_ coluna** iscobrável especifica se os dados ingeridos são faturáveis. Dados com **\_IsBillable** igual a `false` são coletados de graça e não são cobrados da sua conta do Azure.

### <a name="examples"></a>Exemplos
Para obter uma lista de computadores que estão enviando os tipos de dados cobrados, use a seguinte consulta:

> [!NOTE]
> Use consultas com `union withsource = tt *` com moderação, pois a execução de exames entre diferentes tipos de dados é cara. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Isso pode ser estendido para retornar a contagem de computadores por hora que estão enviando tipos de dados cobrados:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
A coluna **\_ BilledSize** especifica o tamanho em bytes de dados que serão cobrados em sua conta do Azure se **\_ isbillble** for true.


### <a name="examples"></a>Exemplos
Para ver o tamanho dos eventos faturáveis ingeridos por computador, use a `_BilledSize` coluna que fornece o tamanho em bytes:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Para ver o tamanho de eventos cobráveis ingeridos por assinatura, use a seguinte consulta:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId | sort by Bytes nulls last 
```

Para ver a contagem de eventos cobráveis ingeridos por grupo de recursos, use a seguinte consulta:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


Para ver a contagem de eventos ingeridos por computador, use a seguinte consulta:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Para ver a contagem de eventos cobráveis ingeridos por computador, use a seguinte consulta: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Para ver a contagem de tipos de dados cobráveis de um computador específico, use a seguinte consulta:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre como os [dados de log do Azure Monitor são armazenados](./log-query-overview.md).
- Obtenha uma lição sobre como [escrever consultas de log](./get-started-queries.md).
- Obtenha uma lição sobre como [unir tabelas em consultas de log](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins).
