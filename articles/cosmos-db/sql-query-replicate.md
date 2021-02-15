---
title: REPLICAr na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a replicação de função do sistema SQL no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7c0f1673c620ceadeb5ccca2a15cc9b7ce8d7685
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341596"
---
# <a name="replicate-azure-cosmos-db"></a>REPLICAte (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Repete um valor da cadeia de caracteres um número especificado de vezes.
  
## <a name="syntax"></a>Sintaxe
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cadeia de caracteres.
  
*num_expr*  
   É uma expressão numérica. Se *num_expr* for negativo ou não finito, o resultado será indefinido.
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão de cadeia de caracteres.
  
## <a name="remarks"></a>Comentários

  O comprimento máximo do resultado é de 10.000 caracteres, ou seja, (length ( *str_expr* ) *  *num_expr* ) <= 10.000. Essa função do sistema não usará o índice.

## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como usar `REPLICATE` em uma consulta.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Este é o conjunto de resultados.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>Próximas etapas

- [Funções de cadeia de caracteres do Azure Cosmos DB](sql-query-string-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
