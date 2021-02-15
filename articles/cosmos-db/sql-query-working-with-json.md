---
title: Trabalhando com JSON no Azure Cosmos DB
description: Saiba mais sobre como consultar e acessar propriedades JSON aninhadas e usar caracteres especiais no Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/19/2020
ms.author: tisande
ms.openlocfilehash: 9a9300db1adc3ff238c44887012400702690b0e8
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337822"
---
# <a name="working-with-json-in-azure-cosmos-db"></a>Trabalhando com JSON no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Na API do SQL (núcleo) do Azure Cosmos DB, os itens são armazenados como JSON. As expressões e sistema de tipos são restritos para lidar somente com tipos JSON. Para obter mais informações, consulte as [especificações do JSON](https://www.json.org/).

Vamos resumir alguns aspectos importantes do trabalho com JSON:

- Os objetos JSON sempre começam com uma `{` chave esquerda e terminam com uma `}` chave direita
- Você pode ter propriedades JSON [aninhadas](#nested-properties) entre si
- Os valores de propriedade JSON podem ser matrizes
- Os nomes de propriedade JSON diferenciam maiúsculas de minúsculas
- O nome de propriedade JSON pode ser qualquer valor de cadeia de caracteres (incluindo espaços ou caracteres que não sejam letras)

## <a name="nested-properties"></a>Propriedades aninhadas

Você pode acessar o JSON aninhado usando um acessador de ponto. Você pode usar Propriedades JSON aninhadas em suas consultas da mesma maneira que pode usar qualquer outra propriedade.

Aqui está um documento com JSON aninhado:

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Nesse caso, as propriedades `state`, `country`e `city` estão todas aninhadas dentro da propriedade `address`.

O exemplo a seguir projeta duas propriedades aninhadas: `f.address.state` e `f.address.city`.

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

## <a name="working-with-arrays"></a>Trabalhando com matrizes

Além das propriedades aninhadas, o JSON também oferece suporte a matrizes.

Aqui está um documento de exemplo com uma matriz:

```json
{
  "id": "WakefieldFamily",
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8
      }
  ],
}
```

Ao trabalhar com matrizes, você pode acessar um elemento específico dentro da matriz referenciando sua posição:

```sql
SELECT *
FROM Families f
WHERE f.children[0].givenName = "Jesse"
```

Na maioria dos casos, no entanto, você usará uma [consulta aninhada](sql-query-subquery.md) ou [autojunção](sql-query-join.md) ao trabalhar com matrizes.

Por exemplo, aqui está um documento que mostra o saldo diário da conta bancária de um cliente.

```json
{
  "id": "Contoso-Checking-Account-2020",
  "balance": [
      {
        "checkingAccount": 1000,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": 100,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": -10,
        "savingsAccount": 5000,
      },
      {
        "checkingAccount": 5000,
        "savingsAccount": 5000,
      }
         ...
  ]
}
```

Se você quisesse executar uma consulta mostrando todos os clientes com saldo negativo em algum momento, você poderia usar [EXISTS](sql-query-subquery.md#exists-expression) com uma consulta aninhada:

```sql
SELECT c.id
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.balance
    WHERE n.checkingAccount < 0
)
```

## <a name="difference-between-null-and-undefined"></a>Diferença entre nulo e indefinido

Se uma propriedade não estiver definida em um item, seu valor será `undefined` . Uma propriedade com o valor `null` deve ser explicitamente definida e atribuir um `null` valor.

Por exemplo, considere este item de exemplo:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": null
}
```

Neste exemplo, a propriedade `isRegistered` tem um valor de `undefined` porque ela é omitida do item. A propriedade `creationDate` tem um `null` valor.

O Azure Cosmos DB dá suporte a duas funções de sistema de verificação de tipo úteis para `null` `undefined` Propriedades e:

* [Is_null](sql-query-is-null.md) -verifica se um valor de propriedade é `null`
* [IS_DEFINED](sql-query-is-defined.md) -verifica se um valor de propriedade está definido

Você pode aprender sobre os [operadores com suporte](sql-query-operators.md) e seus comportamentos `null` e `undefined` valores.

## <a name="reserved-keywords-and-special-characters-in-json"></a>Palavras-chave reservadas e caracteres especiais em JSON

Você pode acessar propriedades usando o operador de propriedade entre aspas `[]`. Por exemplo: `SELECT c.grade` and `SELECT c["grade"]` são equivalentes. Essa sintaxe é útil para substituir uma propriedade que contém espaços, caracteres especiais ou compartilha o mesmo nome que uma palavra-chave ou palavra reservada SQL.

Por exemplo, aqui está um documento com uma propriedade chamada `order` e uma propriedade `price($)` que contém caracteres especiais:

```json
{
  "id": "AndersenFamily",
  "order": {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
   },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Se você executar uma consulta que inclui a propriedade `order` ou a propriedade `price($)`, obterá um erro de sintaxe.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```

```sql
SELECT * FROM c where c.order.price($) > 50
```

O resultado é:

`
Syntax error, incorrect syntax near 'order'
`

Você deve reescrever as mesmas consultas abaixo:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="json-expressions"></a>Expressões JSON

A projeção também oferece suporte para expressões JSON, conforme mostrado no exemplo a seguir:

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

No exemplo anterior, a cláusula `SELECT` precisa criar um objeto JSON e, como o exemplo não fornece nenhuma chave, a cláusula usa o nome da variável de argumento implícito `$1`. A consulta retorna duas variáveis de argumento implícito: `$1` e `$2`.

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

## <a name="aliasing"></a>Atribuição de alias

Você pode usar explicitamente alias de valores em consultas. Caso uma consulta tenha duas propriedades com o mesmo nome, use a atribuição para renomear uma ou as duas propriedades para que elas não sejam ambíguas no resultado projetado.

### <a name="examples"></a>Exemplos

A palavra-chave `AS` usada para atribuição de alias é opcional, conforme mostrado no exemplo a seguir, ao projetar o segundo valor como `NameInfo`:

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

### <a name="aliasing-with-reserved-keywords-or-special-characters"></a>Atribuição de alias com palavras-chave reservadas ou caracteres especiais

Você não pode usar a atribuição de alias para projetar um valor como um nome de propriedade com um espaço, um caractere especial ou uma palavra reservada. Se você quisesse alterar a projeção de um valor para, por exemplo, ter um nome de propriedade com um espaço, poderia usar uma [expressão JSON](#json-expressions).

Aqui está um exemplo:

```sql
    SELECT
           {"JSON expression with a space": { "state": f.address.state, "city": f.address.city }},
           {"JSON expression with a special character!": { "name": f.id }}
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

## <a name="next-steps"></a>Próximas etapas

- [Guia de Introdução](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [Cláusula WHERE](sql-query-where.md)
