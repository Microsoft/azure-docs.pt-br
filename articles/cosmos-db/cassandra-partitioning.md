---
title: Particionamento no Azure Cosmos DB API do Cassandra
description: Saiba mais sobre o particionamento no Azure Cosmos DB API do Cassandra
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: ba615d3e41393afe007238a0fe1e694732ad123e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93087631"
---
# <a name="partitioning-in-azure-cosmos-db-cassandra-api"></a>Particionamento no Azure Cosmos DB API do Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Este artigo descreve como o particionamento funciona no Azure Cosmos DB API do Cassandra. 

API do Cassandra usa o particionamento para dimensionar as tabelas individuais em um keyspace para atender às necessidades de desempenho do seu aplicativo. As partições são formadas com base no valor de uma chave de partição associada a cada registro em uma tabela. Todos os registros em uma partição têm o mesmo valor de chave de partição. Azure Cosmos DB gerencia de forma transparente e automática o posicionamento de partições em todos os recursos físicos para atender com eficiência às necessidades de escalabilidade e desempenho da tabela. À medida que os requisitos de taxa de transferência e armazenamento de um aplicativo aumentam, Azure Cosmos DB move e equilibra os dados em um número maior de computadores físicos.

Da perspectiva do desenvolvedor, o particionamento se comporta da mesma maneira para Azure Cosmos DB API do Cassandra como faz no [Apache Cassandra](https://cassandra.apache.org/)nativo. No entanto, há algumas diferenças em segundo plano. 


## <a name="differences-between-apache-cassandra-and-azure-cosmos-db"></a>Diferenças entre o Apache Cassandra e o Azure Cosmos DB

No Azure Cosmos DB, cada máquina em que as partições são armazenadas é chamada de [partição física](partitioning-overview.md#physical-partitions). A partição física é semelhante a uma máquina virtual; uma unidade de computação dedicada ou um conjunto de recursos físicos. Cada partição armazenada nessa unidade de computação é chamada de [partição lógica](partitioning-overview.md#logical-partitions) no Azure Cosmos DB. Se você já estiver familiarizado com o Apache Cassandra, poderá considerar as partições lógicas da mesma maneira que considera partições regulares no Cassandra. 

O Apache Cassandra recomenda um limite de 100 MB no tamanho de dados que podem ser armazenados em uma partição. O API do Cassandra para Azure Cosmos DB permite até 20 GB por partição lógica e até 30 GB de dados por partição física. Em Azure Cosmos DB, diferentemente do Apache Cassandra, a capacidade de computação disponível na partição física é expressa usando uma única métrica chamada [unidades de solicitação](request-units.md), o que permite que você considere sua carga de trabalho em termos de solicitações (leituras ou gravações) por segundo, em vez de núcleos, memória ou IOPS. Isso pode tornar o planejamento de capacidade mais direto, uma vez que você entende o custo de cada solicitação. Cada partição física pode ter até 10000 RUs de computação disponíveis para ela. Você pode aprender mais sobre as opções de escalabilidade lendo nosso artigo sobre [escala elástica](manage-scale-cassandra.md) em API do Cassandra. 

Em Azure Cosmos DB, cada partição física consiste em um conjunto de réplicas, também conhecido como conjuntos de réplicas, com pelo menos 4 réplicas por partição. Isso é diferente do Apache Cassandra, em que é possível definir um fator de replicação de 1. No entanto, isso leva à baixa disponibilidade se o único nó com os dados ficar inativo. Em API do Cassandra sempre há um fator de replicação de 4 (quorum de 3). Azure Cosmos DB gerencia automaticamente conjuntos de réplicas, enquanto eles precisam ser mantidos usando várias ferramentas no Apache Cassandra. 

O Apache Cassandra tem um conceito de tokens, que são hashes de chaves de partição. Os tokens são baseados em um hash de byte murmur3 64, com valores variando de-2 ^ 63 a-2 ^ 63-1. Esse intervalo é conhecido como "token ring" no Apache Cassandra. O token ring é distribuído em intervalos de token, e esses intervalos são divididos entre os nós presentes em um cluster nativo do Apache Cassandra. O particionamento para Azure Cosmos DB é implementado de forma semelhante, exceto pelo fato de que ele usa um algoritmo de hash diferente e tem um anel de token interno maior. No entanto, externamente, expõemos o mesmo intervalo de token que o Apache Cassandra, ou seja,-2 ^ 63 a-2 ^ 63-1.


## <a name="primary-key"></a>Chave primária

Todas as tabelas no API do Cassandra devem ter um `primary key` definido. A sintaxe de uma chave primária é mostrada abaixo:

```shell
column_name cql_type_definition PRIMARY KEY
```

Suponha que desejamos criar uma tabela de usuário, que armazena mensagens para usuários diferentes:

```shell
CREATE TABLE uprofile.user ( 
   id UUID PRIMARY KEY, 
   user text,  
   message text);
```

Neste design, definimos o `id` campo como a chave primária. A chave primária funciona como o identificador para o registro na tabela e também é usada como a chave de partição no Azure Cosmos DB. Se a chave primária for definida no modo descrito anteriormente, haverá apenas um único registro em cada partição. Isso resultará em uma distribuição perfeitamente horizontal e escalonável durante a gravação de dados no banco e é ideal para casos de uso de pesquisa de valor-chave. O aplicativo deve fornecer a chave primária sempre que estiver lendo dados da tabela, para maximizar o desempenho de leitura. 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning.png" alt-text="Elas" border="false":::


## <a name="compound-primary-key"></a>Chave primária composta

O Apache Cassandra também tem um conceito de  `compound keys` . Um composto `primary key` consiste em mais de uma coluna; a primeira coluna é o `partition key` , e todas as colunas adicionais são as `clustering keys` . A sintaxe de um `compound primary key` é mostrada abaixo:

```shell
PRIMARY KEY (partition_key_column_name, clustering_column_name [, ...])
```

Suponha que desejamos alterar o design acima e torná-lo possível recuperar mensagens com eficiência para um determinado usuário:

```shell
CREATE TABLE uprofile.user (
   user text,  
   id int, 
   message text, 
   PRIMARY KEY (user, id));
```

Neste design, agora estamos definindo `user` como a chave de partição e `id` como a chave de clustering. Você pode definir quantas chaves de clustering desejar, mas cada valor (ou uma combinação de valores) para a chave de clustering deve ser exclusivo para fazer com que vários registros sejam adicionados à mesma partição, por exemplo:

```shell
insert into uprofile.user (user, id, message) values ('theo', 1, 'hello');
insert into uprofile.user (user, id, message) values ('theo', 2, 'hello again');
```

Quando os dados são retornados, eles são classificados pela chave de clustering, conforme esperado no Apache Cassandra:

:::image type="content" source="./media/cassandra-partitioning/select-from-pk.png" alt-text="Captura de tela que mostra os dados retornados que são classificados pela chave de clustering.":::

Com os dados modelados dessa forma, vários registros podem ser atribuídos a cada partição, agrupados por usuário. Assim, podemos emitir uma consulta que é roteada com eficiência pelo `partition key` (neste caso, `user` ) para obter todas as mensagens para um determinado usuário. 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning2.png" alt-text="Diagrama que mostra como vários registros podem ser atribuídos a cada partição, agrupados por usuário." border="false":::


## <a name="composite-partition-key"></a>Chave de partição composta

As chaves de partição compostas funcionam essencialmente da mesma forma que as chaves compostas, exceto que você pode especificar várias colunas como uma chave de partição composta. A sintaxe das chaves de partição compostas é mostrada abaixo:

```shell
PRIMARY KEY (
   (partition_key_column_name[, ...]), 
    clustering_column_name [, ...]);
```
Por exemplo, você pode ter o seguinte, onde a combinação exclusiva de `firstname` e `lastname` formaria a chave de partição, e `id` é a chave de clustering:

```shell
CREATE TABLE uprofile.user ( 
   firstname text, 
   lastname text,
   id int,  
   message text, 
   PRIMARY KEY ((firstname, lastname), id) );
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [particionamento e dimensionamento horizontal em Azure Cosmos DB](partitioning-overview.md).
* Saiba mais sobre a [taxa de transferência provisionada no Azure Cosmos DB](request-units.md).
* Saiba mais sobre a [distribuição global no Azure Cosmos DB](distribute-data-globally.md).
