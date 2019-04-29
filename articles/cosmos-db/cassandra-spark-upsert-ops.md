---
title: Upsert dados na API do Azure Cosmos DB Cassandra da Spark
description: Este artigo detalha como se transformar em tabelas na API do Azure Cosmos DB Cassandra da Spark
author: rockboyfor
ms.author: v-yeche
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
origin.date: 09/24/2018
ms.date: 03/18/2019
ms.openlocfilehash: 7770e7fbe846defc865b3fcc702fcb00bae1b73c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60893382"
---
<!--Verify sucessfully-->
# <a name="upsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Upsert dados na API do Azure Cosmos DB Cassandra da Spark

Este artigo descreve como inserir dados no Azure Cosmos DB Cassandra API do Spark.

## <a name="cassandra-api-configuration"></a>Configuração da API do Cassandra

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.cn")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="dataframe-api"></a>API de Dataframe

### <a name="create-a-dataframe"></a>Crie um dataframe 

```scala
// (1) Update: Changing author name to include prefix of "Sir"
// (2) Insert: adding a new book

val booksUpsertDF = Seq(
    ("b00001", "Sir Arthur Conan Doyle", "A study in scarlet", 1887),
    ("b00023", "Sir Arthur Conan Doyle", "A sign of four", 1890),
    ("b01001", "Sir Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
    ("b00501", "Sir Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
    ("b00300", "Sir Arthur Conan Doyle", "The hounds of Baskerville", 1901),
    ("b09999", "Sir Arthur Conan Doyle", "The return of Sherlock Holmes", 1905)
    ).toDF("book_id", "book_author", "book_name", "book_pub_year")
booksUpsertDF.show()
```

### <a name="upsert-data"></a>Upsert data

```scala
// Upsert is no different from create
booksUpsertDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .save()
```

### <a name="update-data"></a>Atualizar dados

```scala
//This runs on the driver, leverage only for one off updates
cdbConnector.withSessionDo(session => session.execute("update books_ks.books set book_price=99.33 where book_id ='b00300';"))
```

## <a name="rdd-api"></a>API DE RDD
> [!NOTE]
> Upsert da API RDD é o mesmo que a operação de criação 

## <a name="next-steps"></a>Próximas etapas

Prossiga para os artigos a seguir para executar outras operações nos dados armazenados nas tabelas da API do Cassandra do Azure Cosmos DB:

* [Excluir operações](cassandra-spark-delete-ops.md)
* [Aggregation operations](cassandra-spark-aggregation-ops.md)
* [Operações de cópia de tabela](cassandra-spark-table-copy-ops.md)

<!--Verify sucessfully-->
<!--Update_Description: new articles on  -->
<!--ms.date: 03/18/2019-->