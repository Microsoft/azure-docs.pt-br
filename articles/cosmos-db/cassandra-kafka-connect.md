---
title: Integrar Apache Kafka e Azure Cosmos DB API do Cassandra usando o Kafka Connect
description: Saiba como ingerir dados do Kafka para Azure Cosmos DB API do Cassandra usando o conector de Apache Kafka do DataStax
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 12/14/2020
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 25972ba2bb30c39838c4822a42af292e8d8b1dba
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803622"
---
# <a name="ingest-data-from-apache-kafka-into-azure-cosmos-db-cassandra-api-using-kafka-connect"></a>Ingerir dados de Apache Kafka para Azure Cosmos DB API do Cassandra usando o Kafka Connect
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Os aplicativos Cassandra existentes podem facilmente trabalhar com o [Azure Cosmos DB API do Cassandra](cassandra-introduction.md) devido à [compatibilidade do driver CQLv4](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver). Você aproveita esse recurso para integrar com plataformas de streaming, como [Apache Kafka](https://kafka.apache.org/) e colocar dados em Azure Cosmos DB.

Os dados no Apache Kafka (tópicos) só são úteis quando consumidos por outros aplicativos ou ingeridos em outros sistemas. É possível criar uma solução usando as APIs de [produtor/consumidor](https://kafka.apache.org/documentation/#api) do Kafka [usando uma linguagem e um SDK de cliente de sua escolha](https://cwiki.apache.org/confluence/display/KAFKA/Clients). O Kafka Connect fornece uma solução alternativa. É uma plataforma para transmitir dados entre Apache Kafka e outros sistemas de maneira escalonável e confiável. Como o Kafka Connect dá suporte aos conectores de prateleira que incluem o Cassandra, você não precisa escrever código personalizado para integrar o Kafka com Azure Cosmos DB API do Cassandra. 

Neste artigo, usaremos o [conector de Apache Kafka](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html)de software livre DataStax, que funciona na parte superior do Kafka Connect Framework para ingerir registros de um tópico do Kafka em linhas de uma ou mais tabelas do Cassandra. O exemplo fornece uma instalação reutilizável usando Docker Compose. Isso é bastante conveniente, pois permite inicializar todos os componentes necessários localmente com um único comando. Esses componentes incluem Kafka, Zookeeper, Kafka Connect Worker e o aplicativo gerador de dados de exemplo.

Aqui está uma análise dos componentes e suas definições de serviço-você pode consultar o arquivo completo `docker-compose` [no repositório GitHub](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/docker-compose.yaml).

- Kafka e Zookeeper usam imagens [debezium](https://hub.docker.com/r/debezium/kafka/) .
- Para executar como um contêiner do Docker, o conector de Apache Kafka DataStax é inclusas sobre uma imagem existente do Docker- [debezium/Connect-base](https://github.com/debezium/docker-images/tree/master/connect-base/1.2). Essa imagem inclui uma instalação do Kafka e suas bibliotecas do Kafka Connect, tornando-o realmente conveniente para adicionar conectores personalizados. Você pode consultar o [Dockerfile](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/connector/Dockerfile).
- O `data-generator` serviço propaga dados gerados aleatoriamente (JSON) para o `weather-data` tópico Kafka. Você pode consultar o código e `Dockerfile` no [repositório GitHub](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/data-generator/)

## <a name="prerequisites"></a>Pré-requisitos

* [Provisionar uma conta de API do Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)

* [Usar cqlsh ou shell hospedado para validação](cassandra-support.md#hosted-cql-shell-preview)

* Instalar o [Docker](https://docs.docker.com/get-docker/) e o [Docker Compose](https://docs.docker.com/compose/install)

## <a name="create-keyspace-tables-and-start-the-integration-pipeline"></a>Criar um keyspace, tabelas e iniciar o pipeline de integração

Usando o portal do Azure, crie o espaço de keyCassandra e as tabelas necessárias para o aplicativo de demonstração.

> [!NOTE]
> Use os mesmos nomes de keyspace e de tabela, como abaixo

```sql
CREATE KEYSPACE weather WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE weather.data_by_state (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (state, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE weather.data_by_station (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (station_id, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

Clone o repositório GitHub:

```bash
git clone https://github.com/Azure-Samples/cosmosdb-cassandra-kafka
cd cosmosdb-cassandra-kafka
```

Inicie todos os serviços:

```shell
docker-compose --project-name kafka-cosmos-cassandra up --build
```

> [!NOTE]
> Pode levar algum tempo para baixar e iniciar os contêineres: isso é apenas um processo de uma vez.

Para confirmar se todos os contêineres foram iniciados:

```shell
docker-compose -p kafka-cosmos-cassandra ps
```

O aplicativo gerador de dados começará a bombear os dados para o `weather-data` tópico em Kafka. Você também pode fazer uma verificação de integridade rápida para confirmar. Inspecione o contêiner do Docker executando o trabalho do Kafka Connect:


```bash
docker exec -it kafka-cosmos-cassandra_cassandra-connector_1 bash
```

Depois de soltar no Shell do contêiner, basta iniciar o processo de consumidor do console Kafka usual e você deverá ver os dados meteorológicos (no formato JSON) fluindo.

```bash
cd ../bin
./kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic weather-data
```

## <a name="cassandra-sink-connector-setup"></a>Instalação do conector do coletor do Cassandra

Copie o conteúdo JSON abaixo para um arquivo (você pode nomeá-lo `cassandra-sink-config.json` ). Você precisará atualizá-lo de acordo com a sua configuração e o restante desta seção fornecerá orientações sobre este tópico.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "weather-data",
        "contactPoints": "<cosmos db account name>.cassandra.cosmos.azure.com",
        "port": 10350,
        "loadBalancing.localDc": "<cosmos db region e.g. Southeast Asia>",
        "auth.username": "<enter username for cosmosdb account>",
        "auth.password": "<enter password for cosmosdb account>",
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "/etc/alternatives/jre/lib/security/cacerts/",
        "ssl.keystore.password": "changeit",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "topic.weather-data.weather.data_by_state.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "topic.weather-data.weather.data_by_station.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter",
        "value.converter.schemas.enable": false,
        "offset.flush.interval.ms": 10000
    }
}
```

Aqui está um resumo dos atributos:

**Conectividade básica**

- `contactPoints`: Insira o ponto de contato para Cosmos DB Cassandra
- `loadBalancing.localDc`: Insira a região para Cosmos DB conta, por exemplo, Sudeste Asiático
- `auth.username`: Insira o nome de usuário
- `auth.password`: Insira a senha
- `port`: Insira o valor da porta (isto é `10350` , não `9042` . Deixe como está)

**Configuração de SSL**

Azure Cosmos DB impõe [conectividade segura sobre SSL](database-security.md) e o conector do Kafka Connect também dá suporte a SSL.

- `ssl.keystore.path`: caminho para o repositório de chaves do JDK no contêiner- `/etc/alternatives/jre/lib/security/cacerts/`
- `ssl.keystore.password`: Senha do repositório de chaves do JDK (padrão)
- `ssl.hostnameValidation`: Nós desativamos a validação do nome de host do nó
- `ssl.provider`: `JDK` é usado como o provedor SSL

**Parâmetros genéricos**

- `key.converter`: Usamos o conversor de cadeia de caracteres `org.apache.kafka.connect.storage.StringConverter`
- `value.converter`: como os dados nos tópicos do Kafka são JSON, utilizamos `org.apache.kafka.connect.json.JsonConverter`
- `value.converter.schemas.enable`: Como nossa carga JSON não tem um esquema associado a ela (para fins do aplicativo de demonstração), precisamos instruir o Kafka a não procurar um esquema definindo esse atributo como `false` . Não fazer isso resultará em falhas.

### <a name="install-the-connector"></a>Instalar o conector

Instale o conector usando o ponto de extremidade REST do Kafka Connect:

```shell
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

Para verificar o status:

```
curl http://localhost:8080/connectors/kafka-cosmosdb-sink/status
```

Se tudo correr bem, o conector deve começar combinando sua mágica. Ele deve autenticar para Azure Cosmos DB e começar a ingerir dados do tópico Kafka ( `weather-data` ) em tabelas Cassandra- `weather.data_by_state` e `weather.data_by_station`

Agora você pode consultar dados nas tabelas. Vá até a portal do Azure, ative o Shell CQL hospedado para sua conta de Azure Cosmos DB.

:::image type="content" source="./media/cassandra-kafka-connect/cqlsh.png" alt-text="Abrir CQLSH":::

## <a name="query-data-from-azure-cosmos-db"></a>Consultar dados de Azure Cosmos DB

Verifique as `data_by_state` `data_by_station` tabelas e. Aqui estão algumas consultas de exemplo para você começar:

```sql
select * from weather.data_by_state where state = 'state-1';
select * from weather.data_by_state where state IN ('state-1', 'state-2');
select * from weather.data_by_state where state = 'state-3' and ts > toTimeStamp('2020-11-26');

select * from weather.data_by_station where station_id = 'station-1';
select * from weather.data_by_station where station_id IN ('station-1', 'station-2');
select * from weather.data_by_station where station_id IN ('station-2', 'station-3') and ts > toTimeStamp('2020-11-26');
```

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md) 
* [Práticas recomendadas da chave de partição](partitioning-overview.md#choose-partitionkey)
* [Estimar ru/s usando os artigos Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md)
