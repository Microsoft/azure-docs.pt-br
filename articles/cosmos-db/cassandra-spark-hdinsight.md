---
title: Acesse a API do Cassete do Azure Cosmos DB a partir do Spark no YARN com o HDInsight
description: Este artigo aborda como trabalhar com a API do Azure Cosmos DB Cassandra do Spark no YARN com o HDInsight
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f728baedf9e325f224ce52e64325064f553d2671
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60893691"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Acesse a API do Cassete do Azure Cosmos DB a partir do Spark no YARN com o HDInsight

Este artigo aborda como acessar a API do Azure Cosmos DB Cassandra do Spark no YARN com o HDInsight-Spark do shell de ignição. O HDInsight é o Hortonworks Hadoop PaaS da Microsoft no Azure, que aproveita o armazenamento de objetos para o HDFS e é fornecido em vários tipos, incluindo [Spark](../hdinsight/spark/apache-spark-overview.md).  Embora o conteúdo deste documento faça referência ao HDInsight-Spark, ele é aplicável a todas as distribuições do Hadoop.  

## <a name="prerequisites"></a>Pré-requisitos

* [Provisionar a API Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)

* [Examine os conceitos básicos de se conectar à API Cassandra do Azure Cosmos DB](cassandra-spark-generic.md)

* [Provisionar um cluster HDInsight Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Examine os exemplos de código para trabalhar com a API do Cassandra](cassandra-spark-generic.md#next-steps)

* [Usar cqlsh para validação se você assim preferir](cassandra-spark-generic.md##connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Configuração da API do Cassandra no Spark2** – O conector do Spark para o Cassandra exige que os detalhes da conexão do Cassandra sejam inicializados como parte do contexto do Spark. Quando você inicia um notebook Jupyter, a sessão de ignição e o contexto já estão inicializados e não é aconselhável parar e reinicializar o contexto do Spark, a menos que esteja completo com cada configuração definida como parte da inicialização do notebook Jupyter padrão do HDInsight. Uma solução alternativa é incluir os detalhes da instância do Cassandra na configuração do serviço Ambari, Spark2 diretamente. Esta é uma atividade única por cluster que requer uma reinicialização do serviço Spark2.
 
  1. Vá para o Ambari, Spark2 serviço e selecionadas configurações

  2. Em seguida, vá para custom spark2-defaults e inclua uma nova propriedade com o seguinte e reinicie o serviço Spark2:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Acessar o Azure Cosmos DB Cassandra API do shell Spark

O shell Spark é usado para fins de teste / exploração.

* Inicie o spark-shell com as dependências do maven necessárias, compatíveis com a versão do Spark do seu cluster.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Execute algumas operações de DDL e DML

  ```scala
  import org.apache.spark.rdd.RDD
  import org.apache.spark.{SparkConf, SparkContext}

  import spark.implicits._
  import org.apache.spark.sql.functions._
  import org.apache.spark.sql.Column
  import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType,LongType,FloatType,DoubleType, TimestampType}
  import org.apache.spark.sql.cassandra._

  //Spark connector
  import com.datastax.spark.connector._
  import com.datastax.spark.connector.cql.CassandraConnector

  //CosmosDB library for multiple retry
  import com.microsoft.azure.cosmosdb.cassandra

  // Specify connection factory for Cassandra
  spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

  // Parallelism and throughput configs
  spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
  spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
  spark.conf.set("spark.cassandra.output.concurrent.writes", "100")
  spark.conf.set("spark.cassandra.concurrent.reads", "512")
  spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
  spark.conf.set("spark.cassandra.connection.keep_alive_ms", "60000000") //Increase this number as needed
  ```

* Executar operações CRUD

  ```scala
  //1) Create table if it does not exist
  val cdbConnector = CassandraConnector(sc)
  cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000;"))

  //2) Delete data from potential prior runs
  cdbConnector.withSessionDo(session => session.execute("DELETE FROM books_ks.books WHERE book_id IN ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999','b000009');"))

  //3) Generate a few rows
  val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
  ).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

  //4) Persist
  booksDF.write.mode("append").format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000")).save()

  //5) Read the data in the table
  spark.read.format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks")).load.show
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>API de Cassandra acesso do Azure Cosmos DB de blocos de anotações do Jupyter

HDInsight Spark vem com os serviços de bloco de anotações do Zeppelin e Jupyter. Eles são ambos os ambientes de notebook baseado na web que dão suporte ao Scala e Python. Blocos de anotações são ótimos para análises EXPLORATÓRIAS interativas e colaboração, mas não se destina a processos operacionais/productionized.

Os seguintes blocos de anotações do Jupyter podem ser carregados em seu cluster Spark do HDInsight e fornecem exemplos prontos para trabalhar com a API do Cassandra do Azure Cosmos DB. Certifique-se de examinar o primeiro notebook `1.0-ReadMe.ipynb` para examinar a configuração de serviço do Spark para conectar-se à API Cassandra do Azure Cosmos DB.

Baixe esses notebooks sob [azure-cosmos-db-cassandra-api-spark-notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/master/scala/) em seu computador.
  
### <a name="how-to-upload"></a>Como carregar:
Ao iniciar o Jupyter, navegue até o Scala. Crie um diretório pela primeira vez e, em seguida, carregar os blocos de anotações no diretório. O botão carregar é superior, direita lado.  

### <a name="how-to-run"></a>Como executar:
Execute em sequência por meio de cada célula do bloco de anotações e os blocos de anotações.  Clique no botão de execução na parte superior de cada bloco de anotações para executar todas as células, ou shift + enter para cada célula.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Acesse com o Azure Cosmos DB Cassandra API do seu programa Spark Scala

Para processos automatizados em produção, os programas Spark são enviados ao cluster por meio de [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html).

## <a name="next-steps"></a>Próximas etapas

* [Como criar um programa do Spark Scala em um IDE e enviá-lo ao cluster do HDInsight Spark por meio do Livy para execução](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Como se conectar a API Cassandra do Azure Cosmos DB a partir de um programa Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Lista completa de exemplos de código para trabalhar com a API do Cassandra](cassandra-spark-generic.md)
