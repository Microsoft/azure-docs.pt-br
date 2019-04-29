---
title: Trabalhando com a API do Cassandra do Azure Cosmos DB usando o Spark
description: Este artigo é a página principal para a integração da API do Cassandra do Cosmos DB usando o Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 75d2930363b6ad1aeace22d7529df04f31deefe5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60893615"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Conecte-se à API do Cassandra do Azure Cosmos DB usando o Spark

Este artigo está entre uma série de artigos sobre a integração da API do Cassandra do Azure Cosmos DB usando o Spark. Os artigos abordam conectividade, operações de DDL (linguagem de definição de dados), operações de DML (linguagem de manipulação de dados) e integração avançada da API do Cassandra do Azure Cosmos DB usando o Spark. 

## <a name="prerequisites"></a>Pré-requisitos
* [Provisionar uma conta da API do Cassandra do Azure Cosmos DB.](create-cassandra-dotnet.md#create-a-database-account)

* Provisione sua escolha do ambiente do Spark [[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) | [Azure HDInsight-Spark](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) | Outros].

## <a name="dependencies-for-connectivity"></a>Dependências para conectividade
* **Conector do Spark para Cassandra:** O conector do Spark é usado para se conectar à API do Cassandra do Azure Cosmos DB.  Identifique e use a versão do conector localizada em [central do Maven]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) compatível com as versões do Spark e do Scala do seu ambiente do Spark.

* **Biblioteca do auxiliar do Microsoft Azure Cosmos DB para API do Cassandra:** Além do conector do Spark, você precisa de outra biblioteca chamada [azure-cosmos-cassandra-spark-auxiliar]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) do Azure Cosmos DB. Essa biblioteca contém o connection factory customizado e novas classes de política.

  A política de repetição no Azure Cosmos DB está configurada para lidar com exceções com o código de status HTTP 429 ("Taxa grande de solicitação"). A API do Cassandra do Azure Cosmos DB converte essas exceções em erros de sobrecarga no protocolo nativo do Cassandra, e você pode repetir com retiradas. Uma vez que o Azure Cosmos DB usa o modelo de taxa de transferência provisionada, exceções de limitação de taxa de solicitação ocorrem quando as taxas de entrada/saída aumentam. A política de repetição protege seus trabalhos do Spark em relação a picos de dados que excedem momentaneamente a taxa de transferência alocada para a sua coleção.

  > [!NOTE] 
  > A política de repetição pode proteger seus trabalhos do Spark apenas contra picos momentâneo. Se você não tiver configurado RUs suficientes necessárias para executar sua carga de trabalho, a política de repetição não será aplicável e a classe de política de repetição lançará a exceção novamente.

* **Detalhes de conexão de conta do Azure Cosmos DB:** O nome da sua conta da API do Apache Cassandra, ponto de extremidade da conta e chave.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Parâmetros de configuração de taxa de transferência do conector do Spark

A tabela a seguir lista os parâmetros de configuração de taxa de transferência específicos da API do Cassandra do Azure Cosmos DB fornecidos pelo conector. Para obter uma lista detalhada de todos os parâmetros de configuração, veja a página [referência de configuração](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) do repositório GitHub do Conector do Cassandra do Spark.

| **Nome da Propriedade** | **Valor padrão** | **Descrição** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rows |  1 |Número de linhas por lote único. Defina esse parâmetro como 1. Esse parâmetro é usado para atingir uma taxa de transferência maior para cargas de trabalho pesadas. |
| spark.cassandra.connection.connections_per_executor_max  | Nenhum | Número máximo de conexões por nó por executor. 10*n é equivalente a 10 conexões por nó em um cluster do Cassandra de n nós. Assim, se você precisar de cinco conexões por nó por executor para um cluster do Cassandra de cinco nós, deverá definir essa configuração como 25. Modifique esse valor com base no grau de paralelismo ou no número de executores para os quais seus trabalhos do Spark estão configurados.   |
| spark.cassandra.output.concurrent.writes  |  100 | Define o número de gravações paralelas que podem ocorrer por executor. Uma vez que você define "batch.size.rows" como 1, aumente esse valor de acordo. Modifique esse valor com base no grau de paralelismo ou na taxa de transferência que você deseja alcançar para sua carga de trabalho. |
| spark.cassandra.concurrent.reads |  512 | Define o número de leituras paralelas que podem ocorrer por executor. Modifique esse valor com base no grau de paralelismo ou na taxa de transferência que você deseja alcançar para sua carga de trabalho  |
| spark.cassandra.output.throughput_mb_per_sec  | Nenhum | Define a taxa de transferência de gravação total por executor. Esse parâmetro pode ser usado como um limite superior limite para a taxa de transferência do trabalho do Spark e a baseará na produtividade provisionada da Coleção do Cosmos DB.   |
| spark.cassandra.input.reads_per_sec| Nenhum   | Define a taxa de transferência de leitura total por executor. Esse parâmetro pode ser usado como um limite superior limite para a taxa de transferência do trabalho do Spark e a baseará na produtividade provisionada da Coleção do Cosmos DB.  |
| spark.cassandra.output.batch.grouping.buffer.size |  1000  | Define o número de lotes por tarefa única do Spark que podem ser armazenados na memória antes do envio à API do Cassandra |
| spark.cassandra.connection.keep_alive_ms | 60000 | Define o período até o qual as conexões não usadas estão disponíveis. | 

Ajuste a taxa de transferência e o grau de paralelismo desses parâmetros com base na carga de trabalho que você espera para seus trabalhos do Spark e a taxa de transferência que você provisionou para sua conta do Cosmos DB.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Como conectar-se à API do Cassandra do Azure Cosmos DB usando o Spark

### <a name="cqlsh"></a>cqlsh
Os comandos a seguir detalham como se conectar à API do Cassandra do Azure CosmosDB usando cqlsh.  Isso é útil para validação conforme você percorre as amostras no Spark.<br>
**Do Linux/Unix/Mac:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1.  Azure Databricks
O artigo a seguir aborda o provisionamento do cluster do Azure Databricks, configuração do cluster para conexão à API do Cassandra do Azure Cosmos DB e vários blocos de anotações de exemplo que cobrem operações de DDL, operações de DML e muito mais.<BR>
[Trabalhar com a API do Cassandra do Azure Cosmos DB usando o Azure Databricks](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2.  Azure HDInsight-Spark
O artigo a seguir aborda o serviço HDInsight-Spark, provisionamento, configuração do cluster para conexão à API do Cassandra do Azure Cosmos DB e vários blocos de anotações de exemplo que cobrem operações de DDL, operações de DML e muito mais.<BR>
[Trabalhar com a API do Cassandra do Azure Cosmos DB usando o Azure HDInsight-Spark](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3.  Ambiente do Spark em geral
Embora as seções acima fossem específicas para os serviços de PaaS baseados no Spark para Azure, esta seção aborda qualquer ambiente geral do Spark.  Dependências de conector, importações e configuração de sessão do Spark são detalhadas abaixo. A seção "Próximas etapas" aborda os exemplos de código para operações de DDL, operações de DML e muito mais.  

#### <a name="connector-dependencies"></a>Dependências do conector:

1. Adicione as coordenadas do Maven para obter o [conector do Cassandra para o Spark](cassandra-spark-generic.md#dependencies-for-connectivity)
2. Adicione as coordenadas do Maven para a [biblioteca do auxiliar do Azure Cosmos DB](cassandra-spark-generic.md#dependencies-for-connectivity) para a API do Cassandra

#### <a name="imports"></a>Importações:

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Configuração de sessão do Spark:

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>Próximas etapas

Os artigos a seguir demonstram a integração do Spark com a API do Cassandra do Azure Cosmos DB. 
 
* [Operações de DDL](cassandra-spark-ddl-ops.md)
* [Operações de criação/inserção](cassandra-spark-create-ops.md)
* [Operações de leitura](cassandra-spark-read-ops.md)
* [Operações de upsert](cassandra-spark-upsert-ops.md)
* [Excluir operações](cassandra-spark-delete-ops.md)
* [Aggregation operations](cassandra-spark-aggregation-ops.md)
* [Operações de cópia de tabela](cassandra-spark-table-copy-ops.md)
