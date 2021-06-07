---
title: 'Início Rápido: Apache Kafka usando o Azure Resource Manager – HDInsight'
description: Neste início rápido, você aprenderá a criar um cluster Apache Kafka no Azure HDInsight usando o modelo do Azure Resource Manager. Você também aprenderá sobre tópicos, assinantes e consumidores de Kafka.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 72a468057cef5c8616cb3f88063204509aaf7fe0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863319"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-arm-template"></a>Início Rápido: Criar um cluster do Apache Kafka no Azure HDInsight usando um modelo do Resource Manager

Neste guia de início rápido, você usará um modelo do Azure Resource Manager para criar um cluster do [Apache Kafka](./apache-kafka-introduction.md) no Azure HDInsight. O Kafka é uma plataforma de streaming distribuída de software livre. Ela é geralmente usada como um agente de mensagens, pois fornece funcionalidade semelhante a uma fila de mensagens para publicação e assinatura.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

A API do Kafka só pode ser acessada por recursos dentro da mesma rede virtual. Neste início rápido, você acessará o cluster diretamente no SSH. Para conectar a outros serviços, redes ou máquinas virtuais ao Kafka, primeiro crie uma rede virtual e, depois, crie os recursos na rede. Para saber mais, confira o documento [Conectar-se ao Apache Kafka usando uma rede virtual](apache-kafka-connect-vpn-gateway.md).

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[:::image type="icon" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar no Azure" border="false":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-kafka%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-kafka/).

:::code language="json" source="~/quickstart-templates/101-hdinsight-kafka/azuredeploy.json":::

Há dois recursos do Azure definidos no modelo:

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): crie uma Conta de Armazenamento do Azure.
* [Microsoft.HDInsight/cluster](/azure/templates/microsoft.hdinsight/clusters): crie um cluster HDInsight.

## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione o botão **Implantar no Azure** abaixo para entrar no Azure e abrir o modelo do Resource Manager.

   [:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar no Azure" border="false":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-kafka%2Fazuredeploy.json)

1. Digite ou selecione os valores a seguir:

    |Propriedade |Descrição |
    |---|---|
    |Subscription|Na lista suspensa, selecione a assinatura do Azure usada para o cluster.|
    |Resource group|Na lista suspensa, selecione o grupo de recursos existente ou selecione **Criar**.|
    |Location|O valor será preenchido automaticamente com o local usado para o grupo de recursos.|
    |Nome do cluster|Insira um nome global exclusivo. Para este modelo, use apenas letras minúsculas e números.|
    |Nome de usuário de logon do cluster|Forneça o nome de usuário, o padrão é **admin**.|
    |Senha de logon do cluster|Forneça uma senha. A senha precisa ter no mínimo 10 caracteres e precisa conter pelo menos um dígito, uma letra maiúscula, uma minúscula e um caractere não alfanumérico (exceto pelos caracteres ' " ` ). |
    |Nome de Usuário SSH|Forneça o nome de usuário, o padrão é **sshuser**|
    |Senha SSH|Forneça a senha.|

    :::image type="content" source="./media/apache-kafka-quickstart-resource-manager-template/resource-manager-template-kafka.png" alt-text="Uma captura de tela das propriedades do modelo" border="false":::

1. Examine os **TERMOS E CONDIÇÕES**. Em seguida, selecione **Concordo com os termos e condições declarados acima** e **Comprar**. Você receberá uma notificação de que a implantação está em andamento. Demora cerca de 20 minutos para criar um cluster.

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Depois que o cluster for criado, você receberá uma notificação de **Implantação bem-sucedida** com um link **Ir para o recurso**. A página do Grupo de recursos listará o novo cluster HDInsight e o armazenamento padrão associado a ele. Cada cluster tem uma dependência na conta do [Armazenamento de Blobs do Azure](../hdinsight-hadoop-use-blob-storage.md), do [Azure Data Lake Storage Gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md) ou do [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md). Ela é conhecida como a conta de armazenamento padrão. O cluster do HDInsight e sua conta de armazenamento padrão devem estar colocados na mesma região do Azure. A exclusão dos clusters não exclui a conta de armazenamento.

## <a name="get-the-apache-zookeeper-and-broker-host-information"></a>Obter as informações de host do Apache Zookeeper e do Broker

Ao trabalhar com Kafka, você deve conhecer os hosts do *Apache Zookeeper* e *Broker*. Esses hosts são usados com a API Kafka e muitos dos utilitários fornecidos com o Kafka.

Nesta seção, você obtém as informações do host da API REST Ambari no cluster.

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Na conexão SSH, use o comando a seguir para instalar o utilitário `jq`. Esse utilitário é usado para analisar documentos JSON e é útil para recuperar as informações do host:

    ```bash
    sudo apt -y install jq
    ```

1. Para definir uma variável de ambiente para o nome do cluster, use o seguinte comando:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

    Ao receber a solicitação, insira o nome do cluster Kafka.

1. Para definir uma variável de ambiente com informações de host Zookeeper, use o comando especificado abaixo. O comando recupera todos os hosts Zookeeper e retorna apenas as duas primeiras entradas. Isso ocorre porque você deseja certa redundância no caso de um host ficar inacessível.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Ao receber a solicitação, insira a senha para a conta de logon do cluster (não da conta de SSH).

1. Para verificar se a variável de ambiente é definida corretamente, use o seguinte comando:

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    Esse comando retorna informações semelhantes ao seguinte texto:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

1. Para definir uma variável de ambiente com informações de host Broker do Kafka, use o seguinte comando:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    Ao receber a solicitação, insira a senha para a conta de logon do cluster (não da conta de SSH).

1. Para verificar se a variável de ambiente é definida corretamente, use o seguinte comando:

    ```bash
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    Esse comando retorna informações semelhantes ao seguinte texto:

    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Gerenciar tópicos do Apache Kafka

O Kafka armazena fluxos de dados em *tópicos*. Você pode usar o utilitário `kafka-topics.sh` para gerenciar tópicos.

* **Para criar um tópico**, use o seguinte comando para criar uma conexão SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Esse comando se conecta ao Zookeeper usando as informações de host armazenadas em `$KAFKAZKHOSTS`. Depois, ele cria um tópico Kafka chamado **teste**.

    * Os dados armazenados neste tópico são particionados em oito partições.

    * Cada partição é replicada em três nós de trabalho no cluster.

        Se você tiver criado o cluster em uma região do Azure que fornece três domínios de falha, use um fator de replicação de 3. Caso contrário, use um fator de replicação de 4.
        
        Em regiões com três domínios de falha, um fator de replicação de 3 permite a distribuição das réplicas entre os domínios de falha. Em regiões com dois domínios de falha, um fator de replicação de quatro permite a distribuição uniforme das réplicas entre os domínios.
        
        Para obter informações sobre o número de domínios de falha em uma região, consulte o documento [Disponibilidade de máquinas virtuais do Linux](../../virtual-machines/availability.md).

        O Kafka não está ciente dos domínios de falha do Azure. Durante a criação de réplicas da partição para tópicos, ele não poderá distribuir réplicas corretamente para alta disponibilidade.

        Para garantir a alta disponibilidade, use a [Ferramenta de redistribuição de partição do Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Essa ferramenta deve ser executada em uma sessão SSH para o nó principal do seu cluster Kafka.

        Para ter a mais alta disponibilidade de seus dados do Kafka, você deve balancear novamente as réplicas de partição do tópico quando:

        * Você criar um novo tópico ou partição

        * Você escalar um cluster verticalmente

* **Para listar os tópicos**, use o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Esse comando lista os tópicos disponíveis no cluster Kafka.

* **Para excluir um tópico**, use o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    Esse comando exclui o tópico chamado `topicname`.

    > [!WARNING]  
    > Se você excluir o tópico `test` criado anteriormente, deverá recriá-lo. Isso é usado em etapas posteriores neste documento.

Para saber mais sobre os comandos disponíveis com o utilitário `kafka-topics.sh`, use o seguinte comando:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>Produzir e consumir registros

O Kafka armazena *registros* nos tópicos. Os registros são produzidos por *produtores* e consumidos por *consumidores*. Os produtores e consumidores se comunicam com o serviço *Agente de Kafka*. Cada nó de trabalho no cluster HDInsight é um host de agente do Kafka.

Para armazenar registros no tópico teste criado anteriormente e lê-los usando um consumidor, use as seguintes etapas:

1. Para gravar registros no tópico, use o utilitário `kafka-console-producer.sh` da conexão SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```

    Após esse comando, você chega em uma linha em branco.

1. Digite uma mensagem de texto na linha em branco e pressione enter. Insira algumas mensagens dessa forma e depois use **Ctrl + C** para retornar ao prompt normal. Cada linha é enviada como um registro separado para o tópico Kafka.

1. Para ler registros no tópico, use o utilitário `kafka-console-consumer.sh` da conexão SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```

    Esse comando recupera os registros do tópico e os exibe. O uso de `--from-beginning` instrui o consumidor a começar do início do fluxo, para que todos os registros sejam recuperados.

    Se estiver usando uma versão mais antiga do Kafka, substitua `--bootstrap-server $KAFKABROKERS` por `--zookeeper $KAFKAZKHOSTS`.

1. Use __Ctrl + C__ para interromper o consumidor.

Você também pode criar programaticamente produtores e consumidores. Para obter um exemplo de como usar essa API, confira o documento [API do Produtor e Consumidor do Apache Kafka com HDInsight](apache-kafka-producer-consumer-api.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Após concluir o início rápido, poderá ser conveniente excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso.

No portal do Azure, navegue até seu cluster e selecione **Excluir**.

:::image type="content" source="./media/apache-kafka-quickstart-resource-manager-template/azure-portal-delete-kafka.png" alt-text="HBase do modelo do Resource Manager" border="false":::

Também é possível selecionar o nome do grupo de recursos para abrir a página do grupo de recursos, e depois selecionar **Excluir grupo de recursos**. Ao excluir o grupo de recursos, você exclui o cluster HDInsight e a conta de armazenamento padrão.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a criar um cluster do Apache Kafka no HDInsight usando um modelo do Resource Manager. No próximo artigo, você aprenderá a criar um aplicativo que usa a API de Streams do Apache Kafka e a executá-lo com o Kafka no HDInsight.

> [!div class="nextstepaction"]
> [Usar a API de Streams do Apache Kafka no Azure HDInsight](./apache-kafka-streams-api.md)