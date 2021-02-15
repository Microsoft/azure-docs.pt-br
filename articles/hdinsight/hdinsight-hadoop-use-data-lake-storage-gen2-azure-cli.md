---
title: Criar Azure HDInsight-Azure Data Lake Storage Gen2-CLI do Azure
description: Saiba como usar Azure Data Lake Storage Gen2 com clusters do Azure HDInsight usando CLI do Azure.
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 09/17/2020
ms.openlocfilehash: bbc1cd27d5c16eddd3aaad748c34445e5017e209
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945500"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-azure-cli"></a>Criar um cluster com Data Lake Storage Gen2 usando CLI do Azure

Para criar um cluster HDInsight que usa Data Lake Storage Gen2 para armazenamento, siga estas etapas.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com Azure Data Lake Storage Gen2, confira a [seção visão geral](hdinsight-hadoop-use-data-lake-storage-gen2.md). 
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os exemplos de script da CLI, você tem três opções:
    - Usar o [Azure Cloud Shell](../cloud-shell/overview.md) no Portal do Azure (confira a próxima seção).
    - Usar o Azure Cloud Shell inserido por meio do botão "Experimentar", localizado no canto superior direito de cada bloco de código.
    - [Instale a versão mais recente da CLI do Azure](/cli/azure/install-azure-cli) (2.0.13 ou mais recente), se você preferir usar um console da CLI local. Entre no Azure usando `az login` , usando uma conta associada à assinatura do Azure sob a qual você deseja implantar a identidade gerenciada atribuída pelo usuário. CLI do Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Você pode [baixar um arquivo de modelo de exemplo](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) e [baixar um arquivo de parâmetros de exemplo](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Antes de usar o modelo e o trecho de código de CLI do Azure abaixo, substitua os seguintes espaços reservados pelos seus valores corretos:

| Espaço reservado | Descrição |
|---|---|
| `<SUBSCRIPTION_ID>` | A ID da sua assinatura do Azure |
| `<RESOURCEGROUPNAME>` | O grupo de recursos em que você deseja criar o novo cluster e a conta de armazenamento. |
| `<MANAGEDIDENTITYNAME>` | O nome da identidade gerenciada que receberá permissões em sua conta de armazenamento com Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | A nova conta de armazenamento com Azure Data Lake Storage Gen2 que será criado. |
| `<FILESYSTEMNAME>`  | O nome do sistema de arquivos que esse cluster deve usar na conta de armazenamento. |
| `<CLUSTERNAME>` | O nome do seu cluster HDInsight. |
| `<PASSWORD>` | Sua senha escolhida para entrar no cluster usando SSH e o painel do Ambari. |

O trecho de código abaixo faz as seguintes etapas iniciais:

1. Faz logon em sua conta do Azure.
1. Define a assinatura ativa em que as operações de criação serão feitas.
1. Cria um novo grupo de recursos para as novas atividades de implantação.
1. Cria uma identidade gerenciada atribuída pelo usuário.
1. Adiciona uma extensão ao CLI do Azure para usar recursos para Data Lake Storage Gen2.
1. Cria uma nova conta de armazenamento com Data Lake Storage Gen2 usando o `--hierarchical-namespace true` sinalizador.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Em seguida, entre no Portal. Adicione a nova identidade gerenciada atribuída pelo usuário à função **colaborador de dados do blob de armazenamento** na conta de armazenamento. Esta etapa é descrita na etapa 3 em [usando o portal do Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md).

 > [!IMPORTANT]
 > Verifique se sua conta de armazenamento tem a identidade atribuída pelo usuário com permissões de função de **colaborador de dados de blob de armazenamento** . caso contrário, haverá falha na criação do cluster.

```azurecli
az deployment group create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

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

Você criou com êxito um cluster HDInsight. Saiba agora como trabalhar com o cluster.

### <a name="apache-spark-clusters"></a>Clusters do Apache Spark

* [Personalizar clusters HDInsight baseados em Linux usando ações de script](hdinsight-hadoop-customize-cluster-linux.md)
* [Criar um aplicativo autônomo usando Scala](spark/apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark com BI: execute análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](spark/apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para prever os resultados da inspeção de alimentos](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Clusters do Apache Hadoop

* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clusters do Apache HBase

* [Introdução ao Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolvimento de aplicativos Java para Apache HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)
