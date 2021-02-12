---
title: Monitore e depure com métricas no Azure Cosmos DB
description: Use métricas no Azure Cosmos DB para depurar problemas comuns e monitorar o banco de dados.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/22/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: b0760b86012504ea86e4a0cde36ae878e8ff3b26
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685730"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Monitore e depure com métricas no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Cosmos DB fornece métricas de taxa de transferência, armazenamento, consistência, disponibilidade e latência. O portal do Microsoft Azure fornece uma visualização agregada dessas métricas. Você também pode exibir métricas do Azure Cosmos DB pela API do Azure Monitor. Os valores de dimensão para as métricas, como o nome do contêiner, não diferenciam maiúsculas de minúsculas. Portanto, você precisa usar a comparação de maiúsculas e minúsculas ao fazer comparações de cadeias de caracteres nesses valores de dimensão. Para saber mais sobre como exibir as métricas do Azure monitor, consulte o artigo [obter métricas de Azure monitor](./monitor-cosmos-db.md) .

Este artigo explica como casos de uso comuns e como as métricas do Azure Cosmos DB podem ser usadas para analisar e depurar esses problemas. As métricas são coletadas a cada cinco minutos e são mantidas por sete dias.

## <a name="view-metrics-from-azure-portal"></a>Exibir métricas de portal do Azure

1. Entrar [portal do Azure](https://portal.azure.com/)

1. Abra o painel **métricas** . Por padrão, o painel de métricas mostra as métricas de armazenamento, índice e unidades de solicitação para todos os bancos de dados em sua conta do Azure Cosmos. Você pode filtrar essas métricas por banco de dados, contêiner ou região. Você também pode filtrar as métricas em uma granularidade de tempo específica. Mais detalhes sobre as métricas de taxa de transferência, armazenamento, disponibilidade, latência e consistência são fornecidos em guias separadas. 

   :::image type="content" source="./media/use-metrics/performance-metrics.png" alt-text="Cosmos DB métricas de desempenho no portal do Azure":::

As métricas a seguir estão disponíveis no painel de **métricas** : 

* **Métricas de taxa de transferência** – essa métrica mostra o número de solicitações consumidas ou com falha (429 de código de resposta) porque a taxa de transferência ou a capacidade de armazenamento provisionada para o contêiner foi excedida.

* **Métricas de armazenamento** -essa métrica mostra o tamanho do uso de dados e índice.

* **Métricas de disponibilidade** – essa métrica mostra a porcentagem de solicitações bem-sucedidas no total de solicitações por hora. A taxa de sucesso é definida pelos SLAs de Azure Cosmos DB.

* **Métricas de latência** – essa métrica mostra a latência de leitura e gravação observada por Azure Cosmos DB na região em que sua conta está operando. Você pode visualizar a latência entre regiões para uma conta replicada geograficamente. Essa métrica não representa a latência de solicitação de ponta a ponta.

* **Métricas de consistência** – essa métrica mostra como a eventualidade é a consistência do modelo de consistência escolhido. Para contas de várias regiões, essa métrica também mostra a latência de replicação entre as regiões que você selecionou.

* **Métricas do sistema** – essa métrica mostra quantas solicitações de metadados são atendidas pela partição primária. Ele também ajuda a identificar as solicitações limitadas.

As seções a seguir explicam cenários comuns em que você pode usar Azure Cosmos DB métricas. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Reconhecimento de quantas solicitações estão tendo sucesso ou causando erros

Para começar, vá até o [Portal do Azure](https://portal.azure.com) e navegue até a folha **Métricas**. Na folha, localize o * * número de solicitações que excedeu a capacidade por gráfico de 1 minuto. Este gráfico mostra um total de solicitações minuto a minuto segmentadas pelo código de status. Para obter mais informações sobre códigos de status HTTP, consulte [códigos de status HTTP para Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

O código de status de erro mais comum é 429 (limitação/limitação da taxa). Esse erro significa que as solicitações para o Azure Cosmos DB são mais do que a taxa de transferência provisionada. A solução mais comum para esse problema é [expandir as RUs](./set-throughput.md) para determinada coleção.

:::image type="content" source="media/use-metrics/metrics-12.png" alt-text="Número de solicitações por minuto":::

## <a name="determine-the-throughput-distribution-across-partitions"></a>Determine a distribuição da taxa de transferência entre as partições

Ter uma boa cardinalidade de suas chaves de partição é essencial para qualquer aplicativo escalonável. Para determinar a distribuição da produtividade de qualquer contêiner particionado dividida por partições, navegue até a **folha Métricas** no [portal do Azure](https://portal.azure.com). Na guia **Taxa de transferência**, a divisão do armazenamento é mostrada no gráfico **Máx. de RUs/segundo consumidas para cada partição física**. O gráfico a seguir ilustra um exemplo de uma má distribuição de dados, conforme mostrado pela partição distorcida na extrema esquerda.

:::image type="content" source="media/use-metrics/metrics-17.png" alt-text="Partição única vendo uso intenso":::

Uma distribuição desigual de taxa de transferência pode causar partições *quentes*, que por sua vez podem resultar em solicitações limitadas e podem exigir reparticionamento. Para obter mais informações sobre o particionamento no Azure Cosmos DB, consulte [Partição e escala no Azure Cosmos DB](./partitioning-overview.md).

## <a name="determine-the-storage-distribution-across-partitions"></a>Determinar a distribuição de armazenamento nas partições

Ter uma boa cardinalidade em sua partição é essencial para qualquer aplicativo escalonável. Para determinar a distribuição de armazenamento de qualquer contêiner particionado dividida por partições, acesse a folha Métricas no [portal do Azure](https://portal.azure.com). Na guia Armazenamento, a divisão de armazenamento é mostrada no armazenamento Dados + índice consumido pelo gráfico de chaves de partição superior. O gráfico a seguir ilustra uma distribuição ruim de armazenamento de dados, conforme mostrado pela partição distorcida na extrema esquerda.

:::image type="content" source="media/use-metrics/metrics-07.png" alt-text="Exemplo de distribuição de dados ruim":::

Você pode determinar a causa raiz ao identificar qual chave de partição está distorcendo a distribuição, clicando na partição no gráfico.

:::image type="content" source="media/use-metrics/metrics-05.png" alt-text="A chave de partição está distorcendo a distribuição":::

Depois de identificar qual chave de partição está causando a distorção na distribuição, você precisará reparticionar o contêiner com uma chave de partição mais distribuída. Para obter mais informações sobre o particionamento no Azure Cosmos DB, consulte [Partição e escala no Azure Cosmos DB](./partitioning-overview.md).

## <a name="compare-data-size-against-index-size"></a>Compare o tamanho de dados em relação ao tamanho do índice

No Azure Cosmos DB, o armazenamento consumido total é a combinação do Tamanho dos dados e do Tamanho do índice. Normalmente, o tamanho do índice é uma fração do tamanho dos dados. Para saber mais, confira o artigo [tamanho do índice](index-policy.md#index-size) . Na folha Métricas no [Portal do Azure](https://portal.azure.com), a guia Armazenamento apresenta a divisão do consumo de armazenamento com base nos dados e no índice.

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

* Para saber mais sobre como exibir as métricas do Azure monitor, consulte o artigo [obter métricas de Azure monitor](./monitor-cosmos-db.md) . 
* [Teste de desempenho e escala com o Azure Cosmos DB](performance-testing.md)
* [Dicas de desempenho para o Azure Cosmos DB](performance-tips.md)