---
title: Taxa de RU (unidade de solicitação de localização) para consultas de API Gremlin no Azure Cosmos DB
description: Saiba como encontrar a cobrança de RU (unidade de solicitação) para consultas Gremlin executadas em um contêiner Cosmos do Azure. Você pode usar os drivers portal do Azure, .NET e Java para encontrar a carga de RU.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 8393fb431c5148d3f4885135c90fe4d0b8970d52
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082004"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-gremlin-api"></a>Localizar o encargo de unidade de solicitação para operações executadas na API Azure Cosmos DB Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

O Azure Cosmos DB dá suporte a muitas APIs como SQL, MongoDB, Cassandra, Gremlin e Table. Cada API tem seu próprio conjunto de operações de banco de dados. Essas operações variam de simples leituras e gravações de pontos a consultas complexas. Cada operação de banco de dados consome recursos do sistema com base na complexidade da operação.

O custo de todas as operações do banco de dados é normalizado pelo Azure Cosmos DB e expresso por Unidades de Solicitação (ou RUs, abreviado na sigla em inglês). Você pode considerar o RUs como uma moeda de desempenho abstraindo os recursos do sistema, como CPU, IOPS e memória, necessários para executar as operações de banco de dados com suporte pelo Azure Cosmos DB. Independentemente da API usada para interagir com o contêiner do Azure Cosmos DB, os custos sempre serão medidos por RUs. Se a operação do banco de dados é uma gravação, uma leitura de ponto ou uma consulta, os custos são sempre medidos em RUs. Para saber mais, consulte o artigo [sobre as unidades de solicitação e suas considerações](request-units.md) .

Este artigo apresenta as diferentes maneiras pelas quais você pode encontrar o consumo da ru ( [unidade de solicitação](request-units.md) ) para qualquer operação executada em um contêiner na API Azure Cosmos DB Gremlin. Se você estiver usando uma API diferente, consulte [API para MongoDB](find-request-unit-charge-mongodb.md), [API do Cassandra](find-request-unit-charge-cassandra.md), [API do SQL](find-request-unit-charge.md)e artigos de [API de tabela](find-request-unit-charge-table.md) para localizar a cobrança de ru/s.

Os cabeçalhos retornados pela API do Gremlin são mapeados para atributos de status personalizados, que atualmente são exibidos pelo SDK do Java e do .NET do Gremlin. O encargo de solicitação está disponível na chave `x-ms-request-charge`. Ao usar a API do Gremlin, você tem várias opções para encontrar o consumo de RU para uma operação em um contêiner do Azure Cosmos.

## <a name="use-the-azure-portal"></a>Usar o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-graph-gremlin-console.md#create-a-database-account) e alimente-a com alguns dados ou selecione uma conta existente que já contenha dados.

1. Acesse o painel do **Data Explorer** e, em seguida, selecione o contêiner no qual deseja trabalhar.

1. Insira uma consulta válida e, em seguida, selecione **Executar consulta Gremlin** .

1. Selecione **Estatísticas da Consulta** para exibir o preço de solicitação real da solicitação executada.

:::image type="content" source="./media/find-request-unit-charge/portal-gremlin-query.png" alt-text="Captura de tela para obter um encargo de solicitação de consulta Gremlin no portal do Azure":::

## <a name="use-the-net-sdk-driver"></a>Usar o driver SDK do .NET

Quando você usa o [SDK do .NET do Gremlin](https://www.nuget.org/packages/Gremlin.Net/), os atributos de status ficam disponíveis na propriedade `StatusAttributes` do objeto `ResultSet<>`:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Para obter mais informações, consulte [início rápido: criar um aplicativo .NET Framework ou Core usando uma conta de API do Gremlin Azure Cosmos DB](create-graph-dotnet.md).

## <a name="use-the-java-sdk-driver"></a>Usar o driver SDK do Java

Quando você usa o [SDK do Java do Gremlin](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver), você pode recuperar os atributos de status chamando o método `statusAttributes()` no objeto `ResultSet`:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Para obter mais informações, consulte [início rápido: criar um banco de dados de grafo no Azure Cosmos DB usando o SDK do Java](create-graph-java.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como otimizar o consumo de RU, confira estes artigos:

* [Unidades de solicitação e taxa de transferência no Azure Cosmos DB](request-units.md)
* [Otimizar a taxa de transferência provisionada no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo de consulta no Azure Cosmos DB](./optimize-cost-reads-writes.md)