---
title: Gerenciar recursos da Instância Gerenciada do Azure para Apache Cassandra usando a CLI do Azure
description: Saiba mais sobre os comandos comuns para automatizar o gerenciamento do seu Instância Gerenciada do Azure para Apache Cassandra usando o CLI do Azure.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: 68b1ca625b5c8bd7ec195b89de63485c542e6691
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419058"
---
# <a name="manage-azure-managed-instance-for-apache-cassandra-resources-using-azure-cli-preview"></a>Gerenciar Instância Gerenciada do Azure para recursos do Apache Cassandra usando CLI do Azure (versão prévia)

Este artigo descreve os comandos comuns para automatizar o gerenciamento de Instância Gerenciada do Azure para clusters Apache Cassandra usando CLI do Azure.

> [!IMPORTANT]
> A Instância Gerenciada do Azure para Apache Cassandra está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Este artigo exige a CLI do Azure versão 2.12.1 ou posterior. Se você está usando o Azure Cloud Shell, a última versão já está instalada.
>
> Não é possível renomear o Instância Gerenciada do Azure para recursos do Apache Cassandra, pois isso viola como o Azure Resource Manager funciona com URIs de recursos.

## <a name="azure-managed-instance-for-apache-cassandra-clusters"></a>Instância Gerenciada do Azure para clusters Apache Cassandra

As seções a seguir demonstram como gerenciar os Instância Gerenciada do Azure para clusters Apache Cassandra, incluindo:

* [Criar um cluster de instância gerenciada](#create-cluster)
* [Excluir um cluster de instância gerenciada](#delete-cluster)
* [Obter os detalhes do cluster](#get-cluster-details)
* [Obter o status do nó de cluster](#get-cluster-status)
* [Listar clusters por grupo de recursos](#list-clusters-resource-group)
* [Listar clusters por ID de assinatura](#list-clusters-subscription)

### <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Criar um cluster de instância gerenciada

Crie um Instância Gerenciada do Azure para o cluster apache Cassandra:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
location='West US'
delegatedManagementSubnetId='/subscriptions/<subscription id>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/management'
initialCassandraAdminPassword='myPassword'

# You can override the cluster name if the original name is not legal for an Azure resource.
# overrideClusterName='ClusterNameIllegalForAzureResource'
# the default Cassandra version is v3.11

az managed-cassandra cluster create \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName \
    --location $location \
    --delegated-management-subnet-id $delegatedManagementSubnetId \
    --initial-cassandra-admin-password $initialCassandraAdminPassword \
```

### <a name="delete-a-managed-instance-cluster"></a><a id="delete-cluster"></a>Excluir um cluster de instância gerenciada

Excluir um cluster:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster delete \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-details"></a><a id="get-cluster-details"></a>Obter os detalhes do cluster

Obter detalhes do cluster:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster show \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-node-status"></a><a id="get-cluster-status"></a>Obter o status do nó de cluster

Obter detalhes do cluster:

```azurecli-interactive
clusterName='cassandra-hybrid-cluster'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster node-status \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="list-the-clusters-by-resource-group"></a><a id="list-clusters-resource-group"></a>Listar os clusters por grupo de recursos

Listar clusters por grupo de recursos:

```azurecli-interactive
subscriptionId='MySubscriptionId'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster list\
    --resource-group $resourceGroupName
```

### <a name="list-clusters-by-subscription-id"></a><a id="list-clusters-subscription"></a>Listar clusters por ID de assinatura

Listar clusters por ID de assinatura:

```azurecli-interactive
# set your subscription id
az account set -s <subscription id>

az managed-cassandra cluster list
```

## <a name="the-managed-instance-datacenters"></a><a id="managed-instance-datacenter"></a>Os data centers da instância gerenciada

As seções a seguir demonstram como gerenciar os Instância Gerenciada do Azure para datacenters do Apache Cassandra, incluindo:

* [Criar um datacenter](#create-datacenter)
* [Excluir um datacenter](#delete-datacenter)
* [Obter detalhes do datacenter](#get-datacenter-details)
* [Atualizar ou dimensionar um datacenter](#update-datacenter)
* [Obter data centers em um cluster](#get-datacenters-cluster)

### <a name="create-a-datacenter"></a><a id="create-datacenter"></a>Criar um datacenter

Criar um datacenter:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus2'
delegatedSubnetId='/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter create \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --data-center-location $dataCenterLocation \
    --delegated-subnet-id $delegatedSubnetId \
    --node-count 3 
```

### <a name="delete-a-datacenter"></a><a id="delete-datacenter"></a>Excluir um datacenter

Excluir um datacenter:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter delete \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="get-datacenter-details"></a><a id="get-datacenter-details"></a>Obter detalhes do datacenter

Obter detalhes do datacenter:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter show \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="update-or-scale-a-datacenter"></a><a id="update-datacenter"></a>Atualizar ou dimensionar um datacenter

Atualize ou dimensione um datacenter (para dimensionar a alteração nodeCount valor):

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus'
delegatedSubnetId= '/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter update \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --node-count 13 
```

### <a name="get-the-datacenters-in-a-cluster"></a><a id="get-datacenters-cluster"></a>Obter os data centers em um cluster

Obter data centers em um cluster:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra datacenter list \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName
```

## <a name="next-steps"></a>Próximas etapas

* [Criar um cluster de instância gerenciada a partir do portal do Azure](create-cluster-portal.md)
* [Implantar um cluster do Apache Spark gerenciado com o Azure Databricks](deploy-cluster-databricks.md)