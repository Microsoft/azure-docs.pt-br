---
title: Como consultar logs de informações do contêiner | Microsoft Docs
description: As informações de contêiner coletam dados de log e métricas e este artigo descreve os registros e inclui exemplos de consultas.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: c2b7331255e1109f27f89a84d66e25eb07a20569
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201372"
---
# <a name="how-to-query-logs-from-container-insights"></a>Como consultar logs de informações de contêiner

O insights de contêiner coleta métricas de desempenho, dados de inventário e informações de estado de integridade de contêineres e contêineres. Os dados são coletados a cada três minutos e encaminhados para o espaço de trabalho Log Analytics no Azure Monitor. Esses dados estão disponíveis para [consulta](../logs/log-query-overview.md) no Azure monitor. Você pode aplicar esses dados a cenários que incluem planejamento de migração, análise de capacidade, descoberta e solução de problemas de desempenho sob demanda.

## <a name="container-records"></a>Registros de contêiner

Na tabela a seguir, são fornecidos os detalhes dos registros coletados por informações de contêiner. Para obter uma lista das descrições de coluna, consulte a referência para as tabelas [ContainerInventory](/azure/azure-monitor/reference/tables/containerinventory) e [ContainerLog](/azure/azure-monitor/reference/tables/containerlog) .

| Dados | Fonte de dados | Tipo de dados | Campos |
|------|-------------|-----------|--------|
| Inventário de contêiner | Kubelet | `ContainerInventory` | TimeGenerated, computador, nome, ContainerHostname, imagem, ImageTag, Containerstate, ExitCode, EnvironmentVar, comando, Createdtime, preparado, Finishedtime, SourceSystem, Idcontêiner, Imageid |
| Log do contêiner | Docker | `ContainerLog` | TimeGenerated, computador, ID da imagem, nome, LogEntry, LogEntry, SourceSystem, ContainerId |
| Inventário de nós do contêiner | API Kube | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventário de pods em um cluster Kubernetes | API Kube | `KubePodInventory` | TimeGenerated, computador, Clusterid, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus, ContainerStatusReason, Idcontêiner, ContainerName, nome, PodLabel, namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Inventário da parte de nós de um cluster Kubernetes | API Kube | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
|Inventário de volumes persistentes em um cluster kubernetes |API Kube |`KubePVInventory` | TimeGenerated, PVName, PVCapacityBytes, PVCName, PVCNamespace, PVStatus, PVAccessModes, PVType, PVTypeInfo, PVStorageClassName, PVCreationTimestamp, Clusterid, ClusterName, _ResourceId, SourceSystem |
| Eventos de Kubernetes | API Kube | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Serviços no cluster Kubernetes | API Kube | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Métricas de desempenho para a parte de nós do cluster Kubernetes | As métricas de uso são obtidas de cAdvisor e limites da API Kube | `Perf \| where ObjectName == "K8SNode"` | Computer, ObjectName, CounterName &#40;cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, myValue, TimeGenerated, dePath, SourceSystem | 
| Métricas de desempenho para a parte de contêineres do cluster Kubernetes | As métricas de uso são obtidas de cAdvisor e limites da API Kube | `Perf \| where ObjectName == "K8SContainer"` | CounterName &#40;cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, myValue, TimeGenerated, dePath, SourceSystem | 
| Métricas personalizadas ||`InsightsMetrics` | Computador, nome, namespace, origem, SourceSystem, marcas<sup>1</sup>, TimeGenerated, tipo, Va, _ResourceId | 

<sup>1</sup> a propriedade *Tags* representa [várias dimensões](../essentials/data-platform-metrics.md#multi-dimensional-metrics) para a métrica correspondente. Para obter mais informações sobre as métricas coletadas e armazenadas na `InsightsMetrics` tabela e uma descrição das propriedades do registro, consulte [visão geral do InsightsMetrics](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

## <a name="search-logs-to-analyze-data"></a>Pesquisar logs para analisar dados

Os logs de Azure Monitor podem ajudá-lo a procurar tendências, diagnosticar afunilamentos, prever ou correlacionar dados que podem ajudá-lo a determinar se a configuração atual do cluster está sendo executada de forma ideal. Pesquisas de logs predefinidas são fornecidas para você começar a usar imediatamente ou para personalizar para retornar as informações da maneira que você deseja.

Você pode analisar dados interativamente no espaço de trabalho selecionando a opção **Exibir logs de eventos kubernetes** ou **Exibir logs de contêiner** no painel de visualização na lista suspensa **exibição na análise** . A página **Pesquisa de Logs** aparece acima da página de portal do Azure em que você estava.

![Análise de dados no Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)

A saída dos logs de contêiner que é encaminhada para seu espaço de trabalho são STDOUT e STDERR. Como Azure Monitor está monitorando kubernetes (AKS) gerenciados pelo Azure, o Kube-System não é coletado hoje devido ao grande volume de dados gerados. 

### <a name="example-log-search-queries"></a>Exemplo de consultas de pesquisa de log

Costuma ser útil criar consultas começando com um ou dois exemplos e, em seguida, modificá-las de acordo com seus requisitos. Para ajudar a criar consultas mais avançadas, você pode experimentar as seguintes consultas de amostra:

### <a name="list-all-of-a-containers-lifecycle-information"></a>Listar todas as informações do ciclo de vida do contêiner

```kusto
ContainerInventory
| project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime
| render table
```

### <a name="kubernetes-events"></a>Eventos de Kubernetes

``` kusto
KubeEvents_CL
| where not(isempty(Namespace_s))
| sort by TimeGenerated desc
| render table
```
### <a name="image-inventory"></a>Inventário de imagem

``` kusto
ContainerImageInventory
| summarize AggregatedValue = count() by Image, ImageTag, Running
```

### <a name="container-cpu"></a>CPU do contêiner

**Selecione a opção de exibição de gráfico de linhas**

``` kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" 
| summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName 
```

### <a name="container-memory"></a>Memória de contêiner

**Selecione a opção de exibição de gráfico de linhas**

```kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes"
| summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName
```

### <a name="requests-per-minute-with-custom-metrics"></a>Solicitações por minuto com métricas personalizadas

```kusto
InsightsMetrics
| where Name == "requests_count"
| summarize Val=any(Val) by TimeGenerated=bin(TimeGenerated, 1m)
| sort by TimeGenerated asc<br> &#124; project RequestsPerMinute = Val - prev(Val), TimeGenerated
| render barchart 
```

## <a name="query-prometheus-metrics-data"></a>Consultar dados de métricas do Prometheus

O exemplo a seguir é uma consulta de métricas Prometheus mostrando leituras de disco por segundo por disco por nó.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

Para exibir as métricas de Prometheus recortadas por Azure Monitor filtradas pelo namespace, especifique "Prometheus". Aqui está um exemplo de consulta para exibir as métricas de Prometheus do `default` namespace kubernetes.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Os dados de Prometheus também podem ser consultados diretamente por nome.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Erros de configuração de consulta ou de sucata

Para investigar qualquer configuração ou erros de recorte, a consulta de exemplo a seguir retorna eventos informativos da `KubeMonAgentEvents` tabela.

```
KubeMonAgentEvents | where Level != "Info" 
```

A saída mostra resultados semelhantes ao exemplo a seguir:

![Registrar resultados de consulta de eventos informativos do agente](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Próximas etapas

As informações de contêiner não incluem um conjunto predefinido de alertas. Examine [criar alertas de desempenho com o contêiner insights](./container-insights-log-alerts.md) para saber como criar alertas recomendados para alta utilização de CPU e memória para dar suporte aos processos e procedimentos operacionais ou DevOps.