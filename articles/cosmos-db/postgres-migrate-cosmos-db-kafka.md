---
title: Migrar dados do PostgreSQL para Azure Cosmos DB conta de API do Cassandra usando Apache Kafka
description: Saiba como usar o Kafka Connect para sincronizar dados do PostgreSQL para Azure Cosmos DB API do Cassandra em tempo real.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 01/05/2021
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 0038219ee8c1721ff5ab2be76231d33d2bd9064d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98203058"
---
# <a name="migrate-data-from-postgresql-to-azure-cosmos-db-cassandra-api-account-using-apache-kafka"></a>Migrar dados do PostgreSQL para Azure Cosmos DB conta de API do Cassandra usando Apache Kafka
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API do Cassandra no Azure Cosmos DB se tornou uma ótima opção para cargas de trabalho corporativas em execução no Apache Cassandra por vários motivos, como:

* **Economia de custos significativa:** Você pode economizar custos com Azure Cosmos DB, o que inclui o custo da VM, a largura de banda e quaisquer licenças aplicáveis do Oracle. Além disso, você não precisa gerenciar os custos de data centers, servidores, armazenamento de SSD, rede e eletricidade.

* **Melhor escalabilidade e disponibilidade:** Ele elimina pontos únicos de falha, melhor escalabilidade e disponibilidade para seus aplicativos.

* **Sem sobrecarga de gerenciamento e monitoramento:** Como um serviço de nuvem totalmente gerenciado, Azure Cosmos DB remove a sobrecarga de gerenciamento e monitoramento de uma infinidade de configurações.

O [Kafka Connect](https://kafka.apache.org/documentation/#connect) é uma plataforma para transmitir dados entre [Apache Kafka](https://kafka.apache.org/) e outros sistemas de maneira escalonável e confiável. Ele dá suporte a vários conectores de prateleira, o que significa que você não precisa de código personalizado para integrar sistemas externos com o Apache Kafka.

Este artigo demonstrará como usar uma combinação de conectores Kafka para configurar um pipeline de dados para sincronizar continuamente os registros de um banco de dado relacional, como [PostgreSQL](https://www.postgresql.org/) , para [Azure Cosmos DB API do Cassandra](cassandra-introduction.md).

## <a name="overview"></a>Visão geral

Aqui está uma visão geral de alto nível do fluxo de ponta a ponta apresentado neste artigo.

Os dados na tabela PostgreSQL serão enviados por push para Apache Kafka usando o [conector Debezium PostgreSQL](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html), que é um conector de **origem** do Kafka Connect. Inserções, atualizações ou exclusões em registros na tabela PostgreSQL serão capturados como `change data` eventos e enviados a tópicos do Kafka. O [conector do DataStax Apache Kafka](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html) (conector do **coletor** do Kafka Connect), forma a segunda parte do pipeline. Ele sincronizará os eventos de alteração de dados do tópico Kafka para Azure Cosmos DB API do Cassandra tabelas.

> [!NOTE]
> O uso de recursos específicos do conector de Apache Kafka DataStax nos permite enviar dados por push para várias tabelas. Neste exemplo, o conector nos ajudará a manter registros de dados de alteração em duas tabelas Cassandra que podem dar suporte a diferentes requisitos de consulta.

## <a name="prerequisites"></a>Pré-requisitos

* [Provisionar uma conta de API do Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)
* [Usar cqlsh ou shell hospedado para validação](cassandra-support.md#hosted-cql-shell-preview)
* JDK 8 ou superior
* [Docker](https://www.docker.com/) (opcional)

## <a name="base-setup"></a>Configuração básica

### <a name="set-up-postgresql-database-if-you-havent-already"></a>Configure o banco de dados PostgreSQL, caso ainda não tenha feito isso. 

Isso pode ser um banco de dados local existente ou você pode [baixar e instalar um](https://www.postgresql.org/download/) em seu computador local. Também é possível usar um contêiner do [Docker](https://hub.docker.com/_/postgres).

Para iniciar um contêiner:

```bash
docker run --rm -p 5432:5432 -e POSTGRES_PASSWORD=<enter password> postgres
```

Conecte-se à sua instância do PostgreSQL usando o [`psql`](https://www.postgresql.org/docs/current/app-psql.html) cliente:

```bash
psql -h localhost -p 5432 -U postgres -W -d postgres
```

Crie uma tabela para armazenar informações de pedido de exemplo:

```sql
CREATE SCHEMA retail;

CREATE TABLE retail.orders_info (
    orderid SERIAL NOT NULL PRIMARY KEY,
    custid INTEGER NOT NULL,
    amount INTEGER NOT NULL,
    city VARCHAR(255) NOT NULL,
    purchase_time VARCHAR(40) NOT NULL
);
```

### <a name="using-the-azure-portal-create-the-cassandra-keyspace-and-the-tables-required-for-the-demo-application"></a>Usando o portal do Azure, crie o espaço de keyCassandra e as tabelas necessárias para o aplicativo de demonstração.

> [!NOTE]
> Use os mesmos nomes de keyspace e de tabela, como abaixo

```sql
CREATE KEYSPACE retail WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE retail.orders_by_customer (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (customer_id, purchase_time)) WITH CLUSTERING ORDER BY (purchase_time DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE retail.orders_by_city (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (city,order_id)) WITH cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

### <a name="setup-apache-kafka"></a>Apache Kafka de instalação 

Este artigo usa um cluster local, mas você pode escolher qualquer outra opção. [Baixe Kafka](https://kafka.apache.org/downloads), descompacte-o, inicie o cluster Zookeeper e Kafka.

```bash
cd <KAFKA_HOME>/bin

#start zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

#start kafka (in another terminal)
bin/kafka-server-start.sh config/server.properties
```

### <a name="setup-connectors"></a>Conectores de instalação

Instale o conector do Debezium PostgreSQL e do DataStax Apache Kafka. Baixe o arquivo de plug-in do conector do Debezium PostgreSQL. Por exemplo, para baixar a versão 1.3.0 do conector (mais recente no momento da gravação), use [este link](https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.3.0.Final/debezium-connector-postgres-1.3.0.Final-plugin.tar.gz). Baixe o conector de Apache Kafka DataStax deste [link](https://downloads.datastax.com/#akc).

Descompacte ambos os arquivos do conector e copie os arquivos JAR para o [plug-in do Kafka Connect. path](https://kafka.apache.org/documentation/#connectconfigs).


```bash
cp <path_to_debezium_connector>/*.jar <KAFKA_HOME>/libs
cp <path_to_cassandra_connector>/*.jar <KAFKA_HOME>/libs
```

> Para obter detalhes, consulte a documentação do [Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) e do [DataStax](https://docs.datastax.com/en/kafka/doc/) .

## <a name="configure-kafka-connect-and-start-data-pipeline"></a>Configurar o Kafka Connect e iniciar o pipeline de dados

### <a name="start-kafka-connect-cluster"></a>Iniciar o cluster do Kafka Connect

```bash
cd <KAFKA_HOME>/bin
./connect-distributed.sh ../config/connect-distributed.properties
```

### <a name="start-postgresql-connector-instance"></a>Iniciar instância do conector PostgreSQL

Salvar a configuração do conector (JSON) em um exemplo de arquivo `pg-source-config.json`

```json
{
    "name": "pg-orders-source",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "localhost",
        "database.port": "5432",
        "database.user": "postgres",
        "database.password": "password",
        "database.dbname": "postgres",
        "database.server.name": "myserver",
        "plugin.name": "wal2json",
        "table.include.list": "retail.orders_info",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter"
    }
}
```

Para iniciar a instância do conector PostgreSQL:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-config.json http://localhost:8083/connectors
```

> [!NOTE]
> Para excluir, você pode usar: `curl -X DELETE http://localhost:8083/connectors/pg-orders-source`


### <a name="insert-data"></a>Inserir dados

A `orders_info` tabela contém detalhes do pedido, como ID do pedido, ID do cliente, cidade etc. Preencha a tabela com dados aleatórios usando o SQL abaixo.

```sql
insert into retail.orders_info (
    custid, amount, city, purchase_time
)
select
    random() * 10000 + 1,
    random() * 200,
    ('{New Delhi,Seattle,New York,Austin,Chicago,Cleveland}'::text[])[ceil(random()*3)],
    NOW() + (random() * (interval '1 min'))
from generate_series(1, 10) s(i);
```

Ele deve inserir 10 registros na tabela. Certifique-se de atualizar o número de registros `generate_series(1, 10)` abaixo de acordo com seu exemplo de requisitos, para inserir `100` registros, use `generate_series(1, 100)`

Para confirmar:

```bash
select * from retail.orders_info;
```

Verificar os eventos de captura de dados de alterações no tópico Kafka

> [!NOTE]
> Observe que o nome do tópico é de `myserver.retail.orders_info` acordo com a [Convenção do conector](https://debezium.io/documentation/reference/1.3/connectors/postgresql.html#postgresql-topic-names)

```bash
cd <KAFKA_HOME>/bin

./kafka-console-consumer.sh --topic myserver.retail.orders_info --bootstrap-server localhost:9092 --from-beginning
```

Você deve ver o alterar eventos de dados no formato JSON.

### <a name="start-datastax-apache-kafka-connector-instance"></a>Iniciar instância do conector de Apache Kafka do DataStax

Salve a configuração do conector (JSON) em um exemplo de arquivo `cassandra-sink-config.json` e atualize as propriedades de acordo com o seu ambiente.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "myserver.retail.orders_info",
        "contactPoints": "<Azure Cosmos DB account name>.cassandra.cosmos.azure.com",
        "loadBalancing.localDc": "<Azure Cosmos DB region e.g. Southeast Asia>",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "<path to JDK keystore path e.g. <JAVA_HOME>/jre/lib/security/cacerts>",
        "ssl.keystore.password": "<keystore password: it is 'changeit' by default>",
        "port": 10350,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "auth.username": "<Azure Cosmos DB user name (same as account name)>",
        "auth.password": "<Azure Cosmos DB password>",
        "topic.myserver.retail.orders_info.retail.orders_by_customer.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "topic.myserver.retail.orders_info.retail.orders_by_city.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "transforms": "unwrap",
        "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState",
        "offset.flush.interval.ms": 10000
    }
}
```

Para iniciar a instância do conector:

```bash
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

O conector deve Spring em ação e o pipeline de ponta a ponta do PostgreSQL para Azure Cosmos DB será operacional.

### <a name="query-azure-cosmos-db"></a>Consultar o Azure Cosmos DB

Verifique as tabelas Cassandra em Azure Cosmos DB. Aqui estão algumas das consultas que você pode experimentar:

```sql
select count(*) from retail.orders_by_customer;
select count(*) from retail.orders_by_city;

select * from retail.orders_by_customer;
select * from retail.orders_by_city;

select * from retail.orders_by_city where city='Seattle';
select * from retail.orders_by_customer where customer_id = 10;
```

Você pode continuar a inserir mais dados no PostgreSQL e confirmar se os registros estão sincronizados com Azure Cosmos DB.

## <a name="next-steps"></a>Próximas etapas

* [Integrar Apache Kafka e Azure Cosmos DB API do Cassandra usando o Kafka Connect](cassandra-kafka-connect.md)
* [Integrar o Apache Kafka Connect nos hubs de eventos do Azure (versão prévia) com o Debezium para captura de dados de alterações](../event-hubs/event-hubs-kafka-connect-debezium.md)
* [Migre dados do Oracle para o Azure Cosmos DB API do Cassandra usando o Blitzz](oracle-migrate-cosmos-db-blitzz.md)
* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md) 
* [Práticas recomendadas da chave de partição](partitioning-overview.md#choose-partitionkey)
* [Estimar ru/s usando os artigos Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md)

