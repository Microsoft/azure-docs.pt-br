---
title: Avalie suas consultas com a função de perfil de execução para a API do Gremlin do Azure Cosmos DB
description: Saiba como solucionar problemas e melhorar suas consultas Gremlin usando a etapa de perfil de execução.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 4964f485f5e781b7fe0a0f09486512fe6a5b9035
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592488"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Como usar a etapa de perfil de execução para avaliar suas consultas do Gremlin

Este artigo fornece uma visão geral de como usar a etapa de perfil de execução para bancos de dados de grafo de API do Gremlin do Azure Cosmos DB. Essa etapa fornece as informações relevantes para a solução de problemas e otimizações de consulta e é compatível com qualquer consulta Gremlin que pode ser executada em relação a uma conta de API do Gremlin do Cosmos DB.

Para usar essa etapa, basta acrescentar a `executionProfile()` chamada no final da sua consulta Gremlin de função. **Sua consulta Gremlin será executada** e o resultado da operação retornará um objeto de resposta JSON com o perfil de execução de consulta.

Por exemplo:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Depois de chamar o `executionProfile()` etapa, a resposta será um objeto JSON que inclui a etapa de Gremlin executada, o tempo total necessário e uma matriz dos operadores de tempo de execução do Cosmos DB que a instrução resultou em.

> [!NOTE]
> Essa implementação para o perfil de execução não está definida na especificação do Apache Tinkerpop. Ele é específico à implementação da API do Azure Cosmos DB Gremlin.


## <a name="response-example"></a>Exemplo de resposta

O exemplo a seguir é um exemplo anotado da saída que será retornado:

> [!NOTE]
> Este exemplo é anotado com comentários que explicam a estrutura geral da resposta. Uma resposta executionProfile real não conter nenhum comentário.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. Depending on the query, these might be directly adjacent to a set of vertices, or separate, in the case of an E() query.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from the preceding graph operations.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> A etapa executionProfile executará a consulta Gremlin. Isso inclui o `addV` ou `addE`etapas, o que resultarão na criação e confirmação as alterações especificadas na consulta. Como resultado, as unidades de solicitação, gerado pela consulta Gremlin também serão cobradas.

## <a name="execution-profile-response-objects"></a>Objetos de resposta de perfil de execução

A resposta de uma função executionProfile() produzirá uma hierarquia de objetos JSON com a seguinte estrutura:
  - **Objeto de operação do gremlin**: Representa a operação inteira do Gremlin que foi executada. Contém as propriedades a seguir.
    - `gremlin`: A instrução do Gremlin explícita que foi executada.
    - `totalTime`: O tempo, em milissegundos, que é usada para a execução da etapa. 
    - `metrics`: Uma matriz que contém cada um dos operadores de tempo de execução do Cosmos DB que foram executados para realizar a consulta. Essa lista é classificada em ordem de execução.
    
  - **Operadores de tempo de execução do cosmos DB**: Representa cada um dos componentes de toda a operação de Gremlin. Essa lista é classificada em ordem de execução. Cada objeto contém as seguintes propriedades:
    - `name`: Nome do operador. Esse é o tipo da etapa que foi avaliado e executado. Leia mais na tabela a seguir.
    - `time`: Quantidade de tempo, em milissegundos, que levou um determinado operador.
    - `annotations`: Contém informações adicionais, específicas para o operador que foi executado.
    - `annotations.percentTime`: Percentual do tempo total gasto para executar o operador específico.
    - `counts`: Número de objetos que foram retornados da camada de armazenamento por esse operador. Isso está contido no `counts.resultCount` valor escalar dentro.
    - `storeOps`: Representa uma operação de armazenamento que pode abranger uma ou várias partições.
    - `storeOps.fanoutFactor`: Representa o número de partições acessada essa operação de armazenamento específico.
    - `storeOps.count`: Representa o número de resultados retornada a operação de armazenamento.
    - `storeOps.size`: Representa o tamanho em bytes do resultado de uma operação de armazenamento específica.

Operador de tempo de execução de Gremlin do cosmos DB|DESCRIÇÃO
---|---
`GetVertices`| Esta etapa obtém um conjunto predicated de objetos da camada de persistência. 
`GetEdges`| Esta etapa obtém as bordas que são adjacentes a um conjunto de vértices. Esta etapa pode resultar em uma ou muitas operações de armazenamento.
`GetNeighborVertices`| Esta etapa obtém os vértices que são conectados a um conjunto das bordas. As bordas contêm a partição de chaves e IDs de vértices de sua origem e de destino.
`Coalesce`| Esta etapa contas para a avaliação de duas operações sempre que o `coalesce()` Gremlin etapa é executada.
`CartesianProductOperator`| Esta etapa calcula um produto cartesiano entre dois conjuntos de dados. Geralmente é executado sempre que os predicados `to()` ou `from()` são usados.
`ConstantSourceOperator`| Esta etapa calcula uma expressão para produzir um valor constante como resultado.
`ProjectOperator`| Essa etapa prepara e serializa uma resposta usando o resultado de operações anteriores.
`ProjectAggregation`| Essa etapa prepara e serializa uma resposta para uma operação de agregação.

> [!NOTE]
> Esta lista continuará a ser atualizada conforme novos operadores são adicionados.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Exemplos de como analisar uma resposta de perfil de execução

A seguir estão exemplos das otimizações comuns que podem ser identificados usando a resposta de perfil de execução:
  - Consulta de fan-out cegos.
  - Consulta não filtrada.

### <a name="blind-fan-out-query-patterns"></a>Padrões de consulta do tipo fan-out cegos

Suponha que a seguinte resposta de perfil de execução de um **gráfico particionado**:

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

As seguintes conclusões podem ser feitas a partir dele:
- A consulta é uma pesquisa de ID única, uma vez que a instrução do Gremlin segue o padrão `g.V('id')`.
- A julgar a partir de `time` métrica, a latência dessa consulta parece ser alta, pois se trata [mais de 10 ms para uma única operação de leitura do ponto de](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide).
- Se, examinamos os `storeOps` do objeto, podemos ver que o `fanoutFactor` é `5`, o que significa que [5 partições](https://docs.microsoft.com/azure/cosmos-db/partition-data) foram acessados por esta operação.

Como uma conclusão dessa análise, podemos determinar se a primeira consulta está acessando mais partições do que o necessário. Isso pode ser resolvido, especificando a chave de particionamento da consulta como um predicado. Isso levará a menor latência e menos custos por consulta. Saiba mais sobre o [particionamento de grafo](graph-partitioning.md). Uma consulta mais ideal seria `g.V('tt0093640').has('partitionKey', 't1001')`.

### <a name="unfiltered-query-patterns"></a>Padrões de consulta não filtradas

Compare as seguintes respostas de perfil de execução de dois. Para simplificar, nestes exemplos usam um único gráfico particionado.

Esta primeira consulta recupera todos os vértices com o rótulo `tweet` e, em seguida, obtém seus vértices vizinhos:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

Observe o perfil da mesma consulta, mas agora com um filtro adicional, `has('lang', 'en')`, antes de explorar os vértices adjacentes:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

Essas duas consultas alcançado o mesmo resultado, no entanto, a primeira delas exigirá mais unidades de solicitação, pois ele necessária para iterar um conjunto de dados inicial maior antes de consultar os itens adjacentes. Podemos ver indicadores desse comportamento ao comparar os parâmetros de ambas as respostas a seguir:
- O `metrics[0].time` valor é maior na primeira resposta, que indica que essa etapa única demorou mais para serem resolvidos.
- O `metrics[0].counts.resultsCount` valor é mais alto também na primeira resposta, que indica que o conjunto de dados do trabalho inicial era maior.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [Gremlin recursos com suporte](gremlin-support.md) no Azure Cosmos DB. 
* Saiba mais sobre o [API do Gremlin no Azure Cosmos DB](graph-introduction.md).
