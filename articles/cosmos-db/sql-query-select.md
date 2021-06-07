---
title: Cláusula SELECT no Azure Cosmos DB
description: Saiba mais sobre a cláusula SQL SELECT para Azure Cosmos DB. Use SQL como uma linguagem de consulta JSON Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: 072e17b1c0ea312b4adfa1687e447fd2cadde233
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93335408"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Cláusula SELECT no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Cada consulta consiste em uma `SELECT` cláusula e em [](sql-query-from.md) cláusulas opcional e [Where](sql-query-where.md) , por padrões SQL ANSI. Normalmente, a origem na `FROM` cláusula é enumerada e a `WHERE` cláusula aplica um filtro na origem para recuperar um subconjunto de itens JSON. `SELECT`Em seguida, a cláusula projeta os valores JSON solicitados na lista de seleção.

## <a name="syntax"></a>Sintaxe

```sql
SELECT <select_specification>  

<select_specification> ::=
      '*'
      | [DISTINCT] <object_property_list>
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
```  
  
## <a name="arguments"></a>Argumentos
  
- `<select_specification>`  

  Propriedades ou valor a ser selecionado para o conjunto de resultados.  
  
- `'*'`  

  Especifica que o valor deve ser recuperado sem fazer alterações. Especificamente, se o valor processado for um objeto, todas as propriedades serão recuperadas.  
  
- `<object_property_list>`  
  
  Especifica a lista de propriedades a serem recuperadas. Cada valor retornado será um objeto com as propriedades especificadas.  
  
- `VALUE`  

  Especifica que o valor JSON deve ser recuperado em vez do objeto JSON completo. Isso, ao contrário de `<property_list>`, não encapsula o valor projetado em um objeto.  

- `DISTINCT`
  
  Especifica que duplicatas de propriedades projetadas devem ser removidas.  

- `<scalar_expression>`  

  Expressão que representa o valor a ser calculado. Consulte a seção [Expressões escalares](sql-query-scalar-expressions.md) para obter detalhes.  

## <a name="remarks"></a>Comentários

A sintaxe `SELECT *` só será válida se a cláusula FROM tiver declarado exatamente um alias. `SELECT *` fornece uma projeção de identidade, que pode ser útil se nenhuma projeção é necessária. SELECT * só é válida se a cláusula FROM é especificada e introduzida uma única fonte de entrada.  
  
Ambos `SELECT <select_list>` e `SELECT *` são "açúcar sintático" e podem ser expressos, como alternativa, usando instruções SELECT simples, conforme mostrado abaixo.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   é equivalente a:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   é equivalente a:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Exemplos

O exemplo de consulta SELECT a seguir retorna `address` de `Families` cujas `id` correspondências `AndersenFamily` :

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

## <a name="next-steps"></a>Próximas etapas

- [Guia de Introdução](sql-query-getting-started.md)
- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Cláusula WHERE](sql-query-where.md)
