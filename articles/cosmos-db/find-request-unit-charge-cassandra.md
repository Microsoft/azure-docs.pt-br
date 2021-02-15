---
title: Encargos da unidade de solicitação de localização (RU) para uma consulta de API do Cassandra no Azure Cosmos DB
description: Saiba como encontrar a cobrança de RU (unidade de solicitação) para consultas Cassandra executadas em um contêiner Cosmos do Azure. Você pode usar os drivers portal do Azure, .NET e Java para encontrar a carga de RU.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 8f7db5dc48f7fec1bcf2bcd1291b67da7b4412c7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93094805"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-cassandra-api"></a>Localizar o encargo de unidade de solicitação para operações executadas no Azure Cosmos DB API do Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

O Azure Cosmos DB dá suporte a muitas APIs como SQL, MongoDB, Cassandra, Gremlin e Table. Cada API tem seu próprio conjunto de operações de banco de dados. Essas operações variam de simples leituras e gravações de pontos a consultas complexas. Cada operação de banco de dados consome recursos do sistema com base na complexidade da operação.

O custo de todas as operações do banco de dados é normalizado pelo Azure Cosmos DB e expresso por Unidades de Solicitação (ou RUs, abreviado na sigla em inglês). Você pode considerar o RUs como uma moeda de desempenho abstraindo os recursos do sistema, como CPU, IOPS e memória, necessários para executar as operações de banco de dados com suporte pelo Azure Cosmos DB. Independentemente da API usada para interagir com o contêiner do Azure Cosmos DB, os custos sempre serão medidos por RUs. Se a operação do banco de dados é uma gravação, uma leitura de ponto ou uma consulta, os custos são sempre medidos em RUs. Para saber mais, consulte o artigo [sobre as unidades de solicitação e suas considerações](request-units.md) .

Este artigo apresenta as diferentes maneiras pelas quais você pode encontrar o consumo da ru ( [unidade de solicitação](request-units.md) ) para qualquer operação executada em um contêiner no Azure Cosmos DB API do Cassandra. Se você estiver usando uma API diferente, consulte [API para MongoDB](find-request-unit-charge-mongodb.md), [API do SQL](find-request-unit-charge.md), [api do Gremlin](find-request-unit-charge-gremlin.md)e artigos [API de tabela](find-request-unit-charge-table.md) para localizar a cobrança de ru/s.

Quando você executa operações na API do Cassandra do Azure Cosmos DB, o preço de RU é retornado no conteúdo de entrada como um campo chamado `RequestCharge`. Você tem várias opções para recuperar o preço de RU.

## <a name="use-the-net-sdk"></a>Usar o SDK .NET

Ao usar o [SDK do .NET](https://www.nuget.org/packages/CassandraCSharpDriver/), você pode recuperar o conteúdo de entrada na propriedade `Info` de um objeto `RowSet`:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Para obter mais informações, consulte [início rápido: criar um aplicativo Cassandra usando o SDK do .net e Azure Cosmos DB](create-cassandra-dotnet.md).

## <a name="use-the-java-sdk"></a>Usar o SDK do Java

Ao usar o [SDK do Java](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core), você pode recuperar o conteúdo de entrada chamando o método `getExecutionInfo()` em um objeto `ResultSet`:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Para obter mais informações, consulte [início rápido: criar um aplicativo Cassandra usando o SDK do Java e Azure Cosmos DB](create-cassandra-java.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como otimizar o consumo de RU, confira estes artigos:

* [Unidades de solicitação e taxa de transferência no Azure Cosmos DB](request-units.md)
* [Otimizar a taxa de transferência provisionada no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo de consulta no Azure Cosmos DB](./optimize-cost-reads-writes.md)