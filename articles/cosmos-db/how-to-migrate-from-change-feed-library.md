---
title: Migrar da biblioteca do processador do feed de alterações para o SDK Azure Cosmos DB .NET v3
description: Saiba como migrar seu aplicativo do usando a biblioteca do processador do feed de alterações para o SDK do Azure Cosmos DB v3
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/17/2019
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: ce2d4d3ad3ae349718f01584ec077b18e11e4f8d
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341253"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Migrar da biblioteca do processador do feed de alterações para o SDK Azure Cosmos DB .NET v3
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo descreve as etapas necessárias para migrar um código de aplicativo existente que usa a [biblioteca do processador do feed de alterações](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) para o recurso de feed de [alterações](change-feed.md) na versão mais recente do SDK do .net (também conhecido como SDK do .net v3).

## <a name="required-code-changes"></a>Alterações de código necessárias

O SDK do .NET v3 tem várias alterações significativas, as seguintes são as principais etapas para migrar seu aplicativo:

1. Converta as `DocumentCollectionInfo` instâncias em `Container` referências para os contêineres monitorado e concessões.
1. As personalizações que usam `WithProcessorOptions` devem ser atualizadas para usar `WithLeaseConfiguration` e `WithPollInterval` para intervalos, `WithStartTime` [para hora de início](./change-feed-processor.md#starting-time)e `WithMaxItems` para definir a contagem máxima de itens.
1. Defina `processorName` ativado `GetChangeFeedProcessorBuilder` para corresponder ao valor configurado em `ChangeFeedProcessorOptions.LeasePrefix` ou use o `string.Empty` contrário.
1. As alterações não são mais entregues como um `IReadOnlyList<Document>` , em vez disso, é um `IReadOnlyCollection<T>` onde `T` é um tipo que você precisa definir, não há mais nenhuma classe de item base.
1. Para lidar com as alterações, você não precisa mais de uma implementação, em vez disso, precisa [definir um delegado](change-feed-processor.md#implementing-the-change-feed-processor). O delegado pode ser uma função estática ou, se você precisar manter o estado entre execuções, poderá criar sua própria classe e passar um método de instância como delegado.

Por exemplo, se o código original para criar o processador do feed de alterações for semelhante ao seguinte:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

O código migrado terá a seguinte aparência:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

E o delegado, pode ser um método estático:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>Contêiner de estado e concessão

Semelhante à biblioteca do processador do feed de alterações, o recurso de feed de alterações no SDK do .NET v3 usa um [contêiner de concessão](change-feed-processor.md#components-of-the-change-feed-processor) para armazenar o estado. No entanto, os esquemas são diferentes.

O processador do feed de alterações do SDK v3 detectará qualquer estado de biblioteca antigo e o migrará para o novo esquema automaticamente na primeira execução do código do aplicativo migrado. 

Você pode parar com segurança o aplicativo usando o código antigo, migrar o código para a nova versão, iniciar o aplicativo migrado e todas as alterações ocorridas enquanto o aplicativo foi interrompido serão coletadas e processadas pela nova versão.

## <a name="additional-resources"></a>Recursos adicionais

* [SDK do Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Exemplos de código no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Exemplos adicionais sobre o GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Próximas etapas

Agora continue para saber mais sobre o processador do feed de alterações nos seguintes artigos:

* [Visão geral do processador do feed de alterações](change-feed-processor.md)
* [Como usar o avaliador do feed de alterações](how-to-use-change-feed-estimator.md)
* [Hora de início do processador do feed de alterações](./change-feed-processor.md#starting-time)