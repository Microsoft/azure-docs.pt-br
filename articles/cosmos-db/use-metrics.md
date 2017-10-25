---
title: "Monitoramento e a depuração com métricas no Azure Cosmos DB | Microsoft Docs"
description: "Use métricas no Azure Cosmos DB para depurar problemas comuns e monitorar o banco de dados."
keywords: "Métricas"
services: cosmos-db
author: gnot
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: govindk
ms.openlocfilehash: e6399831fe7c6cc727e92b13719df3b69e9981bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-debugging-with-metrics-in-azure-cosmos-db"></a>Monitoramento e a depuração com métricas no Azure Cosmos DB

O Azure Cosmos DB fornece métricas de taxa de transferência, armazenamento, consistência, disponibilidade e latência. O [Portal do Azure](https://portal.azure.com) fornece uma exibição agregada dessas métricas; para métricas mais granulares, tanto o SDK do cliente quanto os [logs de diagnóstico](./logging.md) estão disponíveis.

Para obter uma visão geral das novas métricas e aprender como localizar partições quentes no banco de dados, assista ao seguinte vídeo do Azure Friday:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Cosmos-DB-Get-the-Most-Out-of-Provisioned-Throughput/player]
> 

Este artigo explica como casos de uso comuns e como as métricas do Azure Cosmos DB podem ser usadas para analisar e depurar esses problemas. As métricas são coletadas a cada cinco minutos e são mantidas por sete dias.

## <a name="understanding-how-many-requests-are-succeeding-or-causing-errors"></a>Noções básicas sobre quantas solicitações estão sendo bem-sucedidas ou causando erros

Para começar, vá até o [Portal do Azure](https://portal.azure.com) e navegue até a folha **Métricas**. Nessa folha, localize o gráfico **Número de solicitações que excedeu a capacidade por 1 minuto**. Este gráfico mostra um total de solicitações minuto a minuto segmentadas pelo código de status. Para obter mais informações sobre códigos de status HTTP, consulte [Códigos de Status HTTP para o Azure Cosmos DB](https://docs.microsoft.com/rest/api/documentdb/http-status-codes-for-documentdb).

O código de status de erro mais comum é 429 (limitação), o que significa que solicitações ao Azure Cosmos DB estão excedendo a taxa de transferência provisionada. A solução mais comum para isso é [expandir as RUs](./set-throughput.md) para a coleção em questão.

![Número de solicitações por minuto](media/use-metrics/metrics-12.png)

## <a name="determining-the-throughput-distribution-across-partitions"></a>Determinar a distribuição de taxa de transferência entre partições

Ter uma boa cardinalidade de suas chaves de partição é essencial para qualquer aplicativo escalonável. Para determinar a distribuição de taxa de transferência de qualquer coleção particionada dividida por partições, navegue até a **folha Métricas** no [Portal do Azure](https://portal.azure.com). Na guia **Taxa de transferência**, a divisão do armazenamento é mostrada no gráfico **Máx. de RUs/segundo consumidas para cada partição física**. O gráfico a seguir ilustra um exemplo de uma distribuição de dados ruim, conforme evidenciado pela partição distorcida na extrema esquerda. 

![Partição única apresentando uso intenso às 15h05](media/use-metrics/metrics-17.png)

Uma distribuição desigual de taxa de transferência pode causar partições *quentes*, que por sua vez podem resultar em solicitações limitadas e podem exigir reparticionamento. Para obter mais informações sobre o particionamento no Azure Cosmos DB, consulte [Partição e escala no Azure Cosmos DB](./partition-data.md).

## <a name="determining-the-storage-distribution-across-partitions"></a>Determinando a distribuição de armazenamento entre partições

Ter uma boa cardinalidade em sua partição é essencial para qualquer aplicativo escalonável. Para determinar a distribuição de taxa de transferência de qualquer coleção particionada dividida por partições, navegue até a folha Métricas no [Portal do Azure](https://portal.azure.com). Na guia Taxa de transferência, a divisão do armazenamento é mostrada no gráfico Máx. de RUs/segundo consumidas para cada partição física. O gráfico a seguir ilustra uma distribuição de dados ruim, conforme evidenciado pela partição distorcida na extrema esquerda. 

![Exemplo de distribuição de dados ruim](media/use-metrics/metrics-07.png)

Você pode determinar a causa raiz ao identificar qual chave de partição está distorcendo a distribuição, clicando na partição no gráfico. 

![A chave de partição está distorcendo a distribuição](media/use-metrics/metrics-05.png)

Depois de identificar qual chave de partição está causando a distorção na distribuição, você precisará reparticionar sua coleção com uma chave de partição mais distribuída. Para obter mais informações sobre o particionamento no Azure Cosmos DB, consulte [Partição e escala no Azure Cosmos DB](./partition-data.md).

## <a name="comparing-data-size-against-index-size"></a>Comparando o tamanho dos dados em relação a tamanho do índice

No Azure Cosmos DB, o armazenamento consumido total é a combinação do Tamanho dos dados e do Tamanho do índice. Normalmente, o tamanho do índice é uma fração do tamanho dos dados. Na folha Métricas no [Portal do Azure](https://portal.azure.com), a guia Armazenamento apresenta a divisão do consumo de armazenamento com base nos dados e no índice. Imagem (talvez) Alternativamente, do SDK, você pode encontrar o uso de armazenamento atual por meio de uma leitura de coleção.
```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll")); 
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
``` 
Se você quiser conservar espaço de índice, você poderá ajustar a [política de indexação](./indexing-policies.md).

## <a name="debugging-why-queries-are-running-slow"></a>Depurar porque as consultas estão executando com lentidão

Nos SDKs de API do DocumentDB, o Azure Cosmos DB fornece estatísticas de execução de consulta. 

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
 “SELECT * FROM c WHERE c.city = ‘Seattle’”, 
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

*QueryMetrics* fornece detalhes sobre quanto tempo levou a execução de cada componente da consulta. A causa mais comum de consultas de execução longa são varreduras (a consulta não pôde aproveitar os índices), o que pode ser resolvido com uma condição de filtro melhor.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como monitorar e depurar problemas usando as métricas fornecidas no Portal do Azure, você talvez queira saber mais sobre como melhorar o desempenho do banco de dados, lendo os seguintes artigos:

* [Teste de desempenho e escala com o Azure Cosmos DB](performance-testing.md)
* [Dicas de desempenho para o Azure Cosmos DB](performance-tips.md)
