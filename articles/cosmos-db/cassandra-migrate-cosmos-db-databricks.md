---
title: Migrar dados do Apache Cassandra para Azure Cosmos DB API do Cassandra usando o Spark (databricks)
description: Saiba como migrar dados do Apache Cassandra para Azure Cosmos DB API do Cassandra usando o Azure Databricks e o Spark.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/16/2020
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: 3cbcb7eb3695e6f57daef741d4cd4b15577d8f58
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493264"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-azure-databricks"></a>Migrar dados do Cassandra para a conta de API do Cassandra Azure Cosmos DB usando Azure Databricks
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API do Cassandra no Azure Cosmos DB se tornou uma ótima opção para cargas de trabalho corporativas em execução no Apache Cassandra por vários motivos, como: 

* **Sem sobrecarga de gerenciamento e monitoramento:** Ele elimina a sobrecarga de gerenciar e monitorar uma infinidade de configurações em arquivos do sistema operacional, JVM e YAML e suas interações.

* **Economia de custos significativa:** Você pode economizar custos com Azure Cosmos DB, o que inclui o custo da VM, a largura de banda e todas as licenças aplicáveis. Além disso, você não precisa gerenciar os custos de data centers, servidores, armazenamento de SSD, rede e eletricidade. 

* **Capacidade de usar código e ferramentas existentes:** O Azure Cosmos DB fornece compatibilidade de nível de protocolo de conexão com SDKs e ferramentas Cassandra existentes. Essa compatibilidade garante que você pode usar a base de código existente com a API do Cassandra do Azure Cosmos DB com alterações triviais.

Há várias maneiras de migrar cargas de trabalho de banco de dados de uma plataforma para outra. O [Azure Databricks](https://azure.microsoft.com/services/databricks/) é uma oferta de plataforma como serviço para [Apache Spark](https://spark.apache.org/) que oferece uma maneira de executar migrações offline em grande escala. Este artigo descreve as etapas necessárias para migrar dados de tabelas/espaços de Cassandra nativos do Apache para Azure Cosmos DB API do Cassandra usando Azure Databricks.

## <a name="prerequisites"></a>Pré-requisitos

* [Provisionar uma conta de API do Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)

* [Examine os conceitos básicos de se conectar à API Cassandra do Azure Cosmos DB](cassandra-spark-generic.md)

* Examine os [recursos com suporte no Azure Cosmos DB API do Cassandra](cassandra-support.md) para garantir a compatibilidade.

* Certifique-se de que você já criou tabelas e espaço em branco vazios em sua conta de API do Cassandra de destino Azure Cosmos DB

* [Usar cqlsh ou shell hospedado para validação](cassandra-support.md#hosted-cql-shell-preview)

## <a name="provision-an-azure-databricks-cluster"></a>Provisionar um cluster do Azure Databricks

Você pode seguir as instruções para [provisionar um cluster Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). No entanto, observe Apache Spark 3. x não tem suporte atualmente para o conector do Apache Cassandra. Será necessário provisionar um tempo de execução do databricks com uma versão v2. x com suporte do Apache Spark. É recomendável selecionar uma versão do tempo de execução do databricks que ofereça suporte à versão mais recente do Spark 2. x, sem ser posterior à versão do escala 2,11:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Tempo de execução do databricks":::


## <a name="add-dependencies"></a>Adicionar dependências

Você precisará adicionar a biblioteca do conector do Apache Spark Cassandra ao cluster para se conectar a pontos de extremidade nativos e Cosmos DB Cassandra. Em seu cluster, selecione bibliotecas-> instalar o New-> Maven-> pacotes de pesquisa:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Pacotes de pesquisa do databricks":::

Digite `Cassandra` na caixa de pesquisa e selecione o repositório Maven mais recente disponível e, `spark-cassandra-connector` em seguida, selecione instalar:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages-2.png" alt-text="Selecionar pacotes do databricks":::

> [!NOTE]
> Certifique-se de reiniciar o cluster do databricks após a instalação da biblioteca do conector do Cassandra.

## <a name="create-scala-notebook-for-migration"></a>Criar um bloco de anotações escalar para migração

Crie um bloco de anotações escalar no databricks com o código a seguir. Substitua as configurações de Cassandra de origem e de destino pelas credenciais correspondentes e as keyespaços de origem/destino e tabelas e, em seguida, execute:

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val nativeCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val cosmosCassandra = Map( 
    "spark.cassandra.connection.host" -> "<USERNAME>.cassandra.cosmos.azure.com",
    "spark.cassandra.connection.port" -> "10350",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.connection.connections_per_executor_max" -> "10",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from native Cassandra
val DFfromNativeCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(nativeCassandra)
  .load
  
//Write to CosmosCassandra
DFfromNativeCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(cosmosCassandra)
  .save
```

> [!NOTE]
> As `spark.cassandra.output.batch.size.rows` `spark.cassandra.output.concurrent.writes` configurações, e `connections_per_executor_max` são importantes para evitar a limitação de [taxa](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/), que ocorre quando as solicitações para Azure Cosmos DB excedem a taxa de transferência provisionada/([unidades de solicitação](./request-units.md)). Talvez seja necessário ajustar essas configurações dependendo do número de executores no cluster do Spark e, potencialmente, do tamanho (e, portanto, do custo de RU) de cada registro que está sendo gravado nas tabelas de destino.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="rate-limiting-429-error"></a>Limitação de taxa (erro 429)
Você pode ver um código de erro 429 ou `request rate is large` texto de erro, apesar de reduzir as configurações acima para seus valores mínimos. Estes são alguns cenários:

- **A taxa de transferência alocada para a tabela é inferior a 6000 [unidades de solicitação](./request-units.md)**. Mesmo no mínimo de configurações, o Spark poderá executar gravações a uma taxa de cerca de 6000 unidades de solicitação ou mais. Se você provisionou uma tabela em um keyspace com a taxa de transferência compartilhada provisionada, é possível que essa tabela tenha menos de 6000 RUs disponível em tempo de execução. Verifique se a tabela que você está migrando tem pelo menos 6000 RUs disponíveis ao executar a migração e, se necessário, aloque as unidades de solicitação dedicadas para essa tabela. 
- **Distorção de dados excessiva com volume de dados grande**. Se você tiver uma grande quantidade de dados (ou seja, linhas de tabela) para migrar para uma determinada tabela, mas tiver uma distorção significativa nos dados (ou seja, um grande número de registros sendo gravados para o mesmo valor de chave de partição), você ainda poderá experimentar a limitação de taxa mesmo que tenha uma grande quantidade de [unidades de solicitação](./request-units.md) provisionadas em sua tabela. Isso ocorre porque as unidades de solicitação são divididas igualmente entre as partições físicas e a distorção de dados pesada pode resultar em um afunilamento de solicitações para uma única partição, causando limitação de taxa. Nesse cenário, é aconselhável reduzir as configurações de taxa de transferência mínima no Spark para evitar a limitação da taxa e forçar a execução lenta da migração. Esse cenário pode ser mais comum ao migrar tabelas de referência ou de controle, em que o acesso é menos frequente, mas a distorção pode ser alta. No entanto, se uma distorção significativa estiver presente em qualquer outro tipo de tabela, também poderá ser aconselhável revisar seu modelo de dados para evitar problemas de partição ativa para sua carga de trabalho durante operações de estado estacionário. 
- **Não é possível obter a contagem em uma tabela grande**. `select count(*) from table`Atualmente, não há suporte para execução em tabelas grandes. Você pode obter a contagem de métricas em portal do Azure (consulte nosso [artigo de solução de problemas](cassandra-troubleshoot.md)), mas se precisar determinar a contagem de uma tabela grande de dentro do contexto de um trabalho do Spark, você poderá copiar os dados para uma tabela temporária e, em seguida, usar o Spark SQL para obter a contagem, por exemplo, abaixo (substitua `<primary key>` por algum campo da tabela temporária resultante).

  ```scala
  val ReadFromCosmosCassandra = sqlContext
    .read
    .format("org.apache.spark.sql.cassandra")
    .options(cosmosCassandra)
    .load

  ReadFromCosmosCassandra.createOrReplaceTempView("CosmosCassandraResult")
  %sql
  select count(<primary key>) from CosmosCassandraResult
  ```

## <a name="next-steps"></a>Próximas etapas

* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md) 
* [Práticas recomendadas da chave de partição](partitioning-overview.md#choose-partitionkey)
* [Estimar ru/s usando os artigos Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md)
* [Escala elástica no Azure Cosmos DB API do Cassandra](manage-scale-cassandra.md)
