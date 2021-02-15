---
title: Criar um contêiner no Azure Cosmos DB API de Tabela
description: Saiba como criar um contêiner no Azure Cosmos DB API de Tabela usando portal do Azure, .NET, Java, Python, Node.js e outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: fde3145e7bd7f4e53ae7a0c44f02e066c28ec785
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101571"
---
# <a name="create-a-container-in-azure-cosmos-db-table-api"></a>Criar um contêiner no Azure Cosmos DB API de Tabela
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Este artigo explica as diferentes maneiras de criar um contêiner no Azure Cosmos DB API de Tabela. Ele mostra como criar um contêiner usando portal do Azure, CLI do Azure, PowerShell ou SDKs com suporte. Este artigo demonstra como criar um contêiner, especificar a chave de partição e a taxa de transferência de provisionamento.

Este artigo explica as diferentes maneiras de criar um contêiner no Azure Cosmos DB API de Tabela. Se você estiver usando uma API diferente, confira [API para MongoDB](how-to-create-container-mongodb.md), [API do Cassandra](how-to-create-container-cassandra.md), [API Gremlin](how-to-create-container-gremlin.md)e artigos da [API do SQL](how-to-create-container.md) para criar o contêiner.

> [!NOTE]
> Ao criar contêineres, não crie dois contêineres com o mesmo nome, mas com maiúsculas e minúsculas diferentes. Isso porque algumas partes da plataforma Azure não diferenciam maiúsculas de minúsculas, o que pode resultar em confusão/colisão de telemetria e ações em contêineres com tais nomes.

## <a name="create-using-azure-portal"></a><a id="portal-table"></a>Criar usando o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure Cosmos](create-table-dotnet.md#create-a-database-account)ou selecione uma conta existente.

1. Abra o painel **Data Explorer** e selecione **nova tabela** . Em seguida, forneça os seguintes detalhes:

   * Insira uma ID da tabela.
   * Insira uma taxa de transferência a ser provisionada (por exemplo, 1.000 RUs).
   * Selecione **OK** .

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="Captura de tela da API de Tabela, caixa de diálogo Adicionar tabela":::

> [!Note]
> Para a API de Tabela, a chave de partição é especificada sempre que você adiciona uma nova linha.

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Criar usando CLI do Azure

[Crie uma tabela de API de tabela com CLI do Azure](./scripts/cli/table/create.md). Para obter uma lista de todos os exemplos de CLI do Azure em todas as APIs de Azure Cosmos DB, consulte [CLI do Azure amostras para Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Criar usando o PowerShell

[Crie uma tabela de API de tabela com o PowerShell](./scripts/powershell/table/create.md). Para obter uma lista de todos os exemplos do PowerShell em todas as APIs de Azure Cosmos DB, consulte [exemplos do PowerShell](powershell-samples.md)

## <a name="next-steps"></a>Próximas etapas

* [Particionamento no Azure Cosmos DB](partitioning-overview.md)
* [Unidades de Solicitação no Azure Cosmos DB](request-units.md)
* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md)
* [Como trabalhar com a conta do Azure Cosmos](./account-databases-containers-items.md)