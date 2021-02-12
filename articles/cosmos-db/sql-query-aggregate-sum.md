---
title: SOMAr na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função soma (SUM) do sistema SQL no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: d5a86cd5af504072480e0cd749caa6c0532d0bc1
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551359"
---
# <a name="sum-azure-cosmos-db"></a>SUM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Essa função de agregação retorna a soma dos valores na expressão.
  
## <a name="syntax"></a>Sintaxe
  
```sql
SUM(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
Retorna uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
O exemplo a seguir retorna a soma de `propertyA` :
  
```sql
SELECT SUM(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Comentários

Essa função do sistema se beneficiará de um [índice de intervalo](index-policy.md#includeexclude-strategy). Se qualquer argumento no `SUM` for String, Boolean ou NULL, toda a função de sistema agregada será retornada `undefined` . Se qualquer argumento tiver um `undefined` valor, ele não afetará o `SUM` cálculo.

## <a name="next-steps"></a>Próximas etapas

- [Funções matemáticas no Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema no Azure Cosmos DB](sql-query-system-functions.md)
- [Funções de agregação no Azure Cosmos DB](sql-query-aggregate-functions.md)