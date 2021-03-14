---
title: Localizar o encargo de unidade de solicitação para as operações Azure Cosmos DB API para MongoDB
description: Saiba como encontrar a cobrança de RU (unidade de solicitação) para consultas do MongoDB executadas em um contêiner Cosmos do Azure. Você pode usar os drivers portal do Azure, MongoDB .NET, Java Node.js.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: e488d1acfe116409caf571e7878e454628a9dea9
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201324"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-api-for-mongodb"></a>Localizar o encargo de unidade de solicitação para operações executadas na API Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

O Azure Cosmos DB dá suporte a muitas APIs como SQL, MongoDB, Cassandra, Gremlin e Table. Cada API tem seu próprio conjunto de operações de banco de dados. Essas operações variam de simples leituras e gravações de pontos a consultas complexas. Cada operação de banco de dados consome recursos do sistema com base na complexidade da operação.

O custo de todas as operações do banco de dados é normalizado pelo Azure Cosmos DB e expresso por Unidades de Solicitação (ou RUs, abreviado na sigla em inglês). O encargo da solicitação é as unidades de solicitação consumidas por todas as operações de banco de dados. Você pode considerar o RUs como uma moeda de desempenho abstraindo os recursos do sistema, como CPU, IOPS e memória, necessários para executar as operações de banco de dados com suporte pelo Azure Cosmos DB. Independentemente da API usada para interagir com o contêiner do Azure Cosmos DB, os custos sempre serão medidos por RUs. Se a operação do banco de dados é uma gravação, uma leitura de ponto ou uma consulta, os custos são sempre medidos em RUs. Para saber mais, consulte o artigo [sobre as unidades de solicitação e suas considerações](request-units.md) .

Este artigo apresenta as diferentes maneiras pelas quais você pode encontrar o consumo da ru ( [unidade de solicitação](request-units.md) ) para qualquer operação executada em um contêiner na API Azure Cosmos DB para MongoDB. Se você estiver usando uma API diferente, consulte [API do SQL](find-request-unit-charge.md), [API do Cassandra](find-request-unit-charge-cassandra.md), [api Gremlin](find-request-unit-charge-gremlin.md)e artigos de [API de tabela](find-request-unit-charge-table.md) para encontrar a cobrança de ru/s.

O preço de RU é exposto por um [comando de banco de dados](https://docs.mongodb.com/manual/reference/command/) personalizado chamado `getLastRequestStatistics`. O comando retorna um documento que contém o nome da última operação executada, seu preço de solicitação e sua duração. Se você usar a API do Azure Cosmos DB para MongoDB, você terá várias opções para recuperar o preço de RU.

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) e alimente-a com alguns dados ou selecione uma conta existente que já contenha dados.

1. Acesse o painel do **Data Explorer** e, em seguida, selecione o contêiner no qual deseja trabalhar.

1. Selecione **Nova Consulta**.

1. Insira uma consulta válida e, em seguida, selecione **Executar Consulta**.

1. Selecione **Estatísticas da Consulta** para exibir o preço de solicitação real da solicitação executada.

:::image type="content" source="./media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Captura de tela do preço de solicitação de consulta MongoDB no portal do Azure":::

## <a name="use-the-mongodb-net-driver"></a>Usar o driver do .NET do MongoDB

Ao usar o [driver oficial do .NET do MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/), você pode executar comandos chamando o método `RunCommand` em um objeto `IMongoDatabase`. Esse método exige uma implementação da classe abstrata `Command<>`:

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

Para obter mais informações, consulte [início rápido: compilar um aplicativo Web .NET usando uma API Azure Cosmos DB para MongoDB](create-mongodb-dotnet.md).

## <a name="use-the-mongodb-java-driver"></a>Usar o driver do Java do MongoDB


Ao usar o [driver oficial do Java do MongoDB](https://mongodb.github.io/mongo-java-driver/), você pode executar comandos chamando o método `runCommand` em um objeto `MongoDatabase`:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Para obter mais informações, consulte [início rápido: criar um aplicativo Web usando a API Azure Cosmos DB para MongoDB e o SDK do Java](create-mongodb-java.md).

## <a name="use-the-mongodb-nodejs-driver"></a>Usar o driver do Node.js do MongoDB

Ao usar o [driver oficial do Node.js do MongoDB](https://mongodb.github.io/node-mongodb-native/), você pode executar comandos chamando o método `command` em um objeto `db`:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Para obter mais informações, consulte [início rápido: migrar um aplicativo web Node.js MongoDB existente para Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como otimizar o consumo de RU, confira estes artigos:

* [Unidades de solicitação e taxa de transferência no Azure Cosmos DB](request-units.md)
* [Otimizar a taxa de transferência provisionada no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo de consulta no Azure Cosmos DB](./optimize-cost-reads-writes.md)