---
title: Custo de monitoramento para Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve o custo de monitoramento para métricas & dados de inventário coletados por Azure Monitor para contêineres para ajudar os clientes a gerenciar o uso e os custos associados.
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 81a20f564af68c3da6d63394e4cffe7caed91b46
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903200"
---
# <a name="understand-monitoring-costs-for-azure-monitor-for-containers"></a>Entender os custos de monitoramento para Azure Monitor para contêineres

Este artigo fornece diretrizes de preços para Azure Monitor para contêineres para ajudá-lo a entender o seguinte:

* Como estimar os custos antecipadamente antes de habilitar essa percepção

* Como medir os custos depois que os Azure Monitor para contêineres tiverem sido habilitados para um ou mais contêineres

* Como controlar a coleta de dados e fazer reduções de custos

Os logs de Azure Monitor coletam, indexam e armazenam dados gerados pelo cluster kubernetes. 

O modelo de preços Azure Monitor se baseia principalmente na quantidade de dados ingeridos em gigabytes por dia em seu espaço de trabalho Log Analytics. O custo de um espaço de trabalho Log Analytics não se baseia apenas no volume de dados coletados; ele também depende do plano selecionado e por quanto tempo você optou por armazenar os dados gerados de seus clusters.

>[!NOTE]
>Todos os tamanhos e preços são apenas para estimativas de exemplo. Consulte a página de [preços](https://azure.microsoft.com/pricing/details/monitor/) do Azure monitor para obter os preços mais recentes com base em seu Azure monitor log Analytics modelo de preços e na região do Azure.

Veja a seguir um resumo de quais tipos de dados são coletados de um cluster kubernetes com Azure Monitor para contêineres que influenciam o custo e podem ser personalizados com base no seu uso:

- Stdout, logs de contêiner stderr de cada contêiner monitorado em cada namespace kubernetes no cluster

- Variáveis de ambiente de contêiner de cada contêiner monitorado no cluster

- Trabalhos kubernetes/pods concluídos no cluster que não exigem monitoramento

- Recorte ativo de métricas de Prometheus

- [Coleta de log de diagnóstico](../../aks/view-master-logs.md) dos logs do nó mestre do kubernetes no cluster do AKS para analisar os dados de log gerados por componentes mestres, como *Kube-apiserver* e *Kube-Controller-Manager*.

## <a name="what-is-collected-from-kubernetes-clusters"></a>O que é coletado de clusters kubernetes

Azure Monitor para contêineres inclui um conjunto predefinido de métricas e itens de estoque coletados que são gravados como dados de log em seu espaço de trabalho do Log Analytics. Todas as métricas listadas abaixo são coletadas por padrão a cada minuto.

### <a name="node-metrics-collected"></a>Métricas de nó coletadas

A lista a seguir é a 24 métricas por nó que são coletadas:

- cpuUsageNanoCores
- cpuCapacityNanoCores
- cpuAllocatableNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryCapacityBytes
- memoryAllocatableBytes
- restartTimeEpoch
- usado (disco)
- livre (disco)
- used_percent (disco)
- io_time (diskio)
- gravações (diskio)
- leituras (diskio)
- write_bytes (diskio)
- write_time (diskio)
- iops_in_progress (diskio)
- read_bytes (diskio)
- read_time (diskio)
- err_in (net)
- err_out (net)
- bytes_recv (net)
- bytes_sent (net)
- Kubelet_docker_operations (Kubelet)

### <a name="container-metrics"></a>Métricas de contêiner

A lista a seguir são as oito métricas por contêiner coletadas:

- cpuUsageNanoCores
- cpuRequestNanoCores
- cpuLimitNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryRequestBytes
- memoryLimitBytes
- restartTimeEpoch

### <a name="cluster-inventory"></a>Inventário de cluster

A lista a seguir é os dados de inventário de cluster coletados por padrão:

- KubePodInventory – 1 por minuto por contêiner
- KubeNodeInventory – 1 por nó por minuto
- KubeServices – 1 por serviço por minuto
- ContainerInventory – 1 por contêiner por minuto

## <a name="estimating-costs-to-monitor-your-aks-cluster"></a>Estimando custos para monitorar o cluster AKS

A estimativa abaixo se baseia em um cluster do AKS (serviço de kubernetes do Azure) com o seguinte exemplo de dimensionamento. Além disso, a estimativa se aplica somente a métricas e a dados de inventário coletados. Para logs de contêiner (variáveis stdout, stderr e ambientais), ele varia de acordo com os tamanhos de log gerados pela carga de trabalho e eles são excluídos da nossa estimativa.

Se você tiver habilitado o monitoramento de um cluster AKS configurado da seguinte maneira,

- Três nós
- Dois discos por nó
- Uma interface de rede por nó
- 20 pods (um contêiner em cada pod = 20 contêineres no total)
- Dois namespaces kubernetes
- Cinco serviços Kubernetess (inclui pods, serviços e namespace do sistema Kube)
- Frequência de coleta = 60 segundos (padrão)

Você pode ver as tabelas e o volume de dados gerados por hora no espaço de trabalho Log Analytics atribuído. Para obter mais informações sobre cada uma dessas tabelas, consulte [registros de contêiner](container-insights-log-search.md#container-records).

|Tabela | Estimativa de tamanho (MB/hora) |
|------|---------------|
|Perf | 12,9 |
|InsightsMetrics | 11.3 |
|KubePodInventory | 1.5 |
|KubeNodeInventory | 0,75 |
|KubeServices | 0.13 |
|ContainerInventory | 3,6 |
|KubeHealth | 0,1 |
|KubeMonAgentEvents |0, 5 |

Total = 31 MB/hora = 23,1 GB/mês (um mês = 31 dias)

Usando o [preço](https://azure.microsoft.com/pricing/details/monitor/) padrão para log Analytics, que é um modelo pago conforme o uso, você pode estimar o custo de Azure monitor por mês. Depois de incluir uma reserva de capacidade, o preço seria mais alto por mês, dependendo da reserva selecionada.

## <a name="controlling-ingestion-to-reduce-cost"></a>Controlando a ingestão para reduzir o custo

Considere um cenário em que a unidade de negócios diferente de sua organização compartilha a infraestrutura kubernetes e um espaço de trabalho Log Analytics. Com cada unidade de negócios separada por um namespace kubernetes. Você pode visualizar a quantidade de dados que é ingerida em cada espaço de trabalho usando o runbook de **uso de dados** que está disponível na lista suspensa **exibir pastas de trabalho** .

[![Menu suspenso exibir pastas de trabalho](media/container-insights-cost/workbooks-dropdown.png)](media/container-insights-cost/workbooks-dropdown.png#lightbox)


Esta pasta de trabalho ajuda a visualizar a origem dos seus dados sem precisar criar sua própria biblioteca de consultas do que compartilhamos em nossa documentação. Nesta pasta de trabalho, há gráficos com os quais você pode exibir dados faturáveis de tais perspectivas como:

- Total de dados faturáveis ingeridos em GB por solução
- Dados faturáveis ingeridos por logs de contêiner (logs de aplicativo)
- Logs de contêiner Faturável dados ingeridos por namespace kubernetes
- Logs de contêiner Faturável dados ingeridos, separados por nome de cluster
- Dados de log de contêiner Faturável ingeridos pela entrada LogSource
- Dados de diagnóstico Faturável ingeridos por logs de nó mestre de diagnóstico

[![Pasta de trabalho de uso de dados](media/container-insights-cost/data-usage-workbook.png)](media/container-insights-cost/data-usage-workbook.png#lightbox)

Para saber mais sobre como gerenciar direitos e permissões para a pasta de trabalho, examine o [controle de acesso](../platform/workbooks-access-control.md).

Depois de concluir a análise para determinar qual fonte ou fontes estão gerando a maior parte dos dados ou mais dados que estão excedendo seus requisitos, você pode reconfigurar a coleta de dados. Detalhes sobre como configurar a coleta de variáveis de stdout, stderr e ambientais são descritos no artigo [definir configurações de coleta de dados de agente](container-insights-agent-config.md) .

Veja a seguir exemplos de quais alterações você pode aplicar ao cluster modificando o arquivo ConfigMap para ajudar a controlar o custo.

1. Desabilite os logs de stdout em todos os namespaces no cluster modificando o seguinte no arquivo ConfigMap:

    ```
    [log_collection_settings]       
       [log_collection_settings.stdout]          
          enabled = false
    ```

2. Desabilite a coleta de logs stderr do seu namespace de desenvolvimento (por exemplo, **dev-Test**) e continue coletando logs stderr de outros namespaces (por exemplo, **prod** e **Default**) modificando o seguinte no arquivo ConfigMap:

    >[!NOTE]
    >A coleta de log Kube é desabilitada por padrão. A configuração padrão é mantida, adicionando o namespace **dev-Test** à lista de namespaces de exclusão é aplicado à coleta de log stderr.

    ```
    [log_collection_settings.stderr]          
       enabled = true          
          exclude_namespaces = ["kube-system", "dev-test"]
    ```

3. Desabilite a coleção de variáveis de ambiente em todo o cluster modificando o seguinte no arquivo ConfigMap. Isso é aplicável a todos os contêineres em todos os namespaces kubernetes. 

    ```
    [log_collection_settings.env_var]
        enabled = false
    ```

4. Para limpar os trabalhos concluídos, especifique a política de limpeza na definição de trabalho modificando o seguinte no arquivo ConfigMap:

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi-with-ttl
    spec:
      ttlSecondsAfterFinished: 100
    ```

Depois de aplicar uma ou mais dessas alterações ao seu ConfigMaps, consulte [aplicando o ConfigMap atualizado](container-insights-prometheus-integration.md#applying-updated-configmap) para aplicá-lo ao cluster.

### <a name="prometheus-metrics-scraping"></a>Recorte de métricas de Prometheus

Se você estiver utilizando a [recorte de métrica Prometheus](container-insights-prometheus-integration.md), certifique-se de considerar o seguinte para limitar o número de métricas coletadas do seu cluster:

- Verifique se a frequência de sucata está definida de forma ideal (o padrão é 60 segundos). Embora seja possível aumentar a frequência para 15 segundos, você precisa garantir que as métricas que você está recortando sejam publicadas nessa frequência. Caso contrário, haverá muitas métricas duplicadas resumidas e enviadas ao seu espaço de trabalho de Log Analytics em intervalos que agregam a ingestão de dados e custos de retenção, mas são de menor valor. 

- Azure Monitor para contêineres dá suporte à exclusão & listas de inclusão por nome da métrica. Por exemplo, se você estiver dispensando métricas de **kubedns** em seu cluster, pode haver centenas delas que são recortadas por padrão, mas você provavelmente está interessado apenas em um subconjunto. Confirme que você especificou uma lista de métricas para sucata ou exclua outras, exceto algumas para salvar no volume de ingestão de dados. É fácil habilitar a recorte e não usar muitas dessas métricas, o que adicionará somente encargos adicionais à sua fatura de Log Analytics.

- Ao recorrer a anotações de Pod, certifique-se de filtrar por namespace para que você exclua a recorte de métricas de pod de namespaces que você não usa (por exemplo, o namespace **dev-Test** ).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como entender quais são os custos prováveis com base nos padrões de uso recentes dos dados coletados com Azure Monitor para contêineres, consulte [gerenciar seus custos de uso e de estimativa](../platform/manage-cost-storage.md).