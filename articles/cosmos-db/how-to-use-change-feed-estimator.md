---
title: Usar o avaliador do feed de alterações – Azure Cosmos DB
description: Saiba como usar o avaliador do feed de alterações para analisar o progresso do processador do feed de alterações
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/15/2019
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: a44557d15f437317c2b5fa659ab8d4ca3c208edf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339828"
---
# <a name="use-the-change-feed-estimator"></a>Usar o avaliador do feed de alterações
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo descreve como você pode monitorar o progresso das instâncias do [processador do feed de alterações](./change-feed-processor.md) ao ler o feed de alterações.

## <a name="why-is-monitoring-progress-important"></a>Por que o progresso do monitoramento é importante?

O processador do feed de alterações funciona como um ponteiro que avança no [feed de alterações](./change-feed.md) e entrega as alterações a uma implementação de representante. 

A implantação do processador do feed de alterações pode processar alterações a uma taxa específica com base em seus recursos disponíveis, como CPU, memória, rede e assim por diante.

Se essa taxa for mais lenta do que a taxa na qual as alterações ocorrem no contêiner do Azure Cosmos, o processador começará a ficar atrasado.

A identificação desse cenário ajuda a entender se precisamos dimensionar nossa implantação do processador do feed de alterações.

## <a name="implement-the-change-feed-estimator"></a>Implementar o avaliador do feed de alterações

Assim como o [processador do feed de alterações](./change-feed-processor.md), o avaliador do feed de alterações funciona como um modelo de push. O avaliador medirá a diferença entre o último item processado (definido pelo estado do contêiner de concessões) e a alteração mais recente no contêiner e efetuará push desse valor a um representante. O intervalo no qual a medição é feita também pode ser personalizado com um valor padrão de 5 segundos.

Por exemplo, se o processador do feed de alterações for definido assim:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

A maneira correta de inicializar um avaliador para medir esse processador é usar `GetChangeFeedEstimatorBuilder` desta forma:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

Em que o processador e o avaliador compartilham o mesmo `leaseContainer` e o mesmo nome.

Os outros dois parâmetros são o representante, que receberá um número que representa **o número de alterações pendentes para serem lidas** pelo processador e o intervalo de tempo no qual você deseja que essa medida seja feita.

Um exemplo de um representante que recebe a estimativa é:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

Envie essa estimativa para sua solução de monitoramento e use-a para entender como o progresso está se comportando ao longo do tempo.

> [!NOTE]
> O avaliador do feed de alterações não precisa ser implantado como parte do processador do feed de alterações, nem fazer parte do mesmo projeto. Ele pode ser independente e ser executado em uma instância completamente diferente. Ele só precisa usar o mesmo nome e a mesma configuração de concessão.

## <a name="additional-resources"></a>Recursos adicionais

* [SDK do Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Exemplos de código no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Exemplos adicionais sobre o GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Próximas etapas

Agora continue para saber mais sobre o processador do feed de alterações nos seguintes artigos:

* [Visão geral do processador do feed de alterações](change-feed-processor.md)
* [Hora de início do processador do feed de alterações](./change-feed-processor.md#starting-time)