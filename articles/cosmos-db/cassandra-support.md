---
title: Recursos do Apache Cassandra compatíveis com a API do Cassandra do Azure Cosmos DB
description: Saiba mais sobre o suporte a recursos do Apache Cassandra na API do Cassandra do Azure Cosmos DB
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: f48935edd5746b0f3f3440e249024cbe2901317b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374364"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Recursos do Apache Cassandra compatíveis com a API do Cassandra do Azure Cosmos DB 
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. Você pode se comunicar com a API do Cassandra do Azure Cosmos DB por meio de [drivers](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) de cliente do Cassandra de software livre em conformidade com o [protocolo de transmissão](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) do Protocolo Binário v4 da CQL. 

Ao usar a API do Cassandra do Azure Cosmos DB, você pode aproveitar os benefícios das APIs do Apache Cassandra, bem como as funcionalidades empresariais que o Azure Cosmos DB fornece. As funcionalidades empresariais incluem [distribuição global](distribute-data-globally.md), [particionamento com expansão automática](cassandra-partitioning.md), garantias de disponibilidade e latência, criptografia em repouso, backups e muito mais.

## <a name="cassandra-protocol"></a>Protocolo do Cassandra 

A API do Cassandra do Azure Cosmos DB é compatível com a API da CQL (Linguagem de Consulta do Cassandra) v3.11 (compatível com a versão 2.x anterior). Os comandos, ferramentas, limitações e exceções do CQL compatíveis estão listados abaixo. Qualquer driver de cliente que compreenda esses protocolos deve ser capaz de conectar-se à API do Cassandra do Azure Cosmos DB.

## <a name="cassandra-driver"></a>Driver do Cassandra

As seguintes versões de drivers Cassandra são compatíveis com a API do Cassandra do Azure Cosmos DB:

* [Java 3.5+](https://github.com/datastax/java-driver)  
* [C# 3.5+](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5+](https://github.com/datastax/nodejs-driver)  
* [Python 3.15+](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 

## <a name="cql-data-types"></a>Tipos de dados CQL 

A API do Cassandra do Azure Cosmos DB é compatível com os seguintes tipos de dados do CQL:

|Tipo  |Com suporte |
|---------|---------|
| ascii  | Sim |
| BIGINT  | Sim |
| blob  | Sim |
| booleano  | Sim |
| contador  | Sim |
| date  | Sim |
| decimal  | Sim |
| double  | Sim |
| FLOAT  | Sim |
| frozen  | Sim |
| inet  | Sim |
| INT  | Sim |
| list  | Sim |
| set  | Sim |
| SMALLINT  | Sim |
| text  | Sim |
| time  | Sim |
|  timestamp  | Sim |
| timeuuid  | Sim |
| TINYINT  | Sim |
| tuple  | Sim |
| uuid  | Sim |
| varchar  | Sim |
| varint  | Sim |
| tuples | Sim | 
| udts  | Sim |
| map | Sim |

Static tem suporte para a declaração de tipo de dados.

## <a name="cql-functions"></a>Funções do CQL

A API do Cassandra do Azure Cosmos DB é compatível com as seguintes funções do CQL:

|Comando  |Com suporte |
|---------|---------|
| Token * | Sim |
| ttl *** | Sim |
| writetime *** | Sim |
| cast ** | Sim |

> [!NOTE] 
> \* API do Cassandra dá suporte ao token como uma projeção/seletor e só permite token(pk) no lado esquerdo de uma cláusula where. Por exemplo, há suporte para `WHERE token(pk) > 1024`, mas **não** para `WHERE token(pk) > token(100)`.  
> \*\* A função `cast()` não pode ser aninhada na API do Cassandra. Por exemplo, há suporte para `SELECT cast(count as double) FROM myTable`, mas **não** para `SELECT avg(cast(count as double)) FROM myTable`.    
> \*\*\* Os carimbos de data/hora personalizados e o TTL especificado com a opção `USING` são aplicados em um nível de linha (e não por célula).



Funções de agregação:

|Comando  |Com suporte |
|---------|---------|
| avg | Sim |
| count | Sim |
| Min | Sim |
| max | Sim |
| Sum | Sim |

> [!NOTE]
> As funções de agregação funcionam em colunas regulares, mas **não** há suporte para agregações em colunas de clustering.


Funções de conversão de blob:
 
|Comando  |Com suporte |
|---------|---------|
| typeAsBlob(value)   | Sim |
| blobAsType(value) | Sim |


Funções UUID e timeuuid:
 
|Comando  |Com suporte |
|---------|---------|
| dateOf()  | Sim |
| now()  | Sim |
| minTimeuuid()  | Sim |
| unixTimestampOf()  | Sim |
| toDate(timeuuid)  | Sim |
| toTimestamp(timeuuid)  | Sim |
| toUnixTimestamp(timeuuid)  | Sim |
| toDate(timestamp)  | Sim |
| toUnixTimestamp(timestamp)  | Sim |
| toTimestamp(date)  | Sim |
| toUnixTimestamp(date) | Sim |


  
## <a name="cql-commands"></a>Comandos da CQL

O Azure Cosmos DB dá suporte aos seguintes comandos de banco de dados em contas da API do Cassandra.

|Comando  |Com suporte |
|---------|---------|
| ALLOW FILTERING | Sim |
| ALTER KEYSPACE | N/A (serviço PaaS, replicação gerenciada internamente)|
| ALTER MATERIALIZED VIEW | Não |
| ALTER_ROLE | Não |
| ALTER TABLE | Sim |
| ALTER TYPE | Não |
| ALTER USER | Não |
| BATCH | Sim (somente lote não registrado)|
| COMPACT STORAGE | N/D (serviço PaaS) |
| CREATE AGGREGATE | Não | 
| CREATE CUSTOM INDEX (SASI) | Não |
| CREATE INDEX | Sim (sem [especificar o nome do índice](cassandra-secondary-index.md), e não há suporte para os índices em chaves de cluster ou a coleção FROZEN completa) |
| CREATE FUNCTION | Não |
| CREATE KEYSPACE (configurações de replicação ignorada) | Sim |
| CREATE MATERIALIZED VIEW | Não |
| CREATE TABLE | Sim |
| CREATE TRIGGER | Não |
| CREATE TYPE | Sim |
| CREATE ROLE | Não |
| CREATE USER (preterido no Apache Cassandra nativo) | Não |
| Delete (excluir) | Sim |
| DISTINTO | Não |
| DROP AGGREGATE | Não |
| .DROP FUNCTION | Não |
| DROP INDEX | Sim |
| DROP KEYSPACE | Sim |
| DROP MATERIALIZED VIEW | Não |
| DROP ROLE | Não |
| DROP TABLE | Sim |
| DROP_TRIGGER | Não | 
| DROP TYPE | Sim |
| DROP USER (preterido no Apache Cassandra nativo) | Não |
| GRANT | Não |
| INSERT | Sim |
| LIST PERMISSIONS | Não |
| LIST ROLES | Não |
| LIST USERS (preterido no Apache Cassandra nativo) | Não |
| REVOKE | Não |
| SELECT | Sim |
| UPDATE | Sim |
| TRUNCATE | Não |
| USE | Sim |

## <a name="lightweight-transactions-lwt"></a>Transações leves (LWT)

| Componente  |Com suporte |
|---------|---------|
| DELETE IF EXISTS | Sim |
| Condições de DELETE | Não |
| INSERT IF NOT EXISTS | Sim |
| UPDATE IF EXISTS | Sim |
| UPDATE IF NOT EXISTS | Sim |
| Condições de UPDATE | Não |

## <a name="cql-shell-commands"></a>Comandos do Shell CQL

O Azure Cosmos DB dá suporte aos seguintes comandos de banco de dados em contas da API do Cassandra.

|Comando  |Com suporte |
|---------|---------|
| CAPTURA | Sim |
| CLEAR | Sim |
| CONSISTENCY * | N/D |
| COPIAR | Não |
| DESCRIBE | Sim |
| cqlshExpand | Não |
| EXIT | Sim |
| LOGIN | N/A (não há suporte para a função CQL `USER`, portanto, `LOGIN` é redundante) |
| PAGING | Sim |
| SERIAL CONSISTENCY * | N/D |
| SHOW | Sim |
| ORIGEM | Sim |
| TRACING | N/A (a API do Cassandra API é apoiada pelo Azure Cosmos DB – use [log de diagnóstico](cosmosdb-monitor-resource-logs.md) para solucionar problemas) |

> [!NOTE] 
> \* A consistência funciona de maneira diferente no Azure Cosmos DB, confira [aqui](cassandra-consistency.md) mais informações.  


## <a name="json-support"></a>Suporte a JSON
|Comando  |Com suporte |
|---------|---------|
| SELECT JSON | Sim |
| INSERT JSON | Sim |
| fromJson() | Não |
| toJson() | Não |


## <a name="cassandra-api-limits"></a>Limites de API do Cassandra

A API do Cassandra do Azure Cosmos DB não tem limites para o tamanho dos dados armazenados em uma tabela. Centenas de terabytes ou petabytes de dados podem ser armazenados, com a garantia de que os limites de chave de partição sejam respeitados. Da mesma forma, todas as entidades ou linhas equivalentes não têm nenhum limite no número de colunas. No entanto, o tamanho total da entidade não deve ultrapassar 2 MB. Os dados por chave de partição não podem exceder 20 GB, como em todas as outras APIs.

## <a name="tools"></a>Ferramentas 

A API do Cassandra do Azure Cosmos DB é uma plataforma de serviço gerenciado. Ela não requer nenhuma sobrecarga de gerenciamento nem utilitários como o Coletor de Lixo, a JVM (Máquina Virtual Java) e o nodetool para gerenciar o cluster. Ela dá suporte a ferramentas como o cqlsh, que utiliza a compatibilidade binária CQLv4. 

* O data explorer do portal do Azure, as métricas, os logs de diagnóstico, o PowerShell e a CLI são outros mecanismos compatíveis para gerenciar a conta.

## <a name="hosted-cql-shell-preview"></a>Shell CQL hospedado (versão prévia)

Abra um shell nativo hospedado do Cassandra (CQLSH v5.0.1) diretamente por meio do Data Explorer no [portal do Azure](data-explorer.md) ou no [Azure Cosmos DB Explorer](https://cosmos.azure.com/). Antes de habilitar o shell CQL, você deve [habilitar o recurso](enable-notebooks.md) Notebooks em sua conta (se ainda não estiver habilitado, será solicitado ao clicar em `Open Cassandra Shell`). Confira o artigo [Habilitar notebooks para as contas do Azure Cosmos DB](enable-notebooks.md#supported-regions) para as regiões do Azure com suporte.

:::image type="content" source="./media/cassandra-support/cqlsh.png" alt-text="Abrir CQLSH":::

Você também pode conectar a API do Cassandra no Azure Cosmos DB usando o CQLSH instalado em um computador local. Ele vem com o Apache Cassandra 3.1.1 e fica pronto para o uso definindo as variáveis de ambiente. As seções a seguir incluem instruções para instalar, configurar e conectar a API do Cassandra no Azure Cosmos DB, no Windows ou no Linux usando o CQLSH.

> [!NOTE]
> As conexões com a API do Cassandra do Azure Cosmos DB não funcionarão com as versões DSE (DataStax Enterprise) do CQLSH. Lembre-se de usar apenas as versões do Apache Cassandra de software livre do CQLSH ao se conectar à API do Cassandra. 

**Windows:**

se estiver usando o Windows, recomendamos que você habilite o [sistema de arquivos do Windows para Linux](/windows/wsl/install-win10#install-the-windows-subsystem-for-linux). Em seguida, você pode seguir os comandos do Linux abaixo.

**UNIX/Linux/Mac:**

```bash
# Install default-jre and default-jdk
sudo apt install default-jre
sudo apt-get update
sudo apt install default-jdk

# Import the Baltimore CyberTrust root certificate:
curl https://cacert.omniroot.com/bc2025.crt > bc2025.crt
keytool -importcert -alias bc2025ca -file bc2025.crt

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to Azure Cosmos DB API for Cassandra:
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl

```

Todas as operações CRUD que são executadas por meio de um SDK compatível com CQL v4, retornarão informações adicionais sobre erros e unidades de solicitação consumidas. Os comandos DELETE e UPDATE devem ser tratados com a governança de recursos levada em consideração para garantir o uso mais eficiente da taxa de transferência provisionada.

* Observação O valor de gc_grace_seconds deverá ser zero se for especificado.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($"CustomPayload:  {key}: {value}"); 
        } 
```

## <a name="consistency-mapping"></a>Mapeamento de consistência 

A API do Cassandra do Azure Cosmos DB fornece a opção de consistência para operações de leitura.  O mapeamento de consistência está detalhado [aqui](./cassandra-consistency.md#mapping-consistency-levels).

## <a name="permission-and-role-management"></a>Gerenciamento de funções e de permissões

O Azure Cosmos DB dá suporte ao RBAC do Azure (controle de acesso baseado em função do Azure) para provisionamento, rotação de chaves, exibição de métricas e senhas/chaves de leitura/gravação e somente leitura que podem ser obtidas por meio do [portal do Azure](https://portal.azure.com). O Azure Cosmos DB não dá suporte a funções para atividades CRUD.

## <a name="keyspace-and-table-options"></a>Opções de tabela e de keyspace

As opções para nome da região, classe, replication_factor e datacenter no comando "CREATE KEYSPACE" são ignoradas atualmente. O sistema usa o método de replicação de [distribuição global](global-dist-under-the-hood.md) subjacente do Azure Cosmos DB para adicionar as regiões. Se você precisar da presença de dados entre regiões, poderá habilitá-la no nível da conta com o PowerShell, a CLI ou o portal. Para saber mais, confira o artigo [como adicionar regiões](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Durable_writes não podem ser desabilitadas porque o Azure Cosmos DB garante que cada gravação seja durável. Em todas as regiões, o Azure Cosmos DB replica os dados no conjunto de réplicas composto por quatro réplicas e essa [configuração](global-dist-under-the-hood.md) de conjunto de réplicas não pode ser modificada.
 
Todas as opções são ignoradas ao criar a tabela com a exceção de gc_grace_seconds, que deve ser definida como zero.
O keyspace e a tabela têm uma opção extra chamada "cosmosdb_provisioned_throughput", com um valor mínimo de 400 RU/s. A taxa de transferência do keyspace permite o compartilhamento de taxa de transferência em várias tabelas e é útil para cenários em que todas as tabelas não estão utilizando a taxa de transferência provisionada. O comando ALTER TABLE permite alterar a taxa de transferência provisionada entre as regiões. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```
## <a name="secondary-index"></a>Índice secundário
A API do Cassandra dá suporte a índices secundários em todos os tipos de dados, exceto tipos de coleção frozen, tipos variante e decimal. 

## <a name="usage-of-cassandra-retry-connection-policy"></a>Uso da política de nova tentativa de conexão do Cassandra

O Azure Cosmos DB é um sistema controlado por recursos. Isso significa que você pode fazer um determinado número de operações em um determinado segundo com base nas unidades de solicitação consumidas pelas operações. Se um aplicativo exceder esse limite em um determinado segundo, as solicitações serão limitadas por taxa e exceções serão geradas. A API do Cassandra do Azure Cosmos DB converte essas exceções em erros de sobrecarga no protocolo nativo do Cassandra. Para garantir que seu aplicativo possa interceptar e repetir as solicitações no caso de limitação da taxa, são fornecidas as extensões [spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) e [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions). Consulte também exemplos de código Java dos drivers Datastax [versão 3](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) e [versão 4](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4) ao conectar a API do Cassandra no Azure Cosmos DB. Se você usar outros SDKs para acessar API do Cassandra no Azure Cosmos DB, crie uma política de conexão para tentar novamente ao encontrar essas exceções.

## <a name="next-steps"></a>Próximas etapas

- Introdução à [criação de uma conta de API do Cassandra, banco de dados e uma tabela](create-cassandra-api-account-java.md) pelo uso de um aplicativo Java
