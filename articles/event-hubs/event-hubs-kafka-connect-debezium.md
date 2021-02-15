---
title: Integrar o Apache Kafka conectar-se aos hubs de eventos do Azure com Debezium para captura de dados de alterações
description: Este artigo fornece informações sobre como usar o Debezium com os hubs de eventos do Azure para Kafka.
ms.topic: how-to
author: abhirockzz
ms.author: abhishgu
ms.date: 01/06/2021
ms.openlocfilehash: 0ad1df23e71e652f7d380ffbabb542b81954e038
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935165"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-with-debezium-for-change-data-capture"></a>Integrar o suporte do Apache Kafka Connect nos hubs de eventos do Azure com o Debezium para captura de dados de alterações

A **captura de dados de alteração (CDC)** é uma técnica usada para rastrear alterações em nível de linha em tabelas de banco de dado em resposta a operações de criação, atualização e exclusão. O [Debezium](https://debezium.io/) é uma plataforma distribuída que se baseia em recursos de captura de dados de alteração disponíveis em diferentes bancos (por exemplo, [decodificação lógica no PostgreSQL](https://www.postgresql.org/docs/current/static/logicaldecoding-explanation.html)). Ele fornece um conjunto de [conectores do Kafka Connect](https://debezium.io/documentation/reference/1.2/connectors/index.html) que tocam em alterações em nível de linha em tabelas de banco de dados e os convertem em fluxos de eventos que são enviados para [Apache Kafka](https://kafka.apache.org/).

> [!WARNING]
> O uso da estrutura do Apache Kafka Connect, bem como a plataforma Debezium e seus conectores, **não são elegíveis para suporte ao produto por meio de Microsoft Azure**.
>
> O Apache Kafka Connect pressupõe que sua configuração dinâmica seja mantida em tópicos compactados com retenção ilimitada. Os hubs de eventos do Azure [não implementam a compactação como um recurso de agente](event-hubs-federation-overview.md#log-projections) e sempre impõe um limite de retenção com base no tempo em eventos retidos, raiz do princípio de que os hubs de eventos do Azure são um mecanismo de streaming de eventos em tempo real e não um repositório de dados ou de configuração de longo prazo.
>
> Embora o projeto de Apache Kafka possa se sentir confortável com a combinação dessas funções, o Azure acredita que essas informações são mais bem gerenciadas em um banco de dados ou repositório de configuração adequado.
>
> Muitos cenários do Apache Kafka Connect serão funcionais, mas essas diferenças conceituais entre os modelos de retenção dos Apache Kafka e dos hubs de eventos do Azure podem fazer com que determinadas configurações não funcionem conforme o esperado. 

Este tutorial orienta você sobre como configurar um sistema baseado na captura de dados de alterações no Azure usando os [hubs de eventos do Azure](./event-hubs-about.md?WT.mc_id=devto-blog-abhishgu) (para Kafka), o [BD do Azure para PostgreSQL](../postgresql/overview.md) e o Debezium. Ele usará o [conector Debezium PostgreSQL](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html) para transmitir modificações de banco de dados do PostgreSQL para tópicos do Kafka nos hubs de eventos do Azure

> [!NOTE]
> Este artigo contém referências ao termo *lista de permissões*, um termo que a Microsoft não usa mais. Quando o termo for removido do software, também o removeremos deste artigo.

Neste tutorial, você deve executar as seguintes etapas:

> [!div class="checklist"]
> * Criar um namespace de Hubs de Eventos
> * Instalar e configurar o banco de dados do Azure para PostgreSQL
> * Configurar e executar o Kafka Connect com o conector do Debezium PostgreSQL
> * Testar captura de dados de alterações
> * Adicional Consumir eventos de dados de alteração com um `FileStreamSink` conector

## <a name="pre-requisites"></a>Pré-requisitos
Para concluir essa orientação, você precisará de:

- Assinatura do Azure. Se você não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/).
- Linux/MacOS
- Kafka (versão 1.1.1, versão do Scala 2.11), disponível em [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- Leia o artigo introdutório [Hubs de Eventos para o Apache Kafka](./event-hubs-for-kafka-ecosystem-overview.md)

## <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos
É necessário um namespace do Hubs de Eventos para enviar e receber de qualquer serviço de Hub de Eventos. Consulte [criando um hub de eventos](event-hubs-create.md) para obter instruções para criar um namespace e um hub de eventos. Obtenha a cadeia de conexão dos Hubs de Eventos e o FQDN (nome de domínio totalmente qualificado) para uso posterior. Para obter instruções, confira [Obter uma cadeia de conexão dos Hubs de Eventos](event-hubs-get-connection-string.md). 

## <a name="setup-and-configure-azure-database-for-postgresql"></a>Instalar e configurar o banco de dados do Azure para PostgreSQL
O [banco de dados do Azure para PostgreSQL](../postgresql/overview.md) é um serviço de banco de dados relacional baseado na versão da Comunidade do mecanismo de banco de dados PostgreSQL de software livre e está disponível em duas opções de implantação: servidor único e hiperescala (Citus). [Siga estas instruções](../postgresql/quickstart-create-server-database-portal.md) para criar um servidor de banco de dados do Azure para PostgreSQL usando o portal do Azure. 

## <a name="setup-and-run-kafka-connect"></a>Configurar e executar o Kafka Connect
Esta seção abordará os seguintes tópicos:

- Instalação do conector do Debezium
- Configurando o Kafka Connect para hubs de eventos
- Iniciar o cluster do Kafka Connect com o conector do Debezium

### <a name="download-and-setup-debezium-connector"></a>Baixar e configurar o conector do Debezium
Siga as instruções mais recentes na [documentação do Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) para baixar e configurar o conector.

- Baixe o arquivo de plug-in do conector. Por exemplo, para baixar a versão `1.2.0` do conector, use este link- https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.2.0.Final/debezium-connector-postgres-1.2.0.Final-plugin.tar.gz
- Extraia os arquivos JAR e copie-os para o [plug-in do Kafka Connect. path](https://kafka.apache.org/documentation/#connectconfigs).


### <a name="configure-kafka-connect-for-event-hubs"></a>Configurar o Kafka Connect para os Hubs de Eventos
É necessário um mínimo de reconfiguração para redirecionar a taxa de transferência do Kafka Connect para os Hubs de Eventos.  O exemplo `connect-distributed.properties` abaixo ilustra como configurar o Connect para fazer a autenticação e se comunicar com o ponto de extremidade do Kafka nos Hubs de Eventos:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

> [!IMPORTANT]
> Substitua `{YOUR.EVENTHUBS.CONNECTION.STRING}` pela cadeia de conexão do seu namespace dos Hubs de Eventos. Para ver as instruções sobre como obter uma cadeia de conexão, confira [Obter cadeia de conexão para Hubs de Eventos](event-hubs-get-connection-string.md). Aqui está um exemplo de configuração: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-kafka-connect"></a>Executar o Kafka Connect
Nesta etapa, um trabalho do Kafka Connect é iniciado localmente em modo distribuído, usando os Hubs de Eventos para manter o estado do cluster.

1. Salve o arquivo acima `connect-distributed.properties` localmente.  Substitua todos os valores entre chaves.
2. Navegue até o local da versão do Kafka em seu computador.
3. Execute `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` e aguarde até que o cluster seja iniciado.

> [!NOTE]
> O Kafka Connect usa a API AdminClient do Kafka para criar automaticamente tópicos com configurações recomendadas, incluindo a compactação. Uma verificação rápida do namespace no portal do Azure revela que os tópicos internos do trabalho do Connect foram criados automaticamente.
>
> Os tópicos internos do Kafka Connect **precisam usar a compactação**.  A equipe dos Hubs de Eventos não será responsável por corrigir configurações inadequadas se os tópicos internos do Connect estiverem configurados incorretamente.

### <a name="configure-and-start-the-debezium-postgresql-source-connector"></a>Configurar e iniciar o conector de origem Debezium PostgreSQL

Criar um arquivo de configuração ( `pg-source-connector.json` ) para o conector de origem PostgreSQL – substitua os valores de acordo com a instância do PostgreSQL do Azure.

```json
{
    "name": "todo-connector",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "<replace with Azure PostgreSQL instance name>.postgres.database.azure.com",
        "database.port": "5432",
        "database.user": "<replace with database user name>",
        "database.password": "<replace with database password>",
        "database.dbname": "postgres",
        "database.server.name": "my-server",
        "plugin.name": "wal2json",
        "table.whitelist": "public.todos"
    }
}
```

> [!TIP]
> `database.server.name` o atributo é um nome lógico que identifica e fornece um namespace para o servidor/cluster de banco de dados PostgreSQL que está sendo monitorado.. Para obter informações detalhadas, consulte a [documentação do Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-property-database-server-name)

Para criar uma instância do conector, use o ponto de extremidade da API REST do Kafka Connect:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-connector.json http://localhost:8083/connectors
```

Para verificar o status do conector:

```bash
curl -s http://localhost:8083/connectors/todo-connector/status
```

## <a name="test-change-data-capture"></a>Testar captura de dados de alterações
Para ver a captura de dados de alteração em ação, você precisará criar/atualizar/excluir registros no banco de dados PostgreSQL do Azure.

Comece conectando-se ao banco de dados PostgreSQL do Azure (o exemplo a seguir usa [psql](https://www.postgresql.org/docs/12/app-psql.html))

```bash
psql -h <POSTGRES_INSTANCE_NAME>.postgres.database.azure.com -p 5432 -U <POSTGRES_USER_NAME> -W -d <POSTGRES_DB_NAME> --set=sslmode=require

e.g. 

psql -h my-postgres.postgres.database.azure.com -p 5432 -U testuser@my-postgres -W -d postgres --set=sslmode=require
```

**Criar uma tabela e inserir registros**

```sql
CREATE TABLE todos (id SERIAL, description VARCHAR(50), todo_status VARCHAR(10), PRIMARY KEY(id));

INSERT INTO todos (description, todo_status) VALUES ('setup postgresql on azure', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('setup kafka connect', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('configure and install connector', 'in-progress');
INSERT INTO todos (description, todo_status) VALUES ('start connector', 'pending');
```

O conector agora deve Spring em ação e enviar eventos de dados de alteração para um tópico de hubs de eventos com o seguinte na, e `my-server.public.todos` , supondo que você tenha `my-server` como o valor de `database.server.name` e `public.todos` seja a tabela cujas alterações você está controlando (de acordo com a `table.whitelist` configuração)

**Tópico verificar hubs de eventos**

Vamos introspecção o conteúdo do tópico para verificar se tudo está funcionando conforme o esperado. O exemplo a seguir usa [`kafkacat`](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) , mas você também pode [criar um consumidor usando qualquer uma das opções listadas aqui](apache-kafka-developer-guide.md)

Crie um arquivo chamado `kafkacat.conf` com o seguinte conteúdo:

```
metadata.broker.list=<enter event hubs namespace>.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanisms=PLAIN
sasl.username=$ConnectionString
sasl.password=<enter event hubs connection string>
```

> [!NOTE]
> Atualize `metadata.broker.list` `sasl.password` os atributos e de acordo `kafkacat.conf` com as informações dos hubs de eventos. 

Em um terminal diferente, inicie um consumidor:

```bash
export KAFKACAT_CONFIG=kafkacat.conf
export BROKER=<enter event hubs namespace>.servicebus.windows.net:9093
export TOPIC=my-server.public.todos

kafkacat -b $BROKER -t $TOPIC -o beginning
```

Você deve ver as cargas JSON que representam os eventos de dados de alteração gerados no PostgreSQL em resposta às linhas que você acabou de adicionar à `todos` tabela. Aqui está um trecho de código da carga:


```json
{
    "schema": {...},
    "payload": {
        "before": null,
        "after": {
            "id": 1,
            "description": "setup postgresql on azure",
            "todo_status": "complete"
        },
        "source": {
            "version": "1.2.0.Final",
            "connector": "postgresql",
            "name": "fullfillment",
            "ts_ms": 1593018069944,
            "snapshot": "last",
            "db": "postgres",
            "schema": "public",
            "table": "todos",
            "txId": 602,
            "lsn": 184579736,
            "xmin": null
        },
        "op": "c",
        "ts_ms": 1593018069947,
        "transaction": null
    }
```

O evento consiste em `payload` junto com seu `schema` (omitido para fins de brevidade). Na `payload` seção, observe como a operação de criação ( `"op": "c"` ) é representada- `"before": null` significa que ela foi uma `INSERT` linha recentemente do Ed, `after` fornece valores para as colunas na linha, `source` fornece os metadados da instância PostgreSQL de onde esse evento foi coletado, etc.

Você pode tentar o mesmo com as operações Update ou DELETE também e introspecção nos eventos Change Data. Por exemplo, para atualizar o status da tarefa para `configure and install connector` (supondo que `id` seja `3` ):

```sql
UPDATE todos SET todo_status = 'complete' WHERE id = 3;
```

## <a name="optional-install-filestreamsink-connector"></a>Adicional Instalar conector do FileStreamSink
Agora que todas as `todos` alterações de tabela estão sendo capturadas no tópico de hubs de eventos, usaremos o conector FileStreamSink (que está disponível por padrão no Kafka Connect) para consumir esses eventos.

Criar um arquivo de configuração ( `file-sink-connector.json` ) para o conector – substitua o `file` atributo de acordo com o sistema de arquivos

```json
{
    "name": "cdc-file-sink",
    "config": {
        "connector.class": "org.apache.kafka.connect.file.FileStreamSinkConnector",
        "tasks.max": "1",
        "topics": "my-server.public.todos",
        "file": "<enter full path to file e.g. /Users/foo/todos-cdc.txt>"
    }
}
```

Para criar o conector e verificar seu status:

```bash
curl -X POST -H "Content-Type: application/json" --data @file-sink-connector.json http://localhost:8083/connectors

curl http://localhost:8083/connectors/cdc-file-sink/status
```

Inserir/atualizar/excluir registros de banco de dados e monitorar os registros no arquivo de coletor de saída configurado:

```bash
tail -f /Users/foo/todos-cdc.txt
```


## <a name="cleanup"></a>Limpeza
O Kafka Connect cria tópicos de Hub de Eventos para armazenar status, deslocamentos e configurações que persistirão mesmo depois que o cluster do Connect for desativado. A menos que você queira essa persistência, é recomendável excluir esses tópicos. Você também pode querer excluir o `my-server.public.todos` Hub de eventos que foram criados durante esse passo a passo.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os hubs de eventos para Kafka, consulte os seguintes artigos:  

- [Espelhar um agente do Kafka em um hub de eventos](event-hubs-kafka-mirror-maker-tutorial.md)
- [Conectar o Apache Spark a um hub de eventos](event-hubs-kafka-spark-tutorial.md)
- [Conectar o Apache Flink a um hub de eventos](event-hubs-kafka-flink-tutorial.md)
- [Explorar exemplos em nosso GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Conectar o Akka Streams a um hub de eventos](event-hubs-kafka-akka-streams-tutorial.md)
- [Guia do desenvolvedor de Apache Kafka para hubs de eventos do Azure](apache-kafka-developer-guide.md)