---
title: Provisione a taxa de transferência em recursos de API do Cassandra Azure Cosmos DB
description: Saiba como provisionar a taxa de transferência de contêiner, banco de dados e dimensionamento automático em Azure Cosmos DB API do Cassandra recursos. Você usará portal do Azure, CLI, PowerShell e vários outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 63b633ed67c03a006a154bc69a1aafb4cb4aa6d0
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086271"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-cassandra-api-resources"></a>Provisionar produtividade de banco de dados, contêiner ou dimensionamento automático em Azure Cosmos DB API do Cassandra recursos
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Este artigo explica como provisionar a taxa de transferência no API do Cassandra Azure Cosmos DB. Você pode provisionar a taxa de transferência padrão (manual) ou de dimensionamento automático em um contêiner ou um banco de dados e compartilhá-lo entre os contêineres no banco de dados. Você pode provisionar a taxa de transferência usando os SDKs portal do Azure, CLI do Azure ou Azure Cosmos DB.

Se você estiver usando uma API diferente, consulte [API do SQL](how-to-provision-container-throughput.md), [API para MongoDB](how-to-provision-throughput-mongodb.md), artigos da [API do Gremlin](how-to-provision-throughput-gremlin.md) para provisionar a taxa de transferência.

## <a name="azure-portal"></a><a id="portal-cassandra"></a> portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) ou selecione uma existente.

1. Abra o painel **Data Explorer** e selecione **nova tabela** . Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um novo keyspace ou usando um existente. Selecione a opção **provisionar taxa de transferência do banco de dados** se desejar provisionar a taxa de transferência no nível do keyspace.
   * Insira a ID da tabela no comando CQL.
   * Insira um valor de chave primária (por exemplo, `/userrID` ).
   * Insira uma taxa de transferência que você deseja provisionar (por exemplo, 1.000 RUs).
   * Selecione **OK** .

    :::image type="content" source="./media/how-to-provision-throughput-cassandra/provision-table-throughput-portal-cassandra-api.png" alt-text="Captura de tela de Data Explorer ao criar uma nova coleção com taxa de transferência no nível do banco de dados":::

> [!Note]
> Se você estiver provisionando a taxa de transferência em um contêiner em uma conta do Azure Cosmos configurada com a API do Cassandra, use `/myPrimaryKey` para o caminho da chave de partição.

## <a name="net-sdk"></a><a id="dotnet-cassandra"></a> SDK DO .NET

### <a name="provision-throughput-for-a-cassandra-table"></a>Provisionar a taxa de transferência para uma tabela Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
Comandos semelhantes podem ser emitidos por meio de qualquer driver compatível com CQL.

### <a name="alter-or-change-throughput-for-a-cassandra-table"></a>Alterar ou alterar a taxa de transferência de uma tabela Cassandra

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```

Um comando semelhante pode ser executado por qualquer driver compatível com CQL.

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager modelos podem ser usados para provisionar a taxa de transferência de dimensionamento automático em recursos de banco de dados ou de contêiner para todas as APIs de Azure Cosmos DB Consulte [Azure Resource Manager modelos para Azure Cosmos DB](templates-samples-cassandra.md) para obter exemplos.

## <a name="azure-cli"></a>CLI do Azure

CLI do Azure pode ser usado para provisionar a taxa de transferência de autoescala em um banco de dados ou recursos de nível de contêiner para todas as APIs de Azure Cosmos DB Para obter exemplos, consulte [exemplos de CLI do Azure para Azure Cosmos DB](cli-samples-cassandra.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell pode ser usado para provisionar a taxa de transferência de autoescala em um banco de dados ou recursos de nível de contêiner para todas as APIs de Azure Cosmos DB Para obter exemplos, consulte [exemplos de Azure PowerShell para Azure Cosmos DB](powershell-samples-cassandra.md).

## <a name="next-steps"></a>Próximas etapas

Confira os seguintes artigos para saber mais sobre o provisionamento de taxa de transferência no Azure Cosmos DB:

* [Unidades de solicitação e taxa de transferência no Azure Cosmos DB](request-units.md)