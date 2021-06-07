---
title: Subconsultas SQL para Azure Cosmos DB
description: Saiba mais sobre subconsultas SQL e seus casos de uso comuns e tipos diferentes de subconsultas no Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: f5f209229d17a2587258d21ee90e7560e629d082
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340848"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Exemplos de subconsulta do SQL para Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Uma subconsulta é uma consulta aninhada dentro de outra consulta. Uma subconsulta também é chamada de consulta interna ou seleção interna. A instrução que contém uma subconsulta normalmente é chamada de consulta externa.

Este artigo descreve as subconsultas do SQL e seus casos de uso comuns no Azure Cosmos DB. Todas as consultas de exemplo neste documento podem ser executadas em um conjunto de nutrição que é pré-carregado no [playground para consultas de Azure Cosmos DB](https://www.documentdb.com/sql/demo).

## <a name="types-of-subqueries"></a>Tipos de subconsultas

Há dois tipos principais de subconsultas:

* **Correlacionado**: uma subconsulta que faz referência a valores da consulta externa. A subconsulta é avaliada uma vez para cada linha que a consulta externa processa.
* **Não correlacionado**: uma subconsulta que é independente da consulta externa. Ele pode ser executado sozinho sem depender da consulta externa.

> [!NOTE]
> Azure Cosmos DB dá suporte apenas a Subconsultas correlacionadas.

As subconsultas podem ser classificadas com base no número de linhas e colunas que retornam. Há três tipos:
* **Tabela**: retorna várias linhas e várias colunas.
* **Vários valores**: retorna várias linhas e uma única coluna.
* **Escalar**: retorna uma única linha e uma única coluna.

As consultas SQL em Azure Cosmos DB sempre retornam uma única coluna (um valor simples ou um documento complexo). Portanto, somente subconsultas de valores múltiplos e escalares são aplicáveis em Azure Cosmos DB. Você pode usar uma subconsulta de vários valores somente na cláusula FROM como uma expressão relacional. Você pode usar uma subconsulta escalar como uma expressão escalar na cláusula SELECT ou WHERE ou como uma expressão relacional na cláusula FROM.

## <a name="multi-value-subqueries"></a>Subconsultas de vários valores

Subconsultas de vários valores retornam um conjunto de documentos e são sempre usadas dentro da cláusula FROM. Eles são usados para:

* Otimizando expressões de junção. 
* Avaliar expressões caras uma vez e fazer referência várias vezes.

## <a name="optimize-join-expressions"></a>Otimizar expressões de junção

Subconsultas com vários valores podem otimizar as expressões de junção enviando predicados após cada expressão Select-many em vez de todas as junções cruzadas na cláusula WHERE.

Considere a consulta a seguir:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

Para essa consulta, o índice corresponderá a qualquer documento que tenha uma marca com o nome "Infant Formula". É um item de Nutrient com um valor entre 0 e 10 e um item de serviço com um valor maior que 1. A expressão de junção aqui executará o produto cruzado de todos os itens de marcas, nutrients e servirá matrizes para cada documento correspondente antes de qualquer filtro ser aplicado. 

Em seguida, a cláusula WHERE aplicará o predicado de filtro em cada <c, t, n, s> tupla. Por exemplo, se um documento correspondente tiver 10 itens em cada uma das três matrizes, ele se expandirá para uma tupla de 1 x 10 x 10 x 10 (ou seja, 1.000). O uso de subconsultas aqui pode ajudar na filtragem de itens de matriz Unidos antes da junção com a próxima expressão.

Essa consulta é equivalente à anterior, mas usa subconsultas:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Suponha que apenas um item na matriz de marcas corresponda ao filtro e que haja cinco itens para nutrients e atende a matrizes. As expressões de junção serão expandidas para 1 x 1 x 5 x 5 = 25 itens, em vez de 1.000 itens na primeira consulta.

## <a name="evaluate-once-and-reference-many-times"></a>Avaliar uma vez e fazer referência várias vezes

As subconsultas podem ajudar a otimizar consultas com expressões caras, como UDFs (funções definidas pelo usuário), cadeias de caracteres complexas ou expressões aritméticas. Você pode usar uma subconsulta juntamente com uma expressão de junção para avaliar a expressão uma vez, mas fazer referência a ela muitas vezes.

A consulta a seguir executa o UDF `GetMaxNutritionValue` duas vezes:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Aqui está uma consulta equivalente que executa o UDF somente uma vez:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Tenha em mente o comportamento entre produtos de expressões de junção. Se a expressão UDF puder ser avaliada como indefinida, você deve garantir que a expressão de junção sempre produza uma única linha retornando um objeto da subconsulta em vez do valor diretamente.
>

Veja um exemplo semelhante que retorna um objeto em vez de um valor:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

A abordagem não está limitada a UDFs. Ele se aplica a qualquer expressão potencialmente dispendiosa. Por exemplo, você pode usar a mesma abordagem com a função matemática `avg` :

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>Imitar junção com dados de referência externa

Geralmente, talvez seja necessário fazer referência a dados estáticos que raramente mudam, como unidades de medida ou códigos de país. É melhor não duplicar esses dados para cada documento. Evitar essa duplicação será economizado no armazenamento e melhorará o desempenho de gravação mantendo o tamanho do documento menor. Você pode usar uma subconsulta para imitar semântica de junção interna com uma coleção de dados de referência.

Por exemplo, considere este conjunto de dados de referência:

| **Unidade** | **Nome**            | **Multiplicador** | **Unidade base** |
| -------- | ------------------- | -------------- | ------------- |
| ção       | Cograma            | e-09 1,00       | Gram          |
| µg       | Microgram           | e-06 1,00       | Gram          |
| mg       | Milligram           | e-03 de 1,00       | Gram          |
| g        | Gram                | 1,00 e + 00       | Gram          |
| kg       | Kg            | 1,00 e + 03       | Gram          |
| Mg       | Megagram            | 1,00 e + 06       | Gram          |
| GG       | Gigagram            | 1,00 e + 09       | Gram          |
| nJ       | Nanojoule           | e-09 1,00       | Joule         |
| µJ       | Microjoule          | e-06 1,00       | Joule         |
| mJ       | Millijoule          | e-03 de 1,00       | Joule         |
| J        | Joule               | 1,00 e + 00       | Joule         |
| kJ       | Kilojoule           | 1,00 e + 03       | Joule         |
| MJ       | Megajoule           | 1,00 e + 06       | Joule         |
| GJ       | Gigajoule           | 1,00 e + 09       | Joule         |
| licença      | Calorias             | 1,00 e + 00       | calorias       |
| kcal     | Calorias             | 1,00 e + 03       | calorias       |
| IU       | Unidades internacionais |                |               |


A consulta a seguir imita a junção com esses dados para que você adicione o nome da unidade à saída:

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>Subconsultas escalares

Uma expressão de subconsulta escalar é uma subconsulta que é avaliada como um único valor. O valor da expressão de subconsulta escalar é o valor da projeção (cláusula SELECT) da subconsulta.  Você pode usar uma expressão de subconsulta escalar em muitos locais em que uma expressão escalar é válida. Por exemplo, você pode usar uma subconsulta escalar em qualquer expressão nas cláusulas SELECT e WHERE.

No entanto, o uso de uma subconsulta escalar nem sempre ajuda a otimizar. Por exemplo, passar uma subconsulta escalar como um argumento para um sistema ou funções definidas pelo usuário não fornece nenhum benefício no consumo ou na latência de RU (unidade de recurso).

As subconsultas escalares podem ser classificadas como:
* Subconsultas escalares de expressão simples
* Agregar subconsultas escalares

## <a name="simple-expression-scalar-subqueries"></a>Subconsultas escalares de expressão simples

Uma subconsulta escalar de expressão simples é uma subconsulta correlacionada que tem uma cláusula SELECT que não contém nenhuma expressão de agregação. Essas subconsultas não fornecem benefícios de otimização porque o compilador as converte em uma expressão simples maior. Não há nenhum contexto correlacionado entre as consultas internas e externas.

Veja alguns exemplos:

**Exemplo 1**

```sql
SELECT 1 AS a, 2 AS b
```

Você pode reescrever essa consulta usando uma subconsulta escalar de expressão simples, para:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Ambas as consultas produzem esta saída:

```json
[
  { "a": 1, "b": 2 }
]
```

**Exemplo 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

Você pode reescrever essa consulta usando uma subconsulta escalar de expressão simples, para:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Saída da consulta:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**Exemplo 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

Você pode reescrever essa consulta usando uma subconsulta escalar de expressão simples, para:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Saída da consulta:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>Agregar subconsultas escalares

Uma subconsulta escalar agregada é uma subconsulta que tem uma função de agregação em sua projeção ou filtro que é avaliada como um único valor.

**Exemplo 1:**

Aqui está uma subconsulta com uma única expressão de função de agregação em sua projeção:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Saída da consulta:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**Exemplo 2**

Aqui está uma subconsulta com várias expressões de função de agregação:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Saída da consulta:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**Exemplo 3**

Aqui está uma consulta com uma subconsulta agregada na projeção e no filtro:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Saída da consulta:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Uma maneira mais ideal de escrever essa consulta é ingressar na subconsulta e fazer referência ao alias de subconsulta nas cláusulas SELECT e WHERE. Essa consulta é mais eficiente porque você precisa executar a subconsulta somente dentro da instrução de junção e não na projeção e no filtro.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>Expressão EXISTs

Azure Cosmos DB dá suporte a expressões EXISTs. Essa é uma subconsulta escalar agregada incorporada à API do SQL do Azure Cosmos DB. EXISTs é uma expressão booliana que usa uma expressão de subconsulta e retorna true se a subconsulta retornar qualquer linha. Caso contrário, retornará false.

Como a API do SQL do Azure Cosmos DB não diferencia as expressões boolianas e quaisquer outras expressões escalares, você pode usar EXISTs nas cláusulas SELECT e WHERE. Isso é diferente de T-SQL, em que uma expressão booliana (por exemplo, EXISTs, BETWEEN e IN) é restrita ao filtro.

Se a subconsulta EXISTs retornar um único valor indefinido, EXISTs será avaliado como false. Por exemplo, considere a seguinte consulta que é avaliada como false:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Se a palavra-chave VALUE na subconsulta anterior for omitida, a consulta será avaliada como true:
```sql
SELECT EXISTS (SELECT undefined) 
```

A subconsulta colocará a lista de valores na lista selecionada em um objeto. Se a lista selecionada não tiver valores, a subconsulta retornará o único valor ' {} '. Esse valor é definido, portanto, EXISTs é avaliado como true.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>Exemplo: reescrever ARRAY_CONTAINS e INGRESSAr como existe

Um caso de uso comum de ARRAY_CONTAINS é filtrar um documento pela existência de um item em uma matriz. Nesse caso, estamos verificando se a matriz de marcas contém um item chamado "laranja".

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Você pode reescrever a mesma consulta a ser usada:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Além disso, ARRAY_CONTAINS só pode verificar se um valor é igual a qualquer elemento dentro de uma matriz. Se você precisar de filtros mais complexos em Propriedades de matriz, use JOIN.

Considere a seguinte consulta que filtra com base nas unidades e `nutritionValue` Propriedades na matriz: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Para cada um dos documentos na coleção, um produto cruzado é executado com seus elementos de matriz. Essa operação de junção torna possível filtrar as propriedades dentro da matriz. No entanto, o consumo de RU desta consulta será significativo. Por exemplo, se os documentos 1.000 tivessem 100 itens em cada matriz, eles serão expandidos para tuplas 1.000 x 100 (ou seja, 100.000).

O uso de EXISTs pode ajudar a evitar este caro produto cruzado:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

Nesse caso, você filtra os elementos da matriz dentro da subconsulta EXISTs. Se um elemento de matriz corresponder ao filtro, você o projetará e EXISTIRá avaliado como true.

Você também pode fazer o alias e referenciá-lo na projeção:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Saída da consulta:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>Expressão de matriz

Você pode usar a expressão de matriz para projetar os resultados de uma consulta como uma matriz. Você pode usar essa expressão somente dentro da cláusula SELECT da consulta.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Saída da consulta:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

Assim como ocorre com outras subconsultas, são possíveis filtros com a expressão de matriz.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Saída da consulta:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

As expressões de matriz também podem vir após a cláusula FROM em subconsultas.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Saída da consulta:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>Próximas etapas

- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dados de documento modelo](modeling-data.md)
