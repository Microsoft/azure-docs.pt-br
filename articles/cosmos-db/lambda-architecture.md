---
title: Arquitetura lambda com Azure Cosmos DB e HDInsight (Apache Spark)
description: Este artigo descreve como implementar uma arquitetura lambda usando o Azure Cosmos DB, HDInsight e Spark
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 6902b1a26d02efbf1a31fe9a3a25253a6b5a5604
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61043602"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>O Azure Cosmos DB: Implementar uma arquitetura lambda na plataforma Azure 

As arquiteturas lambda permitem o processamento de dados eficiente de conjuntos de dados em massa. As arquiteturas lambda usam processamento em lotes, processamento de fluxos e uma camada de serviço para minimizar a latência envolvida em consulta de Big Data. 

Para implementar uma arquitetura lambda no Azure, você pode combinar as seguintes tecnologias para acelerar a análise de Big Data em tempo real:
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), o primeiro serviço de banco de dados multimodelo do setor distribuído no mundo inteiro. 
* [Apache Spark para Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), uma estrutura de processamento que executa aplicativos de análise de dados em grande escala
* [Feed de alterações](change-feed.md) do Azure Cosmos DB, que transmite novos dados para a camada de lote do HDInsight processar
* O [Conector do Spark para Azure Cosmos DB](spark-connector.md)

Este artigo descreve os fundamentos de uma arquitetura lambda baseada no design multicamada original e os benefícios de uma arquitetura lambda "reprojetada" que simplifica as operações.  

## <a name="what-is-a-lambda-architecture"></a>O que é uma arquitetura lambda?
Uma arquitetura lambda é uma arquitetura de processamento de dados genérica, escalonável e tolerante a falhas para endereçar cenários de latência de velocidade e do lote, conforme descrito por [Nathan Marz](https://twitter.com/nathanmarz).

![Diagrama mostrando uma arquitetura lambda](./media/lambda-architecture/lambda-architecture-intro.png)

Fonte: http://lambda-architecture.net/

Os princípios básicos de uma arquitetura lambda são descritos no diagrama anterior, conforme [http://lambda-architecture.net](http://lambda-architecture.net/).

 1. Todos os **dados** são enviados por push para *ambas* as *camadas de lote* e *camadas de velocidade*.
 2. A **camada de lote** tem um conjunto de dados mestre (conjunto de dados brutos somente acréscimo e imutável) e pré-computa as exibições de lote.
 3. A **camada de serviço** tem exibições de lote para consultas rápidas. 
 4. A **camada de velocidade** compensa o tempo de processamento (para a camada de serviço) e lida apenas com dados recentes.
 5. Todas as consultas podem ser respondidas, mesclando os resultados das exibições de lote e exibições em tempo real ou executando ping individualmente.

Após uma leitura posterior, poderemos implementar essa arquitetura usando apenas o seguinte:

* Coleções do Azure Cosmos DB
* Cluster HDInsight (Apache Spark 2.1)
* Conector do Spark [1.0](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark_2.1.0_2.11-1.0.0)

## <a name="speed-layer"></a>Camada de velocidade

De uma perspectiva para as operações, a manutenção de dois fluxos de dados, garantindo o estado correto dos dados, poderá ser um esforço complicado. Para simplificar as operações, utilize o [Suporte de feed de alterações do Azure Cosmos DB](change-feed.md) para manter o estado da *camada de lote*, enquanto revela o log de alterações do Azure Cosmos DB por meio da *API de feed de alterações* para a *camada de velocidade*.  
![Diagrama destacando os novos dados, a camada de velocidade e a parte do conjunto de dados mestre da arquitetura lambda](./media/lambda-architecture/lambda-architecture-change-feed.png)

O que é importante nessas camadas:

 1. Todos os **dados** são enviados por push *somente* no Azure Cosmos DB, assim, é possível evitar problemas de multicast.
 2. A **camada de lote** tem um conjunto de dados mestre (conjunto de dados brutos somente acréscimo e imutável) e pré-computa as exibições de lote.
 3. A **camada de serviço** é discutida na próxima seção.
 4. A **camada de velocidade** utiliza HDInsight (Apache Spark) para ler o feed de alterações do Azure Cosmos DB. Isso permite manter seus dados, além de consultar e processá-lo simultaneamente.
 5. Todas as consultas podem ser respondidas, mesclando os resultados das exibições de lote e exibições em tempo real ou executando ping individualmente.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Exemplo de Código: Fluxo estruturado do Spark para um feed de alterações do Azure Cosmos DB
Para executar um protótipo rápido do feed de alterações do Azure Cosmos DB como parte da **camada de velocidade**, é possível testá-lo usando dados do Twitter como parte do exemplo [Alterações de processamento de fluxo usando o feed de alterações do Azure Cosmos DB e Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark). Para iniciar rapidamente a saída do Twitter, consulte o exemplo de código em [Feed de transmissão do Twitter para Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed). Com o exemplo anterior, você está carregando dados do Twitter no Azure Cosmos DB e pode configurar seu Cluster HDInsight (Apache Spark) para conectar-se ao feed de alterações. Para obter mais informações sobre como definir essa configuração, consulte [Configurar o Apache Spark para o Conector do Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

O snippet de código a seguir mostra como configurar `spark-shell` para executar um trabalho de streaming estruturado para conectar-se a um feed de alterações do Azure Cosmos DB, que analisa o fluxo de dados do Twitter em tempo real para executar uma contagem de intervalo de execução.

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceConfigMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[MASTER KEY]",
"Database" -> "[DATABASE]",
"Collection" -> "[COLLECTION]",
"ConnectionMode" -> "Gateway",
"ChangeFeedCheckpointLocation" -> "checkpointlocation",
"changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Interval Count")

// Start reading change feed as a stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()

// Start streaming query to console sink
val query = streamData.withColumn("countcol", streamData.col("id").substr(0, 0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
```

Para obter exemplos de código completo, consulte [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), incluindo:
* [Consulta de streaming do Feed.scala de alterações do Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Consulta Tag de streaming do Feed.scala de alterações do Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

A saída é um console `spark-shell`, que executa continuamente um trabalho de streaming estruturado que realiza uma contagem de intervalo nos dados do Twitter a partir do feed de alterações do Azure Cosmos DB. A seguinte imagem mostra a saída do trabalho de fluxo e contagens do intervalo.

![Saída de streaming mostrando a contagem do intervalo nos dados do Twitter a partir do feed de alterações do Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Para obter mais informações sobre o feed de alterações do Azure Cosmos DB, consulte:

* [Trabalhando com o suporte ao feed de alterações no Azure Cosmos DB](change-feed.md)
* [Introdução à biblioteca do processador do feed de alterações do Azure Cosmos DB](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Alterações no processamento de fluxo: Feed de alterações do Azure CosmosDB + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Camadas de serviço e lote
Como os novos dados são carregados no Azure Cosmos DB (onde o feed de alterações está sendo usado para a camada de velocidade), esse é o local onde o **conjunto de dados mestre** (um conjunto de dados brutos de somente acréscimo e imutável) reside. A partir desse ponto, use o HDInsight (Apache Spark) para executar as funções de pré-cálculo da **camada de lote** até a **camada de serviço**, conforme mostrado na imagem a seguir:

![Diagrama destacando a camada de lote e a camada de serviço da arquitetura lambda](./media/lambda-architecture/lambda-architecture-batch-serve.png)

O que é importante nessas camadas:

 1. Todos os **dados** são enviados por push apenas para o Azure Cosmos DB (para evitar problemas de multicast).
 2. A **camada de lote** tem um conjunto de dados mestre (conjunto de dados brutos somente acréscimo e imutável) armazenado no Azure Cosmos DB. Usando HDI Spark, você pode pré-computar suas agregações para serem armazenadas em suas exibições de lote computadas.
 3. A **camada de serviço** é um banco de dados do Azure Cosmos DB com coleções para o conjunto de dados mestre e exibição de lote computada.
 4. A **camada de velocidade** é discutida neste artigo mais adiante.
 5. Todas as consultas podem ser respondidas, mesclando os resultados das exibições de lote e exibições em tempo real ou executando ping individualmente.

### <a name="code-example-pre-computing-batch-views"></a>Exemplo de código: Exibições em lote de pré-computação
Para mostrar como executar exibições pré-calculadas no seu **conjunto de dados mestre** do Apache Spark para o Azure Cosmos DB, use os seguintes snippets de códigos dos blocos de notas de [Arquitetura lambda reprojetada - Camada de lote](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) e [Arquitetura lambda reprojetada - Lote para camada de serviço](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). Nesse cenário, use os dados do Twitter armazenados no Azure Cosmos DB.

Vamos começar criando a conexão de configuração para os dados do Twitter no Azure Cosmos DB usando o código PySpark abaixo.

```
# Configuration to connect to Azure Cosmos DB
tweetsConfig = {
  "Endpoint" : "[Endpoint URL]",
  "Masterkey" : "[Master Key]",
  "Database" : "[Database]",
  "Collection" : "[Collection]", 
  "preferredRegions" : "[Preferred Regions]",
  "SamplingRatio" : "1.0",
  "schema_samplesize" : "200000",
  "query_custom" : "[Cosmos DB SQL Query]"
}

# Create DataFrame
tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()

# Create Temp View (to run Spark SQL statements)
tweets.createOrReplaceTempView("tweets")
```

Em seguida, vamos executar a seguinte instrução do Spark SQL para determinar as 10 principais hashtags do conjunto de tweets. Para essa consulta SQL do Spark, estamos executando em um bloco de notas Jupyter sem o gráfico de barras de saída diretamente após esse snippet de código.

```
%%sql
select hashtags.text, count(distinct id) as tweets
from (
  select 
    explode(hashtags) as hashtags,
    id
  from tweets
) a
group by hashtags.text
order by tweets desc
limit 10
```

![Gráfico mostrando o número de tweets por hashtag](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Agora que você tem sua consulta, vamos salvá-la de volta para uma coleção usando o Conector do Spark para salvar os dados de saída em uma coleção diferente.  Neste exemplo, use o Scala para demonstrar a conexão. Semelhante ao exemplo anterior, crie a conexão de configuração para salvar o Apache Spark DataFrame em uma coleção diferente do Azure Cosmos DB.

```
val writeConfigMap = Map(
    "Endpoint" -> "[Endpoint URL]",
    "Masterkey" -> "[Master Key]",
    "Database" -> "[Database]",
    "Collection" -> "[New Collection]", 
    "preferredRegions" -> "[Preferred Regions]",
    "SamplingRatio" -> "1.0",
    "schema_samplesize" -> "200000"
)

// Configuration to write
val writeConfig = Config(writeConfigMap)

```

Após especificar o `SaveMode` (indicando se para `Overwrite` ou `Append` documentos), crie um DataFrame `tweets_bytags` semelhante à consulta SQL do Spark no exemplo anterior.  Com o DataFrame `tweets_bytags` criado, você poderá salvá-lo usando o método `write`, usando o `writeConfig` especificado anteriormente.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Essa última instrução salvou o Spark DataFrame em uma nova coleção do Azure Cosmos DB e, a partir de uma perspectiva de arquitetura lambda, essa é sua **exibição de lote** dentro da **camada de serviço**.
 
#### <a name="resources"></a>Recursos

Para obter códigos de exemplos completos, consulte [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) incluindo:
* Arquitetura lambda reprojetada - Camada de lote [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Arquitetura lambda reprojetada - Lote para camada de serviço [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Camada de velocidade
Conforme mencionado anteriormente, o uso da biblioteca de feed de alterações do Azure Cosmos DB permite que você simplifique as operações entre as camadas de velocidade e lote. Nessa arquitetura, use o Apache Spark (por meio do HDInsight) para executar as consultas de  *streaming estruturado* entre os dados. Convém persistir temporariamente os resultados de suas consultas de streaming estruturado para que outros sistemas possam acessar esses dados.

![Diagrama destacando a camada de velocidade da arquitetura lambda](./media/lambda-architecture/lambda-architecture-speed.png)

Para fazer isso, crie uma coleção do Azure Cosmos DB separada para salvar os resultados das consultas de streaming estruturado.  Isso permite que outros sistemas tenham acesso a essas informações, não apenas o Apache Spark. Além disso, com o recurso TTL (vida útil) do Cosmos DB, você poderá configurar seus documentos para serem excluídos automaticamente após uma duração definida.  Para obter mais informações sobre o recurso TTL do Azure Cosmos DB, consulte [Expirar os dados em coleções do Azure Cosmos DE automaticamente com a vida útil](time-to-live.md)

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceCollectionName = "[SOURCE COLLECTION NAME]"
val sinkCollectionName = "[SINK COLLECTION NAME]"

val configMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[COSMOSDB MASTER KEY]",
"Database" -> "[DATABASE NAME]",
"Collection" -> sourceCollectionName,
"ChangeFeedCheckpointLocation" -> "changefeedcheckpointlocation")

val sourceConfigMap = configMap.+(("changefeedqueryname", "Structured Stream replication streaming test"))

// Start to read the stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
val sinkConfigMap = configMap.-("collection").+(("collection", sinkCollectionName))

// Start the stream writer to new collection
val streamingQueryWriter = streamData.writeStream.format(classOf[CosmosDBSinkProvider].getName).outputMode("append").options(sinkConfigMap).option("checkpointLocation", "streamingcheckpointlocation")
var streamingQuery = streamingQueryWriter.start()

```

## <a name="lambda-architecture-rearchitected"></a>Arquitetura lambda: Reprojetada
Conforme observado nas seções anteriores, é possível simplificar a arquitetura lambda original usando os seguintes componentes:
* Azure Cosmos DB
* A biblioteca de feed de alterações do Azure Cosmos DB para evitar a necessidade de multicast dos dados entre as camadas de velocidade e lote
* Apache Spark no HDInsight
* O Conector do Spark para Azure Cosmos DB

![Diagrama mostrando o reprojeto da arquitetura lambda usando o Azure Cosmos DB, Spark e a API de feed de alterações do Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-re-architected.png)

Com esse design, são necessários apenas dois serviços gerenciados, o Azure Cosmos DB e o HDInsight. Juntos, eles endereçam as camadas de velocidade, serviço e lote da arquitetura lambda. Isso simplifica não apenas as operações, mas também o fluxo de dados. 
 1. Todos os dados são enviados por push no Azure Cosmos DB para processamento
 2. A camada de lote tem um conjunto de dados mestre (conjunto de dados brutos somente acréscimo e imutável) e pré-computa as exibições de lote
 3. A camada de serviço tem exibições de lote para consultas rápidas.
 4. A camada de velocidade compensa o tempo de processamento (para a camada de serviço) e lida apenas com dados recentes.
 5. Todas as consultas podem ser respondidas mesclando os resultados das exibições de lote e exibições em tempo real.

### <a name="resources"></a>Recursos

* **Novos dados**: O [ feed de transmissão do Twitter para CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), que é o mecanismo para enviar por push novos dados para o Azure Cosmos DB.
* **Camada do lote:** A camada do lote é composta pelo *conjunto de dados mestre* (conjunto de dados brutos somente acréscimo e imutável) e a capacidade de pré-computar exibições de lote dos dados que são enviados por push para a **camada de serviço**.
   * A **Arquitetura lambda reprojetada - Camada de lote** bloco de nota [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) consulta o *conjunto de dados mestre* das exibições de lote.
* **Camada de serviço:** A **camada de serviço** é composta pelos dados pré-computados resultando em exibições de lote (por exemplo, agregações, segmentações específicas, etc.) para consultas rápidas.
  * A **Arquitetura lambda reprojetada - Lote para camada de serviço** bloco de nota [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) envia por push os dados do lote para a camada de serviço, isto é, o Spark consulta uma coleção de lote de tweets, processa-a e armazena-a em outra coleção (um lote computado).
    * **Camada de velocidade:** A **camada de velocidade** é composta pelo Spark utilizando o feed de alterações do Azure Cosmos DB para ler e agir imediatamente. Os dados também podem ser salvos para *RT computado*, de modo que outros sistemas possam consultar os dados processados em tempo real, em comparação à execução de uma consulta em tempo real.
  * O scala script da [Consulta de streaming do feed de alterações do Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) executa uma consulta de streaming a partir do feed de alterações do Azure Cosmos DB para computar uma contagem do intervalo do spark-shell.
  * O scala script da [Consulta Tag de streaming do feed de alterações do Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) executa uma consulta de streaming a partir do feed de alterações do Azure Cosmos DB para computar uma contagem do intervalo de tags do spark-shell.
  
## <a name="next-steps"></a>Próximas etapas
Se você ainda não baixou, baixe o conector do Spark ao Azure Cosmos DB no repositório GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) e explore os recursos adicionais no repositório:
* [Arquitetura lambda](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Exemplos de agregações distribuídas](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Exemplos de scripts e bloco de notas](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Demonstrações de streaming estruturado](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Demonstrações de feed de alterações](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Alterações de processamento de fluxo usando o feed de alterações do Azure Cosmos DB e Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Convém examinar o [Guia do Apache Spark SQL, quadros de dados e conjuntos de dados](https://spark.apache.org/docs/latest/sql-programming-guide.html) e o artigo [Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
