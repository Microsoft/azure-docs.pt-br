---
title: Provisionar taxa de transferência de banco de dados na API Azure Cosmos DB do SQL
description: Saiba como provisionar a taxa de transferência no nível do banco de dados em Azure Cosmos DB API do SQL usando portal do Azure, CLI, PowerShell e vários outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4ecbee2260da735cd6ba74d3b9ffb55b4a652e9e
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341987"
---
# <a name="provision-standard-manual-throughput-on-a-database-in-azure-cosmos-db---sql-api"></a>Provisionar taxa de transferência padrão (manual) em um banco de dados na API Azure Cosmos DB-SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo explica como provisionar a taxa de transferência padrão (manual) em um banco de dados no Azure Cosmos DB API do SQL. Você pode provisionar a taxa de transferência para um único [contêiner](how-to-provision-container-throughput.md) ou para um banco de dados e compartilhá-la entre os contêineres dentro dele. Para saber quando usar taxa de transferência no nível de banco de dados e de contêiner, consulte o artigo [Casos de uso para provisionar taxa de transferência em contêineres e bancos de dados](set-throughput.md). É possível provisionar a taxa de transferência no nível do banco de dados usando o portal do Azure ou os SDKs do Azure Cosmos DB.

Se você estiver usando uma API diferente, confira [API para MongoDB](how-to-provision-throughput-mongodb.md), [API do Cassandra](how-to-provision-throughput-cassandra.md), artigos da [API Gremlin](how-to-provision-throughput-gremlin.md) para provisionar a taxa de transferência.

## <a name="provision-throughput-using-azure-portal"></a>Provisionar a taxa de transferência usando o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-sql-api-dotnet.md#create-account) ou selecione uma existente.

1. Abra o painel **Data Explorer** e selecione **Novo Banco de Dados**. Forneça os seguintes detalhes:

   * Insira uma ID do banco de dados.
   * Selecione a opção **provisionar taxa de transferência do banco de dados** .
   * Insira uma taxa de transferência (por exemplo, 1000 RUs).
   * Selecione **OK**.

    :::image type="content" source="./media/how-to-provision-database-throughput/provision-database-throughput-portal-sql-api.png" alt-text="Captura de tela da caixa de diálogo Novo Banco de Dados":::

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Provisionar taxa de transferência com a CLI do Azure ou o PowerShell

Para criar um banco de dados com taxa de transferência compartilhada, consulte:

* [Criar um banco de dados usando a CLI do Azure](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Criar um banco de dados usando o PowerShell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Provisionar a taxa de transferência usando o SDK .NET

> [!Note]
> Você pode usar os SDKs do Azure Cosmos para a API do SQL para provisionar a taxa de transferência para todas as APIs. Opcionalmente, você pode usar o exemplo a seguir para a API do Cassandra também.

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos para saber mais sobre taxa de transferência provisionada no Azure Cosmos DB:

* [Taxa de transferência provisionada para dimensionamento global](./request-units.md)
* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md)
* [Como provisionar a taxa de transferência padrão (manual) para um contêiner](how-to-provision-container-throughput.md)
* [Como provisionar a taxa de transferência de dimensionamento automático para um contêiner](how-to-provision-autoscale-throughput.md)
* [Unidades de solicitação e taxa de transferência no Azure Cosmos DB](request-units.md)