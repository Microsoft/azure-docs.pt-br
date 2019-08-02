---
title: Conectar Apache Spark ao Azure Cosmos DB
description: Saiba mais sobre o conector do Spark do Azure do Cosmos do Azure que permite conectar o Apache Spark ao Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: 2a0e88a439400bc36873ed7160b8eb039a16ebfb
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356186"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Acelere a análise de big data usando o conector do Apache Spark para o Azure Cosmos DB

Você pode executar trabalhos do [Spark](https://spark.apache.org/) com dados armazenados em Azure Cosmos DB usando o conector Cosmos DB Spark. Cosmos pode ser usado para processamento em lote e fluxo e como uma camada de serviço para acesso de baixa latência.

Você pode usar o conector com o [Azure Databricks](https://azure.microsoft.com/services/databricks) ou o [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), que fornece clusters Spark gerenciados no Azure. A tabela a seguir mostra as versões do Spark com suporte.

| Componente | Versão |
|---------|-------|
| Apache Spark | 2.4. x, 2.3. x, 2.2. x e 2.1. x |
| Scala | 2.11 |
| Versão de tempo de execução do Azure Databricks | > 3.4 |

> [!WARNING]
> Este conector dá suporte à API de núcleo (SQL) do Azure Cosmos DB.
> Para Cosmos DB API do MongoDB, use o [conector de Spark do MongoDB](https://docs.mongodb.com/spark-connector/master/).
> Para Cosmos DB API do Cassandra, use o [conector do Spark Cassandra](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="quickstart"></a>Início rápido

* Siga as etapas em Introdução ao [SDK do Java](sql-api-async-java-get-started.md) para configurar uma conta de Cosmos DB e preencha alguns dados.
* Siga as etapas em [Azure Databricks introdução](https://docs.azuredatabricks.net/getting-started/index.html) para configurar um Azure Databricks espaço de trabalho e cluster.
* Agora você pode criar novos blocos de anotações e importar a biblioteca do conector do Cosmos DB. Vá para [trabalhando com o conector de Cosmos DB](#bk_working_with_connector) para obter detalhes sobre como configurar seu espaço de trabalho.
* A seção a seguir apresenta trechos de código sobre como ler e gravar usando o conector.

### <a name="batch-reads-from-cosmos-db"></a>Leituras em lote de Cosmos DB

O trecho a seguir mostra como criar um dataframe do Spark para ler de Cosmos DB em PySpark.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "query_custom": "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format(
    "com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

E o mesmo trecho de código em escala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="batch-writes-to-cosmos-db"></a>Gravações em lote para Cosmos DB

O trecho a seguir mostra como gravar um quadro de dados para Cosmos DB no PySpark.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

E o mesmo trecho de código em escala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" : "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>Leituras de streaming de Cosmos DB

O trecho a seguir mostra como se conectar e ler de Azure Cosmos DB feed de alterações.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "ReadChangeFeed": "true",
    "ChangeFeedQueryName": "Departure-Delays",
    "ChangeFeedStartFromTheBeginning": "false",
    "InferStreamSchema": "true",
    "ChangeFeedCheckpointLocation": "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
           .readStream
           .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
           .options(**readConfig)
           .load())
```
E o mesmo trecho de código em escala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "ReadChangeFeed" -> "true",
  "ChangeFeedQueryName" -> "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" -> "false",
  "InferStreamSchema" -> "true",
  "ChangeFeedCheckpointLocation" -> "dbfs:/Departure-Delays"
))

// Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
val df = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(readConfig).load()
```

### <a name="streaming-writes-to-cosmos-db"></a>Streaming de gravações para Cosmos DB

O trecho a seguir mostra como gravar um quadro de dados para Cosmos DB no PySpark.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true",
    "WritingBatchSize": "500",
    "CheckpointLocation": "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
              .writeStream
              .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
              .outputMode("append")
              .options(**writeconfig)
              .start())
```

E o mesmo trecho de código em escala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true",
  "WritingBatchSize" -> "500",
  "CheckpointLocation" -> "/checkpointlocation_write1"
))

// Write to Cosmos DB from the flights DataFrame
df
.writeStream
.format(classOf[CosmosDBSinkProvider].getName)
.options(writeConfig)
.start()
```
Mais trechos de código e amostras de ponta a ponta, consulte [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="bk_working_with_connector"></a>Trabalhando com o conector

Você pode criar o conector da origem no GitHub ou baixar os jars do Uber do Maven nos links abaixo.

| Spark | Scala | Última versão |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.4.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Usando blocos de anotações do databricks

Crie uma biblioteca usando seu espaço de trabalho do databricks seguindo as orientações no guia de Azure Databricks > [usar o conector do Azure Cosmos DB Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Observe que a página **usar o conector do Azure Cosmos DB Spark** não está atualizada no momento. Em vez de baixar os seis jars separados em seis bibliotecas diferentes, você pode baixar o uber jar do Maven https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar) em e instalar este jar/biblioteca.
> 

### <a name="using-spark-cli"></a>Usando o Spark-CLI

Para trabalhar com o conector usando o Spark-CLI (ou seja, `spark-shell` `pyspark` `spark-submit`,,), você pode usar o `--packages` parâmetro com as coordenadas do [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)do conector.

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Usando blocos de anotações do Jupyter

Se você estiver usando blocos de anotações do Jupyter no HDInsight, poderá usar a célula `%%configure` Spark-Magic para especificar as coordenadas do Maven do conector.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Observe que a inclusão do `spark.jars.excludes` é específica para remover possíveis conflitos entre o conector, Apache Spark e Livy.

### <a name="build-the-connector"></a>Criar o conector

Atualmente, esse projeto de conector `maven` usa isso para compilar sem dependências, você pode executar:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Trabalhando com nossos exemplos

O [repositório GitHub do cosmos DB Spark](https://github.com/Azure/azure-cosmosdb-spark) tem os seguintes blocos de anotações e scripts de exemplo que você pode tentar.

* **Desempenho de vôo pontual com Spark e Cosmos dB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb)  |  [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Conecte o Spark ao Cosmos DB usando o serviço de bloco de anotações Jupyter do HDInsight para demonstrar o Spark SQL, GraphFrames e prever atrasos de voo usando pipelines de ML.
* **Fonte do Twitter com Apache Spark e Azure Cosmos DB feed de alterações**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Usando Apache Spark para consultar grafos de Cosmos DB**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Conectando Azure Databricks ao Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** usando `azure-cosmosdb-spark`.  O link aqui também é uma versão Azure Databricks do [notebook de desempenho de vôo pontual](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks).
* **[Arquitetura lambda com Azure Cosmos DB e HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)** : Você pode reduzir a sobrecarga operacional de manter pipelines de Big Data usando o Cosmos DB e o Spark.

## <a name="more-information"></a>Mais informações

Temos mais informações no `azure-cosmosdb-spark` [wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki) , incluindo:

* [Guia do usuário do conector do Spark Azure Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Exemplos de agregações](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Configuração e instalação

* [Configuração do conector do Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Instalação do Spark para o conector do cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (Em andamento)
* [Configurando Power BI consulta direta para Azure Cosmos DB via Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Solução de problemas

* [Usando agregações de Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Problemas conhecidos](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Desempenho

* [Dicas de desempenho](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Execuções de teste de consulta](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Gravando execuções de teste](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Feed de alterações

* [Alterações de processamento de fluxo usando Azure Cosmos DB feed de alterações e Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Demonstrações do feed de alterações](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Demonstrações de fluxo estruturado](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Monitorando

* [Monitorando trabalhos do Spark com o Application insights](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Próximas etapas

Se você ainda não tiver feito isso, faça o download do conector do Spark para o Azure Cosmos DB no repositório [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) do GitHub. Explore os recursos adicionais no repositório a seguir:

* [Exemplos de agregações](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Exemplos de scripts e bloco de notas](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Você também pode revisar o [Apache Spark SQL, o DataFrames e o Guia de Conjuntos de Dados](https://spark.apache.org/docs/latest/sql-programming-guide.html) e o artigo [Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
