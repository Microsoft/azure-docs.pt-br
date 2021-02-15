---
title: Como atualizar Azure Monitor para contêineres para métricas | Microsoft Docs
description: Este artigo descreve como você atualiza Azure Monitor para contêineres para habilitar o recurso de métricas personalizadas que dá suporte à exploração e aos alertas em métricas agregadas.
ms.topic: conceptual
ms.date: 10/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2a94f250c83fbd2779620376087a83b8851e583e
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309445"
---
# <a name="how-to-update-azure-monitor-for-containers-to-enable-metrics"></a>Como atualizar o Azure Monitor para contêineres para habilitar as métricas

Azure Monitor para contêineres está introduzindo o suporte para coletar métricas dos AKS (serviços Kubernetess do Azure) e dos nós de clusters e pods do Azure habilitados para o Arc e gravá-los no repositório de métricas de Azure Monitor. Essa alteração destina-se a fornecer uma linha de tempo aprimorada ao apresentar cálculos agregados (AVG, Count, Max, min, Sum) em gráficos de desempenho, dar suporte à fixação de gráficos de desempenho em painéis de portal do Azure e dar suporte a alertas de métricas.

>[!NOTE]
>Atualmente, esse recurso não dá suporte a clusters do Azure Red Hat OpenShift.
>

As seguintes métricas são habilitadas como parte desse recurso:

| Namespace da métrica | Métrica | Descrição |
|------------------|--------|-------------|
| Informações. contêiner/nós | cpuUsageMillicores, cpuUsagePercentage, memoryRssBytes, memoryRssPercentage, memoryWorkingSetBytes, memoryWorkingSetPercentage, nodesCount, diskUsedPercentage, | Como as métricas de *nó* , elas incluem o *host* como uma dimensão. Eles também incluem o<br> nome do nó como valor para a dimensão do *host* . |
| Percepções. Container/pods | podCount, completedJobsCount, restartingContainerCount, oomKilledContainerCount, podReadyPercentage | Como métricas de *Pod* , eles incluem o seguinte como Dimensions-ControllerName, namespace kubernetes, nome, fase. |
| Informações. contêiner/contêineres | cpuExceededPercentage, memoryRssExceededPercentage, memoryWorkingSetExceededPercentage | |
| Percepções. Container/persistentvolumes | pvUsageExceededPercentage | |

Para dar suporte a esses novos recursos, um novo agente em contêineres está incluído na versão **Microsoft/OMS: ciprod05262020** para AKs e versão **Microsoft/OMS: ciprod09252020** para clusters kubernetes habilitados para Arc do Azure. As novas implantações do AKS incluem automaticamente essa alteração e recursos de configuração. A atualização do cluster para dar suporte a esse recurso pode ser executada no portal do Azure, Azure PowerShell ou com CLI do Azure. Com Azure PowerShell e CLI. Você pode habilitar esse por cluster ou para todos os clusters em sua assinatura.

Qualquer processo atribui a função de **Editor de métricas de monitoramento** à entidade de serviço do cluster ou à MSI atribuída pelo usuário para o complemento de monitoramento para que os dados coletados pelo agente possam ser publicados no recurso de clusters. O monitoramento de métricas Publisher tem permissão apenas para enviar métricas por push para o recurso, ele não pode alterar nenhum estado, atualizar o recurso ou ler dados. Para obter mais informações sobre a função, consulte [monitorando métricas de função de editor](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher). O requisito de função de Publicador de métricas de monitoramento não é aplicável a clusters kubernetes habilitados para Arc do Azure.

> [!IMPORTANT]
> A atualização não é necessária para clusters kubernetes habilitados para o Azure Arc, pois eles já terão a versão mínima necessária do agente.

## <a name="prerequisites"></a>Pré-requisitos

Antes de atualizar o cluster, confirme o seguinte:

* As métricas personalizadas só estão disponíveis em um subconjunto de regiões do Azure. Uma lista de regiões com suporte está documentada [aqui](../platform/metrics-custom-overview.md#supported-regions).

* Você é membro da função **[proprietário](../../role-based-access-control/built-in-roles.md#owner)** no recurso de cluster AKs para habilitar a coleta de métricas de desempenho personalizadas de nó e Pod. Esse requisito não se aplica aos clusters kubernetes habilitados para o Azure Arc.

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando o CLI do Azure versão 2.0.59 ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="upgrade-a-cluster-from-the-azure-portal"></a>Atualizar um cluster do portal do Azure

Para clusters AKS existentes monitorados por Azure Monitor para contêineres, depois de selecionar o cluster para exibir sua integridade do modo de exibição de vários clusters no Azure Monitor ou diretamente do cluster, selecionando **insights** no painel esquerdo, você verá uma faixa na parte superior do Portal.

![Atualizar a faixa do cluster AKS no portal do Azure](./media/container-insights-update-metrics/portal-banner-enable-01.png)

Clicar em **habilitar** iniciará o processo para atualizar o cluster. Esse processo pode levar vários segundos para ser finalizado e você pode acompanhar o progresso em Notificações no menu.

## <a name="upgrade-all-clusters-using-bash-in-azure-command-shell"></a>Atualizar todos os clusters usando o bash no Shell de comando do Azure

Execute as etapas a seguir para atualizar todos os clusters em sua assinatura usando o bash no Shell de comando do Azure.

1. Execute o comando a seguir usando o CLI do Azure.  Edite o valor de **SubscriptionId** usando o valor da página **visão geral do AKS** para o cluster AKs.

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    curl -sL https://aka.ms/ci-md-onboard-atscale | bash -s subscriptionId   
    ```

    A alteração de configuração pode levar alguns segundos para ser concluída. Quando ela for concluída, será exibida uma mensagem semelhante à seguinte e incluindo o resultado:

    ```azurecli
    completed role assignments for all AKS clusters in subscription: <subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-cli"></a>Atualizar por cluster usando CLI do Azure

Execute as etapas a seguir para atualizar um cluster específico em sua assinatura usando CLI do Azure.

1. Execute o comando a seguir usando o CLI do Azure. Edite os valores para **SubscriptionId**, **resourceGroupName**e **ClusterName** usando os valores na página **visão geral do AKS** para o cluster AKs.  Para obter o valor de **clientIdOfSPN**, ele é retornado quando você executa o comando, `az aks show` conforme mostrado no exemplo abaixo.

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPN> --scope <clusterResourceId> --role "Monitoring Metrics Publisher" 
    ```

    Para obter o valor de **clientIdOfSPNOrMsi**, você pode executar o comando, `az aks show` conforme mostrado no exemplo abaixo. Se o objeto **servicePrincipalProfile** tiver um valor *ClientID* válido, você poderá usá-lo. Caso contrário, se estiver definido como *MSI*, você precisará passar o ClientID de `addonProfiles.omsagent.identity.clientId` .

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPNOrMsi> --scope <clusterResourceId> --role "Monitoring Metrics Publisher"
    ```

## <a name="upgrade-all-clusters-using-azure-powershell"></a>Atualizar todos os clusters usando Azure PowerShell

Execute as etapas a seguir para atualizar todos os clusters em sua assinatura usando Azure PowerShell.

1. [Baixe](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding_atscale.ps1) o script de **mdm_onboarding_atscale.ps1** e salve-o em uma pasta local de nosso repositório github.
2. Execute o comando a seguir usando o Azure PowerShell.  Edite o valor de **SubscriptionId** usando o valor da página **visão geral do AKS** para o cluster AKs.

    ```powershell
    .\mdm_onboarding_atscale.ps1 subscriptionId
    ```
    A alteração de configuração pode levar alguns segundos para ser concluída. Quando ela for concluída, será exibida uma mensagem semelhante à seguinte e incluindo o resultado:

    ```powershell
    Completed adding role assignment for the aks clusters in subscriptionId :<subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-powershell"></a>Atualizar por cluster usando Azure PowerShell

Execute as etapas a seguir para atualizar um cluster específico usando Azure PowerShell.

1. [Baixe](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding.ps1) o script de **mdm_onboarding.ps1** e salve-o em uma pasta local de nosso repositório github.

2. Execute o comando a seguir usando o Azure PowerShell. Edite os valores para **SubscriptionId**, **resourceGroupName**e **ClusterName** usando os valores na página **visão geral do AKS** para o cluster AKs.

    ```powershell
    .\mdm_onboarding.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    A alteração de configuração pode levar alguns segundos para ser concluída. Quando ela for concluída, será exibida uma mensagem semelhante à seguinte e incluindo o resultado:

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>Verificar atualização

Depois de iniciar a atualização usando um dos métodos descritos anteriormente, você pode usar Azure Monitor métricas Explorer e verificar no **namespace de métrica** que o **insights** está listado. Se for, você pode começar a configurar [alertas de métrica](../platform/alerts-metric.md) ou fixar seus gráficos nos [painéis](../../azure-portal/azure-portal-dashboards.md).  
