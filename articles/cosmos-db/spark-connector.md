---
title: Conectando o Apache Spark ao Azure Cosmos DB | Microsoft Docs
description: "Use este tutorial para saber mais sobre o conector do Spark ao Azure Cosmos DB que permite a conexão do Apache Spark ao Azure Cosmos DB para realizar agregações distribuídas e ciências de dados no banco de dados multilocatário distribuído globalmente da Microsoft projetado para a nuvem."
keywords: apache spark
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: c4f46007-2606-4273-ab16-29d0e15c0736
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: denlee
ms.openlocfilehash: 8ecbb478c81cde25bbd0d1c9ee07ae02b07f8cc7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="accelerate-real-time-big-data-analytics-with-the-spark-to-azure-cosmos-db-connector"></a>Acelerar a análise de Big Data em tempo real com o conector do Spark ao Azure Cosmos DB

O conector do Spark ao Azure Cosmos DB permite que o Azure Cosmos DB atue como uma fonte de entrada ou um coletor de saída para trabalhos do Apache Spark. Conectar o [Spark](http://spark.apache.org/) ao [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) acelera sua capacidade de solucionar problemas de ciência de dados ágeis na qual você pode usar o Azure Cosmos DB para manter e consultar dados rapidamente. O conector do Spark ao Azure Cosmos DB utiliza com eficiência os índices gerenciados nativos do Azure Cosmos DB. Os índices habilitam colunas atualizáveis quando você executa uma análise e filtragem predicada em dados de alteração rápida distribuídos globalmente, que variam de Internet das coisas (IoT) para ciência de dados e cenários de análise.

Para trabalhar com Spark GraphX e o Gremlin graph APIs do Azure Cosmos DB, consulte [Executar análise de gráfico com Spark e Apache TinkerPop Gremlin](spark-connector-graph.md).

## <a name="download"></a>Baixar

Para começar, baixo o conector do Spark ao Azure Cosmos DB (versão prévia) no repositório [azure-documentdb-spark](https://github.com/Azure/azure-cosmosdb-spark/) do GitHub.

## <a name="connector-components"></a>Componentes do conector

O conector usa os seguintes componentes:

* O [Azure Cosmos DB](http://documentdb.com) permite que os clientes provisionem e dimensionem de forma elástica a produtividade e o armazenamento em qualquer quantidade de regiões geográficas. O serviço oferece:
   * Ativar a chave [distribuição global](distribute-data-globally.md) e escala horizontal
   * A garantia de latências de dígito único no percentil 99
   * [Vários modelos de consistência bem definidos](consistency-levels.md)
   * Garantia de alta disponibilidade com recursos de hospedagem múltipla
   * Todos os recursos são ancorados por [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/cosmos-db) (SLAs) abrangentes e líderes do setor.

* O [Apache Spark](http://spark.apache.org/) é um mecanismo de processamento eficiente com código-fonte aberto criado pensando em velocidade, facilidade de uso e análise sofisticada.

* [Apache Spark em Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md) para que você possa implantar o Apache Spark na nuvem para implantações críticas usando o [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/).

Versões com suporte oficial:

| Componente | Versão |
|---------|-------|
|Apache Spark|2.0+|
| Scala| 2.11|
| SDK de Java do Azure DocumentDB | 1.10.0 |

Este artigo ajuda você a executar alguns exemplos simples com Python (via pyDocumentDB) e a interface Scala.

Há duas abordagens para conectar o Apache Spark e o Azure Cosmos DB:
- Usar pyDocumentDB por meio do [SDK de Python do Azure DocumentDB](https://github.com/Azure/azure-documentdb-python).
- Criar um conector do Spark ao Azure Cosmos DB baseado em Java utilizando o [SDK de Java do Azure DocumentDB](https://github.com/Azure/azure-documentdb-java).

## <a name="pydocumentdb-implementation"></a>implementação de pyDocumentDB
O [SDK do pyDocumentDB](https://github.com/Azure/azure-documentdb-python) atual possibilita a conexão do Spark ao Azure Cosmos DB, conforme mostra o seguinte diagrama:

![Fluxo de dados do Spark ao Azure Cosmos DB por meio do banco de dados do pyDocumentDB](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow-of-the-pydocumentdb-implementation"></a>Fluxo de dados da implementação do pyDocumentDB

O fluxo de dados é o seguinte:

1. O nó mestre do Spark conecta-se ao nó de gateway do Azure Cosmos DB via pyDocumentDB. Um usuário especifica apenas as conexões do Spark e do Azure Cosmos DB. Conexões com os respectivos nós mestre e gateway são transparentes para o usuário.
2. O nó de gateway executa a consulta no Azure Cosmos DB, no qual a consulta é executada posteriormente em partições da coleção nos nós de dados. A resposta para essas consultas é enviada de volta ao nó de gateway e esse conjunto de resultados retorna ao nó mestre do Spark.
3. Consultas subsequentes (por exemplo, em um DataFrame do Spark) são enviadas aos nós de trabalho do Spark para processamento.

A comunicação entre o Spark e o Azure Cosmos DB é limitada ao nó mestre do Spark e aos nós de gateway do Azure Cosmos DB.  As consultas avança tão depressa como a camada de transporte entre esses dois nós permite.

### <a name="install-pydocumentdb"></a>Instalar o pyDocumentDB
Você pode instalar o pyDocumentDB em seu nó de driver usando **pip**, por exemplo:

```
pip install pyDocumentDB
```


### <a name="connect-spark-to-azure-cosmos-db-via-pydocumentdb"></a>Conectar o Spark ao Azure Cosmos DB por meio do pyDocumentDB
A simplicidade do transporte da comunicação torna a execução de uma consulta do Spark ao Azure Cosmos DB usando o pyDocumentDB relativamente simples.

O trecho de código a seguir mostra como usar o pyDocumentDB dentro de um contexto do Spark.

```
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]


# Set keys to connect to Azure Cosmos DB
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA=='
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)
```

Conforme observado no trecho de código:

* O SDK de Python do Azure Cosmos DB (`pyDocumentDB`) contém todos os parâmetros de conexão necessárias. Por exemplo, o parâmetro de locais preferencial escolhe a ordem de prioridade e a réplica de leitura.
*  Importe as bibliotecas necessárias e configure a **masterKey** e o **host** para criar o *client* do Azure Cosmos DB (**pydocumentdb.document_client**).


### <a name="execute-spark-queries-via-pydocumentdb"></a>Executar consultas Spark via pyDocumentDB
Os exemplos a seguir usam a instância do Azure Cosmos DB criada no trecho anterior usando as chaves somente leitura especificadas. O trecho de código a seguir se conecta à coleção **airports.codes** na conta DoctorWho, conforme especificado anteriormente executando uma consulta para extrair as cidades com aeroporto no estado de Washington.

```
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Push into list `elements`
elements = list(query)
```

Após a execução da consulta por meio de **consulta**, o resultado será um **query_iterable.QueryIterable** que é convertido em uma lista de Python. Uma lista de Python pode ser facilmente convertida em um DataFrame do Spark usando o seguinte código:

```
# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(elements)
```

### <a name="why-use-the-pydocumentdb-to-connect-spark-to-azure-cosmos-db"></a>Por que usar o pyDocumentDB para conectar o Spark ao Azure Cosmos DB?
A conexão do Spark ao Azure Cosmos DB usando pyDocumentDB normalmente ocorre para cenários nos quais:

* Você deseja usar Python.
* Você está retornando um conjunto de resultados relativamente pequeno do Azure Cosmos DB para o Spark. Observe que o conjunto de dados subjacente dentro do Azure Cosmos DB pode ser muito grande. Você está aplicando filtros, ou seja, executando filtros de predicado, em sua fonte do Azure Cosmos DB.  

## <a name="spark-to-azure-cosmos-db-connector"></a>Conector do Spark ao Azure Cosmos DB

O conector do Spark ao Azure Cosmos DB utiliza o [SDK do Java do Azure DocumentDB](https://github.com/Azure/azure-documentdb-java) e move dados entre os nós de trabalho do Spark e do Azure Cosmos DB, conforme mostrado no diagrama a seguir:

![Fluxo de dados no conector do Spark ao Azure Cosmos DB](./media/spark-connector/spark-connector.png)

O fluxo de dados é o seguinte:

1. O nó mestre do Spark conecta com o nó de gateway do Azure Cosmos DB para obter o mapa de partições. Um usuário especifica apenas as conexões do Spark e do Azure Cosmos DB. Conexões com os respectivos nós mestre e gateway são transparentes para o usuário.
2. Essas informações são fornecidas de volta ao nó mestre do Spark.  Neste ponto, você deve conseguir analisar a consulta para determinar as partições e suas localizações no Azure Cosmos DB que você precisa acessar.
3. Essas informações são transmitidas para os nós de trabalho do Spark.
4. Os nós de trabalho do Spark se conectam diretamente às partições do Azure Cosmos DB para extrair os dados e retornar os dados para as partições do Spark nos nós de trabalho do Spark.

A comunicação entre o Spark e o Azure Cosmos DB é consideravelmente mais rápida, pois a movimentação de dados ocorre entre os nós de trabalho do Spark e os nós de dados do Azure Cosmos DB (partições).

### <a name="build-the-spark-to-azure-cosmos-db-connector"></a>Criar o conector do Spark ao Azure Cosmos DB
Atualmente, o projeto do conector usa maven. Para compilar o conector sem dependências, execute:
```
mvn clean package
```
Você também pode baixar as versões mais recentes dos arquivos JAR da pasta *releases*.

### <a name="include-the-azure-cosmos-db-spark-jar"></a>Incluir o Azure Cosmos DB Spark JAR
Antes de executar qualquer código, você precisa incluir o Azure Cosmos DB Spark JAR.  Se você estiver usando o **spark-shell**, você poderá incluir o JAR usando a opção **--jars**.  

```
spark-shell --master $master --jars /$location/azure-cosmosdb-spark-0.0.3-jar-with-dependencies.jar
```

se você quiser executar o JAR sem dependências, use o seguinte código:

```
spark-shell --master $master --jars /$location/azure-cosmosdb-spark-0.0.3.jar,/$location/azure-documentdb-1.10.0.jar
```

Se você estiver usando um serviço de notebook, como Jupyter do Azure HDInsight, use os comandos **spark magic**:

```
%%configure
{ "jars": ["wasb:///example/jars/azure-documentdb-1.10.0.jar","wasb:///example/jars/azure-cosmosdb-spark-0.0.3.jar"],
  "conf": {
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
}
```

O comando **jars** permite que você inclua os dois JARs necessários para **azure-cosmosdb-spark** (o próprio e o SDK de Java do Azure DocumentDB) e exclui **scala-reflect** para que ele não interfira nas chamadas do Livy (notebook Jupyter > Livy > Spark).

### <a name="connect-spark-to-azure-cosmos-db-using-the-connector"></a>Conecte o Spark ao Azure Cosmos DB usando o conector
Embora o transporte de comunicação seja um pouco mais complicado, a execução de uma consulta do Spark ao Azure Cosmos DB usando o conector é consideravelmente mais rápida.

O trecho de código a seguir mostra como usar o conector em um contexto do Spark.

```
// Import Necessary Libraries
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Conforme observado no trecho de código:

- **azure-cosmosdb-spark** contém todos os parâmetros de conexão necessários, que incluem os locais preferenciais. Por exemplo, você pode escolher a ordem de prioridade e a réplica de leitura.
- Basta importar as bibliotecas necessárias e configurar a masterKey e o host para criar o cliente do Azure Cosmos DB.

### <a name="execute-spark-queries-via-the-connector"></a>Executar consultas Spark por meio do conector

O exemplo a seguir usa a instância do Azure Cosmos DB criada no trecho anterior usando as chaves somente leitura especificadas. O trecho de código a seguir conecta-se à coleção DepartureDelays.flights_pcoll (na conta DoctorWho, conforme especificado anteriormente) e executa uma consulta para extrair as informações de atraso de voo que estão partindo de Seattle.

```
// Queries
var query = "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'"
val df = spark.sql(query)

// Run DF query (count)
df.count()

// Run DF query (show)
df.show()
```

### <a name="why-use-the-spark-to-azure-cosmos-db-connector-implementation"></a>Por que usar a implementação do conector do Spark ao Azure Cosmos DB?

A conexão do Spark ao Azure Cosmos DB usando o conector é normalmente usada em cenários em que:

* Você deseja usar Scala e atualizá-lo para incluir um wrapper de Python, conforme observado em [Problema 3: Adicionar wrapper de Python e exemplos](https://github.com/Azure/azure-cosmosdb-spark/issues/3).
* Você tem uma grande quantidade de dados para transferir entre o Apache Spark e o Azure Cosmos DB.

Para dar uma ideia da diferença de desempenho da consulta, confira o [wiki de Execuções de teste de consulta](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="distributed-aggregation-example"></a>Exemplo de agregação distribuída
Esta seção fornece alguns exemplos de como você pode fazer agregações distribuídas e análises usando o Apache Spark e o Azure Cosmos DB juntos. O Azure Cosmos DB já oferece suporte para agregações, que é discutido em [agregações de escala do planeta com o blog do Azure Cosmos DB](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/). Aqui está como ele pode ser usado para o próximo nível com o Apache Spark.

Observe que essas agregações fazem referência ao [notebook do Conector do Spark ao Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark-to-CosmosDB_Connector.ipynb).

### <a name="connect-to-flights-sample-data"></a>Conecte-se aos dados de exemplo de voos
Esses exemplos de agregações acessam alguns dados de desempenho de voo armazenados em nosso banco de dados do Azure Cosmos DB **DoctorWho**. Para se conectar a ele, você precisa utilizar o trecho de código a seguir:

```
// Import Spark to Azure Cosmos DB connector
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Connect to Azure Cosmos DB Database
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US 2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Com este trecho de código, também vamos executar uma consulta base que transfere o conjunto de dados filtrado do Azure Cosmos DB para o Spark em que o último pode executar agregações distribuídas. Nesse caso, estamos solicitando voos partindo de Seattle (SEA).

```
// Run, get row count, and time query
val originSEA = spark.sql("SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'")
originSEA.createOrReplaceTempView("originSEA")
```

Os resultados a seguir foram gerados pela execução das consultas do serviço de notebook do Jupyter.  Observe que todos os trechos de código são genéricos e não são específicos a qualquer serviço.

### <a name="running-limit-and-count-queries"></a>Execução de consultas LIMIT e COUNT
Assim como você estava acostumado no SQL/Spark SQL, vamos começar com uma consulta **LIMIT**:

![Consulta LIMIT do Spark](./media/spark-connector/spark-sql-query.png)

A próxima consulta é uma consulta **COUNT** simples e rápida:

![Consulta COUNT do Spark](./media/spark-connector/spark-count-query.png)

### <a name="group-by-query"></a>Consulta GROUP BY
Neste próximo conjunto, podemos executar facilmente consultas **GROUP BY** em nosso banco de dados do Azure Cosmos DB:

```
select destination, sum(delay) as TotalDelays
from originSEA
group by destination
order by sum(delay) desc limit 10
```

![Gráfico da consulta Spark GROUP BY do Spark](./media/spark-connector/group-by-query-graph.png)

### <a name="distinct-order-by-query"></a>Consulta DISTINCT, ORDER BY
E aqui está uma consulta **DISTINCT, ORDER BY**:

![Gráfico da consulta Spark GROUP BY do Spark](./media/spark-connector/order-by-query.png)

### <a name="continue-the-flight-data-analysis"></a>Continuar a análise de dados de voo
Você pode usar os seguintes exemplos de consultas para continuar a análise dos dados de voo:

#### <a name="top-5-delayed-destinations-cities-departing-from-seattle"></a>Cinco principais destinos atrasados (cidades) partindo de Seattle
```
select destination, sum(delay)
from originSEA
where delay < 0
group by destination
order by sum(delay) limit 5
```
![Gráfico dos principais atrasos do Spark](./media/spark-connector/top-delays-graph.png)


#### <a name="calculate-median-delays-by-destination-cities-departing-from-seattle"></a>Calcular a média de atrasos por cidades de destino partindo de Seattle
```
select destination, percentile_approx(delay, 0.5) as median_delay
from originSEA
where delay < 0
group by destination
order by percentile_approx(delay, 0.5)
```

![Gráfico da média de atrasos do Spark](./media/spark-connector/median-delays-graph.png)

## <a name="next-steps"></a>Próximas etapas

Se você ainda não baixou, baixe o conector do Spark ao Azure Cosmos DB no repositório GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) e explore os recursos adicionais no repositório:

* [Exemplos de agregações distribuídas](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Exemplos de scripts e notebooks](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Convém examinar o [Guia do Apache Spark SQL, quadros de dados e conjuntos de dados](http://spark.apache.org/docs/latest/sql-programming-guide.html) e o artigo [Apache Spark no Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md).
