---
title: Migrar dados do Apache Cassandra para o Azure Cosmos DB API do Cassandra usando o Spark (databricks)
description: Saiba como migrar dados de um Cassandra do Apache para o API do Cassandra de Azure Cosmos DB usando Azure Databricks e Spark.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 03/10/2021
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: caedefbf3887205b68bcd5de5e7cd5f1f7d7f53c
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801002"
---
# <a name="migrate-data-from-cassandra-to-an-azure-cosmos-db-cassandra-api-account-by-using-azure-databricks"></a>Migrar dados do Cassandra para uma conta de API do Cassandra Azure Cosmos DB usando Azure Databricks
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API do Cassandra no Azure Cosmos DB se tornou uma ótima opção para cargas de trabalho corporativas em execução no Apache Cassandra por vários motivos:

* **Sem sobrecarga de gerenciamento e monitoramento:** Ele elimina a sobrecarga de gerenciamento e monitoramento de configurações em arquivos do sistema operacional, JVM e YAML e suas interações.

* **Economia de custos significativa:** Você pode economizar custos com o Azure Cosmos DB, que inclui o custo de VMs, largura de banda e quaisquer licenças aplicáveis. Você não precisa gerenciar data centers, servidores, armazenamento SSD, rede e custos de eletricidade.

* **Capacidade de usar código e ferramentas existentes:** O Azure Cosmos DB fornece compatibilidade de nível de protocolo de transmissão com SDKs e ferramentas Cassandra existentes. Essa compatibilidade garante que você possa usar a base de código existente com o Azure Cosmos DB API do Cassandra com alterações triviais.

Há várias maneiras de migrar cargas de trabalho de banco de dados de uma plataforma para outra. O [Azure Databricks](https://azure.microsoft.com/services/databricks/) é uma oferta de PaaS (plataforma como serviço) para [Apache Spark](https://spark.apache.org/) que oferece uma maneira de realizar migrações offline em grande escala. Este artigo descreve as etapas necessárias para migrar dados de tabelas e espaços de Cassandra nativos do Apache para o Azure Cosmos DB API do Cassandra usando Azure Databricks.

## <a name="prerequisites"></a>Pré-requisitos

* [Provisionar uma conta de API do Cassandra Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account).

* [Examine as noções básicas de como se conectar a um API do Cassandra de Azure Cosmos DB](cassandra-spark-generic.md).

* Examine os [recursos com suporte no API do Cassandra Azure Cosmos DB](cassandra-support.md) para garantir a compatibilidade.

* Certifique-se de que você já criou tabelas e espaços em branco vazios em sua conta de API do Cassandra de destino Azure Cosmos DB.

* [Use cqlsh ou shell hospedado para validação](cassandra-support.md#hosted-cql-shell-preview).

## <a name="provision-an-azure-databricks-cluster"></a>Provisionar um cluster do Azure Databricks

Você pode seguir as instruções para [provisionar um cluster Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Recomendamos a seleção do databricks Runtime versão 7,5, que dá suporte ao Spark 3,0.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Captura de tela que mostra a localização da versão de tempo de execução do databricks.":::

## <a name="add-dependencies"></a>Adicionar dependências

Você precisa adicionar a biblioteca do conector do Apache Spark Cassandra ao cluster para se conectar aos pontos de extremidade nativos e Azure Cosmos DB Cassandra. No cluster, selecione **bibliotecas**  >  **instalar novo**  >  **Maven** e, em seguida, adicione as `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` coordenadas do Maven.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Captura de tela que mostra a pesquisa de pacotes Maven no databricks.":::

Selecione **instalar** e reinicie o cluster quando a instalação for concluída.

> [!NOTE]
> Certifique-se de reiniciar o cluster do databricks após a instalação da biblioteca do conector do Cassandra.

## <a name="create-scala-notebook-for-migration"></a>Criar um bloco de anotações escalar para migração

Crie um bloco de anotações escalar no databricks. Substitua as configurações de Cassandra de origem e de destino pelas credenciais correspondentes e os espaços e tabelas de origem e de destino. Em seguida, execute o seguinte código:

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
  .mode(SaveMode.Append)
  .save
```

> [!NOTE]
> Os `spark.cassandra.output.batch.size.rows` `spark.cassandra.output.concurrent.writes` valores e e o número de trabalhadores em seu cluster Spark são configurações importantes a serem ajustadas para evitar a [limitação da taxa](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/). A limitação de taxa ocorre quando as solicitações para Azure Cosmos DB excedem a taxa de transferência provisionada ou as [unidades de solicitação](./request-units.md) (RUs). Talvez seja necessário ajustar essas configurações, dependendo do número de executores no cluster Spark e, potencialmente, do tamanho (e, portanto, do custo de RU) de cada registro que está sendo gravado nas tabelas de destino.

## <a name="troubleshoot"></a>Solucionar problemas

### <a name="rate-limiting-429-error"></a>Limitação de taxa (erro 429)

Você pode ver um código de erro 429 ou o texto de erro "taxa de solicitação é grande" mesmo que você tenha reduzido as configurações para seus valores mínimos. Os cenários a seguir podem causar limitação de taxa:

* **A taxa de transferência alocada para a tabela é inferior a 6.000 [unidades de solicitação](./request-units.md)**. Mesmo no mínimo de configurações, o Spark pode gravar em uma taxa de cerca de 6.000 unidades de solicitação ou mais. Se você provisionou uma tabela em um keyspace com taxa de transferência compartilhada, é possível que essa tabela tenha menos de 6.000 RUs disponíveis em tempo de execução.

    Verifique se a tabela que você está migrando tem pelo menos 6.000 RUs disponíveis quando você executa a migração. Se necessário, aloque unidades de solicitação dedicadas para essa tabela.

* **Distorção de dados excessiva com volume de dados grande**. Se você tiver uma grande quantidade de dados para migrar para uma determinada tabela, mas tiver uma distorção significativa nos dados (ou seja, um grande número de registros sendo gravados para o mesmo valor de chave de partição), você ainda poderá ter uma limitação de taxa mesmo que tenha várias [unidades de solicitação](./request-units.md) provisionadas na tabela. As unidades de solicitação são divididas igualmente entre as partições físicas e a distorção de dados pesada pode causar um afunilamento de solicitações para uma única partição.

    Nesse cenário, reduza as configurações de taxa de transferência mínima no Spark e force a execução lenta da migração. Esse cenário pode ser mais comum quando você está migrando tabelas de referência ou de controle, em que o acesso é menos frequente e a distorção pode ser alta. No entanto, se uma distorção significativa estiver presente em qualquer outro tipo de tabela, talvez você queira examinar seu modelo de dados para evitar problemas de partição ativa para sua carga de trabalho durante operações de estado estacionário.

## <a name="next-steps"></a>Próximas etapas

* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md)
* [Práticas recomendadas da chave de partição](partitioning-overview.md#choose-partitionkey)
* [Estimar RU/s usando o planejador de capacidade Azure Cosmos DB](estimate-ru-with-capacity-planner.md)
* [Escala elástica no Azure Cosmos DB API do Cassandra](manage-scale-cassandra.md)
