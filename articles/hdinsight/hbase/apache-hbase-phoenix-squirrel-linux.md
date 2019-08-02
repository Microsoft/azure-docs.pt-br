---
title: 'Início Rápido: Consulta do Apache HBase no Azure HDInsight – Apache Phoenix'
description: Neste início rápido, você aprendeu a usar o Apache Phoenix no HDInsight. Além disso, saiba como instalar e configurar o SQLLine no seu computador para se conectar a um cluster HBase no HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 20af6d32d03ae5d4fe37b1a37198ef1f2c50ec95
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137414"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Início Rápido: Consulta do Apache HBase no Azure HDInsight com o Apache Phoenix

Neste início rápido, você aprenderá a usar o Apache Phoenix para executar consultas do HBase no Azure HDInsight. O Apache Phoenix é um mecanismo de consulta SQL para o Apache HBase. Ele é acessado como um driver JDBC e permite consultar e gerenciar tabelas do HBase utilizando o SQL. [SQLLine](http://sqlline.sourceforge.net/) é um utilitário de linha de comando para executar o SQL.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache HBase. Confira [Criar cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) para criar um cluster HDInsight.  Escolha o tipo de cluster **HBase**.

* Um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>Identificar um nó do ZooKeeper

Quando se conectar a um cluster HBase, você precisará se conectar a um dos nós do Apache ZooKeeper. Cada cluster HDInsight tem três nós do ZooKeeper. É possível usar o Curl para identificar rapidamente um nó do ZooKeeper. Para editar o comando curl a seguir, substitua `PASSWORD` e `CLUSTERNAME` pelos valores relevantes e, em seguida, digite o comando em um prompt de comando:

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

Uma parte da saída será semelhante a:

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

Anote o valor de `host_name` para uso posterior.

## <a name="create-a-table-and-manipulate-data"></a>Criar uma tabela e manipular dados

Você pode usar o SSH para se conectar a clusters HBase e, em seguida, usar o Apache Phoenix para criar tabelas do HBase, inserir dados e consultar dados.

1. Use o comando `ssh` para se conectar ao cluster HBase. Edite o comando abaixo substituindo `CLUSTERNAME` pelo nome do cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Altere o diretório para o cliente do Phoenix. Digite o seguinte comando:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. Inicie o [SQLLine](http://sqlline.sourceforge.net/). Edite o comando abaixo substituindo `ZOOKEEPER` pelo nome do nó do ZooKeeper identificado anteriormente e, em seguida, insira o comando:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Crie uma tabela do HBase. Digite o seguinte comando:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. Use o comando `!tables` do SQLLine para listar todas as tabelas no HBase. Digite o seguinte comando:

    ```sqlline
    !tables
    ```

6. Insira os valores na tabela. Digite o seguinte comando:

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. Consulte a tabela. Digite o seguinte comando:

    ```sql
    SELECT * FROM Company;
    ```

8. Exclua um registro. Digite o seguinte comando:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. Descarte a tabela. Digite o seguinte comando:

    ```hbase
    DROP TABLE Company;
    ```

10. Use o comando `!quit` do SQLLine para sair do SQLLine. Digite o seguinte comando:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Após concluir o início rápido, poderá ser conveniente excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso.

Para excluir um cluster, confira [Delete an HDInsight cluster using your browser, PowerShell, or the Azure CLI](../hdinsight-delete-cluster.md) (Excluir um cluster HDInsight usando o navegador, o PowerShell ou a CLI do Azure).

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a usar o Apache Phoenix para executar consultas do HBase no Azure HDInsight. Para saber mais sobre o Apache Phoenix, o próximo artigo fornecerá uma análise mais detalhada.

> [!div class="nextstepaction"]
> [Apache Phoenix no HDInsight](../hdinsight-phoenix-in-hdinsight.md)
