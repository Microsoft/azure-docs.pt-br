---
title: Indexação na conta de API do Cassandra Azure Cosmos DB
description: Saiba como a indexação secundária funciona no Azure Azure Cosmos DB conta de API do Cassandra.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 98e8f713ad2e4eef47e40d89a23dbf49a98ad67c
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339879"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Indexação secundária no Azure Cosmos DB API do Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

O API do Cassandra no Azure Cosmos DB aproveita a infraestrutura de indexação subjacente para expor a força de indexação que é inerente à plataforma. No entanto, ao contrário da API do SQL principal, API do Cassandra em Azure Cosmos DB não indexa todos os atributos por padrão. Em vez disso, ele dá suporte à indexação secundária para criar um índice em determinados atributos, que se comportam da mesma forma que o Apache Cassandra.  

Em geral, não é recomendável executar consultas de filtro nas colunas que não estão particionadas. Você deve usar a sintaxe permitir filtragem explicitamente, o que resulta em uma operação que pode não ter um bom desempenho. No Azure Cosmos DB você pode executar essas consultas em atributos de baixa cardinalidade porque elas se descobrem entre partições para recuperar os resultados.

Não é recomendável criar um índice em uma coluna atualizada com frequência. É prudente criar um índice quando você define a tabela. Isso garante que os dados e índices estejam em um estado consistente. Caso você crie um novo índice nos dados existentes, no momento, não é possível rastrear a alteração de progresso do índice para a tabela. Se você precisar acompanhar o progresso dessa operação, precisará solicitar a alteração de progresso por meio de um [tíquete de suporte]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).


> [!NOTE]
> Não há suporte para o índice secundário nos seguintes objetos:
> - tipos de dados como tipos de coleção congelado, decimal e tipos variantes.
> - Colunas estáticas
> - Chaves de clustering

## <a name="indexing-example"></a>Exemplo de indexação

Primeiro, crie uma tabela e um keyspace de exemplo executando os seguintes comandos no prompt do Shell CQL:

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

Em seguida, insira dados de usuário de exemplo com os seguintes comandos:

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

Se você tentar executar a instrução a seguir, encontrará um erro que solicita que você use `ALLOW FILTERING` : 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

Embora o API do Cassandra ofereça suporte à filtragem de permitir, conforme mencionado na seção anterior, não é recomendável. Em vez disso, você deve criar um índice no conforme mostrado no exemplo a seguir:

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
Depois de criar um índice no campo "LastName", agora você pode executar a consulta anterior com êxito. Com API do Cassandra no Azure Cosmos DB, você não precisa fornecer um nome de índice. Um índice padrão com formato `tablename_columnname_idx` é usado. Por exemplo, ` t1_lastname_idx` é o nome do índice para a tabela anterior.

## <a name="dropping-the-index"></a>Descartando o índice 
Você precisa saber qual é o nome do índice para descartar o índice. Execute o `desc schema` comando para obter a descrição da tabela. A saída desse comando inclui o nome do índice no formato `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)` . Em seguida, você pode usar o nome do índice para descartar o índice, conforme mostrado no exemplo a seguir:

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>Próximas etapas
* Saiba como funciona a [indexação automática](index-overview.md) no Azure Cosmos DB
* [Recursos do Apache Cassandra compatíveis com a API do Cassandra do Azure Cosmos DB](cassandra-support.md)
