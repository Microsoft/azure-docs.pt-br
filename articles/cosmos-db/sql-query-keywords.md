---
title: Palavras-chave SQL para o Azure Cosmos DB
description: Saiba mais sobre palavras-chave SQL para o Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: c9024f120e0a55162a1f6dba0cd9cbda97f5eebc
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342471"
---
# <a name="keywords-in-azure-cosmos-db"></a>Palavras-chave no Azure Cosmos DB
Este artigo fornece detalhes sobre as palavras-chave que podem ser usadas em consultas SQL do Azure Cosmos DB.

## <a name="between"></a>ENTRE

Como no ANSI SQL, você pode usar a palavra-chave BETWEEN para expressar consultas a intervalos de valores numéricos ou de cadeia de caracteres. Por exemplo, a consulta a seguir retorna todos os itens em que a classificação do primeiro filho é 1 a 5, inclusivo.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Ao contrário no ANSI SQL, você também pode usar a cláusula BETWEEN na cláusula FROM, como no exemplo a seguir.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Na API de SQL, ao contrário de ANSI SQL, você pode expressar consultas de intervalo em Propriedades de tipos mistos. Por exemplo, `grade` pode ser um número semelhante `5` em alguns itens e uma cadeia de caracteres como `grade4` em outros. Nesses casos, como no JavaScript, a comparação entre os dois tipos diferentes resulta em `Undefined`, portanto, o item será ignorado.

> [!TIP]
> Para tempos de execução de consulta mais rápidos, crie uma política de indexação que usa um tipo de índice de intervalo em relação a qualquer propriedades numéricas ou caminhos que filtra a cláusula BETWEEN.

## <a name="distinct"></a>DISTINTO

A palavra-chave DISTINCT elimina duplicatas na projeção da consulta.

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Neste exemplo, a consulta projeta valores para cada sobrenome.

Os resultados são:

```json
[
    "Andersen"
]
```

Você também pode projetar objetos exclusivos. Nesse caso, o campo lastName não existe em um dos dois documentos, portanto, a consulta retorna um objeto vazio.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Os resultados são:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

DISTINCT também pode ser usado na projeção de dentro de uma subconsulta:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Esta consulta projeta uma matriz que contém o givenName cada criança com as duplicatas removidas. Essa matriz é um alias como ChildNames e projetadas na consulta externa.

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
## <a name="in"></a> IN

Use a palavra-chave IN para verificar se um valor especificado corresponde a qualquer valor em uma lista. Por exemplo, a consulta a seguir retorna todos os itens de família de produtos em que o `id` está `WakefieldFamily` ou `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

O exemplo a seguir retorna todos os itens cujo estado é qualquer um dos valores especificados:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

A API do SQL fornece suporte para [iteração em matrizes JSON](sql-query-object-array.md#Iteration), com uma nova construção de adicionadas por meio da palavra-chave in na fonte de FROM. 

## <a name="top"></a>INÍCIO

A palavra-chave TOP retorna o primeiro `N` número de resultados da consulta em uma ordem indefinida. Como prática recomendada, usar TOP com a cláusula ORDER BY para limitar os resultados para o primeiro `N` número de valores ordenados. Combinar essa duas cláusulas a seguir é a única maneira de indicar de modo previsível quais linhas superior afeta.

Você pode usar TOP com um valor constante, como no exemplo a seguir, ou com um valor da variável usando consultas parametrizadas.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Os resultados são:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>Próximas etapas

- [Guia de Introdução](sql-query-getting-started.md)
- [Junções](sql-query-join.md)
- [Subconsultas](sql-query-subquery.md)