---
title: Operações de Apache Spark compatíveis com o Hive Warehouse Connector no Azure HDInsight
description: Saiba mais sobre os diferentes recursos do Hive Warehouse Connector no Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: 1799aff8bff96d404ddcbefbf58a5f5014cdba6a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871581"
---
# <a name="apache-spark-operations-supported-by-hive-warehouse-connector-in-azure-hdinsight"></a>Operações de Apache Spark compatíveis com o Hive Warehouse Connector no Azure HDInsight

Este artigo mostra as operações baseadas em Spark compatíveis com o HWC (Hive Warehouse Connector). Todos os exemplos mostrados abaixo serão executados por meio do shell do Apache Spark.

## <a name="prerequisite"></a>Pré-requisito

Conclua as etapas da [instalação do Hive Warehouse Connector](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup).

## <a name="getting-started"></a>Introdução

Para iniciar uma sessão do shell do Spark, execute as seguintes etapas:

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster do Apache Spark. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Em sua sessão SSH, execute o seguinte comando para anotar a versão de `hive-warehouse-connector-assembly`:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Edite o código abaixo com a versão de `hive-warehouse-connector-assembly` identificada acima. Para executar o comando para iniciar o shell do Spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Depois de iniciar o shell do Spark, uma instância do Hive Warehouse Connector pode ser iniciada usando os seguintes comandos:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

## <a name="creating-spark-dataframes-using-hive-queries"></a>Criar DataFrames do Spark usando consultas do Hive

Os resultados de todas as consultas que usam a biblioteca do HWC são retornados como um DataFrame. Os exemplos a seguir demonstram como criar uma consulta básica do Hive.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Os resultados da consulta são os DataFrames do Spark, que podem ser usados com bibliotecas do Spark como MLIB e SparkSQL.

## <a name="writing-out-spark-dataframes-to-hive-tables"></a>Gravar DataFrames do Spark em tabelas do Hive

O Spark não dá suporte nativo à gravação em tabelas ACID gerenciadas do Hive. No entanto, usando o HWC, você pode gravar qualquer DataFrame em uma tabela do Hive. Você pode ver essa funcionalidade em operação no seguinte exemplo:

1. Crie uma tabela chamada `sampletable_colorado` e especifique as colunas dela usando o seguinte comando:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Filtre a tabela `hivesampletable` em que a coluna `state` é igual a `Colorado`. Esta consulta do Hive retorna um DataFrame do Spark e é salva na tabela `sampletable_colorado` do Hive usando a função `write`.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table","sampletable_colorado").save()
    ```

1. Veja os resultados usando o seguinte comando:

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png" alt-text="hive warehouse connector show hive table" border="true":::


## <a name="structured-streaming-writes"></a>Gravações de streaming estruturado

Usando o Hive Warehouse Connector, você pode usar o streaming do Spark para gravar dados em tabelas do Hive.

> [!IMPORTANT]
> Gravações de streaming estruturado não são compatíveis com clusters Spark 4.0 habilitados para ESP.

Siga as etapas abaixo para ingerir dados de um fluxo do Spark na porta localhost 9999 em uma tabela do Hive por meio do Hive Warehouse Connector.

1. No shell do Spark aberto, inicie um fluxo do Spark com o seguinte comando:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Gere dados para o fluxo do Spark que você criou, executando as seguintes etapas:
    1. Abra uma segunda sessão SSH no mesmo cluster do Spark.
    1. No prompt de comando, digite `nc -lk 9999`. Esse comando usa o utilitário netcat para enviar dados da linha de comando para a porta especificada.

1. Retorne à primeira sessão SSH e crie uma tabela do Hive para armazenar os dados de streaming. No shell do Spark, digite o seguinte comando:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Em seguida, grave os dados de streaming na tabela recém-criada usando o seguinte comando:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource").option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > Atualmente, as opções `metastoreUri` e `database` precisam ser definidas manualmente devido a um problema conhecido no Apache Spark. Para obter mais informações sobre esse problema, confira [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Retorne à segunda sessão SSH e insira os seguintes valores:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Retorne à primeira sessão SSH e observe a atividade breve. Para ver os dados, use o seguinte comando:

    ```scala
    hive.table("stream_table").show()
    ```

Use **CTRL + C** para interromper o netcat na segunda sessão SSH. Use `:q` para sair do shell do Spark na primeira sessão SSH.

## <a name="next-steps"></a>Próximas etapas

* [Integração do HWC com o Apache Spark e o Apache Hive](./apache-hive-warehouse-connector.md)
* [Usar a Consulta Interativa com o HDInsight](./apache-interactive-query-get-started.md)
* [Integração do HWC ao Apache Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)
