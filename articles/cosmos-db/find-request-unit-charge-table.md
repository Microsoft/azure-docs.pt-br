---
title: Localizar cobrança de RU (unidade de solicitação) para consultas de API de Tabela no Azure Cosmos DB
description: Saiba como encontrar a cobrança da RU (unidade de solicitação) para API de Tabela consultas executadas em um contêiner Cosmos do Azure. Você pode usar as linguagens portal do Azure, .NET, Java, Python e Node.js para encontrar a cobrança de RU.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 0c3f2495630358f48900a9cf27cbc0feb4192e1d
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201303"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-table-api"></a>Localizar o encargo de unidade de solicitação para operações executadas no Azure Cosmos DB API de Tabela
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

O Azure Cosmos DB dá suporte a muitas APIs como SQL, MongoDB, Cassandra, Gremlin e Table. Cada API tem seu próprio conjunto de operações de banco de dados. Essas operações variam de simples leituras e gravações de pontos a consultas complexas. Cada operação de banco de dados consome recursos do sistema com base na complexidade da operação.

O custo de todas as operações do banco de dados é normalizado pelo Azure Cosmos DB e expresso por Unidades de Solicitação (ou RUs, abreviado na sigla em inglês). O encargo da solicitação é as unidades de solicitação consumidas por todas as operações de banco de dados. Você pode considerar o RUs como uma moeda de desempenho abstraindo os recursos do sistema, como CPU, IOPS e memória, necessários para executar as operações de banco de dados com suporte pelo Azure Cosmos DB. Independentemente da API usada para interagir com o contêiner do Azure Cosmos DB, os custos sempre serão medidos por RUs. Se a operação do banco de dados é uma gravação, uma leitura de ponto ou uma consulta, os custos são sempre medidos em RUs. Para saber mais, consulte o artigo [sobre as unidades de solicitação e suas considerações](request-units.md) .

Este artigo apresenta as diferentes maneiras pelas quais você pode encontrar o consumo da ru ( [unidade de solicitação](request-units.md) ) para qualquer operação executada em um contêiner no Azure Cosmos DB API de tabela. Se você estiver usando uma API diferente, confira [API para MongoDB](find-request-unit-charge-mongodb.md), [API do Cassandra](find-request-unit-charge-cassandra.md), [API Gremlin](find-request-unit-charge-gremlin.md)e artigos da [API do SQL](find-request-unit-charge.md) para encontrar a cobrança de ru/s.

## <a name="use-the-net-sdk"></a>Usar o SDK .NET

Atualmente, o único SDK que retorna o preço de RU para operações de tabela é o [SDK do .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). O objeto `TableResult` expõe uma propriedade `RequestCharge` que é populada pelo SDK quando usada na API de Tabela do Azure Cosmos DB:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Para obter mais informações, consulte [início rápido: criar um aplicativo API de tabela usando o SDK do .net e o Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como otimizar o consumo de RU, confira estes artigos:

* [Unidades de solicitação e taxa de transferência no Azure Cosmos DB](request-units.md)
* [Otimizar a taxa de transferência provisionada no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo de consulta no Azure Cosmos DB](./optimize-cost-reads-writes.md)