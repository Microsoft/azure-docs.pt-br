---
title: 'Tutorial: Fluxo estruturado do Apache Spark com Apache Kafka – Azure HDInsight'
description: Saiba como usar o streaming do Apache Spark para transmitir dados para dentro ou fora do Apache Kafka. Neste tutorial, você deve transmitir dados usando um bloco de anotações do Jupyter do Spark no HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: hrasheed
ms.openlocfilehash: da31b6a880344de918a3b3e0f89f60d985db2ce7
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406029"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Tutorial: Usar o fluxo estruturado do Apache Spark com o Apache Kafka no HDInsight

Este tutorial demonstra como usar o [fluxo estruturado do Apache Spark](https://spark.apache.org/docs/latest/structured-streaming-programming-guide) para ler e gravar dados com o [Apache Kafka](https://kafka.apache.org/) no Azure HDInsight.

O Streaming Estruturado do Spark é um mecanismo de processamento de fluxo baseado no Spark SQL. Ele permite expressar cálculos de streaming do mesmo modo que cálculos de lote em dados estáticos.  

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Usar um modelo do Azure Resource Manager para criar clusters
> * Usar o Streaming Estruturado do Spark com o Kafka

Quando você terminar as etapas neste documento, lembre-se de excluir os clusters para evitar cobranças em excesso.

## <a name="prerequisites"></a>Pré-requisitos

* jq, um processador JSON de linha de comando.  Veja [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Familiaridade com o uso de [Jupyter Notebook](https://jupyter.org/) com Spark no HDInsight. Para saber mais, confira o documento [Carregar dados e executar consultas com o Apache Spark no HDInsight](spark/apache-spark-load-data-run-query.md).

* Familiaridade com a linguagem de programação [Scala](https://www.scala-lang.org/). O código usado neste tutorial é gravado em Scala.

* Familiaridade com a criação de tópicos Kafka. Para saber mais, confira o documento [Início rápido do Apache Kafka no HDInsight](kafka/apache-kafka-get-started.md).

> [!IMPORTANT]  
> As etapas neste documento requerem um grupo de recursos do Azure que contém um Spark no HDInsight e um Kafka no cluster de HDInsight. Esses clusters são ambos localizados em uma Rede Virtual do Azure, que permite que o cluster Spark se comunique diretamente com o cluster Kafka.
> 
> Para sua conveniência, este documento direciona para um modelo que pode criar todos os recursos necessários do Azure. 
>
> Para obter mais informações sobre como usar o HDInsight em uma rede virtual, confira o documento [Planejar uma rede virtual para o HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="structured-streaming-with-apache-kafka"></a>Fluxo estruturado com Apache Kafka

O fluxo estruturado do Spark é um mecanismo de processamento de fluxo baseado no mecanismo SQL do Spark. Ao usar fluxo estruturado, você pode gravar consultas de fluxo da mesma maneira que você grava consultas de lote.

Os snippets de código a seguir demonstram a leitura do Kafka e o armazenamento de arquivo. A primeira é uma operação em lote, enquanto a segunda é uma operação de fluxo:

```scala
// Read a batch from Kafka
val kafkaDF = spark.read.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data and write to file
kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .write
                .format("parquet")
                .option("path","/example/batchtripdata")
                .option("checkpointLocation", "/batchcheckpoint")
                .save()
```

```scala
// Stream from Kafka
val kafkaStreamDF = spark.readStream.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data from the stream and write to file
kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .writeStream
                .format("parquet")
                .option("path","/example/streamingtripdata")
                .option("checkpointLocation", "/streamcheckpoint")
                .start.awaitTermination(30000)
```

Em ambos os snippets de código, os dados são lidos do Kafka e gravados no arquivo. As diferenças entre os exemplos são:

| Lote | Fluxo |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

A operação de streaming também usa `awaitTermination(30000)`, que interrompe o fluxo após 30.000 ms. 

Para usar o fluxo estruturado com Kafka, seu projeto deve ter uma dependência no pacote `org.apache.spark : spark-sql-kafka-0-10_2.11`. A versão deste pacote deve corresponder à versão do Spark no HDInsight. Para Spark 2.2.0 (disponível no HDInsight 3.6), você pode encontrar as informações de dependência para diferentes tipos de projeto em [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar).

Para o Jupyter Notebook usado com este tutorial, a seguinte célula carrega essa dependência de pacote:

```
%%configure -f
{
    "conf": {
        "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
        "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
    }
}
```

## <a name="create-the-clusters"></a>Criar os clusters

Apache Kafka no HDInsight não fornece acesso para Agentes de Kafka pela internet pública. Qualquer coisa que usa Kafka deve estar na mesma rede virtual do Azure. Neste tutorial, os clusters de Spark e Kafka estão localizados na mesma rede virtual do Azure. 

O diagrama a seguir mostra como a comunicação flui entre o Spark e o Kafka:

![Diagrama de clusters Spark e Kafka em uma rede virtual do Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]  
> O serviço Kafka é limitado a comunicação dentro da rede virtual. Outros serviços no cluster, como SSH e Ambari, podem ser acessados pela Internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, consulte [portas e URIs usados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Para criar uma Rede Virtual do Azure e, em seguida, criar os clusters Kafka e Spark dentro dela, use as seguintes etapas:

1. Use o botão a seguir para entrar no Azure e abra o modelo do Gerenciador de Recursos no portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>

    O modelo do Azure Resource Manager está localizado em **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json** .

    Este modelo cria os seguintes recursos:

   * Um cluster Kafka no HDInsight 3.6.
   * Um Spark 2.2.0 no cluster HDInsight 3.6.
   * Uma Rede Virtual do Azure, que contém os clusters HDInsight.

     > [!IMPORTANT]  
     > O bloco de anotações de fluxo estruturado usado neste exemplo requer o Spark 2.2.0 no HDInsight 3.6. Se você usar uma versão anterior do Spark no HDInsight, você receberá erros ao usar o bloco de anotações.

2. Use as informações a seguir para preencher as entradas na seção **Modelo personalizado**:

    | Configuração | Valor |
    | --- | --- |
    | Assinatura | Sua assinatura do Azure |
    | Grupo de recursos | O grupo de recursos que contém os recursos. |
    | Local padrão | A região do Azure em que os recursos são criados. |
    | Nome do cluster do Spark | O nome do cluster do Spark. Os primeiro de seis caracteres devem ser diferentes do nome de cluster Kafka. |
    | Nome do cluster do Kafka | O nome do cluster do Kafka. Os primeiro de seis caracteres devem ser diferentes do nome de cluster Spark. |
    | Nome de usuário de logon do cluster | O nome de usuário administrador para os clusters. |
    | Senha de logon do cluster | A senha de usuário administrador para os clusters. |
    | Nome de usuário do SSH | O usuário do SSH para criar os clusters. |
    | Senha SSH | A senha para o usuário do SSH. |
   
    ![Captura de tela do modelo personalizado](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

3. Leia os **Termos e Condições** e, em seguida, selecione **Eu concordo com os termos e condições declarados acima**

4. Por fim, marque **Fixar no painel** e selecione **Comprar**. 

> [!NOTE]  
> A criação de clusters pode levar até 20 minutos.

## <a name="use-spark-structured-streaming"></a>Usar o Streaming Estruturado do Spark

Este exemplo demonstra como usar o Streaming Estruturado do Spark com o Kafka no HDInsight. Ele usa dados de corridas de táxi, que são fornecidos pela cidade de Nova York.  O conjunto de dados usado por este notebook foi obtido dos [Dados de Táxis Verdes de 2016](https://data.cityofnewyork.us/Transportation/2016-Green-Taxi-Trip-Data/hvrh-b6nb).

1. Colete informações do host. Use os comandos do curl e do [jq](https://stedolan.github.io/jq/) abaixo para obter as informações do Kafka ZooKeeper e de hosts do agente. Os comandos foram projetados para um prompt de comando do Windows; pequenas variações serão necessárias para outros ambientes. Substitua `KafkaCluster` pelo nome do cluster Kafka e `KafkaPassword` pela senha de logon do cluster. Além disso, substitua `C:\HDI\jq-win64.exe` pelo caminho real da instalação do jq. Insira os comandos em um prompt de comando do Windows e salve a saída para uso em etapas posteriores.

    ```cmd
    set CLUSTERNAME=KafkaCluster
    set PASSWORD=KafkaPassword
    
    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")"
    
    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/KAFKA/components/KAFKA_BROKER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")"
    ```

2. No navegador da Web, conecte-se ao bloco de anotações do Jupyter em seu cluster Spark. Na URL a seguir, substitua `CLUSTERNAME` pelo nome do cluster __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Quando solicitado, insira o nome de usuário e senha que você inseriu ao criar o cluster.

3. Selecione **Novo > Spark** para criar um notebook.

4. Carregue os pacotes usados pelo Notebook inserindo as informações a seguir em uma célula do Notebook. Execute o comando usando **CTRL + ENTER**.

    ```
    %%configure -f
    {
        "conf": {
            "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
            "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
        }
    }
    ```

5. Crie o tópico do Kafka. Edite o comando abaixo substituindo `YOUR_ZOOKEEPER_HOSTS` pelas informações de host do ZooKeeper extraídas na primeira etapa. Insira o comando editado no Jupyter Notebook para criar o tópico `tripdata`.

    ```scala
    %%bash
    export KafkaZookeepers="YOUR_ZOOKEEPER_HOSTS"

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic tripdata --zookeeper $KafkaZookeepers
    ```

6. Recupere os dados de corridas de táxi. Insira o comando na próxima célula para carregar os dados de corridas de táxi em Nova York. Os dados são carregados em um dataframe e, em seguida, o dataframe é exibido como a saída da célula.

    ```scala
    import spark.implicits._

    // Load the data from the New York City Taxi data REST API for 2016 Green Taxi Trip Data
    val url="https://data.cityofnewyork.us/resource/pqfs-mqru.json"
    val result = scala.io.Source.fromURL(url).mkString
    
    // Create a dataframe from the JSON data
    val taxiDF = spark.read.json(Seq(result).toDS)
    
    // Display the dataframe containing trip data
    taxiDF.show()
    ```

7. Defina as informações de hosts do agente do Kafka. Substitua `YOUR_KAFKA_BROKER_HOSTS` pelas informações de hosts do agente extraídas na etapa 1.  Insira o comando editado na próxima célula do Jupyter Notebook.

    ```scala
    // The Kafka broker hosts and topic used to write to Kafka
    val kafkaBrokers="YOUR_KAFKA_BROKER_HOSTS"
    val kafkaTopic="tripdata"
    
    println("Finished setting Kafka broker and topic configuration.")
    ```

8. Envie os dados para o Kafka. No comando a seguir, o campo `vendorid` é usado como o valor da chave para a mensagem do Kafka. A chave é usada pelo Kafka durante o particionamento de dados. Todos os campos são armazenados na mensagem do Kafka como um valor de cadeia de caracteres JSON. Insira o comando a seguir no Jupyter para salvar os dados no Kafka usando uma consulta em lotes.

    ```scala
    // Select the vendorid as the key and save the JSON string as the value.
    val query = taxiDF.selectExpr("CAST(vendorid AS STRING) as key", "to_JSON(struct(*)) AS value").write.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("topic", kafkaTopic).save()

    println("Data sent to Kafka")
    ```

9. Declare um esquema. O comando a seguir demonstra como usar um esquema durante a leitura de dados JSON do Kafka. Insira o comando na próxima célula do Jupyter.

    ```scala
    // Import bits useed for declaring schemas and working with JSON data
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Define a schema for the data
    val schema = (new StructType).add("dropoff_latitude", StringType).add("dropoff_longitude", StringType).add("extra", StringType).add("fare_amount", StringType).add("improvement_surcharge", StringType).add("lpep_dropoff_datetime", StringType).add("lpep_pickup_datetime", StringType).add("mta_tax", StringType).add("passenger_count", StringType).add("payment_type", StringType).add("pickup_latitude", StringType).add("pickup_longitude", StringType).add("ratecodeid", StringType).add("store_and_fwd_flag", StringType).add("tip_amount", StringType).add("tolls_amount", StringType).add("total_amount", StringType).add("trip_distance", StringType).add("trip_type", StringType).add("vendorid", StringType)
    // Reproduced here for readability
    //val schema = (new StructType)
    //   .add("dropoff_latitude", StringType)
    //   .add("dropoff_longitude", StringType)
    //   .add("extra", StringType)
    //   .add("fare_amount", StringType)
    //   .add("improvement_surcharge", StringType)
    //   .add("lpep_dropoff_datetime", StringType)
    //   .add("lpep_pickup_datetime", StringType)
    //   .add("mta_tax", StringType)
    //   .add("passenger_count", StringType)
    //   .add("payment_type", StringType)
    //   .add("pickup_latitude", StringType)
    //   .add("pickup_longitude", StringType)
    //   .add("ratecodeid", StringType)
    //   .add("store_and_fwd_flag", StringType)
    //   .add("tip_amount", StringType)
    //   .add("tolls_amount", StringType)
    //   .add("total_amount", StringType)
    //   .add("trip_distance", StringType)
    //   .add("trip_type", StringType)
    //   .add("vendorid", StringType)
    
    println("Schema declared")
    ```

10. Selecione dados e inicie o fluxo. O comando a seguir demonstra como recuperar dados do Kafka usando uma consulta em lotes e, em seguida, gravar os resultados no HDFS no cluster Spark. Neste exemplo, o comando `select` recupera a mensagem (campo de valor) do Kafka e aplica o esquema a ela. Os dados são então gravados no HDFS (WASB ou ADL) no formato parquet. Insira o comando na próxima célula do Jupyter.

    ```scala
    // Read a batch from Kafka
    val kafkaDF = spark.read.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()
    
    // Select data and write to file
    val query = kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip").write.format("parquet").option("path","/example/batchtripdata").option("checkpointLocation", "/batchcheckpoint").save()
    
    println("Wrote data to file")
    ```

11. Verifique se os arquivos foram criados inserindo o comando na próxima célula do Jupyter. Ele lista os arquivos no diretório `/example/batchtripdata`.

    ```scala
    %%bash
    hdfs dfs -ls /example/batchtripdata
    ```

12. Embora o exemplo anterior tenha usado uma consulta em lotes, o comando a seguir demonstra como fazer a mesma coisa usando uma consulta de streaming. Insira o comando na próxima célula do Jupyter.

    ```scala
    // Stream from Kafka
    val kafkaStreamDF = spark.readStream.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()
    
    // Select data from the stream and write to file
    kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip").writeStream.format("parquet").option("path","/example/streamingtripdata").option("checkpointLocation", "/streamcheckpoint").start.awaitTermination(30000)
    println("Wrote data to file")
    ```

13. Execute a célula a seguir para verificar se os arquivos foram gravados pela consulta de streaming.

    ```scala
    %%bash
    hdfs dfs -ls /example/streamingtripdata
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados por este tutorial, você pode excluir o grupo de recursos. A exclusão do grupo de recursos também exclui o cluster HDInsight associado e todos os outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos usando o portal do Azure:

1. No portal do Azure, expanda o menu à esquerda para abrir o menu de serviços e escolha __Grupo de Recursos__ para exibir a lista dos seus grupos de recursos.
2. Localize o grupo de recursos a ser excluído e clique com o botão direito do mouse no botão __Mais__ (...) do lado direito da lista.
3. Selecione __Excluir grupo de recursos__ e confirme.

> [!WARNING]  
> A cobrança do cluster HDInsight começa quando um cluster é criado e para quando o cluster é excluído. A cobrança ocorre por minuto, portanto, sempre exclua o cluster quando ele não estiver mais sendo usado.
> 
> Excluir um Kafka no cluster HDInsight exclui todos os dados armazenados no Kafka.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar o [Fluxo Estruturado do Apache Spark](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) para gravar e ler dados do [Apache Kafka](https://kafka.apache.org/) no HDInsight. Use o link a seguir para aprender a usar o [Apache Storm](https://storm.apache.org/) com o Kafka.

> [!div class="nextstepaction"]
> [Usar o Apache Storm com o Apache Kafka](hdinsight-apache-storm-with-kafka.md)
