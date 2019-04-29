---
title: Usar o Spark para ler e gravar dados do HBase - Azure HDInsight
description: Use o conector do HBase Spark para ler e gravar dados de um cluster Spark para um cluster HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: e3f5cb726dddbdbfbd1b1f48c800ac681e7a174c
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765679"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Usar o Apache Spark para ler e gravar dados do Apache HBase

Apache HBase costuma ser consultada com sua API de nível inferior (verificações, obtenções e colocações) ou com uma sintaxe SQL usando Apache Phoenix. Apache também fornece conector HBase Apache Spark, que é uma alternativa conveniente e de alto desempenho para consultar e modificar os dados armazenados pelo HBase.

## <a name="prerequisites"></a>Pré-requisitos

* Dois separados clusters HDInsight, um HBase e Spark um com pelo menos Spark 2.1 (HDInsight 3.6) instalado.
* O cluster Spark precisa se comunicar diretamente com o cluster HBase com latência mínima, portanto a configuração recomendada é implantar ambos os clusters na mesma rede virtual. Para obter mais informações, consulte [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).
* Um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
* O [esquema de URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) para seu armazenamento primário de clusters. Isso seria wasb: / / para o armazenamento de BLOBs do Azure, abfs: / / para o armazenamento do Azure Data Lake Gen2 ou adl: / / para o Azure Data Lake armazenamento Gen1. Se a transferência segura é habilitada para o armazenamento de BLOBs ou Data Lake armazenamento Gen2, o URI seria wasbs: / / ou abfss: / /, respectivamente, consulte também [transferência segura](../storage/common/storage-require-secure-transfer.md).


## <a name="overall-process"></a>Processo geral

O processo de alto nível para habilitar seu cluster Spark para consultar seu cluster HDInsight é o seguinte:

1. Preparar alguns dados de exemplo no HBase.
2. Adquira o arquivo hbase-site.xml da pasta de configuração de cluster HBase (/ etc/hbase/conf).
3. Coloque uma cópia do hbase-site.XML na sua pasta de configuração Spark 2 (/ etc/spark2/conf).
4. Execute `spark-shell` referenciar o conector do HBase Spark por seu Maven coordena na opção `packages`.
5. Defina um catálogo que mapeia o esquema do Spark para HBase.
6. Interagir com os dados do HBase usando as APIs de DataFrame ou RDD.

## <a name="prepare-sample-data-in-apache-hbase"></a>Preparar os dados de exemplo no Apache HBase

Nesta etapa, é possível criar e preencher uma tabela simple no Apache HBase, você pode consultar usando Spark.

1. Conecte-se ao nó principal do cluster HBase usando o SSH. Para obter mais informações, consulte [Conectar ao HDInsight usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).  Editar o comando abaixo, substituindo `HBASECLUSTER` com o nome do cluster HBase, `sshuser` com o ssh usuário o nome da conta e, em seguida, digite o comando.

    ```
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Insira o comando a seguir para iniciar o shell do HBase:

        hbase shell

3. Insira o comando a seguir para criar uma `Contacts` tabela com as famílias de coluna `Personal` e `Office`:

        create 'Contacts', 'Personal', 'Office'

4. Insira os comandos a seguir para carregar algumas linhas de amostra de dados:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

5. Insira o comando a seguir para sair do shell do HBase:

        exit 

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Copie o hbase-site. XML para o cluster Spark
Copie o hbase-site. XML do armazenamento local para a raiz de armazenamento padrão do seu cluster Spark.  Edite o comando a seguir para refletir a configuração.  Em seguida, em sua sessão SSH aberta para o cluster HBase, digite o comando:

| Valor de sintaxe | Novo valor|
|---|---|
|[Esquema de URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Modificar para refletir seu armazenamento.  A sintaxe abaixo é para o armazenamento de BLOBs com transferência segura habilitada.|
|`SPARK_STORAGE_CONTAINER`|Substitua o nome do contêiner de armazenamento padrão usado para o cluster Spark.|
|`SPARK_STORAGE_ACCOUNT`|Substitua pelo nome da conta de armazenamento padrão usado para o cluster Spark.|

```
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Colocar hbase-site.XML em seu cluster Spark

1. Conecte-se ao nó principal do cluster Spark usando o SSH.

2. Insira o comando a seguir para copiar `hbase-site.xml` do armazenamento de padrão do seu cluster Spark para a pasta de configuração Spark 2 no armazenamento local do cluster:

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Execute o Shell de Spark referenciando o conector HBase Spark

1. Em sua sessão SSH aberta para o cluster Spark, digite o comando a seguir para iniciar um spark shell:

    ```
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Mantenha essa instância do Shell do Spark aberta e continue para a próxima etapa.

## <a name="define-a-catalog-and-query"></a>Definir um catálogo e consulta

Nesta etapa, você deve definir um catálogo que mapeia o esquema do Apache Spark para Apache HBase.  

1. Em seu Shell Spark aberto, digite o seguinte `import` instruções:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

2. Insira o comando a seguir para definir um catálogo para a tabela contatos criados no HBase:

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    O código executa o seguinte:  

      a. Definir um esquema de catálogo para a tabela do HBase chamada `Contacts`.  
     b. Identificar a rowkey como `key` e mapear os nomes de coluna usados no Spark para a família de coluna, o nome da coluna e o tipo de coluna como usado no HBase.  
     c. A rowkey também deve ser definida em detalhes como uma coluna nomeada (`rowkey`), que tem uma família de coluna específico `cf` de `rowkey`.  

3. Insira o comando a seguir para definir um método que fornece um DataFrame em torno de seu `Contacts` tabela no HBase:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

4. Crie uma instância do DataFrame:

    ```scala
    val df = withCatalog(catalog)
    ```  

5. Consulte o DataFrame:

    ```scala
    df.show()
    ```

6. Você deve ver duas linhas de dados:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Registre uma tabela temporária para que você possa consultar a tabela do HBase usando Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

8. Emitir uma consulta SQL em relação a `contacts` tabela:

    ```scala
    val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
    query.show()
    ```

9. Você deve ver os resultados como estes:

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>Inserir nova linha

1. Para inserir um novo registro de contato, defina uma `ContactRecord` classe:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

2. Crie uma instância de `ContactRecord` e colocá-la em uma matriz:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

3. Salve a matriz de novos dados em HBase:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

4. Examine os resultados:

    ```scala  
    df.show()
    ```

5. Você deve ver uma saída como a abaixo:

        +------+--------------------+--------------+------------+--------------+
        |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
        +------+--------------------+--------------+------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
        | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
        |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+------------+--------------+

## <a name="next-steps"></a>Próximas etapas

* [Conector Apache Spark HBase](https://github.com/hortonworks-spark/shc)
