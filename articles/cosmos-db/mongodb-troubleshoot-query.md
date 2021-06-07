---
title: Solucionar problemas de consulta ao usar a API de Azure Cosmos DB para MongoDB
description: Saiba como identificar, diagnosticar e solucionar problemas de API do Azure Cosmos DB para problemas de consulta do MongoDB.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.subservice: cosmosdb-mongo
ms.date: 03/02/2021
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 5302cb7bb3f4683d200f6f9ea106991bb934fc17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101659895"
---
# <a name="troubleshoot-query-issues-when-using-the-azure-cosmos-db-api-for-mongodb"></a>Solucionar problemas de consulta ao usar a API de Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Este artigo percorre uma abordagem geral recomendada para a solução de problemas de consultas no Azure Cosmos DB. Embora você não deva considerar as etapas descritas neste artigo uma defesa completa contra possíveis problemas de consulta, incluímos as dicas de desempenho mais comuns aqui. Você deve usar este artigo como um local de início para solucionar problemas de consultas lentas ou dispendiosas na API do Azure Cosmos DB para MongoDB. Se você estiver usando a API do Azure Cosmos DB Core (SQL), consulte o artigo [Guia de solução de problemas da API do SQL](troubleshoot-query-performance.md) .

As otimizações de consulta no Azure Cosmos DB são amplamente categorizadas da seguinte maneira:

- Otimizações que reduzem o preço de RU (unidade de solicitação) da consulta
- Otimizações que apenas reduzem a latência

Se você reduzir a carga de RU de uma consulta, normalmente diminuirá a latência também.

Este artigo fornece exemplos que você pode recriar usando o conjunto de [nutrição](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json).

> [!NOTE] 
> Este artigo pressupõe que você esteja usando a API do Azure Cosmos DB para contas do MongoDB com a versão 3,6 e superior. Algumas consultas que executam insatisfatoriamente na versão 3,2 têm melhorias significativas nas versões 3.6 +. Atualize para a versão 3,6 ao arquivar uma [solicitação de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="use-explain-command-to-get-metrics"></a>Use $explain comando para obter métricas

Quando você otimiza uma consulta no Azure Cosmos DB, a primeira etapa é sempre [obter a cobrança de ru](find-request-unit-charge-mongodb.md) para sua consulta. Como uma diretriz aproximada, você deve explorar maneiras de diminuir a carga de RU para consultas com encargos maiores que 50 RUs. 

Além de obter a cobrança de RU, você deve usar o `$explain` comando para obter as métricas de uso de consulta e índice. Aqui está um exemplo que executa uma consulta e usa o `$explain` comando para mostrar as métricas de uso de consulta e índice:

**$explain comando:**

```
db.coll.find({foodGroup: "Baby Foods"}).explain({"executionStatistics": true })
```

**Saída:**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 905.2888,
            "timeExclusiveMS" : 905.2888,
            "in" : 362,
            "out" : 362,
            "details" : {
                "database" : "db-test",
                "collection" : "collection-test",
                "query" : {
                    "foodGroup" : {
                        "$eq" : "Baby Foods"
                    }
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "e68e6bdd-5e89-4ec5-b053-3dbbc2428140",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 788.5867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 362,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 362,
                    "outputDocumentSizeBytes" : 2553535,
                    "indexHitRatio" : 0.0016802042237178,
                    "totalQueryExecutionTimeMS" : 777.72,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.19,
                        "logicalPlanBuildTimeMS" : 0.14,
                        "physicalPlanBuildTimeMS" : 0.09,
                        "queryOptimizationTimeMS" : 0.03
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 687.22,
                    "vmExecutionTimeMS" : 774.09,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 37.45,
                        "systemFunctionExecutionTimeMS" : 10.82,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 49.42
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

A `$explain` saída do comando é demorada e tem informações detalhadas sobre a execução da consulta. Em geral, no entanto, há algumas seções em que você deve se concentrar ao otimizar o desempenho da consulta:

| Métrica | Descrição | 
| ------ | ----------- |
| `timeInclusiveMS` | Latência de consulta de back-end |
| `pathsIndexed` | Mostra os índices usados pela consulta | 
| `pathsNotIndexed` | Mostra os índices que a consulta poderia ter usado, se disponível | 
| `shardInformation` | Resumo do desempenho da consulta para uma [partição física](./partitioning-overview.md#physical-partitions) específica | 
| `retrievedDocumentCount` | Número de documentos carregados pelo mecanismo de consulta | 
| `outputDocumentCount` | Número de documentos retornados nos resultados da consulta | 
| `estimatedDelayFromRateLimitingInMilliseconds` | Latência de consulta adicional estimada devido à limitação de taxa | 

Depois de obter as métricas de consulta, compare o `retrievedDocumentCount` com o `outputDocumentCount` para sua consulta. Use essa comparação para identificar as seções relevantes que serão examinadas neste artigo. O `retrievedDocumentCount`  é o número de documentos que o mecanismo de consulta precisa carregar. O `outputDocumentCount` é o número de documentos que foram necessários para os resultados da consulta. Se o `retrievedDocumentCount`  for significativamente maior do que o `outputDocumentCount` , havia pelo menos uma parte da consulta que não pôde usar um índice e que precisasse fazer uma verificação.

Veja as seções a seguir para entender as otimizações de consulta relevantes para seu cenário.

### <a name="querys-ru-charge-is-too-high"></a>O preço de RU da consulta é muito alto

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>A Contagem de Documentos Recuperados é significativamente maior que a Contagem de Documentos de Saída

- [Inclua os índices necessários.](#include-necessary-indexes)

- [Entenda quais operações de agregação usam o índice.](#understand-which-aggregation-operations-use-the-index)

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>A Contagem de Documentos Recuperados é aproximadamente igual à Contagem de Documentos de Saída

- [Minimizar consultas entre partições.](#minimize-cross-partition-queries)

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>O preço de RU da consulta é aceitável, mas a latência ainda é muito alta

- [Aprimorar a proximidade.](#improve-proximity)

- [Aumentar a taxa de transferência provisionada.](#increase-provisioned-throughput)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Consultas em que a contagem de documentos recuperados excede a contagem de documentos de saída

 O `retrievedDocumentCount` é o número de documentos que o mecanismo de consulta precisa carregar. O `outputDocumentCount` é o número de documentos retornados pela consulta. Se o `retrievedDocumentCount` for significativamente maior do que o `outputDocumentCount` , havia pelo menos uma parte da consulta que não pôde usar um índice e que precisasse fazer uma verificação.

Veja um exemplo de consulta de verificação que não foi totalmente servida pelo índice:

**$explain comando:**

```
db.coll.find(
  {
    $and : [
            { "foodGroup" : "Cereal Grains and Pasta"}, 
            { "description" : "Oat bran, cooked"}
        ]
  }
).explain({"executionStatistics": true })
```

**Saída:**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 436.5716,
            "timeExclusiveMS" : 436.5716,
            "in" : 1,
            "out" : 1,
            "details" : {
                "database" : "db-test",
                "collection" : "indexing-test",
                "query" : {
                    "$and" : [ 
                        {
                            "foodGroup" : {
                                "$eq" : "Cereal Grains and Pasta"
                            }
                        }, 
                        {
                            "description" : {
                                "$eq" : "Oat bran, cooked"
                            }
                        }
                    ]
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "13a5977e-a10a-4329-b68e-87e4f0081cac",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 435.4867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 1,
                    "outputDocumentSizeBytes" : 6064,
                    "indexHitRatio" : 0.0,
                    "totalQueryExecutionTimeMS" : 433.64,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.12,
                        "logicalPlanBuildTimeMS" : 0.09,
                        "physicalPlanBuildTimeMS" : 0.1,
                        "queryOptimizationTimeMS" : 0.02
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 387.44,
                    "vmExecutionTimeMS" : 432.93,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 45.36,
                        "systemFunctionExecutionTimeMS" : 16.86,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 0.13
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

O `retrievedDocumentCount` (8618) é significativamente maior que o `outputDocumentCount` (1), implicando que essa consulta exigia uma verificação de documento. 

### <a name="include-necessary-indexes"></a>Incluir índices necessários

Você deve verificar a `pathsNotIndexed` matriz e adicionar esses índices. Neste exemplo, os caminhos `foodGroup` e `description` devem ser indexados.

```json
"pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ]
```

As práticas recomendadas de indexação na API do Azure Cosmos DB para MongoDB são diferentes do MongoDB. Na API do Azure Cosmos DB para MongoDB, os índices compostos são usados apenas em consultas que precisam classificar com eficiência por várias propriedades. Se você tiver consultas com filtros em várias propriedades, deverá criar índices de campo único para cada uma dessas propriedades. Os predicados de consulta podem usar vários índices de campo único.

Os [índices curinga](mongodb-indexing.md#wildcard-indexes) podem simplificar a indexação. Ao contrário do MongoDB, os índices curinga podem dar suporte a vários campos em predicados de consulta. Não haverá diferença no desempenho da consulta se você usar um único índice curinga em vez de criar um índice separado para cada propriedade. Adicionar um índice curinga para todas as propriedades é a maneira mais fácil de otimizar todas as suas consultas.

Você pode adicionar novos índices a qualquer momento, sem nenhum efeito na disponibilidade de gravação ou leitura. Você pode [acompanhar o progresso da transformação do índice](./how-to-manage-indexing-policy.md#dotnet-sdk).

### <a name="understand-which-aggregation-operations-use-the-index"></a>Entender quais operações de agregação usam o índice

Na maioria dos casos, as operações de agregação na API do Azure Cosmos DB para o MongoDB usarão os índices parcialmente. Normalmente, o mecanismo de consulta aplicará primeiro os filtros de igualdade e de intervalo e usará índices. Depois de aplicar esses filtros, o mecanismo de consulta poderá avaliar filtros adicionais e recorrer ao carregamento de documentos restantes para computar a agregação, se necessário. 

Veja um exemplo:

```
db.coll.aggregate( [
   { $match: { foodGroup: 'Fruits and Fruit Juices' } },
   {
     $group: {
        _id: "$foodGroup",
        total: { $max: "$version" }
     }
   }
] )
```

Nesse caso, os índices podem otimizar o `$match` estágio. A adição de um índice para `foodGroup` melhorará significativamente o desempenho da consulta. Como no MongoDB, você deve posicionar `$match` o mais cedo possível no pipeline de agregação possível para maximizar o uso de índices.

Na API do Azure Cosmos DB para o MongoDB, os índices não são usados para a agregação real, que nesse caso é `$max` . A adição de um índice em `version` não melhorará o desempenho da consulta.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Consultas em que a contagem de documentos recuperados é igual à contagem de documentos de saída

Se o `retrievedDocumentCount` for aproximadamente igual ao `outputDocumentCount` , o mecanismo de consulta não terá que verificar muitos documentos desnecessários.

### <a name="minimize-cross-partition-queries"></a>Minimizar consultas entre partições

O Azure Cosmos DB usa [particionamento](partitioning-overview.md) para escalar contêineres individuais à medida que as necessidades de armazenamento de dados e da Unidade de Solicitação aumentam. Cada partição física tem um índice separado e independente. Se a consulta tiver um filtro de igualdade que corresponda à chave de partição do contêiner, você precisará verificar apenas o índice da partição relevante. Essa otimização reduz o número total de RUs que a consulta requer. [Saiba mais sobre as diferenças entre consultas em partição e consultas entre partições](how-to-query-container.md).

Se você tiver um grande número de RUs provisionados (mais de 30.000) ou uma grande quantidade de dados armazenados (mais de aproximadamente 100 GB), provavelmente terá um contêiner grande o suficiente para ver uma redução significativa nos preços de RU de consulta. 

Você pode verificar a `shardInformation` matriz para entender as métricas de consulta para cada partição física individual. O número de `shardKeyRangeId` valores exclusivos é o número de partições físicas em que a consulta precisava ser executada. Neste exemplo, a consulta foi executada em quatro partições físicas. É importante entender que a execução é completamente independente da utilização do índice. Em outras palavras, as consultas entre partições ainda podem usar índices.

```json
  "shardInformation" : [ 
                    {
                        "activityId" : "42f670a8-a201-4c58-8023-363ac18d9e18",
                        "shardKeyRangeId" : "5",
                        "durationMS" : 24.3859,
                        "preemptions" : 1,
                        "outputDocumentCount" : 463,
                        "retrievedDocumentCount" : 463
                    }, 
                    {
                        "activityId" : "a8bf762a-37b9-4c07-8ed4-ae49961373c0",
                        "shardKeyRangeId" : "2",
                        "durationMS" : 35.8328,
                        "preemptions" : 1,
                        "outputDocumentCount" : 905,
                        "retrievedDocumentCount" : 905
                    }, 
                    {
                        "activityId" : "3754e36b-4258-49a6-8d4d-010555628395",
                        "shardKeyRangeId" : "1",
                        "durationMS" : 67.3969,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1479,
                        "retrievedDocumentCount" : 1479
                    }, 
                    {
                        "activityId" : "a69a44ee-db97-4fe9-b489-3791f3d52878",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 185.1523,
                        "preemptions" : 1,
                        "outputDocumentCount" : 867,
                        "retrievedDocumentCount" : 867
                    }
                ]
```

## <a name="optimizations-that-reduce-query-latency"></a>Otimizações que reduzem a latência da consulta

Em muitos casos, o preço de RU pode ser aceitável quando a latência de consulta ainda é muito alta. As seções a seguir fornecem uma visão geral das dicas para reduzir a latência da consulta. Se você executar a mesma consulta várias vezes no mesmo conjunto de mesmos, ele normalmente terá a mesma cobrança de RU a cada vez. Mas a latência de consulta pode variar entre as execuções de consulta.

### <a name="improve-proximity"></a>Aprimora a proximidade

As consultas executadas de uma região diferente da conta do Azure Cosmos DB terão uma latência maior do que se fossem executadas dentro da mesma região. Por exemplo, se você estiver executando código em seu computador desktop, deverá esperar que a latência seja de dezenas ou centenas de milissegundos maior (ou mais) do que se a consulta vier de uma máquina virtual na mesma região do Azure que o Azure Cosmos DB. É simples [distribuir dados globalmente no Azure Cosmos DB](tutorial-global-distribution-mongodb.md) para garantir que você possa trazer seus dados mais perto do seu aplicativo.

### <a name="increase-provisioned-throughput"></a>Aumentar a taxa de transferência provisionada

No Azure Cosmos DB, sua taxa de transferência provisionada é medida em RUs (unidades de solicitação). Imagine que você tenha uma consulta que consome 5 RUs de taxa de transferência. Por exemplo, se você provisionar 1.000 RUs, poderá executar essa consulta 200 vezes por segundo. Se você tentou executar a consulta quando não havia taxa de transferência suficiente disponível, Azure Cosmos DB taxa limitará as solicitações. A API do Azure Cosmos DB para o MongoDB repetirá essa consulta automaticamente depois de aguardar um curto período de tempo. As solicitações restritas demoram mais, portanto, aumentar a taxa de transferência provisionada pode aprimorar a latência da consulta.

O valor `estimatedDelayFromRateLimitingInMilliseconds` dá uma noção dos benefícios potenciais de latência se você aumentar a taxa de transferência.

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de desempenho de consulta (API do SQL)](troubleshoot-query-performance.md)
* [Gerenciar a indexação na API do Azure Cosmos DB para MongoDB](mongodb-indexing.md)
