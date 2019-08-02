---
title: Configurar Azure Monitor para coleta de dados de agente de contêineres | Microsoft Docs
description: Este artigo descreve como você pode configurar o Azure Monitor para agente de contêineres para controlar stdout/stderr e a coleção de logs de variáveis de ambiente.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: 12010aaa7bc90bd200264549ad3efb79f46576c6
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867679"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Configurar a coleta de dados do agente para Azure Monitor para contêineres

Azure Monitor para contêineres coleta variáveis stdout, stderr e ambientais de cargas de trabalho de contêiner implantadas em clusters kubernetes gerenciados hospedados no AKS (serviço kubernetes do Azure) do agente em contêiner. Esse agente também pode coletar dados de série temporal (também conhecidos como métricas) do Prometheus usando o agente em contêiner sem precisar configurar e gerenciar um servidor e um banco de dados do Prometheus. Você pode definir as configurações de coleta de dados do agente criando um ConfigMaps kubernetes personalizado para controlar essa experiência. 

Este artigo demonstra como criar ConfigMap e configurar a coleta de dados com base em seus requisitos.

>[!NOTE]
>O suporte para Prometheus é um recurso em visualização pública no momento.
>

## <a name="configure-your-cluster-with-custom-data-collection-settings"></a>Configurar o cluster com configurações personalizadas de coleta de dados

Um arquivo de modelo ConfigMap é fornecido para que você possa editá-lo facilmente com suas personalizações sem precisar criá-lo do zero. Antes de começar, você deve examinar a documentação do kubernetes sobre [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) e se familiarizar com como criar, configurar e implantar o ConfigMaps. Isso permitirá que você filtre stderr e stdout por namespace ou por todo o cluster e variáveis de ambiente para qualquer contêiner em execução em todos os pods/nós no cluster.

>[!IMPORTANT]
>A versão mínima do agente com suporte para coletar variáveis stdout, stderr e ambientais de cargas de trabalho de contêiner é ciprod06142019 ou posterior. A versão mínima do agente com suporte para métricas de Prometheus de sucata é ciprod07092019 ou posterior. Para verificar a versão do agente, na guia **nó** , selecione um nó e, no painel Propriedades, observe o valor da propriedade **marca da imagem do agente** .  

### <a name="overview-of-configurable-data-collection-settings"></a>Visão geral das configurações de coleta de dados configuráveis

A seguir estão as configurações que podem ser definidas para controlar a coleta de dados.

|Chave |Tipo de dados |Valor |DESCRIÇÃO |
|----|----------|------|------------|
|`schema-version` |Cadeia de caracteres (diferencia maiúsculas de minúsculas) |v1 |Esta é a versão do esquema usada pelo agente ao analisar este ConfigMap. A versão de esquema com suporte atualmente é v1. Não há suporte para a modificação desse valor e ele será rejeitado quando ConfigMap for avaliado.|
|`config-version` |Cadeia de caracteres | | Dá suporte à capacidade de controlar a versão deste arquivo de configuração no sistema/repositório do controle do código-fonte. Os caracteres máximos permitidos são 10 e todos os outros caracteres são truncados. |
|`[log_collection_settings.stdout] enabled =` |Boolean | true ou false | Isso controla se a coleta de log de contêiner stdout está habilitada. Quando definido como `true` e nenhum namespace é excluído para coleta de log de stdout`log_collection_settings.stdout.exclude_namespaces` (configuração abaixo), os logs de stdout serão coletados de todos os contêineres em todos os pods/nós no cluster. Se não for especificado em ConfigMaps, o valor padrão `enabled = true`será. |
|`[log_collection_settings.stdout] exclude_namespaces =`|Cadeia de caracteres | Matriz separada por vírgulas |Matriz de namespaces kubernetes para os quais os logs de stdout não serão coletados. Essa configuração só será eficaz se `log_collection_settings.stdout.enabled` o for definido `true`como. Se não for especificado em ConfigMap, o valor padrão `exclude_namespaces = ["kube-system"]`será.|
|`[log_collection_settings.stderr] enabled =` |Boolean | true ou false |Isso controla se a coleta de log de contêiner stderr está habilitada. Quando definido como `true` e nenhum namespace é excluído para coleta de log de stdout`log_collection_settings.stderr.exclude_namespaces` (configuração), os logs de stderr serão coletados de todos os contêineres em todos os pods/nós no cluster. Se não for especificado em ConfigMaps, o valor padrão `enabled = true`será. |
|`[log_collection_settings.stderr] exclude_namespaces =` |Cadeia de caracteres |Matriz separada por vírgulas |Matriz de namespaces kubernetes para os quais os logs de stderr não serão coletados. Essa configuração só será eficaz se `log_collection_settings.stdout.enabled` o for definido `true`como. Se não for especificado em ConfigMap, o valor padrão `exclude_namespaces = ["kube-system"]`será. |
| `[log_collection_settings.env_var] enabled =` |Boolean | true ou false | Isso controla se a coleção de variáveis de ambiente está habilitada. Quando definido como `false`, nenhuma variável de ambiente é coletada para qualquer contêiner em execução em todos os pods/nós no cluster. Se não for especificado em ConfigMap, o valor padrão `enabled = true`será. |

## <a name="overview-of-configurable-prometheus-scraping-settings"></a>Visão geral das configurações de recorte de Prometheus configuráveis

A recorte ativa de métricas de Prometheus são executadas de uma das duas perspectivas:

* URL de todo o cluster-HTTP e descobrir destinos de pontos de extremidade listados de um serviço, serviços k8ss como Kube-DNS e Kube-State-métricas e anotações de Pod específicas para um aplicativo. As métricas coletadas neste contexto serão definidas na seção ConfigMap *[Prometheus data_collection_settings. cluster]* .
* URL em todo o nó-HTTP e descobrir destinos de pontos de extremidade listados de um serviço. As métricas coletadas neste contexto serão definidas na seção ConfigMap *[Prometheus_data_collection_settings. Node]* .

|Escopo | Chave | Tipo de dados | Valor | DESCRIÇÃO |
|------|-----|-----------|-------|-------------|
| Em todo o cluster | | | | Especifique qualquer um dos três métodos a seguir para recorte de pontos de extremidade para métricas. |
| | `urls` | Cadeia de caracteres | Matriz separada por vírgulas | Ponto de extremidade HTTP (endereço IP ou caminho de URL válido especificado). Por exemplo: `urls=[$NODE_IP/metrics]`. ($NODE _IP é um Azure Monitor específico para o parâmetro containers e pode ser usado em vez do endereço IP do nó. Deve estar tudo em maiúsculas.) |
| | `kubernetes_services` | Cadeia de caracteres | Matriz separada por vírgulas | Uma matriz de serviços Kubernetess para recorte de métricas de métricas de Kube-State. Por exemplo,`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Boolean | true ou false | Quando definido como `true` nas configurações de todo o cluster, Azure monitor para o agente de contêineres irá recriar o pods kubernetes em todo o cluster para as seguintes anotações Prometheus:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boolean | true ou false | Habilita a recorte do pod. |
| | `prometheus.io/scheme` | Cadeia de caracteres | http ou https | O padrão é a sucateação sobre HTTP. Se necessário, defina como `https`. | 
| | `prometheus.io/path` | Cadeia de caracteres | Matriz separada por vírgulas | O caminho do recurso HTTP para o qual buscar métricas. Se o caminho de métrica não `/metrics`for, defina-o com esta anotação. |
| | `prometheus.io/port` | Cadeia de caracteres | 9102 | Especifique uma porta para escuta. Se a porta não estiver definida, o padrão será 9102. |
| Em todo o nó | `urls` | Cadeia de caracteres | Matriz separada por vírgulas | Ponto de extremidade HTTP (endereço IP ou caminho de URL válido especificado). Por exemplo: `urls=[$NODE_IP/metrics]`. ($NODE _IP é um Azure Monitor específico para o parâmetro containers e pode ser usado em vez do endereço IP do nó. Deve estar tudo em maiúsculas.) |
| Em todo o nó ou cluster | `interval` | Cadeia de caracteres | 60 s | O padrão de intervalo de coleta é de um minuto (60 segundos). Você pode modificar a coleção para *[prometheus_data_collection_settings. Node]* e/ou *[prometheus_data_collection_settings. cluster]* para unidades de tempo como ns, US (ou Âμs), MS, s, m, h. |
| Em todo o nó ou cluster | `fieldpass`<br> `fielddrop`| Cadeia de caracteres | Matriz separada por vírgulas | Você pode especificar determinadas métricas a serem coletadas ou não no ponto de extremidade, definindo a`fieldpass`listagem Allow ()`fielddrop`e inallow (). Você deve definir a lista de permissões primeiro. |

ConfigMap é uma lista global e pode haver apenas um ConfigMap aplicado ao agente. Você não pode ter outro ConfigMap que se refaça com as coleções.

### <a name="configure-and-deploy-configmaps"></a>Configurar e implantar ConfigMaps

Execute as etapas a seguir para configurar e implantar o arquivo de configuração do ConfigMap no cluster.

1. [Baixe](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) o arquivo do modelo ConfigMap YAML e salve-o como contêiner-AZM-MS-agentconfig. YAML.  
1. Edite o arquivo ConfigMap YAML com suas personalizações.

    - Para excluir namespaces específicos para coleta de log de stdout, configure a chave/valor usando o exemplo a `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`seguir:.
    - Para desabilitar a coleção de variáveis de ambiente para um contêiner específico, defina a `[log_collection_settings.env_var] enabled = true` chave/o valor para habilitar a coleção de variáveis globalmente e siga as etapas [aqui](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) para concluir a configuração do contêiner específico.
    - Para desabilitar a coleção de logs stderr em todo o cluster, configure a chave/valor usando o exemplo `[log_collection_settings.stderr] enabled = false`a seguir:.

1. Crie ConfigMap executando o seguinte comando kubectl: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Exemplo: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    A alteração de configuração pode levar alguns minutos para ser concluída antes de entrar em vigor, e todos os pods de omsagent no cluster serão reiniciados. A reinicialização é uma reinicialização sem interrupção para todos os pods omsagent, nem todas as reinicializações ao mesmo tempo. Quando as reinicializações forem concluídas, será exibida uma mensagem semelhante à seguinte e inclui o resultado: `configmap "container-azm-ms-agentconfig" created`.

Para verificar se a configuração foi aplicada com êxito, use o seguinte comando para examinar os logs de um pod do `kubectl logs omsagent-fdf58 -n=kube-system`agente:. Se houver erros de configuração do pods omsagent, a saída mostrará erros semelhantes ao seguinte:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Os erros relacionados à aplicação de alterações de configuração para Prometheus também estão disponíveis para revisão.  Seja dos logs de um pod de agente usando o mesmo `kubectl logs` comando ou de logs ao vivo. Os logs ao vivo mostram erros semelhantes ao seguinte:

```
2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
```

Os erros impedem que o omsagent analise o arquivo, fazendo com que ele seja reiniciado e use a configuração padrão. Depois de corrigir os erros em ConfigMap, salve o arquivo YAML e aplique o ConfigMaps atualizado executando o comando: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>Aplicando ConfigMap atualizado

Se você já tiver implantado um ConfigMap em seu cluster e quiser atualizá-lo com uma configuração mais recente, poderá simplesmente editar o arquivo ConfigMap que você usou anteriormente e, em seguida, aplicar usando o `kubectl apply -f <configmap_yaml_file.yaml`mesmo comando que antes,.

A alteração de configuração pode levar alguns minutos para ser concluída antes de entrar em vigor, e todos os pods de omsagent no cluster serão reiniciados. A reinicialização é uma reinicialização sem interrupção para todos os pods omsagent, nem todas as reinicializações ao mesmo tempo. Quando as reinicializações forem concluídas, será exibida uma mensagem semelhante à seguinte e inclui o resultado: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Verificando a versão do esquema

As versões de esquema de configuração com suporte estão disponíveis como uma anotação de Pod (versões de esquema) no pod omsagent. Você pode vê-los com o seguinte comando kubectl:`kubectl describe pod omsagent-fdf58 -n=kube-system`

A saída será exibida de forma semelhante à seguinte com a anotação Schema-Versions:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="review-prometheus-data-usage"></a>Examinar o uso de dados do Prometheus

Para identificar o volume de ingestão de cada tamanho de métrica em GB por dia para entender se ele é alto, a consulta a seguir é fornecida.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
A saída mostrará resultados semelhantes ao seguinte:

![Resultados da consulta de log do volume de ingestão de dados](./media/container-insights-agent-config/log-query-example-usage-03.png)

Para estimar o que cada métrica tamanho em GB é para um mês para entender se o volume de dados ingeridos recebidos no espaço de trabalho é alto, a consulta a seguir é fornecida.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

A saída mostrará resultados semelhantes ao seguinte:

![Resultados da consulta de log do volume de ingestão de dados](./media/container-insights-agent-config/log-query-example-usage-02.png)

Mais informações sobre como monitorar o uso de dados e analisar o custo estão disponíveis em [gerenciar o uso e os custos com os logs de Azure monitor](../platform/manage-cost-storage.md).

## <a name="next-steps"></a>Próximas etapas

Azure Monitor para contêineres não inclui um conjunto predefinido de alertas. Examine [criar alertas de desempenho com Azure monitor para contêineres](container-insights-alerts.md) para saber como criar alertas recomendados para alta utilização de CPU e memória para dar suporte aos processos e procedimentos operacionais ou DevOps.

- Para continuar aprendendo a usar o Azure Monitor e monitorar outros aspectos do cluster do AKS, confira [Exibir integridade do Serviço de Kubernetes do Azure](container-insights-analyze.md).

- Exiba [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas predefinidas e exemplos para avaliar ou personalizar para alertar, Visualizar ou analisar seus clusters.