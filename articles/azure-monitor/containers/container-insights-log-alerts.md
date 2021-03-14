---
title: Registrar em log os alertas de informações do contêiner | Microsoft Docs
description: Este artigo descreve como criar alertas de log personalizados para utilização de memória e CPU de insights de contêiner.
ms.topic: conceptual
ms.date: 01/05/2021
ms.openlocfilehash: 64d499d69194ac338d367ae094e42f4c8af23bef
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711188"
---
# <a name="how-to-create-log-alerts-from-container-insights"></a>Como criar alertas de log de insights de contêiner

O insights de contêiner monitora o desempenho de cargas de trabalho de contêiner que são implantadas em clusters gerenciados ou autogerenciados do kubernetes. Para alertar sobre o que importa, este artigo descreve como criar alertas baseados em log para as seguintes situações com clusters AKS:

- Quando a utilização de CPU ou memória em nós de cluster excede um limite
- Quando a utilização de CPU ou memória em qualquer contêiner dentro de um controlador excede um limite em comparação com um limite definido no recurso correspondente
- Contagens de nós de status *não lidos*
- Contagens de fase-Pod *com falha*, *pendentes*, *desconhecidas* ou *com* *êxito*
- Quando o espaço livre em disco em nós de cluster excede um limite

Para alertar sobre alta utilização de CPU ou memória ou pouco espaço livre em disco em nós de cluster, use as consultas que são fornecidas para criar um alerta de métrica ou um alerta de medição de métrica. Embora os alertas de métrica tenham menor latência que os alertas de log, os alertas de log fornecem consulta avançada e maior sofisticação. As consultas de alerta de log comparam um DateTime com o presente usando o operador *Now* e retornando uma hora. (O contêiner de informações armazena todas as datas no formato UTC (tempo Universal Coordenado).)

Se você não estiver familiarizado com alertas de Azure Monitor, consulte [visão geral de alertas no Microsoft Azure](../alerts/alerts-overview.md) antes de iniciar. Para saber mais sobre alertas que usam consultas de log, consulte [alertas de log em Azure monitor](../alerts/alerts-unified-log.md). Para obter mais informações sobre alertas de métrica, consulte [alertas de métrica em Azure monitor](../alerts/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Consultas de pesquisa de log de utilização de recursos

As consultas nesta seção dão suporte a cada cenário de alerta. Eles são usados na etapa 7 da seção [criar alerta](#create-an-alert-rule) deste artigo.

A consulta a seguir calcula a utilização média da CPU como uma média da utilização de CPU de nós membros a cada minuto.  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

A consulta a seguir calcula a utilização média de memória como uma média de utilização de memória dos nós membros a cada minuto.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>As consultas a seguir usam os valores de espaço reservado \<your-cluster-name> e \<your-controller-name> para representar o cluster e o controlador. Substitua-os por valores específicos do seu ambiente ao configurar alertas.

A consulta a seguir calcula a utilização média da CPU de todos os contêineres em um controlador como uma média da utilização da CPU de cada instância de contêiner em um controlador a cada minuto. A medida é uma porcentagem do limite configurado para um contêiner.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

A consulta a seguir calcula a utilização média de memória de todos os contêineres em um controlador como uma média de utilização de memória de cada instância de contêiner em um controlador a cada minuto. A medida é uma porcentagem do limite configurado para um contêiner.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

A consulta a seguir retorna todos os nós e contagens que têm um status de *pronto* e *não legível*.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
A consulta a seguir retorna contagens de fase de Pod com base em todas as fases: *falha*, *pendente*, *desconhecido*, *em execução ou com* *êxito*.  

```kusto
let endDateTime = now(); 
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | summarize PodStatus=any(PodStatus) by TimeGenerated, PodUid, ClusterName
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Para alertar sobre determinadas fases de Pod, como *pendente*, *com falha* ou *desconhecido*, modifique a última linha da consulta. Por exemplo, para alertar sobre o uso do *FailedCount* : <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

A consulta a seguir retorna discos de nós de cluster que excedem 90% de espaço livre usado. Para obter a ID do cluster, primeiro execute a seguinte consulta e copie o valor da `ClusterId` Propriedade:

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>Criar uma regra de alerta

Esta seção percorre a criação de uma regra de alerta de medição de métrica usando dados de desempenho de insights de contêiner. Você pode usar esse processo básico com uma variedade de consultas de log para alertar sobre diferentes contadores de desempenho. Use uma das consultas de pesquisa de log fornecidas anteriormente para começar com o. Para criar usando um modelo do ARM, consulte [amostras de criação de alerta de log usando o modelo de recurso do Azure](../alerts/alerts-log-create-templates.md).

>[!NOTE]
>O procedimento a seguir para criar uma regra de alerta para utilização de recursos de contêiner exige que você alterne para uma nova API de alertas de log, conforme descrito em [preferência de API de switch para alertas de log](../alerts/alerts-log-api-switch.md).
>

1. Entre no [portal do Azure](https://portal.azure.com).
2. No portal do Azure, procure e selecione **Workspaces do Log Analytics**.
3. Na lista de espaços de trabalho do Log Analytics, selecione o espaço de trabalho que dá suporte a informações de contêiner. 
4. No painel no lado esquerdo, selecione **logs** para abrir a página logs de Azure monitor. Use essa página para gravar e executar consultas de log do Azure.
5. Na página **logs** , Cole uma das [consultas](#resource-utilization-log-search-queries) fornecidas anteriormente no campo consulta de **pesquisa** e, em seguida, selecione **executar** para validar os resultados. Se você não executar essa etapa, a opção **+ novo alerta** não estará disponível para seleção.
6. Selecione **+ novo alerta** para criar um alerta de log.
7. Na seção **condição** , selecione o **sempre que a pesquisa de logs personalizada \<logic undefined> for uma condição de** log personalizado predefinida. O tipo de sinal de **pesquisa de log personalizado** é selecionado automaticamente porque estamos criando uma regra de alerta diretamente na página de logs de Azure monitor.  
8. Cole uma das [consultas](#resource-utilization-log-search-queries) fornecidas anteriormente no campo de **consulta de pesquisa** .
9. Configure o alerta da seguinte maneira:

    1. Na lista suspensa **com base em** , selecione medição de **métrica**. Uma medida métrica cria um alerta para cada objeto na consulta que tem um valor acima do nosso limite especificado.
    1. Para **condição**, selecione **maior que** e insira **75** como um **limite** de linha de base inicial para os alertas de utilização de CPU e memória. Para o alerta de pouco espaço em disco, insira **90**. Ou insira um valor diferente que atenda aos seus critérios.
    1. No **alerta de gatilho com base na** seção, selecione **violações consecutivas**. Na lista suspensa, selecione **maior que** e digite **2**.
    1. Para configurar um alerta para utilização de CPU ou memória de contêiner, em **agregar em**, selecione **ContainerName**. Para configurar o alerta de baixo disco do nó do cluster, selecione **clusterid**.
    1. Na seção **avaliado com base em** , defina o valor do **período** como **60 minutos**. A regra será executada a cada 5 minutos e retornará os registros que foram criados na última hora a partir da hora atual. Definir o período de tempo para uma ampla janela de contas para uma possível latência de dados. Ele também garante que a consulta retorne dados para evitar um falso negativo no qual o alerta nunca é acionado.

10. Selecione **concluído** para concluir a regra de alerta.
11. Insira um nome no campo **nome da regra de alerta** . Especifique uma **Descrição** que forneça detalhes sobre o alerta. E selecione um nível de severidade apropriado nas opções fornecidas.
12. Para ativar imediatamente a regra de alerta, aceite o valor padrão para **habilitar a regra na criação**.
13. Selecione um **grupo de ações** existente ou crie um novo grupo. Essa etapa garante que as mesmas ações sejam executadas toda vez que um alerta for disparado. Configure com base em como sua equipe de operações de ti ou DevOps gerencia incidentes.
14. Selecione **criar regra de alerta** para concluir a regra de alerta. Ela começa a ser executada imediatamente.

## <a name="next-steps"></a>Próximas etapas

- Exiba [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas predefinidas e exemplos para avaliar ou personalizar para alertar, Visualizar ou analisar seus clusters.

- Para saber mais sobre Azure Monitor e como monitorar outros aspectos do cluster do kubernetes, consulte [Exibir o desempenho do cluster kubernetes](container-insights-analyze.md) e [exibir a integridade do cluster do kubernetes](./container-insights-overview.md).