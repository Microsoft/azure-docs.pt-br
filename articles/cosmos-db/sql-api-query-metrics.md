---
title: Métricas de consulta SQL para API do SQL do Azure Cosmos DB
description: Saiba mais sobre como instrumentar e depurar o desempenho de consultas SQL de solicitações do Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: d61d3d00de5b46f7dad44625509eabe6836ca7cf
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447267"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Ajustando o desempenho de consulta com o Azure Cosmos DB

O Azure Cosmos DB fornece um [API do SQL para consultar dados](how-to-sql-query.md), sem a necessidade de esquema ou índices secundários. Este artigo fornece as seguintes informações para desenvolvedores:

* Detalhes de alto nível sobre como funciona a execução da consulta SQL do Azure Cosmos DB
* Obter detalhes sobre cabeçalhos de solicitação e resposta de consulta e opções do SDK do cliente
* Algumas dicas e práticas recomendadas para desempenho de consulta
* Exemplos de como utilizar as estatísticas de execução do SQL para depurar o desempenho da consulta

## <a name="about-sql-query-execution"></a>Sobre execução de consulta SQL

No Azure Cosmos DB, você pode armazenar dados em contêineres, que podem aumentar a qualquer [tamanho do armazenamento ou taxa de transferência da solicitação](partition-data.md). O Azure Cosmos DB dimensiona perfeitamente os dados em partições físicas nos bastidores para lidar com o crescimento de dados ou aumentar a taxa de transferência fornecida. Você pode emitir consultas SQL para qualquer contêiner usando a API REST ou um com suporte [SDKs do SQL](sql-api-sdk-dotnet.md).

Uma visão geral de particionamento: definir uma chave de partição como "city", que determina como os dados são divididos em partições físicas. Dados que pertencem a uma chave de partição única (por exemplo, "city" = = "Seattle") são armazenados em uma partição física, mas normalmente uma única partição física tem várias chaves de partição. Quando uma partição atinge seu tamanho de armazenamento, o serviço divide perfeitamente a partição em duas novas partições e divide a chave de partição uniformemente entre essas partições. Como as partições são transitórias, as APIs de usam uma abstração de um "intervalo de chave de partição", que indica os intervalos de hashes de chave de partição. 

Quando você emitir uma consulta ao Azure Cosmos DB, o SDK executa essas etapas lógicas:

* Analise a consulta SQL para determinar o plano de execução de consulta. 
* Se a consulta inclui um filtro com a chave de partição, como `SELECT * FROM c WHERE c.city = "Seattle"`, ela é roteada para uma única partição. Se a consulta não tem um filtro na chave de partição, em seguida, ela é executada em todas as partições e os resultados são mesclados do lado do cliente.
* A consulta é executada dentro de cada partição em série ou em paralelo, com base na configuração do cliente. Dentro de cada partição, a consulta pode fazer uma ou mais viagens de ida e dependendo da complexidade da consulta, do tamanho da página configurada e da taxa de transferência provisionada da coleção. Cada execução retorna o número de [unidades de solicitação](request-units.md) consumido pela execução da consulta e, opcionalmente, as estatísticas de execução de consulta. 
* O SDK executa um resumo dos resultados da consulta em partições. Por exemplo, se a consulta envolver uma ORDER BY em partições, então os resultados de partições individuais são classificados de mesclagem para retornar os resultados em ordem classificada globalmente. Se a consulta é uma agregação como `COUNT`, as contagens de partições individuais são somadas para produzir a contagem total.

Os SDKs fornecem várias opções para a execução da consulta. Por exemplo, no .NET essas opções estão disponíveis na classe `FeedOptions`. A tabela a seguir descreve essas opções e como elas afetam o tempo de execução de consulta. 

| Opção | DESCRIÇÃO |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Deve ser definido como true para qualquer consulta que requer ser executada em mais de uma partição. Este é um sinalizador explícito para que você possa fazer as compensações de desempenho consciente durante o tempo de desenvolvimento. |
| `EnableScanInQuery` | Deve ser definido como true se você rejeitou a indexação, mas deseja executar a consulta por meio de uma verificação mesmo assim. Só aplicável se a indexação para o caminho de filtro solicitado estiver desabilitada. | 
| `MaxItemCount` | O número máximo de itens a serem retornados por viagem de ida e volta ao servidor. Definindo como -1, você pode deixar o servidor gerenciar o número de itens. Ou, você pode diminuir esse valor para recuperar um pequeno número de itens por viagem de ida e volta. 
| `MaxBufferedItemCount` | Isso é uma opção do lado do cliente e usado para limitar o consumo de memória ao executar entre partições ORDER BY. Um valor mais alto ajuda a reduzir a latência de classificação entre partições. |
| `MaxDegreeOfParallelism` | Obtém ou define o número de operações simultâneas executados do lado do cliente durante a execução de consulta paralela no serviço de banco de dados do Azure Cosmos DB. Um valor de propriedade positivo limita o número de operações simultâneas para o valor do conjunto. Se ele for definido como menor que 0, o sistema decide automaticamente o número de operações simultâneas para executar. |
| `PopulateQueryMetrics` | Habilita o log detalhado de estatísticas de tempo gasto em várias fases de execução da consulta como tempo de compilação, o tempo de loop de índice e o documento de tempo de carregamento. Você pode compartilhar a saída de estatísticas de consulta com Azure Support para diagnosticar problemas de desempenho de consulta. |
| `RequestContinuation` | Você pode retomar a execução de consulta, passando o token de continuação opaco retornado por qualquer consulta. O token de continuação encapsula todos os estados necessários para a execução de consulta. |
| `ResponseContinuationTokenLimitInKb` | Você pode limitar o tamanho máximo do token de continuação retornado pelo servidor. Talvez seja necessário definir essa opção se seu host de aplicativo tem limites de tamanho do cabeçalho de resposta. Essa configuração pode aumentar a duração geral e o RUs consumidos para a consulta.  |

Por exemplo, vamos usar uma consulta de exemplo na chave de partição solicitada em uma coleção com `/city` como a chave de partição e provisionada com 100.000 RU/s de taxa de transferência. Solicitar esta consulta usando `CreateDocumentQuery<T>` no .NET com o seguinte:

```cs
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
```

O snippet de código do SDK mostrado acima, corresponde a seguinte solicitação de API REST:

```
POST https://arramacquerymetrics-westus.documents.azure.com/dbs/db/colls/sample/docs HTTP/1.1
x-ms-continuation: 
x-ms-documentdb-isquery: True
x-ms-max-item-count: -1
x-ms-documentdb-query-enablecrosspartition: True
x-ms-documentdb-query-parallelizecrosspartitionquery: True
x-ms-documentdb-query-iscontinuationexpected: True
x-ms-documentdb-populatequerymetrics: True
x-ms-date: Tue, 27 Jun 2017 21:52:18 GMT
authorization: type%3dmaster%26ver%3d1.0%26sig%3drp1Hi83Y8aVV5V6LzZ6xhtQVXRAMz0WNMnUuvriUv%2b4%3d
x-ms-session-token: 7:8,6:2008,5:8,4:2008,3:8,2:2008,1:8,0:8,9:8,8:4008
Cache-Control: no-cache
x-ms-consistency-level: Session
User-Agent: documentdb-dotnet-sdk/1.14.1 Host/32-bit MicrosoftWindowsNT/6.2.9200.0
x-ms-version: 2017-02-22
Accept: application/json
Content-Type: application/query+json
Host: arramacquerymetrics-westus.documents.azure.com
Content-Length: 52
Expect: 100-continue

{"query":"SELECT * FROM c WHERE c.city = 'Seattle'"}
```

Cada página de execução de consulta corresponde a uma API REST `POST` com o cabeçalho `Accept: application/query+json` e a consulta SQL no corpo. Cada consulta faz com que um ou mais processamentos no servidor com o token `x-ms-continuation` ecoada entre o cliente e servidor para continuar a execução. As opções de configuração no FeedOptions são passadas para o servidor na forma de cabeçalhos de solicitação. Por exemplo, `MaxItemCount` corresponde à `x-ms-max-item-count`. 

A solicitação retorna a resposta a seguir (truncados para legibilidade):

```
HTTP/1.1 200 Ok
Cache-Control: no-store, no-cache
Pragma: no-cache
Transfer-Encoding: chunked
Content-Type: application/json
Server: Microsoft-HTTPAPI/2.0
Strict-Transport-Security: max-age=31536000
x-ms-last-state-change-utc: Tue, 27 Jun 2017 21:01:57.561 GMT
x-ms-resource-quota: documentSize=10240;documentsSize=10485760;documentsCount=-1;collectionSize=10485760;
x-ms-resource-usage: documentSize=1;documentsSize=884;documentsCount=2000;collectionSize=1408;
x-ms-item-count: 2000
x-ms-schemaversion: 1.3
x-ms-alt-content-path: dbs/db/colls/sample
x-ms-content-path: +9kEANVq0wA=
x-ms-xp-role: 1
x-ms-documentdb-query-metrics: totalExecutionTimeInMs=33.67;queryCompileTimeInMs=0.06;queryLogicalPlanBuildTimeInMs=0.02;queryPhysicalPlanBuildTimeInMs=0.10;queryOptimizationTimeInMs=0.00;VMExecutionTimeInMs=32.56;indexLookupTimeInMs=0.36;documentLoadTimeInMs=9.58;systemFunctionExecuteTimeInMs=0.00;userFunctionExecuteTimeInMs=0.00;retrievedDocumentCount=2000;retrievedDocumentSize=1125600;outputDocumentCount=2000;writeOutputTimeInMs=18.10;indexUtilizationRatio=1.00
x-ms-request-charge: 604.42
x-ms-serviceversion: version=1.14.34.4
x-ms-activity-id: 0df8b5f6-83b9-4493-abda-cce6d0f91486
x-ms-session-token: 2:2008
x-ms-gatewayversion: version=1.14.33.2
Date: Tue, 27 Jun 2017 21:59:49 GMT
```

Os cabeçalhos de resposta de chave retornados da consulta incluem o seguinte:

| Opção | DESCRIÇÃO |
| ------ | ----------- |
| `x-ms-item-count` | O número de itens retornados na resposta. Isso é dependente de fornecido `x-ms-max-item-count`, o número de itens que podem ser encaixados em tempo de execução de consulta, a taxa de transferência fornecida e o tamanho máximo de resposta da carga. |  
| `x-ms-continuation:` | O token de continuação para retomar a execução da consulta, se houver mais resultados disponíveis. | 
| `x-ms-documentdb-query-metrics` | As estatísticas de consulta para a execução. Isso é uma cadeia de caracteres delimitada que contém as estatísticas de tempo gasto em várias fases de execução de consulta. Retornado se `x-ms-documentdb-populatequerymetrics` é definido como `True`. | 
| `x-ms-request-charge` | O número de [unidades de solicitação](request-units.md) consumidos pela consulta. | 

Para obter detalhes sobre as opções e os cabeçalhos de solicitação da API REST, consulte [Consultar recursos usando a API REST](https://docs.microsoft.com/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Práticas recomendadas para desempenho de consulta
A seguir estão os fatores mais comuns que afetam o desempenho de consulta do Azure Cosmos DB. Podemos obter detalhes de cada um desses tópicos neste artigo.

| Fator | Dica | 
| ------ | -----| 
| Taxa de transferência provisionada | Medir RU por consulta e certificar-se de que você tem a taxa de transferência provisionada necessária para suas consultas. | 
| Particionamento e chaves de partição | Favorecer consultas com o valor de chave de partição na cláusula de filtro de baixa latência. |
| Opções de consulta e de SDK | Siga as práticas recomendadas do SDK com conectividade direta e ajuste as opções de execução de consulta do lado do cliente. |
| Latência da rede | Conta para a rede sobrecarga em medidas e use APIs de hospedagem múltipla para ler a região mais próxima. |
| Política de indexação | Certifique-se de que você tem política/caminhos de indexação necessários para a consulta. |
| Métricas de execução da consulta | Analise as métricas de execução de consulta para identificar potenciais regravações de formas de consulta e dados.  |

### <a name="provisioned-throughput"></a>Taxa de transferência provisionada
No Cosmos DB, você cria contêineres de dados, cada um com a taxa de transferência reservada expressa em RUs (unidades de solicitação) por segundo. Uma leitura de um documento de 1 KB é 1 RU e cada operação (incluindo consultas) é normalizada para um número fixo de RUs com base em sua complexidade. Por exemplo, se você tiver 1000 RU/s provisionado para o contêiner, e você tiver uma consulta como `SELECT * FROM c WHERE c.city = 'Seattle'` que consome 5 RUs, em seguida, você pode executar (1000 RU/s) / (RU/consulta 5) = 200 consulta/s tais consultas por segundo. 

Se você enviar mais de 200 consultas/s, o serviço inicia a limitação de taxa de solicitações de entrada acima 200/s. Os SDKs automaticamente lidam com isso, executando uma repetição/retirada, portanto você pode notar uma latência mais alta para essas consultas. Aumentar a taxa de transferência fornecida para o valor necessário melhora a latência da consulta e a taxa de transferência. 

Para saber mais sobre unidades de solicitação, consulte [Unidades de solicitação](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Particionamento e chaves de partição
Com o Azure Cosmos DB, normalmente executa consultas na seguinte ordem de mais rápido mais eficiente para mais lenta/menos eficiente. 

* OBTER em uma chave de partição única e a chave de item
* Consulta com uma cláusula de filtro em uma chave de partição única
* Consulta sem uma cláusula de filtro de igualdade ou um intervalo em qualquer propriedade
* Consulta sem filtros

Consultas que precisam consultar todas as partições que precisam de latência mais alta e podem consumir RUs superior. Como cada partição tem indexação automática em relação a todas as propriedades, a consulta pode ser atendida com eficiência do índice neste caso. Você pode fazer consultas que abrangem partições mais rapidamente, usando as opções de paralelismo.

Para saber mais sobre particionamento e chaves de partição, consulte [Particionamento no Azure Cosmos DB](partition-data.md).

### <a name="sdk-and-query-options"></a>Opções de consulta e de SDK
Consulte [Dicas de Desempenho](performance-tips.md) e [Testes de Desempenho](performance-testing.md) para saber como obter o melhor desempenho do lado do cliente do Azure Cosmos DB. Isso inclui usar os mais recentes SDKs, definir configurações específicas de plataforma como o número padrão de conexões, a frequência da coleta de lixo e usar as opções de conectividade leve como Direct/TCP. 


#### <a name="max-item-count"></a>Contagem máxima de itens
Em consultas, o valor de `MaxItemCount` pode ter um impacto significativo no tempo de consulta de ponta a ponta. Cada viagem de ida e volta para o servidor retornará somente o número de itens em `MaxItemCount` (padrão de 100 itens). A definição para um valor mais alto (-1 é o máximo, e o recomendado) melhora a duração de consulta geral, limitando o número de viagens de ida e volta entre servidor e cliente, especialmente para consultas com grandes conjuntos de resultados.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Grau máximo de paralelismo
Em consultas, ajuste `MaxDegreeOfParallelism` para identificar as melhores configurações para seu aplicativo, especialmente se você executar consultas entre partições (sem um filtro no valor da chave de partição). `MaxDegreeOfParallelism` controla o número máximo de tarefas paralelas, ou seja, o número máximo de partições a serem visitadas paralelamente. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();
```

Vamos supor que
* D = número máximo padrão de tarefas paralelas (= número total do processador no computador cliente)
* P = número máximo especificado pelo usuário de tarefas paralelas
* N = número de partições que precisam ser visitadas para responder a uma consulta

Veja a seguir as implicações de como as consultas paralelas se comportariam para diferentes valores de P.
* (P == 0) => Modo serial
* (P == 1) => Máximo de uma tarefa
* (P > 1) => Mín. (P, N) de tarefas paralelas 
* (P < 1) => Mín. (N, D) de tarefas paralelas

Notas de versão do SDK e detalhes sobre classes e métodos implementados, consulte [SDKs do SQL](sql-api-sdk-dotnet.md)

### <a name="network-latency"></a>Latência da rede
Consulte [distribuição global do Azure Cosmos DB](tutorial-global-distribution-sql-api.md) para configurar a distribuição global e conectar-se à região mais próxima. Latência de rede tem um impacto significativo no desempenho da consulta quando você precisa fazer vários processos ou recuperar um grande conjunto de resultados de consulta. 

A seção sobre métricas de execução de consulta explica como recuperar o tempo de execução do servidor de consultas ( `totalExecutionTimeInMs`), de modo que você possa diferenciar entre o tempo gasto na execução da consulta e o tempo gasto em trânsito de rede.

### <a name="indexing-policy"></a>Política de indexação
Consulte [Configurando a política de indexação](index-policy.md) para indexação de caminhos, tipos e os modos e como elas afetam a execução da consulta. Por padrão, a política de indexação usa a indexação de Hash para cadeias de caracteres, que é eficiente para consultas de igualdade, mas não para consultas de intervalo/ordem através de consultas. Se você precisar de consultas de intervalo para cadeias de caracteres, é recomendável especificar o tipo de índice de intervalo para todas as cadeias de caracteres. 

Por padrão, o Azure Cosmos DB aplicará a indexação automática para todos os dados. Para alto desempenho inserir cenários, considere excluir os caminhos como isso reduzirá o custo de RU para cada operação de inserção. 

## <a name="query-execution-metrics"></a>Métricas de execução da consulta
Você pode obter métricas detalhadas na execução da consulta, passando no cabeçalho `x-ms-documentdb-populatequerymetrics` opcional (`FeedOptions.PopulateQueryMetrics` no SDK do .NET). O valor retornado em `x-ms-documentdb-query-metrics` tem os seguintes pares chave-valor destinados para solução de problemas avançados de execução de consulta. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;

```

| Métrica | Unidade | DESCRIÇÃO | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | milissegundos | Tempo de execução da consulta | 
| `queryCompileTimeInMs` | milissegundos | Tempo de compilação de consulta  | 
| `queryLogicalPlanBuildTimeInMs` | milissegundos | Tempo para criar o plano de consulta lógica | 
| `queryPhysicalPlanBuildTimeInMs` | milissegundos | Tempo para criar o plano de consulta física | 
| `queryOptimizationTimeInMs` | milissegundos | Tempo gasto na otimização de consulta | 
| `VMExecutionTimeInMs` | milissegundos | Tempo gasto em tempo de execução de consulta | 
| `indexLookupTimeInMs` | milissegundos | Tempo gasto na camada física de índice | 
| `documentLoadTimeInMs` | milissegundos | Tempo gasto no carregamento de documentos  | 
| `systemFunctionExecuteTimeInMs` | milissegundos | Tempo total gasto executando funções de sistema (interno) em milissegundos  | 
| `userFunctionExecuteTimeInMs` | milissegundos | Tempo total gasto executando funções definidas pelo usuário (interno) em milissegundos | 
| `retrievedDocumentCount` | count | Número total de documentos recuperados  | 
| `retrievedDocumentSize` | bytes | Tamanho total dos documentos recuperados em bytes  | 
| `outputDocumentCount` | count | Número de documentos de saída | 
| `writeOutputTimeInMs` | milissegundos | Tempo de execução da consulta em milissegundos | 
| `indexUtilizationRatio` | taxa de (< = 1) | Taxa do número de documentos para correspondência com o filtro para o número de documentos carregados  | 

Os SDKs de cliente internamente podem fazer várias operações de consulta para atender a consulta dentro de cada partição. O cliente faz mais de uma chamada por partição se os resultados total excederem `x-ms-max-item-count`, se a consulta exceder a taxa de transferência fornecida para a partição, ou se a carga de consulta atinge o tamanho máximo por página, ou se a consulta atinge o limite de tempo limite alocado de sistema. Cada execução da consulta parcial retorna um `x-ms-documentdb-query-metrics` para a página. 

Aqui estão alguns exemplos de consultas e como interpretar algumas das métricas retornadas da execução da consulta: 

| Consulta | Métrica de exemplo | DESCRIÇÃO | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | O número de documentos recuperados é 100 +1 para coincidir com a cláusula TOP. Tempo de consulta é gasto principalmente `WriteOutputTime` e `DocumentLoadTime` porque é uma verificação. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount agora é mais alto (500+1 para coincidir com a cláusula TOP). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Cerca de 0,9 ms é gasto na IndexLookupTime para uma pesquisa de chave, porque ele é um índice de pesquisa `/N/?`. | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Um pouco mais de tempo (ms 1.7) é gasto em IndexLookupTime por um exame de intervalo, porque ele é um índice de pesquisa `/N/?`. | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Mesmo tempo gasto em `DocumentLoadTime` como consultas anteriores, mas inferior `WriteOutputTime` porque estamos projetando apenas uma propriedade. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Cerca de 213 ms é gasto na `UserDefinedFunctionExecutionTime` executando o UDF em cada valor de `c.N`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Cerca de 0,6 ms é gasto na `IndexLookupTime` em `/Name/?`. A maioria do tempo de execução de consulta (~ 7 ms) em `SystemFunctionExecutionTime`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | A consulta é executada como uma verificação, pois ela usa `LOWER`, e 500 de 2491 documentos recuperados são retornados. |


## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre os operadores de consulta SQL e palavras-chave com suporte, consulte [Consulta SQL](sql-query-getting-started.md). 
* Para saber sobre unidades de solicitação, consulte [Unidades de solicitação](request-units.md).
* Para saber sobre a política de indexação, consulte [Política de indexação](index-policy.md) 


