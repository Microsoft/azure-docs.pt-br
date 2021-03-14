---
title: Configurar o Azure Red Hat OpenShift v3. x com o contêiner insights | Microsoft Docs
description: Este artigo descreve como configurar o monitoramento de um cluster kubernetes com Azure Monitor hospedado no Azure Red Hat OpenShift versão 3 e superior.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: b46dfda0bdb0f3b582aa751786187a4d74524f75
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708366"
---
# <a name="configure-azure-red-hat-openshift-v3-with-container-insights"></a>Configurar o Azure Red Hat OpenShift v3 com informações de contêiner

>[!IMPORTANT]
> O Azure Red Hat OpenShift 3,11 será desativado em junho de 2022.
>
> A partir de outubro de 2020, você não poderá mais criar novos clusters 3,11.
> Os clusters 3,11 existentes continuarão a operar até 2022 de junho, mas não haverá mais suporte após essa data.
>
> Siga este guia para [criar um cluster do Azure Red Hat OpenShift 4](../../openshift/tutorial-create-cluster.md).
> Se você tiver dúvidas específicas, [entre em contato conosco](mailto:aro-feedback@microsoft.com).

As informações de contêiner fornecem uma experiência de monitoramento avançada para os clusters do AKS (serviço kubernetes do Azure) e do mecanismo do AKS. Este artigo descreve como habilitar o monitoramento de clusters kubernetes hospedados no [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) versão 3 e a versão mais recente com suporte da versão 3, para obter uma experiência de monitoramento semelhante.

>[!NOTE]
>O suporte para o Azure Red Hat OpenShift é um recurso em visualização pública no momento.
>

As informações de contêiner podem ser habilitadas para novas ou uma ou mais implantações existentes do Azure Red Hat OpenShift usando os seguintes métodos com suporte:

- Para um cluster existente do portal do Azure ou usando o modelo Azure Resource Manager.
- Para um novo cluster usando Azure Resource Manager modelo ou ao criar um novo cluster usando o [CLI do Azure](/cli/azure/openshift#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Recursos com e sem suporte

O contêiner insights dá suporte ao monitoramento do Azure Red Hat OpenShift, conforme descrito no artigo de [visão geral](container-insights-overview.md) , com exceção dos seguintes recursos:

- Dados dinâmicos (visualização)
- [Coletar métricas](container-insights-update-metrics.md) de nós de cluster e pods e armazená-los no banco de dados de métricas de Azure monitor

## <a name="prerequisites"></a>Pré-requisitos

- Um [workspace do Log Analytics](../logs/design-logs-deployment.md).

    O insights de contêiner dá suporte a um espaço de trabalho Log Analytics nas regiões listadas em produtos do Azure [por região](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Para criar seu próprio espaço de trabalho, ele pode ser criado por meio de [Azure Resource Manager](../logs/resource-manager-workspace.md), por meio do [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)ou no [portal do Azure](../logs/quick-create-workspace.md).

- Para habilitar e acessar os recursos em insights de contêiner, no mínimo você precisa ser um membro da função *colaborador* do Azure na assinatura do Azure e um membro da função colaborador de [*log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) do espaço de trabalho log Analytics configurado com informações de contêiner.

- Para exibir os dados de monitoramento, você é um membro da permissão da função [*leitor de log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) com o espaço de trabalho log Analytics configurado com o contêiner insights.

## <a name="identify-your-log-analytics-workspace-id"></a>Identificar sua ID do espaço de trabalho Log Analytics

 Para integrar com um espaço de trabalho Log Analytics existente, comece identificando a ID de recurso completo de seu espaço de trabalho Log Analytics. A ID de recurso do espaço de trabalho é necessária para o parâmetro `workspaceResourceId` quando você habilita o monitoramento usando o método de modelo Azure Resource Manager.

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

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Habilitar para um novo cluster usando um modelo de Azure Resource Manager

Execute as etapas a seguir para implantar um cluster do Azure Red Hat OpenShift com monitoramento habilitado. Antes de continuar, examine o tutorial [criar um cluster do Azure Red Hat OpenShift](../../openshift/tutorial-create-cluster.md) para entender as dependências que você precisa configurar para que seu ambiente seja configurado corretamente.

Esse método inclui dois modelos JSON. Um modelo especifica a configuração para implantar o cluster com monitoramento habilitado e o outro contém valores de parâmetro que você configura para especificar o seguinte:

- A ID de recurso de cluster do Azure Red Hat OpenShift.

- O grupo de recursos no qual o cluster é implantado.

- [Azure Active Directory ID de locatário](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) observada depois de executar as etapas para criar uma ou uma já criada.

- [Azure Active Directory ID do aplicativo cliente](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) anotada depois de executar as etapas para criar uma ou uma já criada.

- [Azure Active Directory segredo do cliente](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) observado depois de executar as etapas para criar uma ou uma já criada.

- [Grupo de segurança do Azure ad](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) anotado depois de executar as etapas para criar uma ou uma já criada.

- ID de recurso de um espaço de trabalho de Log Analytics existente. Consulte [identificar sua ID do espaço de trabalho log Analytics](#identify-your-log-analytics-workspace-id) para saber como obter essas informações.

- O número de nós mestres a serem criados no cluster.

- O número de nós de computação no perfil do pool de agentes.

- O número de nós de infraestrutura no perfil do pool de agentes.

Se você não estiver familiarizado com o conceito de implantação de recursos usando um modelo, veja:

- [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando o CLI do Azure versão 2.0.65 ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Baixe e salve em uma pasta local, o modelo de Azure Resource Manager e o arquivo de parâmetro, para criar um cluster com o complemento de monitoramento usando os seguintes comandos:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Entrar no Azure

    ```azurecli
    az login
    ```

    Caso tenha acesso a várias assinaturas, execute `az account set -s {subscription ID}` substituindo `{subscription ID}` pela assinatura que você deseja usar.

3. Crie um grupo de recursos para o cluster se você ainda não tiver um. Para obter uma lista de regiões do Azure que dão suporte a OpenShift no Azure, consulte [regiões com suporte](../../openshift/supported-resources.md#azure-regions).

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. Edite o arquivo de parâmetro JSON **newClusterWithMonitoringParam.jsem** e atualize os seguintes valores:

    - *local*
    - *clusterName*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. A etapa a seguir implanta o cluster com monitoramento habilitado usando o CLI do Azure.

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    A saída é semelhante ao seguinte:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Habilitar para um cluster existente

Execute as etapas a seguir para habilitar o monitoramento de um cluster do Azure Red Hat OpenShift implantado no Azure. Você pode fazer isso na portal do Azure ou usando os modelos fornecidos.

### <a name="from-the-azure-portal"></a>No portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com).

2. No menu portal do Azure ou na home page do, selecione **Azure monitor**. Na seção **Insights**, selecione **Contêineres**.

3. Na página **Monitor – contêineres**, selecione **Clusters não monitorados**.

4. Na lista de clusters não monitorados, localize o cluster na lista e clique em **habilitar**. Você pode identificar os resultados na lista procurando o valor **toa** sob o **tipo de cluster** de coluna.

5. Na página **integração com o contêiner insights** , se você tiver um espaço de trabalho log Analytics existente na mesma assinatura que o cluster, selecione-o na lista suspensa.  
    A lista seleciona o espaço de trabalho padrão e o local no qual o cluster é implantado na assinatura.

    ![Habilitar o monitoramento de clusters não monitorados](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Se você quiser criar um novo espaço de trabalho do Log Analytics para armazenar os dados de monitoramento do cluster, siga as instruções em [Criar um espaço de trabalho do Log Analytics](../logs/quick-create-workspace.md). Certifique-se de criar o espaço de trabalho na mesma assinatura em que o cluster do RedHat OpenShift é implantado.

Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir as métricas de integridade para o cluster.

### <a name="enable-using-an-azure-resource-manager-template"></a>Habilitar usando um modelo de Azure Resource Manager

Esse método inclui dois modelos JSON. Um modelo especifica a configuração para habilitar o monitoramento e o outro contém valores de parâmetro que você configura para especificar o seguinte:

- A ID de recurso de cluster do Azure RedHat OpenShift.

- O grupo de recursos no qual o cluster é implantado.

- Um workspace do Log Analytics. Consulte [identificar sua ID do espaço de trabalho log Analytics](#identify-your-log-analytics-workspace-id) para saber como obter essas informações.

Se você não estiver familiarizado com o conceito de implantação de recursos usando um modelo, veja:

- [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando o CLI do Azure versão 2.0.65 ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Baixe o modelo e o arquivo de parâmetro para atualizar o cluster com o complemento de monitoramento usando os seguintes comandos:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Entrar no Azure

    ```azurecli
    az login
    ```

    Caso tenha acesso a várias assinaturas, execute `az account set -s {subscription ID}` substituindo `{subscription ID}` pela assinatura que você deseja usar.

3. Especifique a assinatura do cluster do Azure RedHat OpenShift.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Execute o seguinte comando para identificar o local do cluster e a ID do recurso:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Edite o arquivo de parâmetro JSON **existingClusterParam.jsem** e atualize os valores *aroResourceId* e *aroResourceLocation*. O valor para **workspaceResourceId** é a ID do recurso completa do espaço de trabalho do Log Analytics, que inclui o nome do espaço de trabalho.

6. Para implantar com o CLI do Azure, execute os seguintes comandos:

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    A saída é semelhante ao seguinte:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Próximas etapas

- Com o monitoramento habilitado para coletar a utilização de recursos e de integridade do cluster do RedHat OpenShift e das cargas de trabalho em execução neles, saiba [como usar o](container-insights-analyze.md) contêiner insights.

- Por padrão, o agente em contêiner coleta os logs de contêiner stdout/stderr de todos os contêineres em execução em todos os namespaces, exceto Kube-System. Para configurar a coleta de log de contêiner específica para namespaces ou namespaces específicos, examine [configuração do agente do insights de contêiner](container-insights-agent-config.md) para definir as configurações de coleta de dados desejadas para o arquivo de configurações do ConfigMap.

- Para revisar e analisar as métricas de Prometheus do seu cluster, examine [Configurar a recorte de métricas do Prometheus](container-insights-prometheus-integration.md)

- Para saber como parar de monitorar o cluster com o contêiner insights, consulte [como parar de monitorar o cluster do Azure Red Hat OpenShift](./container-insights-optout-openshift-v3.md).