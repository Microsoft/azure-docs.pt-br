---
title: Método Pesquisa do Graph – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Use o método Pesquisa do Graph na API de Conhecimento Acadêmico para retornar um conjunto de entidades acadêmicas com base em padrões de grafos específicos.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 5d47b938560fb1bd15adfe1a1c2d35b7359d47a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61339107"
---
# <a name="graph-search-method"></a>Método de pesquisa de grafo

A **pesquisa de grafo** é usada para retornar um conjunto de entidades acadêmicas com base nos padrões de grafos fornecidos.  A resposta é um conjunto de caminhos de grafos satisfazendo as restrições especificadas pelo usuário. Um caminho de grafo é uma sequência intercalada de nós e arestas na forma de _v0, e0, v1, e1, ..., vn_, onde _v0_ é o nó inicial do caminho.
<br>

**Ponto de extremidade REST:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?
```   
<br>

## <a name="request-parameters"></a>Parâmetros de solicitação  

NOME     | Value | Obrigatório?  | DESCRIÇÃO
-----------|-----------|---------|--------
**modo**       | Cadeia de caracteres de texto | Sim | Nome do modo que você quer usar. O valor é *json* ou *lambda*.

O método de pesquisa de grafo deve ser chamado via uma solicitação HTTP POST. A solicitação POST deve incluir o cabeçalho content-type: **application/json**.

##### <a name="json-search"></a>Sintaxe de pesquisa JSON 

Para a pesquisa *json*, o corpo POST é um objeto JSON. O objeto JSON descreve um padrão de caminho com restrições especificadas pelo usuário (consulte a especificação [do objeto JSON](JSONSearchSyntax.md) para a pesquisa *json*).


##### <a name="lambda-search"></a>Sintaxe de pesquisa lambda

Para a pesquisa *lambda*, o corpo POST é uma cadeia de caracteres de texto sem formatação. O corpo POST é uma cadeia de caracteres de consulta lambda LIKQ, que é uma única instrução C# (consulte a especificação [cadeia de caracteres de consulta](LambdaSearchSyntax.md) para a pesquisa *lambda*). 

<br>

## <a name="response-json"></a>Resposta (JSON)

NOME | DESCRIÇÃO
-------|-----   
**resultados** | Uma matriz de 0 ou mais entidades que correspondem à expressão de consulta. Cada entidade contém os valores dos atributos solicitados. Este campo estará presente, se a solicitação for processada com êxito.
**error** | Códigos de status HTTP. Este campo estará presente, se a solicitação falhar.
**message** | Mensagem de erro. Este campo estará presente, se a solicitação falhar.

Se uma consulta não puder ser processada em _800 ms_, um erro de _tempo limite_ será retornado. 

<br>

#### <a name="example"></a>Exemplo:

##### <a name="json-search"></a>Sintaxe de pesquisa JSON
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=json
```
<br>
Para a pesquisa *json*, se quiser obter artigos cujos títulos contenham "mecanismo de grafo" e escritos por "bin shao", será possível especificar a consulta da seguinte maneira.

```JSON
{
  "path": "/paper/AuthorIDs/author",
  "paper": {
    "type": "Paper",
    "NormalizedTitle": "graph engine",
    "select": [
      "OriginalTitle"
    ]
  },
  "author": {
    "return": {
      "type": "Author",
      "Name": "bin shao"
    }
  }
}
```

A saída de uma consulta é uma matriz de caminhos de grafos. Um caminho de grafo é uma matriz de objetos do nó correspondente aos nós especificados no caminho da consulta. Esses objetos de nó possuem pelo menos uma propriedade *CellID*, que representa a ID da entidade. Outras propriedades podem ser recuperadas especificando os nomes das propriedades através do operador de seleção de um [*Objeto de Ação de Travessia*](JSONSearchSyntax.md).

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668,
        "OriginalTitle": "Trinity: a distributed graph engine on a memory cloud"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2171539317,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2411554868,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 73304046,
        "OriginalTitle": "The Trinity graph engine"
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
 ```

##### <a name="lambda-search"></a>Sintaxe de pesquisa lambda 

```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=lambda
```
<br>
Para a pesquisa *lambda*, se quiser obter as IDs de um determinado artigo, é possível gravar uma consulta, conforme a seguir.

```
MAG.StartFrom(@"{
    type  : ""Paper"",
    match : {
        NormalizedTitle : ""trinity: a distributed graph engine on a memory cloud""
    }
}").FollowEdge("AuthorIDs").VisitNode(Action.Return)
```

A saída de uma consulta de pesquisa *lambda* também é uma matriz de caminhos de grafos:

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2142490828
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2116756368
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
```
 
## <a name="graph-schema"></a>Esquema de grafo

O esquema de grafo é útil para gravar consultas de pesquisa de grafo. Isso é mostrado na figura a seguir.

![Esquema do Microsoft Academic Graph](./Images/AcademicGraphSchema.png)
