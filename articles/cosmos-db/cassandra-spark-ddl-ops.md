---
title: Operações de DDL na API do Cassandra do Azure Cosmos DB do Spark
description: Este artigo fornece detalhes sobre as operações de DDL de tabela e de keyspace mediante a API do Cassandra do Azure Cosmos DB do Spark.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/07/2020
ms.openlocfilehash: 73d31fff362807937cbd87b8e1313cf601909802
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092170"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>Operações de DDL na API do Cassandra do Azure Cosmos DB do Spark
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Este artigo fornece detalhes sobre as operações de DDL de tabela e de keyspace mediante a API do Cassandra do Azure Cosmos DB do Spark.

## <a name="cassandra-api-related-configuration"></a>Configuração relacionada à API do Cassandra 

```scala
import org.apache.spark.sql.cassandra._

//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
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

## <a name="keyspace-ddl-operations"></a>Operações de DDL de keyspace

### <a name="create-a-keyspace"></a>Criar um keyspace

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>Validar no cqlsh

Execute o comando a seguir no cqlsh e você deverá ver o keyspace criado anteriormente.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>Remover um keyspace

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>Validar no cqlsh

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>Operações de DDL de tabela

**Considerações:**  

- A taxa de transferência pode ser atribuída no nível de tabela usando a instrução create table.  
- Uma chave de partição pode armazenar 20 GB de dados.  
- Um registro pode armazenar um máximo de 2 MB de dados.  
- Um intervalo de chaves de partição pode armazenar várias chaves de partição.

### <a name="create-a-table"></a>Criar uma tabela

```scala
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks1.books(book_id TEXT,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT, PRIMARY KEY(book_id,book_pub_year)) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>Validar no cqlsh

Execute o comando a seguir no cqlsh e você deverá ver a tabela chamada “books”: 

```bash
USE books_ks;
DESCRIBE books;
```

Os valores de TTL padrão e a taxa de transferência provisionada não são mostrados na saída do comando anterior. Você pode obter esses valores no portal.

### <a name="alter-table"></a>Alter table

Você pode alterar os valores a seguir usando o comando alter table:

* taxa de transferência provisionada 
* valor de vida útil
<br>Não há suporte para alterações a colunas.

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>Remover tabela

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>Validar no cqlsh

Execute o comando a seguir no cqlsh e você verá que a tabela "books" não está mais disponível:

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>Próximas etapas

Depois de criar o keyspace e a tabela, prossiga para os artigos a seguir para operações de CRUD e muito mais:
 
* [Operações de criação/inserção](cassandra-spark-create-ops.md)  
* [Operações de leitura](cassandra-spark-read-ops.md)  
* [Operações de Upsert](cassandra-spark-upsert-ops.md)  
* [Operações de exclusão](cassandra-spark-delete-ops.md)  
* [Operações de agregação](cassandra-spark-aggregation-ops.md)  
* [Operações de cópia de tabela](cassandra-spark-table-copy-ops.md)  
