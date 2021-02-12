---
title: Criar um contêiner em Azure Cosmos DB API do SQL
description: Saiba como criar um contêiner em Azure Cosmos DB API do SQL usando portal do Azure, .NET, Java, Python, Node.js e outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 302c5d6e8e523a11b8773f10bb6089e3bea09bdd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006841"
---
# <a name="create-a-container-in-azure-cosmos-db-sql-api"></a>Criar um contêiner em Azure Cosmos DB API do SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo explica as diferentes maneiras de criar um contêiner em Azure Cosmos DB API do SQL. Ele mostra como criar um contêiner usando o portal do Azure, CLI do Azure, PowerShell ou SDKs com suporte. Este artigo demonstra como criar um contêiner, especificar a chave de partição e a taxa de transferência de provisionamento.

Este artigo explica as diferentes maneiras de criar um contêiner em Azure Cosmos DB API do SQL. Se você estiver usando uma API diferente, consulte [API para MongoDB](how-to-create-container-mongodb.md), [API do Cassandra](how-to-create-container-cassandra.md), [api Gremlin](how-to-create-container-gremlin.md)e artigos de [API de tabela](how-to-create-container-table.md) para criar o contêiner.

> [!NOTE]
> Ao criar contêineres, não crie dois contêineres com o mesmo nome, mas com maiúsculas e minúsculas diferentes. Isso porque algumas partes da plataforma Azure não diferenciam maiúsculas de minúsculas, o que pode resultar em confusão/colisão de telemetria e ações em contêineres com tais nomes.

## <a name="create-a-container-using-azure-portal"></a><a id="portal-sql"></a>Criar um contêiner usando o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-sql-api-dotnet.md#create-account) ou selecione uma existente.

1. Abra o painel **Data Explorer** e selecione **novo contêiner**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um banco de dados ou usando um existente.
   * Insira uma ID de contêiner.
   * Insira uma chave de partição.
   * Insira uma taxa de transferência a ser provisionada (por exemplo, 1.000 RUs).
   * Selecione **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="Captura de tela do painel de Data Explorer, com o novo contêiner realçado":::

## <a name="create-a-container-using-azure-cli"></a><a id="cli-sql"></a>Criar um contêiner usando CLI do Azure

[Crie um contêiner com CLI do Azure](manage-with-cli.md#create-a-container). Para obter uma lista de todos os exemplos de CLI do Azure em todas as APIs de Azure Cosmos DB, consulte [CLI do Azure amostras para Azure Cosmos DB](cli-samples.md).

## <a name="create-a-container-using-powershell"></a>Criar um contêiner usando o PowerShell

[Crie um contêiner com o PowerShell](manage-with-powershell.md#create-container). Para obter uma lista de todos os exemplos do PowerShell em todas as APIs de Azure Cosmos DB, consulte [exemplos do PowerShell](powershell-samples.md)

## <a name="create-a-container-using-net-sdk"></a><a id="dotnet-sql"></a>Criar um contêiner usando o SDK do .NET

Se você encontrar uma exceção de tempo limite ao criar uma coleção, faça uma operação de leitura para validar se a coleção foi criada com êxito. A operação de leitura gera uma exceção até que a operação de criação da coleção seja bem-sucedida. Para obter a lista de códigos de status com suporte pela operação de criação, consulte os [códigos de status HTTP para Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) artigo.

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

## <a name="next-steps"></a>Próximas etapas

* [Particionamento no Azure Cosmos DB](partitioning-overview.md)
* [Unidades de Solicitação no Azure Cosmos DB](request-units.md)
* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md)
* [Como trabalhar com a conta do Azure Cosmos](./account-databases-containers-items.md)