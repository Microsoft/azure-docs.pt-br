---
title: Obter métricas de execução de & de desempenho de consulta SQL
description: Saiba como recuperar métricas de execução de consulta SQL e perfil de desempenho de consulta SQL de solicitações de Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/17/2019
ms.author: girobins
ms.custom: devx-track-csharp
ms.openlocfilehash: 66aefea441d78303ccd611d9df10eea985d61e7c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097389"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Obter métricas de execução de consulta SQL e analisar o desempenho de consulta usando o SDK do .NET
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo apresenta como criar o perfil do desempenho de consulta do SQL no Azure Cosmos DB. Essa criação de perfil pode ser feita usando `QueryMetrics` o recuperado do SDK do .net e é detalhada aqui. [QueryMetrics](/dotnet/api/microsoft.azure.documents.querymetrics) é um objeto fortemente tipado com informações sobre a execução da consulta de back-end. Essas métricas são documentadas com mais detalhes no artigo [ajustar o desempenho da consulta](./sql-api-query-metrics.md) .

## <a name="set-the-feedoptions-parameter"></a>Definir o parâmetro Feedoptions

Todas as sobrecargas para [DocumentClient. CreateDocumentQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentquery) assumem um parâmetro opcional [feedoptions](/dotnet/api/microsoft.azure.documents.client.feedoptions) . Essa opção é o que permite que a execução da consulta seja ajustada e parametrizada. 

Para coletar as métricas de execução de consulta SQL, você deve definir o parâmetro [PopulateQueryMetrics](/dotnet/api/microsoft.azure.documents.client.feedoptions.populatequerymetrics#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) no [feedoptions](/dotnet/api/microsoft.azure.documents.client.feedoptions) como `true` . Definir `PopulateQueryMetrics` como true fará com que o conterá `FeedResponse` o relevante `QueryMetrics` . 

## <a name="get-query-metrics-with-asdocumentquery"></a>Obter métricas de consulta com AsDocumentQuery ()
O exemplo de código a seguir mostra como recuperar métricas ao usar o método [AsDocumentQuery ()](/dotnet/api/microsoft.azure.documents.linq.documentqueryable.asdocumentquery) :

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>Agregando QueryMetrics

Na seção anterior, observe que havia várias chamadas para o método [ExecuteNextAsync](/dotnet/api/microsoft.azure.documents.linq.idocumentquery-1.executenextasync) . Cada chamada retornou um `FeedResponse` objeto que tem um dicionário de `QueryMetrics` ; um para cada continuação da consulta. O exemplo a seguir mostra como agregar esses `QueryMetrics` usando LINQ:

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>Agrupando métricas de consulta por ID de partição

Você pode agrupar o `QueryMetrics` pela ID da partição. O agrupamento por ID de partição permite que você veja se uma partição específica está causando problemas de desempenho quando comparada com outras pessoas. O exemplo a seguir mostra como agrupar `QueryMetrics` com LINQ:

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>LINQ no DocumentQuery

Você também pode obter o `FeedResponse` de uma consulta LINQ usando o `AsDocumentQuery()` método:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Consultas Dispendiosas

Você pode capturar as unidades de solicitação consumidas por cada consulta para investigar consultas caras ou consultas que consomem alta taxa de transferência. Você pode obter o encargo da solicitação usando a propriedade [RequestCharge](/dotnet/api/microsoft.azure.documents.client.feedresponse-1.requestcharge) em `FeedResponse` . Para saber mais sobre como obter o encargo de solicitação usando o portal do Azure e diferentes SDKs, confira [localizar o artigo encargos de unidade de solicitação](find-request-unit-charge.md) .

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>Obter o tempo de execução da consulta

Ao calcular o tempo necessário para executar uma consulta do lado do cliente, certifique-se de incluir apenas o tempo para chamar o `ExecuteNextAsync` método e não outras partes da sua base de código. Apenas essas chamadas ajudam a calcular quanto tempo a execução da consulta levou, conforme mostrado no exemplo a seguir:

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>Examinar consultas (comumente lentas e caras)

Uma consulta de verificação refere-se a uma consulta que não foi servida pelo índice, devido a quais muitos documentos são carregados antes de retornar o conjunto de resultados.

Veja abaixo um exemplo de uma consulta de verificação:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

O filtro desta consulta usa a função de sistema UPPER, que não é servida a partir do índice. Executar essa consulta em uma coleção grande produziu as seguintes métricas de consulta para a primeira continuação:

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Observe os seguintes valores da saída de métricas de consulta:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Essa consulta carregou 60.951 documentos, que totalizaram 399.998.938 bytes. O carregamento desse número de bytes resulta em alto custo ou encargo de unidade de solicitação. Também leva muito tempo para executar a consulta, o que é claro com a propriedade tempo total gasto:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

O que significa que a consulta levou 4,5 segundos para ser executada (e essa era apenas uma continuação).

Para otimizar esta consulta de exemplo, evite o uso de UPPER no filtro. Em vez disso, quando documentos são criados ou atualizados, os `c.description` valores devem ser inseridos em caracteres maiúsculos. A consulta então se torna: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Esta consulta agora pode ser servida a partir do índice.

Para saber mais sobre como ajustar o desempenho da consulta, consulte o artigo [ajustar o desempenho da consulta](./sql-api-query-metrics.md) .

## <a name="references"></a><a id="References"></a>Referências

- [Especificação do SQL no Azure Cosmos DB](./sql-query-getting-started.md)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Próximas etapas

- [Desempenho de consulta de ajuste](sql-api-query-metrics.md)
- [Visão geral da indexação](index-overview.md)
- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)