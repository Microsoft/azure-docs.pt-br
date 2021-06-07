---
title: MÉDIA em linguagem de consulta de Azure Cosmos DB
description: Saiba mais sobre a função média (média) do sistema SQL no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2cfbca798a7b404e4ee12b93396b2a5b08d7d5bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96551860"
---
# <a name="avg-azure-cosmos-db"></a>Méd (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Essa função de agregação retorna a média dos valores na expressão.
  
## <a name="syntax"></a>Sintaxe
  
```sql
AVG(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
Retorna uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
O exemplo a seguir retorna o valor médio de `propertyA` :
  
```sql
SELECT AVG(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Comentários

Essa função do sistema se beneficiará de um [índice de intervalo](index-policy.md#includeexclude-strategy). Se qualquer argumento no `AVG` for String, Boolean ou NULL, toda a função de sistema agregada será retornada `undefined` . Se qualquer argumento tiver um `undefined` valor, ele não afetará o `AVG` cálculo.

## <a name="next-steps"></a>Próximas etapas

- [Funções matemáticas no Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema no Azure Cosmos DB](sql-query-system-functions.md)
- [Funções de agregação no Azure Cosmos DB](sql-query-aggregate-functions.md)