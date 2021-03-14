---
title: Criar clusters Apache Hadoop usando o CLI do Azure-Azure HDInsight
description: Saiba como criar clusters do Azure HDInsight usando o CLI do Azure de plataforma cruzada.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 02/03/2020
ms.openlocfilehash: 9028d85346611341afec0d0598f27a77e4f37fdf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715489"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Criar os clusters do HDInsight usando a CLI do Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

As etapas neste documento orientam a criação de um cluster HDInsight 3,6 usando o CLI do Azure.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-cluster"></a>Criar um cluster

1. Acesse a sua assinatura do Azure. Se você pretende usar o Azure Cloud Shell, selecione **Experimentar** no canto superior direito do bloco de código. Caso contrário, insira o comando a seguir:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Configure as variáveis de ambiente. O uso de variáveis neste artigo baseia-se no bash. Pequenas variações serão necessárias para outros ambientes. Consulte [AZ-hdinsight-Create](/cli/azure/hdinsight#az-hdinsight-create) para obter uma lista completa de possíveis parâmetros para a criação do cluster.

    |Parâmetro | Descrição |
    |---|---|
    |`--workernode-count`| O número de nós de trabalho no cluster. Este artigo usa a variável `clusterSizeInNodes` como o valor passado para `--workernode-count` . |
    |`--version`| a versão do cluster do HDInsight. Este artigo usa a variável `clusterVersion` como o valor passado para `--version` . Consulte também: [versões do HDInsight com suporte](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Tipo de cluster HDInsight, como: Hadoop, interactivehive, HBase, Kafka, Storm, Spark, rserver, mlservices.  Este artigo usa a variável `clusterType` como o valor passado para `--type` . Consulte também: [tipos de cluster e configuração](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type).|
    |`--component-version`|As versões de vários componentes do Hadoop, em versões separadas por espaços no formato ' componente = versão '. Este artigo usa a variável `componentVersion` como o valor passado para `--component-version` . Consulte também: [componentes do Hadoop](./hdinsight-component-versioning.md).|

    Substitua `RESOURCEGROUPNAME` , `LOCATION` , `CLUSTERNAME` , `STORAGEACCOUNTNAME` e `PASSWORD` pelos valores desejados. Altere os valores para as outras variáveis conforme desejado. Em seguida, insira os comandos da CLI.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'

    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. [Crie o grupo de recursos](/cli/azure/group#az-group-create) inserindo o comando abaixo:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Para obter uma lista de locais válidos, use o `az account list-locations` comando e, em seguida, use um dos locais do `name` valor.

4. [Crie uma conta de armazenamento do Azure](/cli/azure/storage/account#az-storage-account-create) inserindo o comando a seguir:

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. [Extraia a chave primária da conta de armazenamento do Azure](/cli/azure/storage/account/keys#az-storage-account-keys-list) e armazene-a em uma variável digitando o comando a seguir:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Crie um contêiner de armazenamento do Azure](/cli/azure/storage/container#az-storage-container-create) inserindo o comando a seguir:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Crie o cluster HDInsight](/cli/azure/hdinsight#az-hdinsight-create) inserindo o seguinte comando:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --workernode-count $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > Clusters HDInsight são fornecidos em uma variedade de tipos que correspondem à carga de trabalho ou à tecnologia para a qual o cluster está ajustado. Não há nenhum método com suporte para criar um cluster que combina vários tipos, como o Storm e HBase em um cluster.

    Pode levar vários minutos para que o processo de criação do cluster seja concluído. Geralmente, cerca de 15 minutos.

## <a name="clean-up-resources"></a>Limpar os recursos

Após a conclusão do artigo, convém excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso.

Insira todos ou alguns dos comandos a seguir para remover recursos:

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>Solucionar problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [requisitos de controle de acesso](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Próximas etapas

Agora que você criou com êxito um cluster HDInsight usando o CLI do Azure, use o seguinte para aprender a trabalhar com o cluster:

### <a name="apache-hadoop-clusters"></a>Clusters do Apache Hadoop

* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clusters do Apache HBase

* [Introdução ao Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolvimento de aplicativos Java para Apache HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clusters do Apache Storm

* [Desenvolver topologias em Java para o Apache Storm no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Usar componentes de Python no Apache Storm no HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com o Apache Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
