---
title: ARRAY_CONCAT na linguagem de consulta Azure Cosmos DB
description: Saiba como a função de sistema SQL Concat da matriz em Azure Cosmos DB retorna uma matriz que é o resultado da concatenação de dois ou mais valores de matriz
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b6424a161b5fc4d0c075ade6f852b33dff20a09
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93332739"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retorna uma matriz que é o resultado da concatenação de dois ou mais valores de matriz.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*arr_expr*  
   É uma expressão de matriz para concatenar com os outros valores. A `ARRAY_CONCAT` função requer pelo menos dois argumentos *arr_expr* .  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão de matriz.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como concatenar duas matrizes.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>Comentários

Essa função do sistema não usará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções de matriz Azure Cosmos DB](sql-query-array-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
