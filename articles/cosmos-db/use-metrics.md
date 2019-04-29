---
title: Monitore e depure com métricas no Azure Cosmos DB
description: Use métricas no Azure Cosmos DB para depurar problemas comuns e monitorar o banco de dados.
ms.service: cosmos-db
author: kanshiG
ms.author: sngun
ms.topic: conceptual
ms.date: 11/15/2018
ms.reviewer: sngun
ms.openlocfilehash: ff6e0b6084eebf236d01b4dd00a46897687938c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765768"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Monitore e depure com métricas no Azure Cosmos DB

O Azure Cosmos DB fornece métricas de taxa de transferência, armazenamento, consistência, disponibilidade e latência. O [portal do Microsoft Azure](https://portal.azure.com) fornece uma visualização agregada dessas métricas. Para métricas mais granulares, o SDK do cliente e os [Logs de diagnóstico](./logging.md) estão disponíveis.

Este artigo explica como casos de uso comuns e como as métricas do Azure Cosmos DB podem ser usadas para analisar e depurar esses problemas. As métricas são coletadas a cada cinco minutos e são mantidas por sete dias.

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Reconhecimento de quantas solicitações estão tendo sucesso ou causando erros

Para começar, vá até o [Portal do Azure](https://portal.azure.com) e navegue até a folha **Métricas**. Nessa folha, localize o gráfico **Número de solicitações que excedeu a capacidade por 1 minuto**. Este gráfico mostra um total de solicitações minuto a minuto segmentadas pelo código de status. Para obter mais informações sobre códigos de status HTTP, consulte [Códigos de status HTTP para o banco de dados do Microsoft Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

O código de status de erro mais comum é 429 (limitação/limitação da taxa). Esse erro significa que as solicitações para o Azure Cosmos DB são mais do que a taxa de transferência provisionada. A solução mais comum para esse problema é [expandir as RUs](./set-throughput.md) para determinada coleção.

![Número de solicitações por minuto](media/use-metrics/metrics-12.png)

## <a name="determine-the-throughput-distribution-across-partitions"></a>Determine a distribuição da taxa de transferência entre as partições

Ter uma boa cardinalidade de suas chaves de partição é essencial para qualquer aplicativo escalonável. Para determinar a distribuição da produtividade de qualquer contêiner particionado dividida por partições, navegue até a **folha Métricas** no [portal do Azure](https://portal.azure.com). Na guia **Taxa de transferência**, a divisão do armazenamento é mostrada no gráfico **Máx. de RUs/segundo consumidas para cada partição física**. O gráfico a seguir ilustra um exemplo de uma má distribuição de dados, conforme mostrado pela partição distorcida na extrema esquerda.

![Partição única apresentando uso intenso às 15h05](media/use-metrics/metrics-17.png)

Uma distribuição desigual de taxa de transferência pode causar partições *quentes*, que por sua vez podem resultar em solicitações limitadas e podem exigir reparticionamento. Para obter mais informações sobre o particionamento no Azure Cosmos DB, consulte [Partição e escala no Azure Cosmos DB](./partition-data.md).

## <a name="determine-the-storage-distribution-across-partitions"></a>Determinar a distribuição de armazenamento nas partições

Ter uma boa cardinalidade em sua partição é essencial para qualquer aplicativo escalonável. Para determinar a distribuição de armazenamento de qualquer contêiner particionado dividida por partições, acesse a folha Métricas no [portal do Azure](https://portal.azure.com). Na guia Armazenamento, a divisão de armazenamento é mostrada no armazenamento Dados + índice consumido pelo gráfico de chaves de partição superior. O gráfico a seguir ilustra uma distribuição ruim de armazenamento de dados, conforme mostrado pela partição distorcida na extrema esquerda.

![Exemplo de distribuição de dados ruim](media/use-metrics/metrics-07.png)

Você pode determinar a causa raiz ao identificar qual chave de partição está distorcendo a distribuição, clicando na partição no gráfico.

![A chave de partição está distorcendo a distribuição](media/use-metrics/metrics-05.png)

Depois de identificar qual chave de partição está causando a distorção na distribuição, você precisará reparticionar o contêiner com uma chave de partição mais distribuída. Para obter mais informações sobre o particionamento no Azure Cosmos DB, consulte [Partição e escala no Azure Cosmos DB](./partition-data.md).

## <a name="compare-data-size-against-index-size"></a>Compare o tamanho de dados em relação ao tamanho do índice

No Azure Cosmos DB, o armazenamento consumido total é a combinação do Tamanho dos dados e do Tamanho do índice. Normalmente, o tamanho do índice é uma fração do tamanho dos dados. Na folha Métricas no [Portal do Azure](https://portal.azure.com), a guia Armazenamento apresenta a divisão do consumo de armazenamento com base nos dados e no índice.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Se você quiser conservar espaço de índice, você poderá ajustar a [política de indexação](index-policy.md).

## <a name="debug-why-queries-are-running-slow"></a>Depurar o motivo pelo qual as consultas estão executando lentamente

Nos SDKs de API do SQL, o Azure Cosmos DB fornece estatísticas de execução de consulta.

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* fornece detalhes sobre quanto tempo levou a execução de cada componente da consulta. A causa raiz mais comum para consultas demoradas é a varredura, o que significa que a consulta não conseguiu alavancar os índices. Esse problema pode ser resolvido com uma melhor condição de filtro.

## <a name="next-steps"></a>Próximas etapas

Agora você aprendeu como monitorar e depurar problemas usando as métricas fornecidas no portal do Azure. Você pode querer aprender mais sobre como melhorar o desempenho do banco de dados lendo os seguintes artigos:

* [Teste de desempenho e escala com o Azure Cosmos DB](performance-testing.md)
* [Dicas de desempenho para o Azure Cosmos DB](performance-tips.md)
