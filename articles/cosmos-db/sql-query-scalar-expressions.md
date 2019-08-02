---
title: Expressões escalares em consultas SQL do Azure Cosmos DB
description: Saiba mais sobre a sintaxe SQL de expressão escalar para o Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 4464c39a45c47c680a13f3ebc34841b47ee0d7c6
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342425"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Expressões escalares em consultas SQL do Azure Cosmos DB

O [cláusula SELECT](sql-query-select.md) dá suporte a expressões escalares. Uma expressão escalar é uma combinação de símbolos e operadores que podem ser avaliados para se obter um único valor. Exemplos de expressões escalares incluem: constantes, referências de propriedade, referências de elemento de matriz, referências de alias ou chamadas de função. Expressões escalares podem ser combinadas em expressões complexas usando operadores.

## <a name="syntax"></a>Sintaxe
  
```sql  
<scalar_expression> ::=  
       <constant>
     | input_alias
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>
     | <create_array_expression>  
     | (<scalar_expression>)
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```

## <a name="arguments"></a>Argumentos
  
- `<constant>`  
  
   Representa um valor constante. Consulte a seção [Constantes](sql-query-constants.md) para obter detalhes.  
  
- `input_alias`  
  
   Representa um valor definido pelo `input_alias` introduzido na cláusula `FROM`.  
  Esse valor é garantidamente diferente de **indefinido**; os valores **indefinidos** na entrada são ignorados.  
  
- `<scalar_expression>.property_name`  
  
   Representa um valor da propriedade de um objeto. Se a propriedade não existe ou é referenciada em um valor, que não é um objeto, então a expressão é avaliada como **indefinido** valor.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Representa um valor da propriedade com nome `property_name` ou o elemento de matriz com índice `array_index` de uma matriz. Se o índice de propriedade/matriz não existir ou o índice de propriedade/matriz for referenciado em um valor que não seja um objeto/matriz, a expressão será avaliada como um valor indefinido.  
  
- `unary_operator <scalar_expression>`  
  
   Representa um operador que é aplicado a um único valor. Consulte a seção [Operadores](sql-query-operators.md) para obter detalhes.  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Representa um operador que é aplicado a dois valores. Consulte a seção [Operadores](sql-query-operators.md) para obter detalhes.  
  
- `<scalar_function_expression>`  
  
   Representa um valor definido por um resultado de uma chamada de função.  
  
- `udf_scalar_function`  
  
   Nome da função escalar definida pelo usuário.  
  
- `builtin_scalar_function`  
  
   Nome da função escalar interna.  
  
- `<create_object_expression>`  
  
   Representa um valor obtido pela criação de um novo objeto com propriedades especificadas e seus valores.  
  
- `<create_array_expression>`  
  
   Representa um valor obtido pela criação de uma nova matriz com valores especificados como elementos  
  
- `parameter_name`  
  
   Representa um valor do nome de parâmetro especificado. Os nomes de parâmetro devem ter uma única \@ como primeiro caractere.  
  
## <a name="remarks"></a>Comentários
  
  Ao chamar uma função escalar interna ou definida pelo usuário, todos os argumentos devem ser definidos. Se um dos argumentos for indefinido, a função não será chamada e o resultado será indefinido.  
  
  Ao criar um objeto, as propriedades a que forem atribuídas um valor indefinido serão ignoradas e não serão incluídas no objeto criado.  
  
  Ao criar uma matriz, os valores de elemento a que forem atribuídos um valor **indefinido** serão ignorados e não serão incluídos no objeto criado. Isso fará com que o próximo elemento definido assuma o seu lugar, de forma que a matriz criada não tenha índices ignorados.  

## <a name="examples"></a>Exemplos

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

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Subconsultas](sql-query-subquery.md)