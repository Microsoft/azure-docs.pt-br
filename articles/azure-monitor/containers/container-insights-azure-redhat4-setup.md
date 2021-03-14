---
title: Configurar o Azure Red Hat OpenShift v4. x com o contêiner insights | Microsoft Docs
description: Este artigo descreve como configurar o monitoramento de um cluster kubernetes com Azure Monitor hospedado no Azure Red Hat OpenShift versão 4 ou posterior.
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 02cb794463b965ebafef0b6861477dbf69227511
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506405"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-container-insights"></a>Configurar o Azure Red Hat OpenShift v4. x com informações de contêiner

As informações de contêiner fornecem uma experiência de monitoramento avançada para os clusters do AKS (serviço kubernetes do Azure) e do mecanismo do AKS. Este artigo descreve como obter uma experiência de monitoramento semelhante habilitando o monitoramento de clusters kubernetes hospedados no [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) versão 4. x.

>[!NOTE]
>O suporte para o Azure Red Hat OpenShift é um recurso em visualização pública no momento.
>

Você pode habilitar informações de contêiner para uma ou mais implantações existentes do Azure Red Hat OpenShift v4. x usando os métodos com suporte descritos neste artigo.

Para um cluster existente, execute esse [script de bash na CLI do Azure](/cli/azure/openshift#az-openshift-create&preserve-view=true).

## <a name="supported-and-unsupported-features"></a>Recursos com e sem suporte

O contêiner insights dá suporte ao monitoramento do Azure Red Hat OpenShift v4. x, conforme descrito em [visão geral do contêiner insights](container-insights-overview.md), com exceção dos seguintes recursos:

- Dados dinâmicos (visualização)
- [Coletando métricas](container-insights-update-metrics.md) de nós de cluster e pods e armazenando-as no banco de dados de métricas Azure monitor

## <a name="prerequisites"></a>Pré-requisitos

- O CLI do Azure versão 2.0.72 ou posterior  

- A ferramenta da CLI do [Helm 3](https://helm.sh/docs/intro/install/)

- [Versão 4 do bash](https://www.gnu.org/software/bash/)

- A ferramenta de linha de comando [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- Um [workspace do Log Analytics](../logs/design-logs-deployment.md).

    O insights de contêiner dá suporte a um espaço de trabalho Log Analytics nas regiões listadas em produtos do Azure [por região](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Para criar seu próprio espaço de trabalho, ele pode ser criado por meio de [Azure Resource Manager](../logs/resource-manager-workspace.md), por meio do [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)ou no [portal do Azure](../logs/quick-create-workspace.md).

- Para habilitar e acessar os recursos em informações de contêiner, você precisa ter, no mínimo, uma função de *colaborador* do Azure na assinatura do Azure e uma função [*colaborador de log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) no espaço de trabalho log Analytics, configurada com o contêiner insights.

- Para exibir os dados de monitoramento, você precisa ter [*log Analytics função leitor*](../logs/manage-access.md#manage-access-using-azure-permissions) no espaço de trabalho log Analytics, configurada com o contêiner insights.

## <a name="enable-monitoring-for-an-existing-cluster"></a>Habilitar o monitoramento de um cluster existente

Para habilitar o monitoramento de um cluster do Azure Red Hat OpenShift versão 4 ou posterior implantado no Azure usando o script bash fornecido, faça o seguinte:

1. Execute o seguinte comando para entrar no Azure:

    ```azurecli
    az login
    ```

1. Baixe e salve em uma pasta local o script que configura o cluster com o suplemento de monitoramento executando o seguinte comando:

    `curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script`

1. Conecte-se ao cluster de toa v4 usando as instruções em [tutorial: conectar-se a um cluster do Azure Red Hat OpenShift 4](../../openshift/tutorial-connect-cluster.md).


### <a name="integrate-with-an-existing-workspace"></a>Integrar com um espaço de trabalho existente

Nesta seção, você habilitará o monitoramento do cluster usando o script bash baixado anteriormente. Para integrar com um espaço de trabalho Log Analytics existente, comece identificando a ID de recurso completo do espaço de trabalho Log Analytics necessário para o `logAnalyticsWorkspaceResourceId` parâmetro e, em seguida, execute o comando para habilitar o suplemento de monitoramento no espaço de trabalho especificado.

Se você não tiver um espaço de trabalho para especificar, poderá pular para a seção [integrar com o espaço de trabalho padrão](#integrate-with-the-default-workspace) e deixar que o script crie um novo espaço de trabalho para você.

1. Liste todas as assinaturas às quais você tem acesso executando o seguinte comando:

    ```azurecli
    az account list --all -o table
    ```

    A saída terá a aparência a seguir:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. Copie o valor de **SubscriptionId**.

1. Alterne para a assinatura que hospeda o espaço de trabalho Log Analytics executando o seguinte comando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. Exiba a lista de espaços de trabalho em suas assinaturas no formato JSON padrão executando o seguinte comando:

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. Na saída, localize o nome do espaço de trabalho e copie a ID de recurso completo do espaço de trabalho Log Analytics sob a **ID** do campo.

1. Para habilitar o monitoramento, execute o comando a seguir. Substitua os valores dos `azureAroV4ClusterResourceId` parâmetros e `logAnalyticsWorkspaceResourceId` .

    ```bash
    export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>" 
    ```

    Aqui está o comando que você deve executar depois de preencher as 3 variáveis com comandos de exportação:

    `bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --workspace-id $logAnalyticsWorkspaceResourceId`

Depois de habilitar o monitoramento, pode levar cerca de 15 minutos para que você possa exibir as métricas de integridade do cluster.

### <a name="integrate-with-the-default-workspace"></a>Integrar com o espaço de trabalho padrão

Nesta seção, você habilita o monitoramento para o cluster do Azure Red Hat OpenShift v4. x usando o script bash que você baixou.

Neste exemplo, não é necessário criar previamente ou especificar um espaço de trabalho existente. Esse comando simplifica o processo para você criando um espaço de trabalho padrão no grupo de recursos padrão da assinatura do cluster, se ainda não existir uma na região.

O espaço de trabalho padrão criado está no formato *defaultworkspace- \<GUID> - \<Region>*.  

Substitua o valor do `azureAroV4ClusterResourceId` parâmetro.

```bash
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
```

Por exemplo:

' bash enable-monitoring.sh--ID do recurso $azureAroV 4ClusterResourceId 

Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir as métricas de integridade para o cluster.

### <a name="enable-monitoring-from-the-azure-portal"></a>Habilitar o monitoramento do portal do Azure

A exibição de vários clusters no contêiner insights realça os clusters do Azure Red Hat OpenShift que não têm o monitoramento habilitado na guia **clusters não monitorados** . A opção **habilitar** ao lado de seu cluster não inicia a integração do monitoramento no Portal. Você será redirecionado para este artigo para habilitar o monitoramento manualmente seguindo as etapas que foram descritas anteriormente neste artigo.

1. Entre no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo ou na home page, selecione **Azure monitor**.

1. Na seção **insights** , selecione **contêineres**.

1. Na página **monitorar contêineres** , selecione **clusters não monitorados**.

1. Na lista de clusters não monitorados, selecione o cluster e, em seguida, selecione **habilitar**.

    Você pode identificar os resultados na lista procurando o valor de **toa** na coluna tipo de **cluster** . Depois de selecionar **habilitar**, você será redirecionado para este artigo.

## <a name="next-steps"></a>Próximas etapas

- Agora que você habilitou o monitoramento para coletar a utilização de recursos e de integridade do cluster do RedHat OpenShift versão 4. x e das cargas de trabalho em execução neles, saiba [como usar](container-insights-analyze.md) o contêiner insights.

- Por padrão, o agente em contêiner coleta os logs de contêiner *stdout* e *stderr* de todos os contêineres que estão em execução em todos os namespaces, exceto Kube-System. Para configurar uma coleção de log de contêiner específica para um namespace ou namespaces específicos, examine a [configuração do agente do insights de contêiner](container-insights-agent-config.md) para definir as configurações de coleta de dados desejadas para o arquivo de configuração do *ConfigMap* .

- Para revisar e analisar as métricas de Prometheus do seu cluster, examine [Configurar a recorte de métricas do Prometheus](container-insights-prometheus-integration.md).

- Para saber como parar de monitorar o cluster usando o contêiner insights, consulte [como parar de monitorar o cluster do Azure Red Hat OpenShift](./container-insights-optout-openshift-v3.md).
