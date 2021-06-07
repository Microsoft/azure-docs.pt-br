---
title: Tutorial – Monitorar métricas no nível do aplicativo Apache Spark com Prometheus e Grafana
description: Tutorial – Saiba como implantar a solução de métricas do aplicativo Apache Spark em um cluster do AKS (Serviço de Kubernetes do Azure) e saiba como integrar os painéis do Grafana.
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: 6a0b63dc7fda25e3911ae713a0bea7ae7a0969f9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602291"
---
# <a name="tutorial-monitor-apache-spark-application-level-metrics-with-prometheus-and-grafana"></a>Tutorial: monitorar métricas no nível do aplicativo Apache Spark com Prometheus e Grafana

## <a name="overview"></a>Visão geral

Neste tutorial, você aprenderá a implantar a solução de métricas do aplicativo Apache Spark em um cluster do AKS (Serviço de Kubernetes do Azure) e aprenderá a integrar os painéis do Grafana.

Você pode usar essa solução para coletar e consultar os dados de métricas do Apache Spark quase em tempo real. Os painéis integrados do Grafana permitem diagnosticar e monitorar o aplicativo Apache Spark. O código-fonte e as configurações foram transformados em software livre no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

1.  [CLI do Azure](/cli/azure/install-azure-cli)
2.  [Helm client 3.30+](https://github.com/helm/helm/releases)
3.  [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
4.  [AKS (Serviço de Kubernetes do Azure)](https://azure.microsoft.com/en-us/services/kubernetes-service/)

Ou use o [Azure Cloud Shell](https://shell.azure.com/), que já inclui a CLI do Azure, o Helm client e o kubectl prontos para uso.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

```bash
az login
az account set --subscription "<subscription_id>"
```

## <a name="create-an-azure-kubernetes-service-instance-aks"></a>Criar uma instância de AKS (Serviço de Kubernetes do Azure)

Use o comando da CLI do Azure para criar um cluster de Kubernetes na assinatura.

```
az aks create --name <kubernetes_name> --resource-group <kubernetes_resource_group> --location <location> --node-vm-size Standard_D2s_v3
az aks get-credentials --name <kubernetes_name> --resource-group <kubernetes_resource_group>
```

Observação: esta etapa poderá ser ignorada se você já tiver um cluster de AKS.

## <a name="create-a-service-principal-and-grant-permission-to-synapse-workspace"></a>Criar uma entidade de serviço e conceder permissão para o workspace do Synapse

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

O resultado deve ter esta aparência:

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

Anote a appId, senha e tenantID.

[![captura de tela da concessão de permissão do SRBAC](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png#lightbox)

1. Faça logon no [workspace do Azure Synapse Analytics](https://web.azuresynapse.net/) como administrador do Synapse

2. No Synapse Studio, no painel do lado esquerdo, selecione **Gerenciar > Controle de acesso**

3. Clique no botão Adicionar na parte superior esquerda para **adicionar uma atribuição de função**

4. Para escopo, escolha **Workspace**

5. Para função, escolha **Operador de computação do Synapse**

6. Para selecionar usuário, insira o **<service_principal_name>** e clique na entidade de serviço

7. Clique em **Aplicar** (aguarde 3 minutos para que a permissão entre em vigor.)

## <a name="install-connector-prometheus-server-grafana-dashboard"></a>Instalar conector, servidor Prometheus, painel do Grafana

1. Adicione o repositório synapse-charts ao cliente Helm.

```bash
helm repo add synapse-charts https://github.com/microsoft/azure-synapse-spark-metrics/releases/download/helm-chart
```

2.  Instalar componentes por meio do cliente Helm:

```bash
helm install spo synapse-charts/synapse-prometheus-operator --create-namespace --namespace spo \
    --set synapse.workspaces[0].workspace_name="<workspace_name>" \
    --set synapse.workspaces[0].tenant_id="<tenant_id>" \
    --set synapse.workspaces[0].service_principal_name="<service_principal_app_id>" \
    --set synapse.workspaces[0].service_principal_password="<service_principal_password>" \
    --set synapse.workspaces[0].subscription_id="<subscription_id>" \
    --set synapse.workspaces[0].resource_group="<workspace_resource_group_name>"
```

 - workspace_name: nome do workspace do Synapse.
 - subscription_id: ID da assinatura do workspace do Synapse.
 - workspace_resource_group_name: nome do grupo de recursos do workspace do Synapse.
 - tenant_id: ID de locatário do workspace do Synapse.
 - service_principal_app_id: a "appId" da entidade de serviço
 - service_principal_password: a senha da entidade de serviço criada.

## <a name="log-in-to-grafana"></a>Fazer logon no Grafana

Obtenha a senha e o endereço padrão do Grafana. Você pode alterar a senha nas configurações do Grafana.

```bash
kubectl get secret --namespace spo spo-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
kubectl -n spo get svc spo-grafana
```

Obtenha o IP de serviço, copie e cole o IP externo no navegador e faça logon com o nome de usuário "admin" e a senha.

## <a name="use-grafana-dashboards"></a>Use os painéis do Grafana

Encontre o painel do Synapse no canto superior esquerdo da página Grafana (Página Inicial -> Workspace do Synapse/Aplicativo Synapse), tente executar um código de exemplo no Synapse Studio e aguarde alguns segundos para a extração de métricas.

Além disso, você pode usar os painéis "Workspace do Synapse/Workspace" e "Workspace do Synapse/Pools do Spark" para obter uma visão geral do workspace e dos pools do Apache Spark.

## <a name="uninstall"></a>Desinstalar

Remova os componentes usando o comando Helm da seguinte maneira.

```bash
helm delete <release_name> -n <namespace>
```

Exclua o cluster do AKS.

```bash
az aks delete --name <kubernetes_cluster_name> --resource-group <kubernetes_cluster_rg>
```

## <a name="components-introduction"></a>Introdução aos componentes

O Azure Synapse Analytics fornece um [gráfico do Helm](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm) com base no operador do Prometheus e no conector do Synapse Prometheus. O gráfico do Helm inclui o servidor Prometheus, o servidor Grafana e os painéis do Grafana para métricas no nível de aplicativo do Apache Spark. Você pode usar o [Prometheus](https://prometheus.io/), um sistema de monitoramento de software livre popular, para coletar essas métricas quase em tempo real e usar o [Grafana](https://github.com/grafana/grafana) para visualização.

### <a name="synapse-prometheus-connector"></a>Conector do Synapse Prometheus

O conector do Synapse Prometheus ajuda a conectar o pool do Apache Spark do Azure Synapse e o servidor Prometheus. Ele implementa:

1.  Autenticação: é a autenticação baseada no AAD e pode atualizar automaticamente o token do AAD da entidade de serviço para descoberta de aplicativos, ingestão de métricas e outras funções.
2.  Descoberta de aplicativos do Apache Spark: quando você envia aplicativos no workspace de destino, o conector do Synapse Prometheus pode descobrir esses aplicativos automaticamente.
3.  Metadados de aplicativo do Apache Spark: coleta as informações básicas do aplicativo e exporta os dados para o Prometheus.

O conector do Synapse Prometheus é lançado como imagem do Docker hospedada no [Microsoft Container Registry](https://github.com/microsoft/containerregistry). É software livre e está localizado nas [métricas do aplicativo Azure Synapse Spark](https://github.com/microsoft/azure-synapse-spark-metrics).

### <a name="prometheus-server"></a>Servidor Prometheus

O Prometheus é um kit de ferramentas de monitoramento e alerta de software livre. O Prometheus se formou na CNCF (Cloud Native Computing Foundation) e se tornou o padrão de fato para monitoramento nativo da nuvem. O Prometheus pode ajudar a coletar, consultar e armazenar grandes volumes de dados de série temporal e pode ser facilmente integrado com o Grafana. Nesta solução, implantamos o componente Prometheus com base no gráfico do Helm.

### <a name="grafana-and-dashboards"></a>Grafana e painéis

O Grafana é um software de análise e visualização de software livre. Permite consultar, Visualizar, alertar e explorar as métricas. O Azure Synapse Analytics fornece um conjunto de painéis padrão do Grafana para visualizar as métricas no nível do aplicativo Apache Spark.

O painel "Workspace do Synapse/Workspace" fornece uma exibição no nível do workspace de todos os pools do Apache Spark, contagens de aplicativos, núcleos de CPU, etc.

[![captura de tela do workspace do painel](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png#lightbox)

O painel "Workspace do Synapse/pools do Spark" contém as métricas dos aplicativos Apache Spark em execução no pool do Apache Spark selecionado durante o período.

[![captura de tela do pool Spark do painel](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png#lightbox)

O painel "Workspace do Synapse/aplicativo Spark" contém o aplicativo Apache Spark selecionado.

[![captura de tela do aplicativo do painel](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png#lightbox)

Os modelos de painel acima foram transformados em software livre nas [métricas do aplicativo Azure Synapse Spark](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm/synapse-prometheus-operator/grafana_dashboards).