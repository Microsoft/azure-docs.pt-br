---
title: Usar o perfil de execução para avaliar consultas na API Azure Cosmos DB Gremlin
description: Saiba como solucionar problemas e aprimorar suas consultas do Gremlin usando a etapa perfil de execução.
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 03/27/2019
ms.author: chrande
ms.openlocfilehash: 18cefb1dd80368a8ccdad9f6f3ffc30881a8a889
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93087478"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Como usar a etapa de perfil de execução para avaliar suas consultas do Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Este artigo fornece uma visão geral de como usar a etapa de perfil de execução para bancos de dados de grafo da API Gremlin do Azure Cosmos DB. Esta etapa fornece informações relevantes para solução de problemas e otimizações de consulta e é compatível com qualquer consulta Gremlin que possa ser executada em uma conta de API do Gremlin do Cosmos DB.

Para usar essa etapa, basta acrescentar a `executionProfile()` chamada de função ao final da consulta Gremlin. **Sua consulta Gremlin será executada** e o resultado da operação retornará um objeto de resposta JSON com o perfil de execução de consulta.

Por exemplo:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Depois de chamar a `executionProfile()` etapa, a resposta será um objeto JSON que inclui a etapa Gremlin executada, o tempo total que levou e uma matriz do cosmos DB operadores de tempo de execução que a instrução resultou.

> [!NOTE]
> Esta implementação para o perfil de execução não está definida na especificação do Apache Tinkerpop. É específico para Azure Cosmos DB implementação da API Gremlin.


## <a name="response-example"></a>Exemplo de resposta

Este é um exemplo anotado da saída que será retornada:

> [!NOTE]
> Este exemplo é anotado com comentários que explicam a estrutura geral da resposta. Uma resposta executionProfile real não conterá nenhum comentário.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
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
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
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
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
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
> A etapa executionProfile executará a consulta Gremlin. Isso inclui as `addV` `addE` etapas ou, que resultarão na criação e confirmarão as alterações especificadas na consulta. Como resultado, as unidades de solicitação geradas pela consulta Gremlin também serão cobradas.

## <a name="execution-profile-response-objects"></a>Objetos de resposta do perfil de execução

A resposta de uma função executionProfile () produzirá uma hierarquia de objetos JSON com a seguinte estrutura:
  - **Objeto de operação Gremlin**: representa a operação Gremlin inteira que foi executada. Contém as propriedades a seguir.
    - `gremlin`: A instrução Gremlin explícita que foi executada.
    - `totalTime`: O tempo, em milissegundos, que a execução da etapa incorreu no. 
    - `metrics`: Uma matriz que contém cada um dos operadores de tempo de execução Cosmos DB que foram executados para atender à consulta. Essa lista é classificada em ordem de execução.
    
  - **Operadores de tempo de execução Cosmos DB**: representa cada um dos componentes de toda a operação Gremlin. Essa lista é classificada em ordem de execução. Cada objeto contém as seguintes propriedades:
    - `name`: O nome do operador. Esse é o tipo de etapa que foi avaliado e executado. Leia mais na tabela a seguir.
    - `time`: Quantidade de tempo, em milissegundos, que um determinado operador levou.
    - `annotations`: Contém informações adicionais, específicas para o operador que foi executado.
    - `annotations.percentTime`: Porcentagem do tempo total necessário para executar o operador específico.
    - `counts`: Número de objetos que foram retornados da camada de armazenamento por este operador. Isso está contido no `counts.resultCount` valor escalar dentro de.
    - `storeOps`: Representa uma operação de armazenamento que pode abranger uma ou várias partições.
    - `storeOps.fanoutFactor`: Representa o número de partições que essa operação de armazenamento específico acessou.
    - `storeOps.count`: Representa o número de resultados que essa operação de armazenamento retornou.
    - `storeOps.size`: Representa o tamanho em bytes do resultado de uma determinada operação de armazenamento.

Cosmos DB operador de tempo de execução Gremlin|Descrição
---|---
`GetVertices`| Esta etapa Obtém um conjunto de objetos predicados da camada de persistência. 
`GetEdges`| Esta etapa Obtém as bordas que são adjacentes a um conjunto de vértices. Essa etapa pode resultar em uma ou várias operações de armazenamento.
`GetNeighborVertices`| Esta etapa Obtém os vértices que estão conectados a um conjunto de bordas. As bordas contêm as chaves de partição e as IDs de seus vértices de origem e de destino.
`Coalesce`| Esta etapa conta a avaliação de duas operações sempre que a `coalesce()` etapa Gremlin é executada.
`CartesianProductOperator`| Esta etapa computa um produto cartesiano entre dois conjuntos de os. Geralmente executado sempre que os predicados `to()` ou `from()` são usados.
`ConstantSourceOperator`| Esta etapa computa uma expressão para produzir um valor constante como resultado.
`ProjectOperator`| Esta etapa prepara e serializa uma resposta usando o resultado de operações anteriores.
`ProjectAggregation`| Esta etapa prepara e serializa uma resposta para uma operação de agregação.

> [!NOTE]
> Esta lista continuará a ser atualizada à medida que novos operadores forem adicionados.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Exemplos de como analisar uma resposta de perfil de execução

Veja a seguir exemplos de otimizações comuns que podem ser expostas usando a resposta do perfil de execução:
  - Consulta de Fan-out.
  - Consulta não filtrada.

### <a name="blind-fan-out-query-patterns"></a>Padrões de consulta de Fan-out cego

Suponha a seguinte resposta de perfil de execução de um **grafo particionado**:

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

As seguintes conclusões podem ser feitas a partir dela:
- A consulta é uma pesquisa de ID única, uma vez que a instrução Gremlin segue o padrão `g.V('id')` .
- Julgamento da `time` métrica, a latência dessa consulta parece estar alta, pois é [mais de 10 ms para uma única operação de leitura de ponto](./introduction.md#guaranteed-speed-at-any-scale).
- Se olharmos para o `storeOps` objeto, podemos ver que o `fanoutFactor` é `5` , o que significa que [5 partições](./partitioning-overview.md) foram acessadas por essa operação.

Como uma conclusão dessa análise, podemos determinar que a primeira consulta está acessando mais partições do que o necessário. Isso pode ser resolvido especificando-se a chave de particionamento na consulta como um predicado. Isso resultará em menos latência e menos custo por consulta. Saiba mais sobre o [particionamento de grafo](graph-partitioning.md). Uma consulta mais ideal seria `g.V('tt0093640').has('partitionKey', 't1001')` .

### <a name="unfiltered-query-patterns"></a>Padrões de consulta não filtrados

Compare as duas respostas de perfil de execução a seguir. Para simplificar, esses exemplos usam um único grafo particionado.

Essa primeira consulta recupera todos os vértices com o rótulo `tweet` e, em seguida, obtém seus vértices vizinhos:

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

Observe o perfil da mesma consulta, mas agora com um filtro adicional, `has('lang', 'en')` , antes de explorar os vértices adjacentes:

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

Essas duas consultas atingiram o mesmo resultado, no entanto, a primeira exigirá mais unidades de solicitação, pois precisava iterar um conjunto de dado inicial maior antes de consultar os itens adjacentes. Podemos ver os indicadores desse comportamento ao comparar os seguintes parâmetros de ambas as respostas:
- O `metrics[0].time` valor é maior na primeira resposta, o que indica que essa única etapa demorou mais para ser resolvida.
- O `metrics[0].counts.resultsCount` valor é mais alto também na primeira resposta, o que indica que o conjunto de valores de trabalho inicial foi maior.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre os [recursos de Gremlin com suporte](gremlin-support.md) no Azure Cosmos DB. 
* Saiba mais sobre a [API Gremlin no Azure Cosmos DB](graph-introduction.md).