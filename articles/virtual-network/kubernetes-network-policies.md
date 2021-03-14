---
title: Políticas de rede do Kubernetes do Azure | Microsoft Docs
description: Saiba mais sobre as políticas de rede kubernetes para proteger o cluster kubernetes.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: a68e1a3f60930e290e97084ff2ec9350b18e2873
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594978"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Visão geral das políticas de rede kubernetes do Azure

As políticas de rede fornecem a microsegmentação para pods, assim como NSGs (grupos de segurança de rede) fornecem a microsegmentação para VMs. A implementação do Azure Network Policy Manager (também conhecida como Azure NPM) dá suporte à especificação de política de rede kubernetes padrão. Você pode usar rótulos para selecionar um grupo de pods e definir uma lista de regras de entrada e saída para filtrar o tráfego de e para esses pods. Saiba mais sobre as políticas de rede do Kubernetes na [documentação do Kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Visão geral das políticas de rede do Kubernetes](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

A implementação do Azure NPM funciona em conjunto com o CNI do Azure que fornece integração VNet para contêineres. O NPM tem suporte apenas no Linux hoje. A implementação impõe a filtragem de tráfego configurando as regras de permissão e negação de IP no Linux IPTables com base nas políticas definidas. Essas regras são agrupadas usando o Linux IPSets.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Planejar a segurança para o cluster Kubernetes
Ao implementar a segurança para o cluster, use NSGs (grupos de segurança de rede) para filtrar o tráfego que entra e sai da sub-rede do cluster (tráfego Norte-Sul). Use o NPM do Azure para o tráfego entre pods no seu cluster (tráfego leste-oeste).

## <a name="using-azure-npm"></a>Usando o Azure NPM
O Azure NPM pode ser usado das seguintes maneiras para fornecer a microsegmentação para pods.

### <a name="azure-kubernetes-service-aks"></a>AKS (Serviço de Kubernetes do Azure)
O NPM está disponível nativamente no AKS e pode ser habilitado no momento da criação do cluster. Saiba mais sobre isso em [tráfego seguro entre pods usando as políticas de rede no serviço de kubernetes do Azure (AKs)](../aks/use-network-policies.md).

### <a name="aks-engine"></a>AKS-mecanismo
O AKS-Engine é uma ferramenta que gera um modelo do Azure Resource Manager para a implantação de um cluster Kubernetes no Azure. A configuração do cluster é especificada em um arquivo JSON que é passado para a ferramenta ao gerar o modelo. Para saber mais sobre a lista completa de configurações de cluster compatíveis e suas descrições, consulte Mecanismo do Serviço de Contêiner do Microsoft Azure – definição de cluster.

Para habilitar as políticas em clusters implantados usando o acs-engine, especifique o valor da configuração networkPolicy no arquivo de definição do cluster como "azure".

#### <a name="example-configuration"></a>Configuração de exemplo

A configuração de exemplo JSON abaixo cria uma rede virtual e uma sub-rede, e implanta um cluster Kubernetes nele com o Azure CNI. É recomendável que você use o "Bloco de notas do Windows" para editar o arquivo JSON. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="do-it-yourself-diy-kubernetes-clusters-in-azure"></a>Faça você mesmo (DIY) clusters kubernetes no Azure
 Para clusters DIY, primeiro instale o plug-in CNI e habilite-o em cada máquina virtual em um cluster. Para ver instruções detalhadas, confira [Implantar o plug-in em um cluster Kubernetes que você implanta por conta própria](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Depois que o cluster for implantado, execute o seguinte `kubectl` comando para baixar e aplicar o *daemon* NPM do Azure definido para o cluster.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
A solução também é um software livre e o código está disponível no [repositório da Rede de Contêiner do Azure](https://github.com/Azure/azure-container-networking/tree/master/npm).

## <a name="monitor-and-visualize-network-configurations-with-azure-npm"></a>Monitorar e visualizar as configurações de rede com o Azure NPM
O Azure NPM inclui métricas Prometheus informativas que permitem monitorar e entender melhor suas configurações. Ele fornece visualizações internas nos laboratórios portal do Azure ou Grafana. Você pode começar a coletar essas métricas usando Azure Monitor ou um servidor Prometheus.

### <a name="benefits-of-azure-npm-metrics"></a>Benefícios das métricas do Azure NPM
Os usuários anteriormente só podiam aprender sobre a configuração de rede com o comando `iptables -L` executado dentro de um nó de cluster, o que resulta em uma saída detalhada e difícil de entender. As métricas do NPM fornecem os seguintes benefícios relacionados às políticas de rede, regras de IPTables e IPSets.
- Fornece informações sobre a relação entre as três e uma dimensão de tempo para depurar uma configuração.
- Número de entradas em todos os IPSets e em cada IPSet.
- Tempo necessário para aplicar uma política com granularidade de nível de IPTable/IPSet.
 
### <a name="supported-metrics"></a>Métricas com suporte
A seguir está a lista de métricas com suporte:

|Nome da métrica |Descrição  |Tipo de métrica Prometheus  |Rótulos  |
|---------|---------|---------|---------|
|`npm_num_policies`     |número de políticas de rede          |Medidor         |-         |
|`npm_num_iptables_rules`     | número de regras IPTables     | Medidor        |-         |         
|`npm_num_ipsets`     |número de IPSets         |Medidor            |-         |
|`npm_num_ipset_entries`     |número de entradas de endereço IP em todos os IPSets         |Medidor         |-         |
|`npm_add_policy_exec_time`     |tempo de execução para adicionar uma política de rede         |Resumo         |Quantil (0,5, 0,9 ou 0,99)         |
|`npm_add_iptables_rule_exec_time`     |tempo de execução para adicionar uma regra IPTables         |Resumo         |Quantil (0,5, 0,9 ou 0,99)         |
|`npm_add_ipset_exec_time`     |tempo de execução para adicionar um IPSet         |Resumo         |Quantil (0,5, 0,9 ou 0,99)         |
|`npm_ipset_counts` avançadas     |número de entradas dentro de cada IPSet individual         |GaugeVec         |definir o nome & hash         |

Os diferentes níveis de Quantil em métricas de "exec_time" ajudam a diferenciar entre os cenários geral e pior caso.

Também há uma métrica "exec_time_count" e "exec_time_sum" para cada métrica de resumo "exec_time".

As métricas podem ser recortadas por meio de Azure Monitor para contêineres ou por meio de Prometheus.

### <a name="setup-for-azure-monitor"></a>Instalação para Azure Monitor
A primeira etapa é habilitar Azure Monitor para contêineres para o cluster kubernetes. As etapas podem ser encontradas na [visão geral de Azure monitor para contêineres](../azure-monitor/containers/container-insights-overview.md). Quando você tiver Azure Monitor para contêineres habilitados, configure o [Azure monitor para contêineres ConfigMap](https://aka.ms/container-azm-ms-agentconfig) para habilitar a integração NPM e a coleção de métricas NPM Prometheus. O Azure monitor para contêineres ConfigMap tem uma ```integrations``` seção com configurações para coletar métricas NPM. Essas configurações são desabilitadas por padrão no ConfigMap. Habilitar a configuração básica ```collect_basic_metrics = true``` coletará métricas de NPM básicas. Habilitar a configuração avançada ```collect_advanced_metrics = true``` coletará métricas avançadas, além de métricas básicas. 

Depois de editar o ConfigMap, salve-o localmente e aplique o ConfigMap ao cluster da seguinte maneira.

```kubectl apply -f container-azm-ms-agentconfig.yaml``` Abaixo está um trecho de código do [Azure monitor para contêineres ConfigMap](https://aka.ms/container-azm-ms-agentconfig), que mostra a integração do NPM habilitada com a coleção de métricas avançadas.
```
integrations: |-
    [integrations.azure_network_policy_manager]
        collect_basic_metrics = false
        collect_advanced_metrics = true
```
As métricas avançadas são opcionais e ligá-las ativará automaticamente a coleta básica de métricas. As métricas avançadas atualmente incluem apenas `npm_ipset_counts`

Saiba mais sobre o [Azure monitor para configurações de coleção de contêineres no mapa de configuração](../azure-monitor/containers/container-insights-agent-config.md)

### <a name="visualization-options-for-azure-monitor"></a>Opções de visualização para Azure Monitor
Quando a coleta de métricas do NPM estiver habilitada, você poderá exibir as métricas no portal do Azure usando o contêiner insights ou no Grafana.

#### <a name="viewing-in-azure-portal-under-insights-for-the-cluster"></a>Exibindo no portal do Azure em insights para o cluster
Abra o portal do Azure. Uma vez nas informações do cluster, navegue até "pastas de trabalho" e abra "configuração do Gerenciador de políticas de rede (NPM)".

Além de exibir a pasta de trabalho (imagens abaixo), você também pode consultar diretamente as métricas de Prometheus em "logs" na seção insights. Por exemplo, essa consulta retornará todas as métricas que estão sendo coletadas.
| onde o TimeGenerated > atrás (5h) | em que nome contém "npm_"

Você também pode consultar Log Analytics diretamente para as métricas. Saiba mais sobre isso com [Introdução log Analytics consultas](../azure-monitor/containers/container-insights-log-search.md) 

#### <a name="viewing-in-grafana-dashboard"></a>Exibindo no painel do Grafana
Configure o servidor Grafana e configure uma fonte de dados Log Analytics conforme descrito [aqui](https://grafana.com/grafana/plugins/grafana-azure-monitor-datasource). Em seguida, importe o [painel do Grafana com um log Analytics back-end](https://grafana.com/grafana/dashboards/10956) para seus Grafana Labs.

O painel tem visuais semelhantes à pasta de trabalho do Azure. Você pode adicionar painéis ao gráfico & Visualizar métricas NPM da tabela InsightsMetrics.

### <a name="setup-for-prometheus-server"></a>Instalação do servidor Prometheus
Alguns usuários podem optar por coletar métricas com um servidor Prometheus em vez de Azure Monitor para contêineres. Você simplesmente precisa adicionar dois trabalhos à sua configuração de recorte para coletar métricas de NPM.

Para instalar um servidor Prometheus simples, adicione este repositório do Helm no cluster
```
helm repo add stable https://kubernetes-charts.storage.googleapis.com
helm repo update
```
em seguida, adicione um servidor
```
helm install prometheus stable/prometheus -n monitoring \
--set pushgateway.enabled=false,alertmanager.enabled=false, \
--set-file extraScrapeConfigs=prometheus-server-scrape-config.yaml
```
onde `prometheus-server-scrape-config.yaml` consiste em
```
- job_name: "azure-npm-node-metrics"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: node
  relabel_configs:
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
- job_name: "azure-npm-cluster-metrics"
  metrics_path: /cluster-metrics
  kubernetes_sd_configs:
  - role: service
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_service_name]
    regex: npm-metrics-cluster-service
    action: keep
# Comment from here to the end to collect advanced metrics: number of entries for each IPSet
  metric_relabel_configs:
  - source_labels: [__name__]
    regex: npm_ipset_counts
    action: drop
```


Você também pode substituir o `azure-npm-node-metrics` trabalho pelo conteúdo abaixo ou incorporá-lo a um trabalho preexistente para o kubernetes pods:
```
- job_name: "azure-npm-node-metrics-from-pod-config"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: pod
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_pod_annotationpresent_azure_npm_scrapeable]
    action: keep
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
```

### <a name="visualization-options-for-prometheus"></a>Opções de visualização para Prometheus
Ao usar um Prometheus Server, somente o painel Grafana tem suporte. 

Se você ainda não fez isso, configure o servidor Grafana e configure uma fonte de dados Prometheus. Em seguida, importe nosso [painel do Grafana com um back-end Prometheus](https://grafana.com/grafana/dashboards/13000) para seus laboratórios de Grafana.

Os elementos visuais desse painel são idênticos ao painel com um back-end do contêiner/Log Analytics backend.

### <a name="sample-dashboards"></a>Painéis de exemplo
A seguir estão alguns painéis de exemplo para métricas de NPM em informações de contêiner (CI) e Grafana

#### <a name="ci-summary-counts"></a>Contagens de Resumo de CI
![Contagens de resumo da pasta de trabalho do Azure](media/kubernetes-network-policies/workbook-summary-counts.png)

#### <a name="ci-counts-over-time"></a>Contagens de CI ao longo do tempo
[![Contagens da pasta de trabalho do Azure ao longo do tempo](media/kubernetes-network-policies/workbook-counts-over-time.png)](media/kubernetes-network-policies/workbook-counts-over-time.png#lightbox)

#### <a name="ci-ipset-entries"></a>Entradas de CI IPSet
[![Entradas de IPSet da pasta de trabalho do Azure](media/kubernetes-network-policies/workbook-ipset-entries.png)](media/kubernetes-network-policies/workbook-ipset-entries.png#lightbox)

#### <a name="ci-runtime-quantiles"></a>Quantis de tempo de execução de CI
![Quantis de tempo de execução da pasta do Azure](media/kubernetes-network-policies/workbook-runtime-quantiles.png)

#### <a name="grafana-dashboard-summary-counts"></a>Contagens de resumo do painel Grafana
![Contagens de resumo do painel Grafana](media/kubernetes-network-policies/grafana-summary-counts.png)

#### <a name="grafana-dashboard-counts-over-time"></a>O painel do Grafana conta com o tempo
[![O painel do Grafana conta com o tempo](media/kubernetes-network-policies/grafana-counts-over-time.png)](media/kubernetes-network-policies/grafana-counts-over-time.png#lightbox)

#### <a name="grafana-dashboard-ipset-entries"></a>Entradas do painel do Grafana IPSet
[![Entradas do painel do Grafana IPSet](media/kubernetes-network-policies/grafana-ipset-entries.png)](media/kubernetes-network-policies/grafana-ipset-entries.png#lightbox)

#### <a name="grafana-dashboard-runtime-quantiles"></a>Quantis de tempo de execução do Grafana Dashboard
[![Quantis de tempo de execução do Grafana Dashboard](media/kubernetes-network-policies/grafana-runtime-quantiles.png)](media/kubernetes-network-policies/grafana-runtime-quantiles.png#lightbox)



## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [Serviço de Kubernetes do Azure](../aks/intro-kubernetes.md).
-  Saiba mais sobre [redes de contêiner](container-networking-overview.md).
- [Implante o plug-in](deploy-container-networking.md) para clusters kubernetes ou contêineres do Docker.

