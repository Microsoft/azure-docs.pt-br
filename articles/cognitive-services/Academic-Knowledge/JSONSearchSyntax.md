---
title: Sintaxe de pesquisa JSON – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre a sintaxe de pesquisa JSON que pode ser utilizada na API de Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: fddd2291fe7fbb46c57d31e9aebc7fc6244df971
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61336959"
---
# <a name="json-search-syntax"></a>Sintaxe de pesquisa JSON

```javascript
/* Query Object:
   Suppose we have a query path /v0/e0/v1/e1/...
   A query object contains constraints to be applied to graph nodes in a path.
   Constraints are given in <"node identifier", {constraint object}> key-value pairs: 
*/
{
    /* query path can also be set in the query object */
    path: "/v0/e0/v1/e1/.../vn/",
    v0: { /* A Constraint Object */ },
    v1: { /* A Constraint Object */ },
}
```

Os nomes de nós em um caminho de consulta (_v0, v1, ..._) servem como identificadores de nós que podem ser referenciados no objeto de consulta; os nomes das arestas (_e0, e1, ..._) no caminho representam os tipos das arestas correspondentes. É possível usar um asterisco _*_ como um nó ou nome de borda (exceto para o nó inicial, que deve ser fornecido) para declarar que não há restrições em tal elemento. Por exemplo, um caminho de consulta `/v0/*/v1/e1/*/` recupera caminhos do grafo sem restringir o tipo da borda _(v0, v1)_. Enquanto isso, a consulta também não possui restrições no destino (o último nó) do caminho.

Quando um caminho contém apenas um nó, digamos _v0_, a consulta simplesmente retornará todas as entidades que satisfazem as restrições. Um objeto de restrição aplicado ao nó inicial é chamado de *Objeto de Consulta Inicial*, cuja especificação é fornecida da seguinte forma.

```javascript
/* Starting Query Object:
   Specifies constraints on the starting node
*/
{
    /* "match" operator searches for entities with the specified properties. 
       All properties defined in the graph schema be queried such as "Name" and "NormalizedTitle".
     */
    "match": { 
        "Name" : "some name",
        ...
    },
    /* "id" operator directly specifies the IDs of the starting nodes. 
       When it is present, the "match" operator is ignored. 
     */
    "id": [ id1, id2, id3... ],
    /* "type" operator limits results to a certain type of entities,
       for example, "Author" or "Paper".
     */
    "type": "Author",
    /* "select" operator pulls properties from the database and 
       returns them to the client.
     */
    "select": ["PaperRank", ...]
}
```

Quando um caminho contém mais que apenas um nó inicial, o processador de consultas realizará uma travessia de gráfico seguindo o padrão de caminho especificado. Quando chegarem a um nó, as ações de travessia especificadas pelo usuário serão disparadas, ou seja, para parar no nó atual e retornar ou continuar a explorar o grafo. Quando nenhuma ação de travessia é especificada, as ações padrão serão tomadas. Para um nó intermediário, a ação padrão é continuar explorando o grafo. Para o último nó de um caminho, a ação padrão é parar e retornar. Um objeto de restrição que especifica as ações de travessia é chamado de *Objeto de Ação de Travessia*. A especificação é dada da seguinte forma:

```javascript
/* Traversal Action Object:
   Specifies graph traversal actions on a node (except for the starting node).
 */
{
    /* Set the continue condition here. */
    continue: { 
        /* simple property exact match */
        "property_key" : "property_value", 
        /* Advanced query operators */
        /* -- Numerical comparisons */
        "property_key_2" : { "gt" /* or simply ">" */ : 123 },
        /* -- Substring query */
        "property_key_3" : { "substring" : "456" },
        /* -- CellID query */
        "id": [ 111, 222, 333... ],
        /* -- Entity type query */
        "type": "cell_type",
        /* -- Property existance query */
        "has" : "property_key_4",
        /* -- Logical operators */
        /* ---- Note that, by default the operators are combined with AND semantics */
        
        /* -- OR operator */
        "or": {
          /* Query operators... */
        },
        /* -- NOT operator */
        "not": {
            /* Query operators... */
        }
    },
    /* Set the return condition here. */
    return: {
        /* Same operators as the continue object */
    },
    /* The selected properties to return. */
    select: ["property_key_1", ...]
}
```

O corpo POST de uma consulta de pesquisa *json* deve conter pelo menos um padrão de *caminho*. Objetos de ação de travessia são opcionais. A seguir, são apresentados dois exemplos.

```JSON
{
  "path": "/series/ConferenceInstanceIDs/conference/FieldOfStudyIDs/field",
  "series": {
    "type": "ConferenceSeries",
    "FullName": "graph",
    "select": [ "FullName", "ShortName" ]
  },
  "conference": {
    "type": "ConferenceInstance",
    "select": [ "FullName" ]
  },
  "field": {
    "type": "FieldOfStudy",
    "select": [ "Name" ],
    "return": { "Name": { "substring" : "World Wide Web" } }
  }
}
```

```JSON
{
  "path": "/author/PaperIDs/paper",
  "author": {
    "type": "Author",
    "select": [ "DisplayAuthorName" ],
    "match": { "Name": "leslie lamport" }
  },
  "paper": {
    "type": "Paper",
    "select": [ "OriginalTitle", "CitationCount" ],
    "return": { "CitationCount": { "gt": 100 } }
  }
}
```

