---
title: Migrar dados relacionais de um para alguns para Azure Cosmos DB API do SQL
description: Saiba como lidar com a migração de dados complexa para relações de um para poucos na API do SQL
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 53a3317f38cc22ffa3745f5f0e58cc01a54b825c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096738"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Migrar dados relacionais de um para alguns para Azure Cosmos DB conta da API do SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Para migrar de um banco de dados relacional para Azure Cosmos DB API do SQL, pode ser necessário fazer alterações no modelo de dado para otimização.

Uma transformação comum é a desnormalização de dados inserindo subitens relacionados em um documento JSON. Aqui, vamos examinar algumas opções para isso usando Azure Data Factory ou Azure Databricks. Para obter diretrizes gerais sobre a modelagem de dados para Cosmos DB, examine [modelagem de dados no Azure Cosmos DB](modeling-data.md).  

## <a name="example-scenario"></a>Cenário de Exemplo

Suponha que tenhamos as duas tabelas a seguir em nosso banco de dados SQL, Orders e OrderDetails.


:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/orders.png" alt-text="Captura de tela que mostra as tabelas Orders e OrderDetails no banco de dados SQL." border="false" :::

Queremos combinar essa relação de um para poucos em um documento JSON durante a migração. Para fazer isso, podemos criar uma consulta T-SQL usando "FOR JSON", como a seguir:

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

Os resultados dessa consulta teriam a seguinte aparência: 

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png" alt-text="Captura de tela que mostra as tabelas Orders e OrderDetails no banco de dados SQL." lightbox="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png":::

O ideal é que você queira usar uma única atividade de cópia de Azure Data Factory (ADF) para consultar dados SQL como a origem e gravar a saída diretamente em Azure Cosmos DB coletor como objetos JSON apropriados. Atualmente, não é possível executar a transformação JSON necessária em uma atividade de cópia. Se tentarmos copiar os resultados da consulta acima em um contêiner Azure Cosmos DB API do SQL, veremos o campo OrderDetails como uma propriedade de cadeia de caracteres de nosso documento, em vez da matriz JSON esperada.

Podemos solucionar essa limitação atual de uma das seguintes maneiras:

* **Use Azure data Factory com duas atividades de cópia** : 
  1. Obter dados formatados em JSON do SQL para um arquivo de texto em um local de armazenamento de blob intermediário e 
  2. Carregue dados do arquivo de texto JSON em um contêiner em Azure Cosmos DB.

* **Use Azure Databricks para ler do SQL e gravar em Azure Cosmos DB** -apresentaremos duas opções aqui.


Vamos examinar essas abordagens mais detalhadamente:

## <a name="azure-data-factory"></a>Fábrica de dados do Azure

Embora não seja possível inserir OrderDetails como uma matriz JSON no documento de Cosmos DB de destino, podemos contornar o problema usando duas atividades de cópia separadas.

### <a name="copy-activity-1-sqljsontoblobtext"></a>#1 da atividade de cópia: SqlJsonToBlobText

Para os dados de origem, usamos uma consulta SQL para obter o conjunto de resultados como uma única coluna com um objeto JSON (representando a ordem) por linha usando o SQL Server OPENJSON e para os recursos de caminho JSON:

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf1.png" alt-text="Captura de tela que mostra as tabelas Orders e OrderDetails no banco de dados SQL." nenhum caractere de aspas ".

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf2.png" alt-text="Captura de tela que mostra as tabelas Orders e OrderDetails no banco de dados SQL.":::

### <a name="copy-activity-2-blobjsontocosmos"></a>#2 da atividade de cópia: BlobJsonToCosmos

Em seguida, modificamos nosso pipeline do ADF adicionando a segunda atividade de cópia que procura no armazenamento de BLOBs do Azure para o arquivo de texto que foi criado pela primeira atividade. Ele o processa como origem "JSON" para inserir no coletor de Cosmos DB como um documento por linha JSON encontrado no arquivo de texto.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf3.png" alt-text="Captura de tela que mostra as tabelas Orders e OrderDetails no banco de dados SQL." ao pipeline para que ele exclua todos os arquivos anteriores restantes na pasta/Orders/antes de cada execução. Nosso pipeline do ADF agora é semelhante a este:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf4.png" alt-text="Captura de tela que mostra as tabelas Orders e OrderDetails no banco de dados SQL.":::

Depois de dispararmos o pipeline acima, vemos um arquivo criado em nosso local de armazenamento de BLOBs do Azure intermediário que contém um objeto JSON por linha:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf5.png" alt-text="Captura de tela que mostra as tabelas Orders e OrderDetails no banco de dados SQL.":::

Também vemos documentos de pedidos com OrderDetails inseridos corretamente inseridos em nossa coleção de Cosmos DB:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf6.png" alt-text="Captura de tela que mostra as tabelas Orders e OrderDetails no banco de dados SQL.":::


## <a name="azure-databricks"></a>Azure Databricks

Também podemos usar o Spark no [Azure Databricks](https://azure.microsoft.com/services/databricks/) para copiar os dados de nossa origem do banco do dados SQL para o destino Azure Cosmos DB sem criar os arquivos de texto/JSON intermediários no armazenamento de BLOBs do Azure. 

> [!NOTE]
> Para fins de clareza e simplicidade, os trechos de código a seguir incluem senhas de banco de dados fictícias explicitamente embutidas, mas você sempre deve usar os segredos Azure Databricks.
>

Primeiro, criamos e anexamos o [conector do SQL](https://docs.databricks.com/data/data-sources/sql-databases-azure.html) e as bibliotecas de [conectores de Azure Cosmos DB](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) necessárias ao nosso cluster de Azure Databricks. Reinicie o cluster para garantir que as bibliotecas sejam carregadas.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks1.png" alt-text="Captura de tela que mostra as tabelas Orders e OrderDetails no banco de dados SQL.":::

Em seguida, apresentamos dois exemplos para escala e Python. 

### <a name="scala"></a>Scala
Aqui, obtemos os resultados da consulta SQL com a saída "FOR JSON" em um dataframe:

```scala
// Connect to Azure SQL https://docs.databricks.com/data/data-sources/sql-databases-azure.html
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks2.png" alt-text="Captura de tela que mostra as tabelas Orders e OrderDetails no banco de dados SQL.":::

Em seguida, nos conectamos ao nosso banco de dados e coleção de Cosmos DB:

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

Por fim, definimos nosso esquema e usamos from_json para aplicar o dataframe antes de salvá-lo na coleção CosmosDB.

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks3.png" alt-text="Captura de tela que mostra as tabelas Orders e OrderDetails no banco de dados SQL.":::


### <a name="python"></a>Python

Como uma abordagem alternativa, talvez seja necessário executar transformações JSON no Spark (se o banco de dados de origem não oferecer suporte a "FOR JSON" ou uma operação semelhante), ou se você quiser usar operações paralelas para um conjunto de dados muito grande. Aqui, apresentamos um exemplo de PySpark. Comece configurando as conexões de banco de dados de origem e de destino na primeira célula:

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

Em seguida, consultaremos o banco de dados de origem (neste caso, SQL Server) para os registros de detalhes de ordem e ordem, colocando os resultados em quadros de dados do Spark. Também criaremos uma lista que contém todas as IDs de pedidos e um pool de threads para operações paralelas:

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

Em seguida, crie uma função para gravar pedidos na coleção de API do SQL de destino. Essa função filtrará todos os detalhes do pedido para a ID de pedido fornecida, os converterá em uma matriz JSON e inserirá a matriz em um documento JSON que iremos gravar na coleção de API do SQL de destino para essa ordem:

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

Por fim, chamaremos o acima usando uma função de mapa no pool de threads, para executar em paralelo, passando a lista de IDs de ordem que criamos anteriormente:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
Em qualquer abordagem, no final, devemos receber OrderDetails inseridos corretamente em cada documento de pedido na coleção de Cosmos DB:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks4.png" alt-text="Captura de tela que mostra as tabelas Orders e OrderDetails no banco de dados SQL.":::

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a [modelagem de dados no Azure Cosmos DB](./modeling-data.md)
* Saiba [como modelar e particionar dados em Azure Cosmos DB](./how-to-model-partition-example.md)