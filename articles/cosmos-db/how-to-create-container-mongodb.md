---
title: Criar um contêiner na API Azure Cosmos DB para MongoDB
description: Saiba como criar um contêiner na API Azure Cosmos DB para MongoDB usando portal do Azure, .NET, Java, Node.js e outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: a5669b15c041f663605a62ef8d02b206928d0c14
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101588"
---
# <a name="create-a-container-in-azure-cosmos-db-api-for-mongodb"></a>Criar um contêiner na API Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Este artigo explica as diferentes maneiras de criar um contêiner em Azure Cosmos DB API para MongoDB. Ele mostra como criar um contêiner usando portal do Azure, CLI do Azure, PowerShell ou SDKs com suporte. Este artigo demonstra como criar um contêiner, especificar a chave de partição e a taxa de transferência de provisionamento.

Este artigo explica as diferentes maneiras de criar um contêiner em Azure Cosmos DB API para MongoDB. Se você estiver usando uma API diferente, consulte [API do SQL](how-to-create-container.md), [API do Cassandra](how-to-create-container-cassandra.md), [api Gremlin](how-to-create-container-gremlin.md)e artigos de [API de tabela](how-to-create-container-table.md) para criar o contêiner.

> [!NOTE]
> Ao criar contêineres, não crie dois contêineres com o mesmo nome, mas com maiúsculas e minúsculas diferentes. Isso porque algumas partes da plataforma Azure não diferenciam maiúsculas de minúsculas, o que pode resultar em confusão/colisão de telemetria e ações em contêineres com tais nomes.

## <a name="create-using-azure-portal"></a><a id="portal-mongodb"></a>Criar usando o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account)ou selecione uma conta existente.

1. Abra o painel **Data Explorer** e selecione **novo contêiner** . Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um banco de dados ou usando um existente.
   * Insira uma ID de contêiner.
   * Insira uma chave de fragmento.
   * Insira uma taxa de transferência a ser provisionada (por exemplo, 1.000 RUs).
   * Selecione **OK** .

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="Captura de tela da API Azure Cosmos DB para MongoDB, caixa de diálogo Adicionar contêiner":::

## <a name="create-using-net-sdk"></a><a id="dotnet-mongodb"></a>Criar usando o SDK do .NET

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> O protocolo de transmissão do MongoDB não compreende o conceito de [Unidades de Solicitação](request-units.md). Para criar uma coleção contendo taxa de transferência provisionada, use o portal do Azure ou os SDKs do Cosmos DB para a API do SQL.

Se você encontrar uma exceção de tempo limite ao criar uma coleção, faça uma operação de leitura para validar se a coleção foi criada com êxito. A operação de leitura gera uma exceção até que a operação de criação da coleção seja bem-sucedida. Para obter a lista de códigos de status com suporte pela operação de criação, consulte os [códigos de status HTTP para Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) artigo.

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Criar usando CLI do Azure

[Crie uma coleção para Azure Cosmos DB para a API do MongoDB com CLI do Azure](./scripts/cli/mongodb/create.md). Para obter uma lista de todos os exemplos de CLI do Azure em todas as APIs de Azure Cosmos DB, consulte [CLI do Azure amostras para Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Criar usando o PowerShell

[Crie uma coleção para Azure Cosmos DB para a API do MongoDB com o PowerShell](./scripts/powershell/mongodb/create.md). Para obter uma lista de todos os exemplos do PowerShell em todas as APIs de Azure Cosmos DB, consulte [exemplos do PowerShell](powershell-samples.md)

## <a name="create-a-container-using-azure-resource-manager-templates"></a>Criar um contêiner usando modelos de Azure Resource Manager

[Crie uma coleção para Azure Cosmos DB para a API do MongoDB com o modelo do Resource Manager](./manage-with-templates.md#azure-cosmos-account-with-standard-provisioned-throughput).

## <a name="next-steps"></a>Próximas etapas

* [Particionamento no Azure Cosmos DB](partitioning-overview.md)
* [Unidades de Solicitação no Azure Cosmos DB](request-units.md)
* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md)
* [Como trabalhar com a conta do Azure Cosmos](./account-databases-containers-items.md)