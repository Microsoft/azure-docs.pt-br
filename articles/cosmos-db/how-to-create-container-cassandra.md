---
title: Criar um contêiner no Azure Cosmos DB API do Cassandra
description: Saiba como criar um contêiner no Azure Cosmos DB API do Cassandra usando portal do Azure, .NET, Java, Python, Node.js e outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 01030a563c15eee7786058c2eae30d23803dfe42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93101639"
---
# <a name="create-a-container-in-azure-cosmos-db-cassandra-api"></a>Criar um contêiner no Azure Cosmos DB API do Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Este artigo explica as diferentes maneiras de criar um contêiner no Azure Cosmos DB API do Cassandra. Ele mostra como criar um contêiner usando portal do Azure, CLI do Azure, PowerShell ou SDKs com suporte. Este artigo demonstra como criar um contêiner, especificar a chave de partição e a taxa de transferência de provisionamento.

Este artigo explica as diferentes maneiras de criar um contêiner no Azure Cosmos DB API do Cassandra. Se você estiver usando uma API diferente, confira [API para MongoDB](how-to-create-container-mongodb.md), [api Gremlin](how-to-create-container-gremlin.md), [API de tabela](how-to-create-container-table.md)e artigos da [API do SQL](how-to-create-container.md) para criar o contêiner.

> [!NOTE]
> Ao criar contêineres, não crie dois contêineres com o mesmo nome, mas com maiúsculas e minúsculas diferentes. Isso porque algumas partes da plataforma Azure não diferenciam maiúsculas de minúsculas, o que pode resultar em confusão/colisão de telemetria e ações em contêineres com tais nomes.

## <a name="create-using-azure-portal"></a><a id="portal-cassandra"></a>Criar usando o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure Cosmos](create-cassandra-dotnet.md#create-a-database-account)ou selecione uma conta existente.

1. Abra o painel **Data Explorer** e selecione **nova tabela**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um keyspace ou usando um existente.
   * Insira um nome de tabela.
   * Insira as propriedades e especifique uma chave primária.
   * Insira uma taxa de transferência a ser provisionada (por exemplo, 1.000 RUs).
   * Selecione **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-cassandra.png" alt-text="Captura de tela da API do Cassandra, caixa de diálogo Adicionar tabela":::

> [!NOTE]
> Para a API do Cassandra, a chave primária é usada como a chave de partição.

## <a name="create-using-net-sdk"></a><a id="dotnet-cassandra"></a>Criar usando o SDK do .NET

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

Se você encontrar uma exceção de tempo limite ao criar uma coleção, faça uma operação de leitura para validar se a coleção foi criada com êxito. A operação de leitura gera uma exceção até que a operação de criação da coleção seja bem-sucedida. Para obter a lista de códigos de status com suporte pela operação de criação, consulte os [códigos de status HTTP para Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) artigo.

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Criar usando CLI do Azure

[Crie uma tabela Cassandra com CLI do Azure](./scripts/cli/cassandra/create.md). Para obter uma lista de todos os exemplos de CLI do Azure em todas as APIs de Azure Cosmos DB, consulte [CLI do Azure amostras para Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Criar usando o PowerShell

[Crie uma tabela Cassandra com o PowerShell](./scripts/powershell/cassandra/create.md). Para obter uma lista de todos os exemplos do PowerShell em todas as APIs de Azure Cosmos DB, consulte [exemplos do PowerShell](powershell-samples.md)

## <a name="next-steps"></a>Próximas etapas

* [Particionamento no Azure Cosmos DB](partitioning-overview.md)
* [Unidades de Solicitação no Azure Cosmos DB](request-units.md)
* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md)
* [Como trabalhar com a conta do Azure Cosmos](./account-databases-containers-items.md)