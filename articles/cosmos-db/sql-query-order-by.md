---
title: Cláusula ORDER BY no Azure Cosmos DB
description: Saiba mais sobre a cláusula ORDER BY do SQL do Azure Cosmos DB. Use o SQL como uma linguagem de consulta JSON do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: d0a1ed33d5848c3ed8d5f83af8b320d77fe0dc65
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342705"
---
# <a name="order-by-clause"></a>Cláusula ORDER BY

A cláusula ORDER BY opcional especifica a ordem de classificação para resultados retornados pela consulta.

## <a name="syntax"></a>Sintaxe
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumentos
  
- `<sort_specification>`  
  
   Especifica uma propriedade ou expressão pela qual classificar o conjunto de resultados da consulta. Uma coluna de classificação pode ser especificada como um alias de nome ou propriedade.  
  
   Várias propriedades podem ser especificadas. Nomes de propriedade devem ser exclusivos. A sequência das propriedades de classificação na cláusula ORDER BY define a organização do conjunto de resultados classificado. Ou seja, o conjunto de resultados é classificado pela primeira propriedade e, em seguida, essa lista ordenada é classificada pela segunda propriedade e assim por diante.  
  
   Os nomes de propriedade referenciados na cláusula ORDER BY devem corresponder para uma propriedade na lista de seleção ou a uma propriedade definida na coleção especificada na cláusula FROM sem qualquer ambiguidade.  
  
- `<sort_expression>`  
  
   Especifica um ou mais propriedades ou expressões na qual classificar o conjunto de resultados de consulta.  
  
- `<scalar_expression>`  
  
   Consulte a seção [Expressões escalares](sql-query-scalar-expressions.md) para obter detalhes.  
  
- `ASC | DESC`  
  
   Especifica que os valores na coluna especificada devem ser classificados em ordem crescente ou decrescente. ASC classifica do valor mais baixo para o valor mais alto. DESC classifica do valor mais alto para o valor mais baixo. ASC é a ordem de classificação padrão. Os valores nulos são tratados como os menores valores possíveis.  
  
## <a name="remarks"></a>Comentários  
  
   A cláusula ORDER BY requer que a política de indexação inclua um índice para os campos que estão sendo classificadas. O tempo de execução de consulta do Azure Cosmos DB dá suporte à classificação em relação a um nome de propriedade e não em relação a propriedades computadas. O Azure Cosmos DB dá suporte a várias propriedades de ORDER BY. Para executar uma consulta com várias propriedades de ORDER BY, você deve definir um [índice composto](index-policy.md#composite-indexes) aos campos que estão sendo classificadas.

## <a name="examples"></a>Exemplos

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

Além disso, você pode ordenar por várias propriedades. Requer uma consulta que ordena por várias propriedades de um [índice composto](index-policy.md#composite-indexes). Considere a seguinte consulta:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Essa consulta recupera a família `id` em ordem crescente do nome da cidade. Se vários itens têm o mesmo nome de cidade, a consulta será ordenar pelo `creationDate` em ordem decrescente.

## <a name="next-steps"></a>Próximas etapas

- [Guia de Introdução](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [Cláusula de deslocamento de limite](sql-query-offset-limit.md)
