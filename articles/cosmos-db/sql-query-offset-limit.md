---
title: Cláusula de deslocamento de limite no Azure Cosmos DB
description: Saiba mais sobre a cláusula de deslocamento de limite para o Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 60ac28c80e9f7cc72f4d6005c12cb5f68671341e
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342604"
---
# <a name="offset-limit-clause"></a>Cláusula de deslocamento de limite

A cláusula de limite de deslocamento é uma cláusula opcional para ignorar e levar a um número de valores da consulta. A contagem de deslocamento e a contagem de limite são necessários na cláusula de deslocamento de limite.

Quando o limite de deslocamento é usado em conjunto com uma cláusula ORDER BY, o conjunto de resultados é produzido, fazendo ignorar e levar nos valores ordenados. Se nenhuma cláusula ORDER BY for usada, isso resultará em uma ordem determinista de valores.

## <a name="syntax"></a>Sintaxe
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumentos

- `<offset_amount>`

   Especifica o número inteiro de itens que devem ignorar os resultados da consulta.

- `<limit_amount>`
  
   Especifica o número inteiro de itens que os resultados da consulta devem incluir

## <a name="remarks"></a>Comentários
  
  A contagem de deslocamento e a contagem de limite são necessárias na cláusula de deslocamento de limite. Se um opcional `ORDER BY` cláusula as for usada, o conjunto de resultados é produzido, fazendo a ignorar sobre os valores ordenados. Caso contrário, a consulta retornará uma ordem fixa de valores. No momento, esta cláusula é tem suporte para consultas em uma única partição, ele ainda não suportam a consultas entre partições.

## <a name="examples"></a>Exemplos

Por exemplo, aqui está uma consulta que ignora o primeiro valor e retorna o segundo valor (na ordem do nome da cidade do residente):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Os resultados são:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Aqui está uma consulta que ignora o primeiro valor e retorna o segundo valor (sem ordenação):

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Os resultados são:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Próximas etapas

- [Guia de Introdução](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [Cláusula ORDER BY](sql-query-order-by.md)
