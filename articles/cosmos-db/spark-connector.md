---
title: Conectar Apache Spark ao Azure Cosmos DB
description: Saiba mais sobre o conector do Spark do Azure do Cosmos do Azure que permite conectar o Apache Spark ao Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: ramkris
ms.openlocfilehash: 60afd8128224050d456699e798d814a259e106ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61330239"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Acelere a análise de big data usando o conector do Apache Spark para o Azure Cosmos DB

Você pode executar [Spark](https://spark.apache.org/) trabalhos com os dados armazenados no Azure Cosmos DB usando o conector Spark do Cosmos DB. Cosmos pode ser usado para o lote e processamento de fluxo e como uma camada de serviço para acesso de baixa latência.

Você pode usar o conector com [Azure Databricks](https://azure.microsoft.com/services/databricks) ou [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), que oferece clusters gerenciados do Spark no Azure. A tabela a seguir mostra as versões de Spark com suporte.

| Componente | Version |
|---------|-------|
| Apache Spark | 2.1. x, 2.3, 2.2 e 2.4 |
| Scala | 2.11 |
| Versão de tempo de execução do Azure Databricks | > 3.4 |

> [!WARNING]
> Este conector dá suporte a API do núcleo (SQL) do Azure Cosmos DB.
> Para o Cosmos DB para API do MongoDB, use o [conector do MongoDB Spark](https://docs.mongodb.com/spark-connector/master/).
> Para a API Cassandra do Cosmos DB, use o [conector do Spark Cassandra](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="quickstart"></a>Início rápido

* Siga as etapas em [começar com o SDK do Java](sql-api-async-java-get-started.md) para configurar uma conta do Cosmos DB e popular alguns dados.
* Siga as etapas em [guia de Introdução do Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html) para configurar um espaço de trabalho do Azure Databricks e cluster.
* Agora você pode criar novos Notebooks e importar a biblioteca do conector do Cosmos DB. Ir para [trabalhando com o conector do Cosmos DB](#bk_working_with_connector) para obter detalhes sobre como configurar seu espaço de trabalho.
* A seção a seguir tem trechos de código sobre como ler e gravar usando o conector.

### <a name="reading-from-cosmos-db"></a>Leitura do Cosmos DB

O trecho a seguir mostra como criar um DataFrame do Spark para ler do Cosmos DB no PySpark.

```python
# Read Configuration
readConfig = {
  "Endpoint" : "https://doctorwho.documents.azure.com:443/",
  "Masterkey" : "YOUR-KEY-HERE",
  "Database" : "DepartureDelays",
  "Collection" : "flights_pcoll",
  "query_custom" : "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

E mesmo trecho de código Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
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

### <a name="writing-to-cosmos-db"></a>Escrevendo para o Cosmos DB

O trecho a seguir mostra como gravar um quadro de dados no Cosmos DB no PySpark.

```python
# Write configuration
writeConfig = {
 "Endpoint" : "https://doctorwho.documents.azure.com:443/",
 "Masterkey" : "YOUR-KEY-HERE",
 "Database" : "DepartureDelays",
 "Collection" : "flights_fromsea",
 "Upsert" : "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(**writeConfig).save()
```

E mesmo trecho de código Scala:

```scala
// Configure connection to the sink collection
val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" : "true"
))

// Upsert the dataframe to Cosmos DB
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Há muito mais trechos e exemplos de ponta a ponta, consulte [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="bk_working_with_connector"></a> Trabalhando com o conector

Você pode criar o conector da fonte no Github ou baixar os jars uber do Maven nos links abaixo.

| Spark | Scala | Última versão |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.3.5](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Usando blocos de anotações do Databricks

Criar uma biblioteca usando o seu espaço de trabalho do Databricks, seguindo as orientações no guia do Databricks do Azure > [usar o conector Spark do Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Observe que o **usar o conector Spark do Azure Cosmos DB** página atualmente não está atualizada. Em vez de baixar os seis jars separados em seis bibliotecas diferentes, você pode baixar o jar uber do maven no https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar) e instalar essa um jar/biblioteca.
> 

### <a name="using-spark-cli"></a>Usando a cli do spark

Para trabalhar com o conector usando a cli do spark (ou seja, `spark-shell`, `pyspark`, `spark-submit`), você pode usar os `--packages` parâmetro com o conector [coordenadas maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11).

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5"

```

### <a name="using-jupyter-notebooks"></a>Usando blocos de anotações do Jupyter

Se você estiver usando os blocos de anotações do Jupyter dentro do HDInsight, você pode usar a mágica do spark `%%configure` célula para especificar as coordenadas do maven do conector.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Observe, a inclusão da `spark.jars.excludes` é específico para remover conflitos potenciais entre o conector, Apache Spark e Livy.

### <a name="build-the-connector"></a>Criar o conector

Atualmente, este projeto do conector usa `maven` para compilar sem dependências, você pode executar:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Trabalhando com nossos exemplos

O [repositório GitHub do Cosmos DB Spark](https://github.com/Azure/azure-cosmosdb-spark) tem os seguintes blocos de anotações de exemplo e scripts que você pode tentar.

* **Desempenho pontual de voo com Spark e Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Conecte o Spark ao Cosmos DB usando o serviço de bloco de anotações do Jupyter de HDInsight para demonstrar o Spark SQL, GraphFrames e previsão de atrasos de voo usando pipelines do ML.
* **[Conexão do Spark com o feed de alterações do Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark%2Band%2BCosmos%2BDB%2BChange%2BFeed.ipynb)**: Uma demonstração rápida sobre como conectar o Spark ao Feed de alterações do Cosmos DB.
* **Fonte com o Apache Spark e o Feed de alterações do Azure Cosmos DB do Twitter**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Usando o Apache Spark para consultar o Cosmos DB grafos**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Conectando o Azure Databricks para o Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)**  usando `azure-cosmosdb-spark`.  Vinculadas aqui também é uma versão do Azure Databricks a [bloco de anotações do desempenho de voo em tempo](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks).
* **[Arquitetura lambda com Azure Cosmos DB e HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**: Você pode reduzir a sobrecarga operacional da manutenção de pipelines de big data usando o Cosmos DB e Spark.

## <a name="more-information"></a>Mais informações

Temos mais informações na `azure-cosmosdb-spark` [wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki) incluindo:

* [Guia do usuário do conector Spark do Azure Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Exemplos de agregações](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Configuração e instalação

* [Configuração do conector do Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Spark para instalação do conector do Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (em andamento)
* [Configurando o Power BI Direct Query ao Azure Cosmos DB por meio do Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>solução de problemas

* [Usando o Cosmos DB agregações](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Problemas Conhecidos](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Desempenho

* [Dicas de desempenho](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Execuções de teste de consulta](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Execuções de teste de gravação](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Feed de alterações

* [Alterações de processamento de Stream usando o Feed de alterações do Azure Cosmos DB e Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Demonstrações de Feed de alteração](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Demonstrações de Structured Stream](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Monitoramento

* [Monitoramento de trabalhos do Spark com o application insights](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Próximas etapas

Se você ainda não tiver feito isso, faça o download do conector do Spark para o Azure Cosmos DB no repositório [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) do GitHub. Explore os recursos adicionais no repositório a seguir:

* [Exemplos de agregações](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Exemplos de scripts e bloco de notas](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Você também pode revisar o [Apache Spark SQL, o DataFrames e o Guia de Conjuntos de Dados](https://spark.apache.org/docs/latest/sql-programming-guide.html) e o artigo [Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
