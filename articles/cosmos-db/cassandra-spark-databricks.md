---
title: Acesso do Azure Cosmos DB API do Apache Cassandra
description: Este artigo aborda como trabalhar com a API do Apache Cassandra Azure Cosmos DB do Azure Databricks.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 37a06b19285c1196b5d87830ea176d4bd0d4eade
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60894004"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Acessar dados de API do Cassandra do Azure Cosmos DB do Azure Databricks

Este artigo detalha como trabalhar com a API do Apache Cassandra Azure do Spark no [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks).

## <a name="prerequisites"></a>Pré-requisitos

* [Provisionar uma conta de API do Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)

* [Examinar os conceitos básicos de se conectar à API do Apache Cassandra do Azure Cosmos DB](cassandra-spark-generic.md)

* [Provisionar um cluster do Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md)

* [Examine os exemplos de código para trabalhar com a API do Cassandra](cassandra-spark-generic.md#next-steps)

* [Usar cqlsh para validação se você assim preferir](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Configuração da instância de API do Cassandra para o conector do Cassandra:**

  O conector da API do Cassandra requer os detalhes de conexão do Cassandra para ser inicializado como parte do contexto do Spark. Quando você inicia um notebook do Databricks, o contexto do spark já é inicializado e não é aconselhável parar e reinicializá-lo. Uma solução é adicionar a configuração de instância de API do Cassandra em um nível de cluster, a configuração do cluster spark. Esta é uma atividade de uso única por cluster. Adicione o seguinte código para a configuração do Spark como par de valor de chave separado do espaço:
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>Adicione as dependências necessárias

* **Conector Spark do Cassandra:** – para integrar a API do Cassandra do Azure Cosmos DB ao Spark, o conector do Cassandra deve ser anexado ao cluster do Azure Databricks. Para anexar o cluster:

  * Examine a versão de tempo de execução do Databricks, a versão do Spark. Em seguida, localize as [coordenadas maven](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) que são compatíveis com o conector Spark do Cassandra e anexe-as ao cluster. Veja o artigo ["Carregar um pacote de Maven ou o pacote Spark"](https://docs.databricks.com/user-guide/libraries.html) para anexar a biblioteca do conector para ao cluster. Por exemplo, coordenada maven para "Databricks Runtime versão 4.3", "Spark 2.3.1," e "Scala 2.11" é `spark-cassandra-connector_2.11-2.3.1`

* **Biblioteca específica da API do Cassandra do Azure Cosmos DB:** um alocador de conexão personalizado é necessário para configurar a política de repetição do conector do Spark do Cassandra à API do Cassandra do Azure Cosmos DB. Adicione as `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0` [coordenadas maven](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) para anexar biblioteca ao cluster.

## <a name="sample-notebooks"></a>Notebooks de exemplo

Uma lista dos [notebooks de exemplo](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/notebooks/scala) do Azure Databricks estão disponíveis no repositório do GitHub para fazer o download. Esses exemplos incluem como se conectar à API do Apache Cassandra do Azure Cosmos DB do Spark e executar diferentes operações CRUD nos dados. Você também pode [importar todos os notebooks](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/dbc) para o Databricks do workspace do cluster e executá-lo. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Acessar a API do Apache Cassandra do Azure Cosmos DB de programas do Spark Scala

Programas de Spark devem ser executados como processos automatizados no Azure Databricks são enviados para o cluster usando [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html)) e agendado para ser executado por meio de trabalhos do Azure Databricks.

Estes são links para ajudá-lo a começar a criar programas Spark Scala para interagir com a API do Cassandra do Azure Cosmos DB.
* [Como se conectar a API do Apache Cassandra do Azure Cosmos DB a partir de um programa Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Como retornar um programa Scala Spark como um trabalho automatizado no Azure Databricks](https://docs.azuredatabricks.net/user-guide/jobs.html)
* [Lista completa de exemplos de código para trabalhar com a API do Cassandra](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>Próximas etapas

Introdução à [criação de uma conta de API do Cassandra, banco de dados e uma tabela](create-cassandra-api-account-java.md) usando um aplicativo Java.
