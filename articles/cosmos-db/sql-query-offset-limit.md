---
title: Cláusula de limite de deslocamento no Azure Cosmos DB
description: Saiba como usar a cláusula de limite de deslocamento para ignorar e pegar alguns valores ao consultar em Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: 459bd8511577067766cf488f53df57c1dc33fad1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93338277"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Cláusula de limite de deslocamento no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A cláusula de limite de deslocamento é uma cláusula opcional para ignorar e, em seguida, pegar um número de valores da consulta. A contagem de deslocamento e a contagem de limites são necessárias na cláusula de limite de deslocamento.

Quando o limite de deslocamento é usado em conjunto com uma cláusula ORDER BY, o conjunto de resultados é produzido ao fazer a ação de ignorar e assumir os valores ordenados. Se nenhuma cláusula ORDER BY for usada, ela resultará em uma ordem determinística de valores.

## <a name="syntax"></a>Sintaxe
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumentos

- `<offset_amount>`

   Especifica o número inteiro de itens que os resultados da consulta devem ignorar.

- `<limit_amount>`
  
   Especifica o número inteiro de itens que os resultados da consulta devem incluir

## <a name="remarks"></a>Comentários
  
  A `OFFSET` contagem e a `LIMIT` contagem são necessárias na `OFFSET LIMIT` cláusula. Se uma `ORDER BY` cláusula opcional for usada, o conjunto de resultados será produzido fazendo o ignorar os valores ordenados. Caso contrário, a consulta retornará uma ordem fixa de valores.

  A cobrança de RU de uma consulta com `OFFSET LIMIT` aumentará conforme o número de termos que estão sendo deslocados aumenta. Para consultas que têm [várias páginas de resultados](sql-query-pagination.md), geralmente recomendamos o uso de [tokens de continuação](sql-query-pagination.md#continuation-tokens). Os tokens de continuação são um "indicador" para o local onde a consulta pode ser retomada posteriormente. Se você usar `OFFSET LIMIT` , não haverá "indicador". Se você quisesse retornar a próxima página da consulta, precisaria começar desde o início.
  
  Você deve usar `OFFSET LIMIT` para casos em que deseja ignorar completamente os itens e salvar os recursos do cliente. Por exemplo, você deve usar `OFFSET LIMIT` se quiser pular para o resultado da consulta 1000th e não precisar exibir os resultados de 1 a 999. No back-end, o `OFFSET LIMIT` ainda carrega cada item, incluindo os ignorados. A vantagem de desempenho é uma economia nos recursos do cliente, evitando o processamento de itens que não são necessários.

## <a name="examples"></a>Exemplos

Por exemplo, aqui está uma consulta que ignora o primeiro valor e retorna o segundo valor (na ordem do nome da cidade residente):

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
