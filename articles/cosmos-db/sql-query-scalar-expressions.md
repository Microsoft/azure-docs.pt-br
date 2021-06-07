---
title: Expressões escalares em consultas do Azure Cosmos DB SQL
description: Saiba mais sobre a sintaxe SQL de expressão escalar para Azure Cosmos DB. Este artigo também descreve como combinar expressões escalares em expressões complexas usando operadores.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 34df2d2f4a2209cca93c3f7ac12dcd203bf4a089
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339624"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Expressões escalares em consultas do Azure Cosmos DB SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A [cláusula SELECT](sql-query-select.md) oferece suporte a expressões escalares. Uma expressão escalar é uma combinação de símbolos e operadores que podem ser avaliados para se obter um único valor. Exemplos de expressões escalares incluem: constantes, referências de propriedade, referências de elemento de matriz, referências de alias ou chamadas de função. Expressões escalares podem ser combinadas em expressões complexas usando operadores.

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
  
   Representa um valor da propriedade de um objeto. Se a propriedade não existir ou se a propriedade for referenciada em um valor, que não é um objeto, a expressão será avaliada como valor **indefinido** .  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Representa um valor da propriedade com o nome `property_name` ou elemento de matriz com o índice `array_index` de uma matriz. Se o índice de propriedade/matriz não existir ou o índice de propriedade/matriz for referenciado em um valor que não seja um objeto/matriz, a expressão será avaliada como um valor indefinido.  
  
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

Na consulta a seguir, o resultado da expressão escalar é um booliano:

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
- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Subconsultas](sql-query-subquery.md)