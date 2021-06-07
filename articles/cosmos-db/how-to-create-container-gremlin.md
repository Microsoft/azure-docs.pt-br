---
title: Criar um contêiner na API Azure Cosmos DB Gremlin
description: Saiba como criar um contêiner na API Azure Cosmos DB Gremlin usando portal do Azure, .NET e outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: f7e9de1f23ec46af08fe96b5db3170fac9a7eb2e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93101622"
---
# <a name="create-a-container-in-azure-cosmos-db-gremlin-api"></a>Criar um contêiner na API Azure Cosmos DB Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Este artigo explica as diferentes maneiras de criar um contêiner na API Azure Cosmos DB Gremlin. Ele mostra como criar um contêiner usando portal do Azure, CLI do Azure, PowerShell ou SDKs com suporte. Este artigo demonstra como criar um contêiner, especificar a chave de partição e a taxa de transferência de provisionamento.

Este artigo explica as diferentes maneiras de criar um contêiner na API Azure Cosmos DB Gremlin. Se você estiver usando uma API diferente, confira [API para MongoDB](how-to-create-container-mongodb.md), [API do Cassandra](how-to-create-container-cassandra.md), [API de tabela](how-to-create-container-table.md)e artigos da [API do SQL](how-to-create-container.md) para criar o contêiner.

> [!NOTE]
> Ao criar contêineres, não crie dois contêineres com o mesmo nome, mas com maiúsculas e minúsculas diferentes. Isso porque algumas partes da plataforma Azure não diferenciam maiúsculas de minúsculas, o que pode resultar em confusão/colisão de telemetria e ações em contêineres com tais nomes.

## <a name="create-using-azure-portal"></a><a id="portal-gremlin"></a>Criar usando o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure Cosmos](create-graph-dotnet.md#create-a-database-account)ou selecione uma conta existente.

1. Abra o painel de **Data Explorer** e selecione **novo grafo**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um banco de dados ou usando um existente.
   * Insira uma ID do grafo.
   * Selecione a capacidade de armazenamento **Ilimitada**.
   * Insira uma chave de partição para vértices.
   * Insira uma taxa de transferência a ser provisionada (por exemplo, 1.000 RUs).
   * Selecione **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-gremlin.png" alt-text="Captura de tela da API do Gremlin, caixa de diálogo Adicionar grafo":::

## <a name="create-using-net-sdk"></a><a id="dotnet-sql-graph"></a>Criar usando o SDK do .NET

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

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Criar usando CLI do Azure

[Crie um grafo Gremlin com CLI do Azure](./scripts/cli/gremlin/create.md). Para obter uma lista de todos os exemplos de CLI do Azure em todas as APIs de Azure Cosmos DB, consulte [CLI do Azure amostras para Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Criar usando o PowerShell

[Crie um grafo do Gremlin com o PowerShell](./scripts/powershell/gremlin/create.md). Para obter uma lista de todos os exemplos do PowerShell em todas as APIs de Azure Cosmos DB, consulte [exemplos do PowerShell](powershell-samples.md)

## <a name="next-steps"></a>Próximas etapas

* [Particionamento no Azure Cosmos DB](partitioning-overview.md)
* [Unidades de Solicitação no Azure Cosmos DB](request-units.md)
* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md)
* [Como trabalhar com a conta do Azure Cosmos](./account-databases-containers-items.md)