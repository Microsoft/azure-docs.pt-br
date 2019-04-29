---
title: Consultas SQL para o Azure Cosmos DB
description: Saiba mais sobre a sintaxe SQL, os conceitos sobre banco de dados e as consultas SQL do Azure Cosmos DB. Use o SQL como uma linguagem de consulta JSON do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mjbrown
ms.openlocfilehash: 04a88558e3aea33c6d99bd0e4f1354c4316f5529
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61054100"
---
# <a name="sql-query-examples-for-azure-cosmos-db"></a>Exemplos de consulta SQL para o Azure Cosmos DB

As contas do Azure Cosmos DB SQL API suportam a itens de consultas usando a linguagem SQL (Structured Query) como uma linguagem de consulta JSON. As metas de design da linguagem de consulta do Azure Cosmos DB são:

* Dá suporte ao SQL, uma das linguagens de consulta mais conhecidas e populares, em vez de inventar uma nova linguagem de consulta. SQL fornece um modelo de programação formal para consultas avançadas em itens JSON.  

* Use o modelo de programação do JavaScript como a base para a linguagem de consulta. Sistema de tipos, avaliação de expressão e invocação de função do JavaScript são as raízes de API do SQL. Essas raízes fornecem um modelo de programação natural para recursos como projeções relacionais, navegação hierárquica entre itens JSON, autojunções, consultas espaciais e invocação de funções definidas pelo usuário (UDFs) gravadas inteiramente em JavaScript.

Este artigo explica alguns exemplos de consultas SQL em itens JSON simples. Para saber mais sobre a sintaxe da linguagem SQL do Azure Cosmos DB, consulte [referência de sintaxe SQL](sql-api-query-reference.md).

## <a id="GettingStarted"></a>Introdução às consultas SQL

Em sua conta do Cosmos DB SQL API, crie um contêiner chamado `Families`. Crie dois itens simples de JSON no contêiner e executar algumas consultas simples em relação a elas.

### <a name="create-json-items"></a>Criar itens de JSON

O código a seguir cria dois itens JSON simples sobre famílias. Os itens JSON simples para as famílias Andersen e Barros incluem os pais, filhos e seus animais de estimação, endereço e informações de registro. O primeiro item tem cadeias de caracteres, números, boolianos, matrizes e propriedades aninhadas.


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

O segundo item usa `givenName` e `familyName` em vez de `firstName` e `lastName`.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female", 
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>Consultar os itens JSON

Tente algumas consultas nos dados JSON para entender alguns dos principais aspectos da linguagem de consulta SQL do Azure Cosmos DB.

A consulta a seguir retorna os itens onde a `id` correspondências de campo `AndersenFamily`. Uma vez que ele é um `SELECT *` consulta, a saída da consulta é o item JSON completo. Para obter mais informações sobre a sintaxe SELECT, consulte [instrução SELECT](sql-api-query-reference.md#select-query). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados da consulta são: 

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

A consulta a seguir reformata a saída JSON em uma forma diferente. A consulta em um novo JSON `Family` objeto com dois campos selecionados, `Name` e `City`, quando a cidade do endereço é o mesmo que o estado. Nesse caso, corresponde a "NY, NY".

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Os resultados da consulta são:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

A consulta a seguir retorna todos os nomes dos filhos na família cuja `id` corresponde a `WakefieldFamily`, ordenada por classificação.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.grade ASC
```

Os resultados são:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

Os exemplos anteriores mostram vários aspectos da linguagem de consulta do Cosmos DB:  

* Uma vez que a API do SQL trabalha com valores JSON, ele lida com entidades em forma de árvore em vez de linhas e colunas. Você pode consultar os nós de árvore em qualquer profundidade arbitrária, como `Node1.Node2.Node3…..Nodem`, de forma semelhante à referência de duas partes de `<table>.<column>` no ANSI SQL.

* Como funciona a linguagem de consulta com o repositório de dados, o sistema de tipo deve ser vinculado dinamicamente. A mesma expressão pode obter tipos diferentes em itens diferentes. O resultado de uma consulta é um valor JSON válido, mas não é garantido para ser de um esquema fixo.  

* O Azure Cosmos DB dá suporte somente a itens JSON estritos. O sistema de tipos e as expressões são restritas para lidar somente com tipos JSON. Para obter mais informações, consulte o [especificação JSON](https://www.json.org/).  

* Um contêiner do Cosmos DB é uma coleção de itens JSON sem esquema. As relações de dentro e entre itens de contêiner são capturadas implicitamente pela contenção, não pela chave primária e as relações de chave estrangeiras. Esse recurso é importante para as junções de intra-item, discutidas posteriormente neste artigo.

## <a id="SelectClause"></a>Cláusula SELECT

Toda consulta consiste em uma cláusula SELECT e FROM opcional e cláusulas WHERE, acordo com os padrões ANSI SQL. Normalmente, a origem na cláusula FROM é enumerada, e a cláusula WHERE aplica um filtro na fonte de recuperar um subconjunto de itens JSON. A cláusula SELECT, em seguida, projeta os valores JSON solicitados na lista de seleção. Para obter mais informações sobre a sintaxe, consulte [instrução SELECT](sql-api-query-reference.md#select-query).

A instrução SELECT a seguir consulta retorna de exemplo `address` de `Families` cuja `id` corresponde à `AndersenFamily`:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a id="EscapingReservedKeywords"></a>Acessador de propriedade entre aspas
Você pode acessar as propriedades usando o operador de propriedade entre aspas []. Por exemplo: `SELECT c.grade` and `SELECT c["grade"]` são equivalentes. Essa sintaxe é útil para substituir uma propriedade que contém espaços, caracteres especiais, ou tem o mesmo nome que uma palavra reservada ou uma palavra-chave SQL.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="nested-properties"></a>Propriedades aninhadas

O exemplo a seguir projeta duas propriedades aninhadas, `f.address.state` e `f.address.city`.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="json-expressions"></a>Expressões de JSON

Projeção também dá suporte a expressões JSON, conforme mostrado no exemplo a seguir:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

No exemplo anterior, a cláusula SELECT precisa criar um objeto JSON, e como o exemplo não fornece nenhuma chave, a cláusula usa o nome da variável de argumento implícito `$1`. A consulta a seguir retorna duas variáveis de argumento implícito: `$1` e `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="ValueKeyword"></a>Palavra-chave VALUE

A palavra-chave do valor fornece uma maneira de retornar o valor JSON sozinho. Por exemplo, a consulta mostrada abaixo retorna a expressão escalar `"Hello World"` em vez de `{$1: "Hello World"}`:

```sql
    SELECT VALUE "Hello World"
```

A consulta a seguir retorna os valores JSON sem o `address` rótulo:

```sql
    SELECT VALUE f.address
    FROM Families f
```

Os resultados são:

```json
    [
      {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan",
        "city": "NY"
      }
    ]
```

O exemplo a seguir mostra como retornar valores JSON primitivos (o nível folha da árvore JSON):


```sql
    SELECT VALUE f.address.state
    FROM Families f
```

Os resultados são:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="aliasing"></a>Atribuição de alias

É possível explicitamente alias valores em consultas. Se uma consulta tem duas propriedades com o mesmo nome, use aliases para renomear uma ou ambas as propriedades para que eles estiverem sem ambiguidade no resultado projetado.

A palavra-chave usada para definir alias é opcional, conforme mostrado no exemplo a seguir ao projetar o segundo valor como `NameInfo`:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="FromClause"></a>Cláusula FROM

FROM (`FROM <from_specification>`) cláusula é opcional, a menos que a fonte seja filtrada ou projetada mais adiante na consulta. Para obter mais informações sobre a sintaxe, consulte [da sintaxe](sql-api-query-reference.md#bk_from_clause). Uma consulta como `SELECT * FROM Families` enumera em todo o `Families` contêiner. Você também pode usar o identificador especial ROOT para o contêiner em vez de usar o nome do contêiner.

A cláusula FROM impõe as regras por consulta a seguir:

* O contêiner pode ser um alias, como `SELECT f.id FROM Families AS f` ou simplesmente `SELECT f.id FROM Families f`. Aqui `f` é o alias `Families`. COMO é o identificador de uma palavra-chave opcional para o alias.  

* Após receber um alias, o nome original do código-fonte não pode ser associado. Por exemplo, `SELECT Families.id FROM Families f` é sintaticamente inválido porque o identificador `Families` tem sido um alias e não pode mais ser resolvido.  

* Todas as propriedades de referência devem ser totalmente qualificadas evitar associações ambíguas na ausência de adesão de esquema estrita. Por exemplo, `SELECT id FROM Families f` é sintaticamente inválido porque a propriedade `id` não está associada.

### <a name="get-subitems-by-using-the-from-clause"></a>Obter subitens, usando a cláusula FROM

A cláusula FROM pode reduzir o código-fonte para um subconjunto menor. Para enumerar somente uma subárvore de cada item, a sub-raiz pode se tornar o código-fonte, conforme mostrado no exemplo a seguir:

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
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
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

A consulta anterior usado uma matriz como a origem, mas você também pode usar um objeto como a origem. A consulta considera qualquer valor JSON válido, definido na fonte para inclusão no resultado. O exemplo a seguir excluiria `Families` que não têm um `address.state` valor.

```sql
    SELECT *
    FROM Families.address.state
```

Os resultados são:

```json
    [
      "WA",
      "NY"
    ]
```

## <a id="WhereClause"></a>Cláusula WHERE

A cláusula WHERE opcional (`WHERE <filter_condition>`) Especifica as condições que os itens de JSON de origem devem satisfazer para incluí-los nos resultados da consulta. Um item de JSON deve avaliar as condições especificadas como `true` para ser considerado para o resultado. A camada de índice usa a cláusula WHERE para determinar o subconjunto menor de itens de origem que podem fazer parte do resultado. Para obter mais informações sobre a sintaxe, consulte [sintaxe WHERE](sql-api-query-reference.md#bk_where_clause).

A consulta a seguir os itens de solicitações que contêm uma `id` propriedade cujo valor é `AndersenFamily`. Ele exclui qualquer item que não tem um `id` propriedade ou cujo valor não corresponde ao `AndersenFamily`.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Expressões escalares na cláusula WHERE

O exemplo anterior mostrou uma consulta de igualdade simples. A API do SQL também dá suporte a vários [expressões escalares](#scalar-expressions). As expressões mais usadas são as binárias e unárias. Referências de propriedade do objeto JSON fonte também são expressões válidas.

Você pode usar os seguintes operadores binários com suporte:  

|**Tipo de operador**  | **Valores** |
|---------|---------|
|Aritmético | +,-,*,/,% |
|Bit a bit    | \|, &, ^, <<, >>, >>> (deslocamento à direita com preenchimento com zero) |
|Lógico    | AND, OR, NOT      |
|Comparação | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Cadeia de caracteres     |  \|\| (concatenar) |

As seguintes consultas usam operadores binários:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

Você também pode usar os operadores unários +,-, ~ e não em consultas, conforme mostrado nos exemplos a seguir:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Você também pode usar referências de propriedade em consultas. Por exemplo, `SELECT * FROM Families f WHERE f.isRegistered` retorna o item JSON que contém a propriedade `isRegistered` com valor igual a `true`. Qualquer outro valor, como `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`, ou `<array>`, exclui o item do resultado. 

### <a name="equality-and-comparison-operators"></a>Operadores de igualdade e de comparação

A tabela a seguir mostra o resultado de comparações de igualdade na API do SQL entre dois tipos JSON quaisquer.

| **Op** | **Indefinido** | **Nulo** | **Booliano** | **Número** | **Cadeia de caracteres** | **Objeto** | **Matriz** |
|---|---|---|---|---|---|---|---|
| **Indefinido** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido |
| **Nulo** | Indefinido | **Ok** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido |
| **Booliano** | Indefinido | Indefinido | **Ok** | Indefinido | Indefinido | Indefinido | Indefinido |
| **Número** | Indefinido | Indefinido | Indefinido | **Ok** | Indefinido | Indefinido | Indefinido |
| **Cadeia de caracteres** | Indefinido | Indefinido | Indefinido | Indefinido | **Ok** | Indefinido | Indefinido |
| **Objeto** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | **Ok** | Indefinido |
| **Matriz** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | **Ok** |

Para operadores de comparação, como `>`, `>=`, `!=`, `<`, e `<=`, comparação em todos os tipos ou entre dois objetos ou matrizes produz `Undefined`.  

Se o resultado da expressão escalar `Undefined`, o item não está incluído no resultado, porque `Undefined` não é igual a `true`.

### <a name="logical-and-or-and-not-operators"></a>Operadores lógicos (AND, OR e NOT)

Operadores lógicos funcionam em valores boolianos. As tabelas a seguir mostram as tabelas de verdade lógicas desses operadores:

**Operador OU**

| OU | True | Falso | Indefinido |
| --- | --- | --- | --- |
| True |True |True |True |
| Falso |True |Falso |Indefinido |
| Indefinido |True |Indefinido |Indefinido |

**E o operador**

| E | True | Falso | Indefinido |
| --- | --- | --- | --- |
| True |True |Falso |Indefinido |
| Falso |Falso |Falso |Falso |
| Indefinido |Indefinido |Falso |Indefinido |

**NÃO operador**

| NÃO |  |
| --- | --- |
| True |Falso |
| Falso |True |
| Indefinido |Indefinido |

## <a name="between-keyword"></a>Palavra-chave BETWEEN

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

## <a name="in-keyword"></a>Palavra-chave IN

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

## <a name="-operator"></a>* operador

O operador especial * projetos todo o item como está. Quando usado, ele deve ser o único campo projetado. Uma consulta como `SELECT * FROM Families f` é válido, mas `SELECT VALUE * FROM Families f` e `SELECT *, f.id FROM Families f` não são válidos. O [primeiro consultar esse artigo](#query-the-json-items) usado o * operador. 

## <a name="-and--operators"></a>? e?? Operadores

Você pode usar o Ternário (?) e de união (?) operadores para construir expressões condicionais, como na programação de linguagens, como C# e JavaScript. 

Você pode usar o? operador para construir novas propriedades JSON em tempo real. Por exemplo, a consulta a seguir classifica os níveis de nível empresarial em `elementary` ou `other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Você também pode aninhar chamadas para o? operador, como a consulta a seguir: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Assim como acontece com outros operadores de consulta, o? operador exclui itens se as propriedades de referência estão ausentes ou os tipos que estão sendo comparados forem diferentes.

Use a?? operador para verificar uma propriedade em um item com eficiência ao consultar em relação aos dados semi-estruturados ou tipo misto. Por exemplo, a seguinte consulta retorna `lastName` se estiver presente, ou `surname` se `lastName` não estiver presente.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="TopKeyword"></a>Operador TOP

A palavra-chave TOP retorna o primeiro `N` número de resultados da consulta em uma ordem indefinida. Como prática recomendada, usar TOP com a cláusula ORDER BY para limitar os resultados para o primeiro `N` número de valores ordenados. Combinar essa duas cláusulas a seguir é a única maneira de indicar de modo previsível quais linhas superior afeta. 

Você pode usar TOP com um valor constante, como no exemplo a seguir, ou com um valor da variável usando consultas parametrizadas. Para obter mais informações, consulte o [consultas parametrizadas](#parameterized-queries) seção.

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

## <a id="OrderByClause"></a>Cláusula ORDER BY

Como no ANSI SQL, você pode incluir uma cláusula opcional ORDER BY em consultas. O argumento ASC ou DESC opcional especifica se é necessário recuperar os resultados em ordem crescente ou decrescente. ASC é o padrão.

Por exemplo, aqui está uma consulta que recupera famílias em ordem crescente de nome da cidade do residente:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Os resultados são:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

A consulta a seguir recupera a família `id`s na ordem de sua data de criação do item. Item `creationDate` é um número que representa o *época*, ou o tempo decorrido desde 1 de janeiro de 1970, em segundos.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Os resultados são:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```
## <a name="scalar-expressions"></a>Expressões escalares

A cláusula SELECT dá suporte a expressões escalares como constantes, expressões aritméticas e expressões lógicas. A consulta a seguir usa uma expressão escalar:


```sql
    SELECT ((2 + 11 % 7)-2)/3
```

Os resultados são:

```json
    [{
      "$1": 1.33333
    }]
```

Na consulta a seguir, o resultado da expressão escalar é um valor booliano:


```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

Os resultados são:

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="object-and-array-creation"></a>Criação de objeto e de matriz

Um recurso-chave de API do SQL é a criação de matriz e objeto. O exemplo anterior criou um novo objeto JSON, `AreFromSameCityState`. Também é possível construir matrizes, conforme mostrado no exemplo a seguir:


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


## <a id="Iteration"></a>Iteração

A API do SQL fornece suporte para a iteração em matrizes JSON, com uma nova construção de adicionadas por meio de palavra-chave IN na fonte de FROM. No exemplo a seguir:

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

A próxima consulta que realiza a iteração em `children` no `Families` contêiner. A matriz de saída é diferente da consulta anterior. Este exemplo divide `children`e mescla os resultados em uma única matriz:  

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

Você pode filtrar ainda mais cada entrada individual da matriz, conforme mostrado no exemplo a seguir:

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

Você também pode agregar o resultado de uma iteração de matriz. Por exemplo, a consulta a seguir conta o número de filhos entre todas as famílias de:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Os resultados são:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a id="Joins"></a>Junções

Em um banco de dados relacional, junções entre tabelas são o padrão lógico para criar esquemas normalizados. Em contraste, a API do SQL usa o modelo de dados desnormalizados de itens sem esquema, que é a lógica equivalente de um *autojunção*.

A linguagem dá suporte a sintaxe `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Esta consulta retorna um conjunto de tuplas com `N` valores. Cada tupla possui valores produzidos pela iteração de todos os aliases de contêiner sobre seus respectivos conjuntos. Em outras palavras, essa consulta cria o produto do cruzamento completo dos conjuntos que participam da junção.

Os exemplos a seguir mostram como a cláusula junção funciona. No exemplo a seguir, o resultado é vazio, porque o produto cruzado de cada item de origem e um conjunto vazio é vazio:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

O resultado é:

```json
    [{
    }]
```

No exemplo a seguir, a junção é um produto cruzado entre dois objetos JSON, a raiz do item `id` e o `children` sub-raiz. O fato de que `children` é uma matriz não é eficaz na junção, como ele lida com uma única raiz que é o `children` matriz. O resultado contém apenas dois resultados, porque o produto cruzado de cada item com a matriz produz exatamente apenas um item.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

Os resultados são:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

O exemplo a seguir mostra uma junção mais convencional:

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

Os resultados são:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

A fonte FROM da cláusula de junção é um iterador. Portanto, o fluxo no exemplo anterior é:  

1. Expanda cada elemento filho `c` na matriz.
2. Aplique um produto cruzado com a raiz do item `f` com cada elemento filho `c` que a primeira etapa nivelado.
3. Por fim, o objeto raiz do projeto `f` `id` propriedade sozinha.

O primeiro item, `AndersenFamily`, contém apenas um `children` elemento, portanto, o conjunto de resultados contém apenas um único objeto. O segundo item, `WakefieldFamily`, contém dois `children`, portanto, o produto cruzado produz dois objetos, um para cada `children` elemento. Os campos raiz em ambos os itens são os mesmos, exatamente como você esperaria em um produto cruzado.

A utilidade real da cláusula de junção é formar tuplas do produto cruzado em uma forma que é difícil ao projeto. O exemplo a seguir filtros na combinação de uma tupla que permite que o usuário escolha uma condição satisfeita pelas tuplas de modo geral.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

Os resultados são:

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

A seguinte extensão do exemplo anterior realiza uma junção dupla. Você pode exibir o produto cruzado como o pseudocódigo a seguir:

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily` tem um filho que tem um animal de estimação, portanto, o produto cruzado traz uma linha (1\*1\*1) desta família. `WakefieldFamily` tem dois filhos, apenas um dos quais tem animais de estimação, mas esse filho tem dois animais de estimação. O produto cruzado para essa família resulta em 1\*1\*2 = 2 linhas.

No exemplo a seguir, há um filtro adicional no `pet`, que exclui todas as tuplas em que o nome do Animal não é `Shadow`. Você pode criar tuplas de matrizes, filtrar qualquer um dos elementos da tupla e qualquer combinação dos elementos do projeto.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

Os resultados são:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a id="UserDefinedFunctions"></a>Funções definidas pelo usuário (UDFs)

A API do SQL fornece suporte para funções definidas pelo usuário (UDFs). Com UDFs escalares, você pode passar zero ou muitos argumentos e retornar um resultado único argumento. A API verifica cada argumento para valores JSON legais.  

A API estende a sintaxe SQL para dar suporte à lógica de aplicativo personalizado usando UDFs. Você pode registrar as UDFs com a API do SQL e referenciá-los em consultas SQL. Na verdade, as UDFs são projetadas de maneira especial para serem chamadas por consultas. Como resultado, as UDFs não tem acesso ao objeto de contexto, como outros tipos de JavaScript, como procedimentos armazenados e gatilhos. Consultas são somente leitura e podem executar em réplicas primárias ou secundárias. UDFs, ao contrário de outros tipos de JavaScript, são projetados para funcionar em réplicas secundárias.

O exemplo a seguir registra um UDF em um contêiner de itens no banco de dados do Cosmos DB. O exemplo cria um UDF cujo nome é `REGEX_MATCH`. Ele aceita dois valores de cadeia de caracteres JSON, `input` e `pattern`, e verifica se as correspondências primeiro o padrão especificado no segundo uso de JavaScript `string.match()` função.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                       return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Agora, use esta UDF em uma projeção de consulta. Você deve qualificar UDFs com o prefixo que diferencia maiusculas de minúsculas `udf.` ao chamá-las de dentro de consultas.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Os resultados são:

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

Você pode usar a UDF qualificada com o `udf.` prefixo dentro de um filtro, como no exemplo a seguir:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Os resultados são:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

Em essência, as UDFs são expressões escalares válidas que você pode usar em projeções e filtros.

Para expandir o poder das UDFs, ver outro exemplo com lógica condicional:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

O exemplo a seguir aplica a UDF:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Os resultados são:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Se as propriedades referidas aos UDFs parâmetros não estão disponíveis no valor JSON, o parâmetro é considerado indefinido e a invocação das UDFs é ignorada. Da mesma forma, se o resultado das UDFs for indefinido, ele não está incluído no resultado.

Como mostram os exemplos anteriores, as UDFs integram o poder da linguagem JavaScript com a API do SQL. UDFs fornecem uma rica interface programável para complexas realizar procedimentos condicional lógicas com a Ajuda de recursos internos de tempo de execução do JavaScript. A API do SQL fornece os argumentos às UDFs para cada item de origem na cláusula SELECT ou onde atual estágio do processamento. O resultado é incorporado perfeitamente no pipeline de execução geral. Em resumo, as UDFs são ótimas ferramentas para realizar a lógica de negócios complexos como parte das consultas.

## <a id="Aggregates"></a>Funções de agregação

Funções agregadas executam um cálculo em um conjunto de valores na cláusula SELECT e retornam um único valor. Por exemplo, a consulta a seguir retorna a contagem de itens dentro de `Families` contêiner:

```sql
    SELECT COUNT(1)
    FROM Families f
```

Os resultados são:

```json
    [{
        "$1": 2
    }]
```

Você também pode retornar apenas o valor escalar da agregação usando a palavra-chave do valor. Por exemplo, a consulta a seguir retorna a contagem de valores como um único número:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Os resultados são:

```json
    [ 2 ]
```

Você também pode combinar as agregações com filtros. Por exemplo, a consulta a seguir retorna a contagem de itens com o estado do endereço do `WA`.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Os resultados são:

```json
    [ 1 ]
```

A API de SQL suporta as seguintes funções de agregação. SUM e AVG operam em valores numéricos e COUNT, MIN e MAX trabalham em números, cadeias de caracteres, boolianos e nulos.

| Função | DESCRIÇÃO |
|-------|-------------|
| COUNT | Retorna o número de itens na expressão. |
| SUM   | Retorna a soma de todos os valores na expressão. |
| MÍN.   | Retorna o valor mínimo na expressão. |
| MÁX.   | Retorna o valor máximo na expressão. |
| AVG   | Retorna a média dos valores na expressão. |

Você também pode agregar os resultados de uma iteração de matriz. Para obter mais informações, consulte o [iteração](#Iteration) seção.

> [!NOTE]
> No Gerenciador de dados do portal do Azure, as consultas de agregação podem agregar resultados parciais em página de apenas uma consulta. O SDK produz um único valor cumulativo em todas as páginas. Para executar consultas de agregação usando o código, você precisa do .NET SDK 1.12.0, .NET Core SDK 1.1.0 ou Java SDK 1.9.5 ou posterior.
>

## <a id="BuiltinFunctions"></a>Funções internas

O cosmos DB também dá suporte a um número de funções internas para operações comuns, que pode ser usado em consultas como as funções definidas pelo usuário (UDFs).

| Grupo de funções | Operações |
|---------|----------|
| Funções matemáticas | ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN, TAN |
| Funções de verificação de tipo | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Funções de cadeia de caracteres | CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING, UPPER |
| Funções de matriz | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH e ARRAY_SLICE |
| Funções espaciais | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Se você estiver usando uma função definida pelo usuário (UDF) para o qual uma função interna agora está disponível, a função interna correspondente será executado mais rápido e mais eficiente.

A principal diferença entre as funções de Cosmos DB e ANSI SQL é que funções do Cosmos DB foram projetadas para funcionar bem com dados sem esquema e de esquema misto. Por exemplo, se uma propriedade está ausente ou tem um valor não numérico como `unknown`, o item será ignorado em vez de retornar um erro.

### <a name="mathematical-functions"></a>Funções matemáticas

As funções matemáticas executam um cálculo, com base em valores de entrada fornecidos como argumentos e retornam um valor numérico. Aqui está uma tabela de funções matemáticas internas com suporte.

| Uso | DESCRIÇÃO |
|----------|--------|
| ABS (num_expr) | Retorna o valor absoluto (positivo) da expressão numérica especificada. |
| CEILING (num_expr) | Retorna o menor valor de número inteiro maior ou igual à expressão numérica especificada. |
| FLOOR (num_expr) | Retorna o maior inteiro menor ou igual à expressão numérica especificada. |
| EXP (num_expr) | Retorna o expoente da expressão numérica especificada. |
| LOG (num_expr, base) | Retorna o logaritmo natural da expressão numérica especificada ou o logaritmo usando a base especificada. |
| LOG10 (num_expr) | Retorna o valor logarítmico de base 10 da expressão numérica especificada. |
| ROUND (num_expr) | Retorna um valor numérico, arredondado para o valor inteiro mais próximo. |
| TRUNC (num_expr) | Retorna um valor numérico, truncado para o valor inteiro mais próximo. |
| SQRT (num_expr) | Retorna a raiz quadrada de expressão numérica especificada. |
| SQUARE (num_expr) | Retorna o quadrado de expressão numérica especificada. |
| POWER (num_expr, num_expr) | Retorna a potência da expressão numérica especificada para o valor especificado. |
| SIGN (num_expr) | Retorna o valor de entrada (-1, 0, 1) da expressão numérica especificada. |
| ACOS (expr_num) | Retorna o ângulo, em radianos, cujo cosseno é a expressão numérica especificada (também chamado de arco cosseno). |
| ASIN (expr_num) | Retorna o ângulo, em radianos, cujo seno é a expressão numérica especificada. Essa função também é chamada de arco seno. |
| ATAN (expr_num) | Retorna o ângulo, em radianos, cuja tangente é a expressão numérica especificada. Essa função também é chamada o arco tangente. |
| ATN2 (expr_num) | Retorna o ângulo, em radianos, entre o eixo x positivo e o raio da origem até o ponto (x, y), em que x e y são os valores de duas expressões flutuantes especificadas. |
| COS (expr_num) | Retorna o cosseno trigonométrico do ângulo especificado, em radianos, na expressão especificada. |
| COT (expr_num) | Retorna a cotangente trigonométrica do ângulo especificado, em radianos, na expressão numérica especificada. |
| DEGREES (expr_num) | Retorna o ângulo correspondente, em graus, para um ângulo especificado em radianos. |
| PI () | Retorna o valor constante de PI. |
| RADIANS (expr_num) | Retorna radianos quando uma expressão numérica é inserida em graus. |
| SIN (expr_num) | Retorna o seno trigonométrico do ângulo especificado, em radianos, na expressão especificada. |
| TAN (expr_num) | Retorna a tangente da expressão de entrada, na expressão especificada. |

Você pode executar consultas como o exemplo a seguir:

```sql
    SELECT VALUE ABS(-4)
```

O resultado é:

```json
    [4]
```

### <a name="type-checking-functions"></a>Funções de verificação de tipo

As funções de verificação de tipo permitem que você verifique o tipo de uma expressão dentro de uma consulta SQL. Você pode usar funções de verificação de tipo para determinar os tipos de propriedades dentro dos itens em um piscar de olhos, quando eles são desconhecido ou variável. Aqui está uma tabela de funções de verificação do tipo de internas com suporte:

| **Uso** | **Descrição** |
|-----------|------------|
| [IS_ARRAY (expr)](sql-api-query-reference.md#bk_is_array) | Retorna um booliano indicando se o tipo do valor é uma matriz. |
| [IS_BOOL (expr)](sql-api-query-reference.md#bk_is_bool) | Retorna um booliano indicando se o tipo do valor é um booliano. |
| [IS_NULL (expr)](sql-api-query-reference.md#bk_is_null) | Retorna um booliano indicando se o tipo do valor é nulo. |
| [IS_NUMBER (expr)](sql-api-query-reference.md#bk_is_number) | Retorna um booliano indicando se o tipo do valor é um número. |
| [IS_OBJECT (expr)](sql-api-query-reference.md#bk_is_object) | Retorna um booliano indicando se o tipo do valor é um objeto JSON. |
| [IS_STRING (expr)](sql-api-query-reference.md#bk_is_string) | Retorna um booliano indicando se o tipo do valor é uma cadeia de caracteres. |
| [IS_DEFINED (expr)](sql-api-query-reference.md#bk_is_defined) | Retorna um valor booliano que indica se um valor foi atribuído à propriedade. |
| [IS_PRIMITIVE (expr)](sql-api-query-reference.md#bk_is_primitive) | Retorna um valor booliano que indica se o tipo do valor é uma cadeia de caracteres, número, booliano ou null. |

Usando essas funções, você pode executar consultas como o exemplo a seguir:

```sql
    SELECT VALUE IS_NUMBER(-4)
```

O resultado é:

```json
    [true]
```

### <a name="string-functions"></a>Funções de cadeia de caracteres

As funções escalares a seguir executam uma operação em um valor de cadeia de caracteres de entrada e retornam um valor de cadeia de caracteres, numéricos ou booleanos. Aqui temos uma tabela de funções de cadeia de caracteres internas:

| Uso | DESCRIÇÃO |
| --- | --- |
| [LENGTH (str_expr)](sql-api-query-reference.md#bk_length) | Retorna o número de caracteres da expressão de cadeia de caracteres especificada. |
| [CONCAT (str_expr, str_expr [, str_expr])](sql-api-query-reference.md#bk_concat) | Retorna uma cadeia de caracteres que é o resultado da concatenação de dois ou mais valores de cadeia de caracteres. |
| [SUBSTRING (str_expr, num_expr, num_expr)](sql-api-query-reference.md#bk_substring) | Retorna parte de uma expressão de cadeia de caracteres. |
| [STARTSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_startswith) | Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres começa com a segunda. |
| [ENDSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_endswith) | Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres termina com a segunda. |
| [CONTAINS (str_expr, str_expr)](sql-api-query-reference.md#bk_contains) | Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres contém a segunda. |
| [INDEX_OF (str_expr, str_expr)](sql-api-query-reference.md#bk_index_of) | Retorna a posição inicial da primeira ocorrência da segunda cadeia de expressão dentro da primeira expressão de cadeia de caracteres especificada ou -1 se a cadeia de caracteres não for encontrada. |
| [LEFT (str_expr, num_expr)](sql-api-query-reference.md#bk_left) | Retorna a parte esquerda de uma cadeia de caracteres com o número especificado de caracteres. |
| [RIGHT (str_expr, num_expr)](sql-api-query-reference.md#bk_right) | Retorna a parte direita de uma cadeia de caracteres com o número especificado de caracteres. |
| [LTRIM (str_expr)](sql-api-query-reference.md#bk_ltrim) | Retorna uma expressão de cadeia de caracteres após remover os espaços em branco iniciais. |
| [RTRIM (str_expr)](sql-api-query-reference.md#bk_rtrim) | Retorna uma expressão de cadeia de caracteres após truncar todos os espaços em branco finais. |
| [LOWER (str_expr)](sql-api-query-reference.md#bk_lower) | Retorna uma expressão de cadeia de caracteres depois de converter dados de caracteres maiúsculos em minúsculos. |
| [UPPER (str_expr)](sql-api-query-reference.md#bk_upper) | Retorna uma expressão de cadeia de caracteres depois de converter dados de caracteres minúsculos em maiúsculos. |
| [REPLACE (str_expr, str_expr, str_expr)](sql-api-query-reference.md#bk_replace) | Substitui todas as ocorrências de um valor de cadeia de caracteres especificado por outro valor de cadeia de caracteres. |
| [REPLICATE (str_expr, num_expr)](sql-api-query-reference.md#bk_replicate) | Repete um valor de cadeia de caracteres por um número de vezes especificado. |
| [REVERSE (str_expr)](sql-api-query-reference.md#bk_reverse) | Retorna a ordem inversa de um valor de cadeia de caracteres. |

Usando essas funções, você pode executar consultas como a seguir, que retorna a família `id` em letras maiusculas:

```sql
    SELECT VALUE UPPER(Families.id)
    FROM Families
```

Os resultados são:

```json
    [
        "WAKEFIELDFAMILY",
        "ANDERSENFAMILY"
    ]
```

Ou concatenar cadeias de caracteres, como neste exemplo:

```sql
    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families
```

Os resultados são:

```json
    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "Seattle,WA"
    }]
```

Você também pode usar funções de cadeia de caracteres na cláusula WHERE para filtrar os resultados, como no exemplo a seguir:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")
```

Os resultados são:

```json
    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]
```

### <a name="array-functions"></a>Funções de matriz

As funções escalares a seguir executam uma operação em um valor de matriz de entrada e retornam um numérico, booliano ou valor de matriz. Aqui temos uma tabela de funções de matriz internas:

| Uso | DESCRIÇÃO |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](sql-api-query-reference.md#bk_array_length) |Retorna o número de elementos da expressão de matriz especificada. |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](sql-api-query-reference.md#bk_array_concat) |Retorna uma matriz que é o resultado da concatenação de dois ou mais valores de matriz. |
| [ARRAY_CONTAINS (arr_expr, expr [, bool_expr])](sql-api-query-reference.md#bk_array_contains) |Retorna um valor booliano que indica se a matriz contém o valor especificado. Pode especificar se a correspondência é completa ou parcial. |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](sql-api-query-reference.md#bk_array_slice) |Retorna parte de uma expressão de matriz. |

Use funções de matriz para manipular matrizes contidas no JSON. Por exemplo, aqui está uma consulta que retorna todos os itens `id`s em que um dos `parents` é `Robin Wakefield`: 

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })
```

O resultado é:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Você pode especificar um fragmento parcial para elementos correspondentes dentro da matriz. A consulta a seguir localiza todos os itens `id`s que possuem `parents` com o `givenName` de `Robin`:

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)
```

O resultado é:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Aqui está outro exemplo que usa ARRAY_LENGTH para obter o número de `children` por família:

```sql
    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 
```

Os resultados são:

```json
    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]
```

### <a name="spatial-functions"></a>Funções espaciais

O cosmos DB suporta as seguintes funções internas do Open Geospatial Consortium (OGC) para consultas geoespaciais: 

| Uso | DESCRIÇÃO |
| --- | --- |
| ST_DISTANCE (point_expr, point_expr) | Retorna a distância entre os dois GeoJSON `Point`, `Polygon`, ou `LineString` expressões. |
| T_WITHIN (point_expr, polygon_expr) | Retorna uma expressão booleana que indica se o primeiro objeto GeoJSON (`Point`, `Polygon`, ou `LineString`) está dentro do segundo objeto GeoJSON (`Point`, `Polygon`, ou `LineString`). |
| ST_INTERSECTS (spatial_expr, spatial_expr) | Retorna uma expressão booleana que indica se os dois especificados objetos GeoJSON (`Point`, `Polygon`, ou `LineString`) se cruzam. |
| ST_ISVALID | Retorna um valor booliano que indica se o GeoJSON especificado `Point`, `Polygon`, ou `LineString` expressão é válida. |
| ST_ISVALIDDETAILED | Retorna um valor JSON que contém um valor booliano, se especificado GeoJSON `Point`, `Polygon`, ou `LineString` expressão é válida e se for inválida, o motivo como um valor de cadeia de caracteres. |

Você pode usar funções espaciais para executar consultas de proximidade em dados espaciais. Por exemplo, aqui está uma consulta que retorna todos os itens de família que estejam dentro de 30 km de um local especificado usando a função interna ST_DISTANCE:

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

O resultado é:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Para obter informações sobre o suporte geoespacial no Cosmos DB, consulte [Trabalhando com dados geoespaciais no Azure Cosmos DB](geospatial.md). 

## <a name="parameterized-queries"></a>Consultas parametrizadas

O cosmos DB dá suporte a consultas com parâmetros expressados pela familiar notação @. SQL parametrizado fornece tratamento robusto e escape da entrada do usuário e evita a exposição acidental de dados por meio de injeção de SQL.

Por exemplo, você pode escrever uma consulta que demora `lastName` e `address.state` como parâmetros e executá-lo para vários valores de `lastName` e `address.state` com base na entrada do usuário.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Você pode enviar essa solicitação ao Cosmos DB como uma consulta JSON parametrizada semelhante ao seguinte:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

O exemplo a seguir define o argumento TOP com uma consulta parametrizada: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Valores de parâmetro podem ser qualquer JSON válido: cadeias de caracteres, números, boolianos, nulos, ou mesmo matrizes e JSON aninhado. Como o Cosmos DB é sem esquema, os parâmetros não são validados em relação a qualquer tipo.

## <a id="JavaScriptIntegration"></a>Integração do JavaScript

O Azure Cosmos DB fornece um modelo de programação para executar a lógica do aplicativo com base em JavaScript diretamente em contêineres, usando procedimentos armazenados e gatilhos. Esse modelo oferece suporte a:

* Operações CRUD transacionais de alto desempenho e consultas em relação a itens em um contêiner, em virtude da profunda integração do tempo de execução de JavaScript dentro do mecanismo de banco de dados.
* Um Modelamento natural de fluxo de controle, variáveis de escopo e atribuição e integração de primitivos de tratamento de exceções com transações de banco de dados. 

Para obter mais informações sobre a integração do JavaScript do Azure Cosmos DB, consulte a [API JavaScript do lado do servidor](#JavaScriptServerSideApi) seção.

### <a name="operator-evaluation"></a>Avaliação de operador

Cosmos DB, devido à presença de um banco de dados JSON, tem paralelos com operadores JavaScript e semântica de avaliação. O cosmos DB tenta preservar a semântica do JavaScript em termos de suporte do JSON, mas a avaliação da operação desvia em alguns casos.

Na API do SQL, ao contrário no SQL tradicional, os tipos de valores são geralmente não conhecidos até que a API recupera os valores do banco de dados. Para executar consultas com eficiência, a maioria dos operadores tem requisitos restritos de tipo.

Ao contrário do JavaScript, a API do SQL não realiza conversões implícitas. Por exemplo, uma consulta como `SELECT * FROM Person p WHERE p.Age = 21` corresponde a itens que contêm um `Age` propriedade cujo valor é `21`. Ele não corresponder a nenhum outro item cuja `Age` possivelmente infinita de variações como corresponde a propriedade `twenty-one`, `021`, ou `21.0`. Isso contrasta com JavaScript, onde os valores de cadeia de caracteres são convertidos implicitamente em números com base em operador, por exemplo: `==`. Esse comportamento da API do SQL é crucial para correspondência de índice eficiente.

## <a id="ExecutingSqlQueries"></a>Execução de consulta SQL

Qualquer linguagem capacitada para fazer solicitações HTTP/HTTPS pode chamar a API REST do Cosmos DB. O cosmos DB também oferece bibliotecas de programação para linguagens de programação .NET, Node. js, JavaScript e Python. A API REST e bibliotecas dão suporte a consultas por meio do SQL, e o SDK do .NET também suporta [consulta LINQ](#Linq).

Os exemplos a seguir mostram como criar uma consulta e enviá-la a uma conta de banco de dados do Cosmos DB.

### <a id="RestAPI"></a>API REST

O Cosmos DB oferece um modelo de programação RESTful por HTTP. O modelo de recurso consiste em um conjunto de recursos em uma conta de banco de dados, que provisiona uma assinatura do Azure. A conta de banco de dados consiste em um conjunto de *bancos de dados*, cada um deles pode conter vários *contêineres*, que por sua vez contêm *itens*, UDFs e outros tipos de recursos. Cada recurso do Cosmos DB é acessível usando um URI lógico e estável. Um conjunto de recursos é chamado um *feed*. 

O modelo de interação básico com esses recursos é por meio dos verbos HTTP `GET`, `PUT`, `POST`, e `DELETE`, com suas interpretações padrão. Use `POST` para criar um novo recurso, executar um procedimento armazenado ou emitir uma consulta do Cosmos DB. As consultas sempre são operações somente leitura, sem efeitos colaterais.

Os exemplos a seguir mostram um `POST` para uma consulta de API do SQL em relação a itens de exemplo. A consulta tem um filtro simples no JSON `name` propriedade. O `x-ms-documentdb-isquery` e Content-Type: `application/query+json` cabeçalhos indicam que a operação é uma consulta. Substitua `mysqlapicosmosdb.documents.azure.com:443` com o URI para sua conta do Cosmos DB.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

Os resultados são:

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

A consulta em seguida, mais complexa retorna vários resultados de uma junção:

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": [] 
    }
```

Os resultados são: 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

Se os resultados de uma consulta pode não caberem em uma única página, a API REST retorna um token de continuação por meio de `x-ms-continuation-token` cabeçalho de resposta. Os clientes podem paginar os resultados incluindo o cabeçalho nos resultados subsequentes. Você também pode controlar o número de resultados por página por meio de `x-ms-max-item-count` cabeçalho do número. 

Se uma consulta tem uma função de agregação como contagem, a página de consulta pode retornar um valor parcialmente agregado em apenas uma página de resultados. Os clientes devem executar uma segunda agregação nesses resultados para produzir os resultados finais. Por exemplo, soma das contagens retornadas nas páginas individuais para retornar a contagem total.

Para gerenciar a política de consistência de dados para consultas, use o `x-ms-consistency-level` cabeçalho como em todas as solicitações da API REST. Consistência de sessão também requer ecoar a versão mais recente `x-ms-session-token` cabeçalho de cookie na solicitação de consulta. A política de indexação do contêiner consultado também pode influenciar a consistência dos resultados da consulta. Com as configurações de política para contêineres de indexação padrão, o índice sempre é atualizado com o conteúdo do item e os resultados da consulta correspondem à consistência escolhida para os dados. Para obter mais informações, consulte [níveis de consistência do Azure Cosmos DB][consistency-levels].

Se a política de indexação configurada no contêiner não oferecer suporte a consulta especificada, o servidor do Azure Cosmos DB retornará 400 "solicitação incorreta". Retorna essa mensagem de erro para consultas com caminhos excluídos explicitamente da indexação. Você pode especificar o `x-ms-documentdb-query-enable-scan` cabeçalho para permitir que a consulta executar uma verificação quando um índice não está disponível.

Você pode obter métricas detalhadas na execução da consulta, definindo o `x-ms-documentdb-populatequerymetrics` cabeçalho para `true`. Para obter mais informações, consulte [Métricas de consulta SQL para o Azure Cosmos DB](sql-api-query-metrics.md).

### <a id="DotNetSdk"></a>C#(SDK DO .NET)

O SDK .NET suporta consultas LINQ e SQL. O exemplo a seguir mostra como executar a consulta de filtro anterior com o .NET:

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

O exemplo a seguir compara duas propriedades quanto à igualdade dentro de cada item e usa projeções anônimas.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

O exemplo seguinte mostra junções, expressadas por meio de LINQ `SelectMany`.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

O cliente .NET itera automaticamente em todas as páginas de resultados da consulta no `foreach` bloqueia, conforme mostrado no exemplo anterior. As opções de consulta introduzidas na [API REST](#RestAPI) seção também estão disponíveis no SDK do .NET, usando o `FeedOptions` e `FeedResponse` classes no `CreateDocumentQuery` método. Você pode controlar o número de páginas usando o `MaxItemCount` configuração.

Você também pode controlar explicitamente paginação criando `IDocumentQueryable` usando o `IQueryable` objeto, em seguida, lendo os `ResponseContinuationToken` valores e transmiti-los de volta como `RequestContinuationToken` em `FeedOptions`. Você pode definir `EnableScanInQuery` para permitir verificações quando a consulta não é compatível com a política de indexação configurada. Para contêineres particionados, você pode usar `PartitionKey` para executar a consulta em relação a uma única partição, embora o Azure Cosmos DB pode extraí-la do texto da consulta. Você pode usar `EnableCrossPartitionQuery` para executar consultas em várias partições.

Para obter mais exemplos de .NET com consultas, consulte o [amostras do .NET do Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet) no GitHub.

### <a id="JavaScriptServerSideApi"></a>API do lado servidor do JavaScript

O cosmos DB fornece um modelo de programação para executar lógica de aplicativo baseados em JavaScript diretamente em contêineres, usando procedimentos armazenados e gatilhos. A lógica de JavaScript registrada no nível do contêiner pode então emitir operações de banco de dados nos itens do contêiner determinado, encapsuladas em transações ACID ambientes.

O exemplo a seguir mostra como usar `queryDocuments` no servidor do JavaScript API para realizar consultas de dentro de procedimentos e gatilhos armazenados:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a id="Linq"></a>LINQ para a API do SQL

O LINQ é um modelo de programação do .NET que expressa a computação como consultas em fluxos de objeto. O Cosmos DB oferece uma biblioteca do lado do cliente para fazer interface com o LINQ ao facilitar a conversão entre objetos JSON e .NET e um mapeamento por meio de um subconjunto de consultas LINQ para consultas do Cosmos DB.

O diagrama a seguir mostra a arquitetura de suporte a consultas LINQ usando o Cosmos DB. Usando o cliente do Cosmos DB, você pode criar um `IQueryable` objeto diretamente consulta o provedor de consulta do Cosmos DB e converte a consulta do LINQ em uma consulta do Cosmos DB. Você, em seguida, passar a consulta para o servidor do Cosmos DB, que recupera um conjunto de resultados no formato JSON. O desserializador JSON converte os resultados em um fluxo de objetos .NET no lado do cliente.

![Arquitetura de suporte a consultas LINQ usando a API do SQL – sintaxe SQL, linguagem de consulta JSON, conceitos de banco de dados e consultas SQL][1]

### <a name="net-and-json-mapping"></a>Mapeamento de .NET e JSON

O mapeamento entre objetos .NET e itens JSON é natural. Cada campo de membro de dados é mapeado para um objeto JSON, em que o nome do campo é mapeado para o *chave* parte do objeto e o valor de forma recursiva é mapeado para o *valor* faz parte do objeto. O código a seguir mapeia os `Family` classe a um item de JSON e, em seguida, cria um `Family` objeto:

```csharp
    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };
```

O exemplo anterior cria o item JSON a seguir:

```json
    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                "givenName": "Jesse",
                "gender": "female",
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller",
              "givenName": "Lisa",
              "gender": "female",
              "grade": 8
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };
```

### <a name="linq-to-sql-translation"></a>Tradução de LINQ em SQL

O provedor de consultas do Cosmos DB realiza um mapeamento de melhor esforço de uma consulta LINQ para uma consulta SQL do Cosmos DB. A seguinte descrição pressupõe uma familiaridade básica com o LINQ.

O sistema de tipo de provedor de consulta dá suporte a apenas os tipos primitivos de JSON: numérico, booliano, cadeia de caracteres e nulos. 

O provedor de consulta dá suporte a expressões escalares a seguir:

- Valores de constantes, incluindo valores de constantes de tipos de dados primitivos no momento da avaliação de consulta.
  
- Expressões de índice de matriz/propriedade se refere à propriedade de um objeto ou um elemento de matriz. Por exemplo: 
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Expressões aritméticas, inclusive expressões aritméticas comuns em valores numéricos e boolianos. Para obter a lista completa, consulte o [especificação de SQL do Azure Cosmos DB](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Expressões de comparação de cadeia de caracteres, que incluem a comparação de um valor de cadeia de caracteres para algum valor de cadeia de caracteres constante.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Expressões de criação de objeto/matriz, que retornam um objeto do tipo de valor composto ou tipo anônimo ou uma matriz desses objetos. Você pode aninhar esses valores.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

### <a id="SupportedLinqOperators"></a>Operadores LINQ com suporte

O provedor LINQ incluído no SDK do .NET do SQL suporta os seguintes operadores:

- **Selecionar**: As projeções são convertidas para SQL SELECT, incluindo a construção do objeto.
- **Onde**: Filtros de traduzir para SQL WHERE e dão suporte à conversão entre `&&`, `||`, e `!` para os operadores SQL
- **SelectMany**: permite o desenrolamento de matrizes à cláusula SQL JOIN. Use para encadear ou aninhar expressões para filtrar elementos de matriz.
- **OrderBy** e **OrderByDescending**: Traduza para ORDER BY com ASC ou DESC.
- Operadores **Count**, **Sum**, **Min**, **Max** e **Average** para agregação e os seus equivalentes assíncronos, **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** e **AverageAsync**.
- **CompareTo**: é convertido em comparações de intervalo. Normalmente usado para cadeias de caracteres, desde que eles não são comparáveis no .NET.
- **Take**: Se traduz em SQL TOP para limitar os resultados de uma consulta.
- **Funções matemáticas**: Oferece suporte à conversão do .NET `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`, `Sign`, `Sin`, `Sqrt`, `Tan`, e `Truncate` para funções internas do SQL equivalentes.
- **Funções de cadeia de caracteres**: Oferece suporte à conversão do .NET `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd`, e `TrimStart` para funções internas do SQL equivalentes.
- **Funções de matriz**: Oferece suporte à conversão do .NET `Concat`, `Contains`, e `Count` para funções internas do SQL equivalentes.
- **Funções de extensão geoespacial**: Dá suporte à conversão dos métodos stub `Distance`, `IsValid`, `IsValidDetailed`, e `Within` para funções internas do SQL equivalentes.
- **Função de extensão de função definida pelo usuário**: Oferece suporte à conversão do método stub `UserDefinedFunctionProvider.Invoke` à função definida pelo usuário correspondente.
- **Diversos**: Oferece suporte à conversão de `Coalesce` e operadores condicionais. É possível converter a `Contains` para cadeia de caracteres CONTAINS, ARRAY_CONTAINS ou SQL IN, dependendo do contexto.

### <a name="sql-query-operators"></a>Operadores de consulta SQL

Os exemplos a seguir ilustram como alguns dos operadores de consulta LINQ padrão são convertidas para consultas do Cosmos DB.

#### <a name="select-operator"></a>Selecionar operador

A sintaxe é `input.Select(x => f(x))`, em que `f` é uma expressão escalar.

**Selecione o operador de exemplo 1:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Selecione o operador de exemplo 2:** 

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Selecione o operador de exemplo 3:**

- **Expressão lambda do LINQ**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

#### <a name="selectmany-operator"></a>Operador SelectMany

A sintaxe é `input.SelectMany(x => f(x))`, em que `f` é uma expressão escalar que retorna um tipo de contêiner.

- **Expressão lambda do LINQ**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

#### <a name="where-operator"></a>Operador Where

A sintaxe é `input.Where(x => f(x))`, em que `f` é uma expressão escalar que retorna um valor booliano.

**Em que operador, exemplo 1:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Em que operador, o exemplo 2:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

### <a name="composite-sql-queries"></a>Consultas SQL composta

Você pode compor os operadores anteriores para formar consultas mais poderosas. Como o Cosmos DB oferece suporte a contêineres aninhados, você pode concatenar ou aninhar a composição.

#### <a name="concatenation"></a>Concatenação

A sintaxe é `input(.|.SelectMany())(.Select()|.Where())*`. Uma consulta concatenada pode começar com um recurso opcional `SelectMany` consulta, seguida por múltiplos `Select` ou `Where` operadores.

**Concatenação, exemplo 1:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family=>family.parents[0])
          .Where(familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Concatenação, exemplo 2:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**Concatenação, exemplo 3:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**Concatenação, o exemplo 4:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

#### <a name="nesting"></a>Aninhamento

A sintaxe é `input.SelectMany(x=>x.Q())` onde `Q` é um `Select`, `SelectMany`, ou `Where` operador.

Uma consulta aninhada aplica-se a consulta interna para cada elemento do contêiner externo. Um recurso importante é que a consulta interna pode se referir aos campos dos elementos no contêiner externo, como uma autojunção.

**Aninhamento, exemplo 1:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**Aninhamento, o exemplo 2:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**Aninhamento, o exemplo 3:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```

## <a id="References"></a>Referências

- [Especificação do SQL no Azure Cosmos DB](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [Especificação de JavaScript](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 
- Graefe, Goetz. [Técnicas de avaliação para grandes bancos de dados de consulta](https://dl.acm.org/citation.cfm?id=152611). *ACM computação pesquisas* 25, nenhum. 2 (1993).
- Graefe, G. "A estrutura em cascata para otimização da consulta." *Eng IEEE dados. Bull.* 18, no. 3 (1995).
- Lu, Ooi, Tan. "Processamento de consultas em sistemas de banco de dados relacional paralelo." *IEEE Computer sociedade pressione* (1994).
- Olston, Christopher, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, and Andrew Tomkins. "Pig Latin: Uma linguagem não-para-externo para processamento de dados." *SIGMOD* (2008).

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Cosmos DB][introduction]
- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Níveis de consistência do Azure Cosmos DB][consistency-levels]

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
