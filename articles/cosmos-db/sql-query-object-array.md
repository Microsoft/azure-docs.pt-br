---
title: Trabalhando com matrizes e objetos no Azure Cosmos DB
description: Aprenda a sintaxe do SQL para criar matrizes e objetos no Azure Cosmos DB. Este artigo também fornece alguns exemplos para executar operações em objetos de matriz
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tisande
ms.openlocfilehash: 1dccb8e51fbc578f8f218fe1582f95f7bcaf42d7
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493780"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Trabalhando com matrizes e objetos no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Um recurso importante do Azure Cosmos DB API do SQL é a criação de matriz e objeto. Este documento usa exemplos que podem ser recriados usando o [conjunto](sql-query-getting-started.md#upload-sample-data)de e da família.

Aqui está um item de exemplo neste conjunto de um:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

## <a name="arrays"></a>Matrizes

Você pode construir matrizes, conforme mostrado no exemplo a seguir:

```sql
SELECT [f.address.city, f.address.state] AS CityState
FROM Families f
```

Os resultados são:

```json
[
  {
    "CityState": [
      "Seattle",
      "WA"
    ]
  },
  {
    "CityState": [
      "NY", 
      "NY"
    ]
  }
]
```

Você também pode usar a [expressão de matriz](sql-query-subquery.md#array-expression) para construir uma matriz de resultados da [subconsulta](sql-query-subquery.md) . Essa consulta obtém todos os nomes distintos de filhos em uma matriz.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Os resultados são:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

## <a name="iteration"></a><a id="Iteration"></a>Iteração

A API do SQL fornece suporte para iteração em matrizes JSON, com a [palavra-chave in](sql-query-keywords.md#in) na origem do. No exemplo a seguir:

```sql
SELECT *
FROM Families.children
```

Os resultados são:

```json
[
  [
    {
      "firstName": "Henriette Thaulow",
      "gender": "female",
      "grade": 5,
      "pets": [{ "givenName": "Fluffy"}]
    }
  ], 
  [
    {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1
    }, 
    {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }
  ]
]
```

A próxima consulta executa a iteração `children` no `Families` contêiner. A matriz de saída é diferente da consulta anterior. Este exemplo divide `children` e mescla os resultados em uma única matriz:  

```sql
SELECT *
FROM c IN Families.children
```

Os resultados são:

```json
[
  {
      "firstName": "Henriette Thaulow",
      "gender": "female",
      "grade": 5,
      "pets": [{ "givenName": "Fluffy" }]
  },
  {
      "familyName": "Merriam",
      "givenName": "Jesse",
      "gender": "female",
      "grade": 1
  },
  {
      "familyName": "Miller",
      "givenName": "Lisa",
      "gender": "female",
      "grade": 8
  }
]
```

Você pode filtrar mais detalhadamente cada entrada individual da matriz, conforme mostrado no exemplo a seguir:

```sql
SELECT c.givenName
FROM c IN Families.children
WHERE c.grade = 8
```

Os resultados são:

```json
[{
  "givenName": "Lisa"
}]
```

Você também pode agregar o resultado de uma iteração de matriz. Por exemplo, a consulta a seguir conta o número de filhos entre todas as famílias:

```sql
SELECT COUNT(1) AS Count
FROM child IN Families.children
```

Os resultados são:

```json
[
  {
    "Count": 3
  }
]
```

> [!NOTE]
> Ao usar a palavra-chave IN para iteração, você não pode filtrar ou projetar nenhuma propriedade fora da matriz. Em vez disso, você deve usar [junções](sql-query-join.md).

Para obter exemplos adicionais, leia nossa [postagem de blog sobre como trabalhar com matrizes no Azure Cosmos DB](https://devblogs.microsoft.com/cosmosdb/understanding-how-to-query-arrays-in-azure-cosmos-db/).

## <a name="next-steps"></a>Próximas etapas

- [Guia de Introdução](sql-query-getting-started.md)
- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Junções](sql-query-join.md)
