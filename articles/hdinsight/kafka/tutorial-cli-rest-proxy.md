---
title: 'Tutorial: Criar um cluster habilitado para proxy REST do Apache Kafka no HDInsight usando a CLI do Azure'
description: Saiba como executar operações do Apache Kafka usando um proxy REST do Kafka no Azure HDInsight.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: cfb9ff9e6b107c9da84b164a055453994fc29229
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786631"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Tutorial: Criar um cluster habilitado para proxy REST do Apache Kafka no HDInsight usando a CLI do Azure

Neste tutorial, você aprenderá a criar um cluster [habilitado para proxy REST](./rest-proxy.md) do Apache Kafka no Azure HDInsight usando a CLI (interface de linha de comando) do Azure. O Azure HDInsight é um serviço de análise de software livre gerenciado e de amplo espectro para empresas. O Apache Kafka é uma plataforma de streaming distribuída de software livre. Ela é geralmente usada como um agente de mensagens, pois fornece funcionalidade semelhante a uma fila de mensagens para publicação e assinatura. O proxy REST do Kafka permite que você interaja com o cluster do Kafka por meio de uma [API REST](/rest/api/hdinsight-kafka-rest-proxy/) via HTTP. A CLI do Azure é a experiência da linha de comando de plataforma cruzada da Microsoft para gerenciar os recursos do Azure.

A API do Apache Kafka só pode ser acessada por recursos dentro da mesma rede virtual. Você pode acessar o cluster diretamente usando SSH. Para conectar a outros serviços, redes ou máquinas virtuais ao Apache Kafka, primeiro crie uma rede virtual e, depois, crie os recursos na rede. Para saber mais, confira [Conectar-se ao Apache Kafka usando uma rede virtual](./apache-kafka-connect-vpn-gateway.md).

Neste tutorial, você aprenderá:

> [!div class="checklist"]
> * Pré-requisitos para o proxy REST do Kafka
> * Criar um cluster Apache Kafka usando a CLI do Azure

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um aplicativo registrado no Azure AD. Os aplicativos cliente que você escreve para interagir com o proxy REST do Kafka usarão a ID e o segredo do aplicativo para autenticação no Azure. Para obter mais informações, confira [Registrar um aplicativo na plataforma de identidade da Microsoft](../../active-directory/develop/quickstart-register-app.md).

* Um grupo de segurança do Azure AD com seu aplicativo registrado como um membro. Esse grupo de segurança será usado para controlar quais aplicativos têm permissão para interagir com o proxy REST. Para obter mais informações sobre como criar grupos do Azure AD, confira [Criar um grupo básico e adicionar membros usando o Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* CLI do Azure. Verifique se você tem pelo menos a versão 2.0.79. Confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-an-apache-kafka-cluster"></a>Criar um cluster do Apache Kafka

1. Entre na sua assinatura do Azure.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Configure as variáveis de ambiente. O uso de variáveis neste tutorial baseia-se em Bash. Pequenas variações serão necessárias para outros ambientes.

    |Variável | Descrição |
    |---|---|
    |resourceGroupName|Substitua RESOURCEGROUPNAME pelo nome do novo grupo de recursos.|
    |local|Substitua LOCATION por uma região em que o cluster será criado. Para obter uma lista de locais válidos, use o comando `az account list-locations`|
    |clusterName|Substitua CLUSTERNAME por um nome exclusivo global para o novo cluster.|
    |storageAccount|Substitua STORAGEACCOUNTNAME por um nome para sua nova conta de armazenamento.|
    |httpPassword|Substitua PASSWORD por uma senha para o logon do cluster, **administrador**.|
    |sshPassword|Substitua PASSWORD por uma senha para o nome de usuário do Secure Shell, **sshuser**.|
    |securityGroupName|Substitua SECURITYGROUPNAME pelo nome do grupo de segurança do AAD do cliente para o proxy REST do Kafka. A variável será passada para o parâmetro `--kafka-client-group-name` para `az-hdinsight-create`.|
    |securityGroupID|Substitua SECURITYGROUPID pela ID do grupo de segurança do AAD do cliente para o proxy REST do Kafka. A variável será passada para o parâmetro `--kafka-client-group-id` para `az-hdinsight-create`.|
    |storageContainer|Contêiner de armazenamento que o cluster usará, deixe no estado em que se encontra para este tutorial. Essa variável será definida com o nome do cluster.|
    |workernodeCount|Número de nós de trabalho no cluster, deixe no estado em que se encontram para este tutorial. Para garantir alta disponibilidade, o Kafka requer no mínimo 3 nós de trabalho|
    |clusterType|Tipo de cluster HDInsight, deixe no estado em que se encontra para este tutorial.|
    |clusterVersion|Versão do cluster HDInsight, deixe no estado em que se encontra para este tutorial. O proxy REST do Kafka requer pelo menos a versão 4.0 do cluster.|
    |componentVersion|Versão do Kafka, deixe no estado em que se encontra para este tutorial. O proxy REST do Kafka requer pelo menos a versão 2.1 do componente.|

    Atualize as variáveis com os valores desejados. Em seguida, insira os comandos da CLI para definir as variáveis de ambiente.

    ```azurecli
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export storageAccount=STORAGEACCOUNTNAME
    export httpPassword='PASSWORD'
    export sshPassword='PASSWORD'
    export securityGroupName=SECURITYGROUPNAME
    export securityGroupID=SECURITYGROUPID

    export storageContainer=$(echo $clusterName | tr "[:upper:]" "[:lower:]")
    export workernodeCount=3
    export clusterType=kafka
    export clusterVersion=4.0
    export componentVersion=kafka=2.1
    ```

1. [Crie o grupo de recursos](/cli/azure/group#az_group_create) inserindo o seguinte comando:

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. [Crie uma conta de Armazenamento do Azure](/cli/azure/storage/account#az_storage_account_create) inserindo o seguinte comando:

    ```azurecli
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $storageAccount \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

1. [Extraia a chave primária](/cli/azure/storage/account/keys#az_storage_account_keys_list) da conta de Armazenamento do Azure e armazene-a em uma variável, digitando o seguinte comando:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. [Crie um contêiner de Armazenamento do Azure](/cli/azure/storage/container#az_storage_container_create) inserindo o seguinte comando:

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [Criar o cluster HDInsight](/cli/azure/hdinsight#az_hdinsight_create). Antes de inserir o comando, observe os seguintes parâmetros:

    1. Parâmetros necessários para clusters Kafka:

        |Parâmetro | Descrição|
        |---|---|
        |--type|O valor deve ser **Kafka**.|
        |--workernode-data-disks-per-node|O número de discos de dados a serem usados por nó de trabalho. O Kafka do HDInsight só é compatível com discos de dados. Este tutorial usa um valor de **2**.|

    1. Parâmetros necessários para o proxy REST do Kafka:

        |Parâmetro | Descrição|
        |---|---|
        |--kafka-management-node-size|O tamanho do nó. Este tutorial usa o valor **Standard_D4_v2**.|
        |--kafka-client-group-id|A ID do grupo de segurança do AAD do cliente para o proxy REST do Kafka. O valor é passado da variável **$securityGroupID**.|
        |--kafka-client-group-name|O nome do grupo de segurança do AAD do cliente para o proxy REST do Kafka. O valor é passado da variável **$securityGroupName**.|
        |--versão|A versão do cluster HDInsight deve ser pelo menos 4.0. O valor é passado da variável **$clusterVersion**.|
        |--component-version|A versão do Kafka deve ser pelo menos 2.1. O valor é passado da variável **$componentVersion**.|
    
        Se você quiser criar o cluster sem o proxy REST, elimine `--kafka-management-node-size`, `--kafka-client-group-id` e `--kafka-client-group-name` do comando `az hdinsight create`.

    1. Se você tiver uma rede virtual, adicione os parâmetros `--vnet-name` e `--subnet` e seus valores.

    Digite o seguinte comando para criar o cluster:

    ```azurecli
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpPassword \
        --http-user admin \
        --location $location \
        --ssh-password $sshPassword \
        --ssh-user sshuser \
        --storage-account $storageAccount \
        --storage-account-key $storageAccountKey \
        --storage-container $storageContainer \
        --version $clusterVersion \
        --workernode-count $workernodeCount \
        --workernode-data-disks-per-node 2 \
        --kafka-management-node-size "Standard_D4_v2" \
        --kafka-client-group-id $securityGroupID \
        --kafka-client-group-name "$securityGroupName"
    ```

    Pode levar vários minutos para que o processo de criação de cluster seja concluído. Geralmente, cerca de 15 minutos.

## <a name="clean-up-resources"></a>Limpar recursos

Após a conclusão do artigo, convém excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso.

Insira todos ou alguns dos comandos a seguir para remover recursos:

```azurecli
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $storageAccount  \
    --name $storageContainer

# Remove storage account
az storage account delete \
    --name $storageAccount  \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

Agora que você criou com êxito um cluster habilitado para proxy REST do Apache Kafka no Azure HDInsight usando CLI do Azure, use o código Python para interagir com o proxy REST:

> [!div class="nextstepaction"]
> [Criar o aplicativo de exemplo](./rest-proxy.md#client-application-sample)