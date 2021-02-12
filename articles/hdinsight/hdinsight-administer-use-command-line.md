---
title: Gerenciar clusters do Azure HDInsight usando o CLI do Azure
description: Saiba como usar o CLI do Azure para gerenciar clusters do Azure HDInsight. Os tipos de cluster incluem Apache Hadoop, Spark, HBase, Storm, Kafka, consulta interativa e serviços de ML.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-azurecli
ms.date: 02/26/2020
ms.openlocfilehash: b17c5a2abc036c16ff3ce36b81428f9149e36b4b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942866"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Gerenciar clusters do Azure HDInsight usando o CLI do Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Saiba como usar [CLI do Azure](/cli/azure/) para gerenciar clusters do Azure HDInsight. A CLI (interface de linha de comando) do Azure é a experiência da linha de comando de plataforma cruzada da Microsoft para gerenciar os recursos do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* CLI do Azure. Se você ainda não tiver instalado a CLI do Azure, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli) para obter as etapas.

* Um cluster do Apache Hadoop no HDInsight. Consulte [Introdução ao HDInsight no Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Conectar-se ao Azure

Entre na sua assinatura do Azure. Se você pretende usar o Azure Cloud Shell, selecione **Experimentar** no canto superior direito do bloco de código. Caso contrário, insira o comando a seguir:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Listar clusters

Use [AZ hdinsight List](/cli/azure/hdinsight#az-hdinsight-list) para listar clusters. Edite os comandos a seguir substituindo pelo `RESOURCE_GROUP_NAME` nome do seu grupo de recursos e, em seguida, insira os comandos:

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>Mostrar cluster

Use [AZ hdinsight show](/cli/azure/hdinsight#az-hdinsight-show) para mostrar informações de um cluster especificado. Edite o comando a seguir substituindo `RESOURCE_GROUP_NAME` e `CLUSTER_NAME` com as informações relevantes e, em seguida, digite o comando:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Excluir clusters

Use [AZ hdinsight Delete](/cli/azure/hdinsight#az-hdinsight-delete) para excluir um cluster especificado. Edite o comando a seguir substituindo `RESOURCE_GROUP_NAME` e `CLUSTER_NAME` com as informações relevantes e, em seguida, digite o comando:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Você também pode excluir um cluster excluindo o grupo de recursos que contém o cluster. Observe que isso excluirá todos os recursos no grupo, incluindo a conta de armazenamento padrão.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Dimensionar clusters

Use [AZ hdinsight Resize](/cli/azure/hdinsight#az-hdinsight-resize) para redimensionar o cluster hdinsight especificado para o tamanho especificado. Edite o comando a seguir substituindo `RESOURCE_GROUP_NAME` e `CLUSTER_NAME` com as informações relevantes. Substitua `WORKERNODE_COUNT` pelo número desejado de nós de trabalho para o cluster. Para obter mais informações sobre o dimensionamento de clusters, consulte [dimensionar clusters HDInsight](./hdinsight-scaling-best-practices.md). Insira o comando:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a executar diferentes tarefas administrativas do cluster HDInsight. Para saber mais, leia os seguintes artigos:

* [Gerenciar clusters do Apache Hadoop no HDInsight usando o portal do Azure](hdinsight-administer-use-portal-linux.md)
* [Administrar clusters HDInsight usando o Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli)
