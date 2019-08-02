---
title: Gerenciar clusters de HDInsight do Azure usando a CLI do Azure
description: Saiba como usar a CLI do Azure para gerenciar clusters de HDInsight do Azure. Tipos de cluster incluem Apache Hadoop, Spark, HBase, Storm, Kafka, consulta interativa e os serviços do ML.
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tyfox
ms.openlocfilehash: 5ae97b17d06fa0a9934a58ac662ef12116cce4f6
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137394"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Gerenciar clusters de HDInsight do Azure usando a CLI do Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Saiba como usar [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) para gerenciar clusters de HDInsight do Azure. A CLI (interface de linha de comando) do Azure é a experiência da linha de comando de plataforma cruzada da Microsoft para gerenciar os recursos do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* CLI do Azure. Se você ainda não tiver instalado a CLI do Azure, confira [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) para obter as etapas.

* Um cluster do Apache Hadoop no HDInsight. Consulte [Introdução ao HDInsight no Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Conecte-se ao Azure

Entre em sua assinatura do Azure. Se você planeja usar o Azure Cloud Shell, basta selecionar **Experimente** no canto superior direito do bloco de código. Caso contrário, insira o comando a seguir:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Listar clusters

Use [lista de hdinsight az](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) para listar os clusters. Edite os comandos abaixo substituindo `RESOURCE_GROUP_NAME` com o nome do seu grupo de recursos, em seguida, digite os comandos:

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

Use [show do az hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) para mostrar informações para um cluster especificado. Editar o comando abaixo, substituindo `RESOURCE_GROUP_NAME`, e `CLUSTER_NAME` com as informações relevantes, em seguida, digite o comando:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Excluir clusters

Use [az hdinsight exclui](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) para excluir um cluster especificado. Editar o comando abaixo, substituindo `RESOURCE_GROUP_NAME`, e `CLUSTER_NAME` com as informações relevantes, em seguida, digite o comando:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Você também pode excluir um cluster excluindo o grupo de recursos que contém o cluster. Observe que isso excluirá todos os recursos no grupo, incluindo a conta de armazenamento padrão.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Dimensionar clusters

Use [redimensionar az hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) para redimensionar o cluster HDInsight especificado para o tamanho especificado. Editar o comando abaixo, substituindo `RESOURCE_GROUP_NAME`, e `CLUSTER_NAME` com as informações relevantes. Substitua `TARGET_INSTANCE_COUNT` com o número de nós de trabalho para o cluster desejado. Para obter mais informações sobre o dimensionamento de clusters, consulte [clusters do HDInsight de escala](./hdinsight-scaling-best-practices.md). Insira o comando:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --target-instance-count TARGET_INSTANCE_COUNT
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a executar diferentes tarefas administrativas de cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* [Gerenciar clusters do Apache Hadoop no HDInsight usando o portal do Azure](hdinsight-administer-use-portal-linux.md)
* [Administrar o HDInsight usando o Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Introdução à CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
