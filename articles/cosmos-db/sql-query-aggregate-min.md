---
title: MÍN. de Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função de sistema SQL min (MIN) no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 679814822cca5a72bd261d3c8944863715e31f70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96551858"
---
# <a name="min-azure-cosmos-db"></a>MIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Essa função de agregação retorna o mínimo dos valores na expressão.
  
## <a name="syntax"></a>Sintaxe
  
```sql
MIN(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*scalar_expr*  
   É uma expressão escalar. 
  
## <a name="return-types"></a>Tipos de retorno
  
Retorna uma expressão escalar.  
  
## <a name="examples"></a>Exemplos
  
O exemplo a seguir retorna o valor mínimo de `propertyA` :
  
```sql
SELECT MIN(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Comentários

Essa função do sistema se beneficiará de um [índice de intervalo](index-policy.md#includeexclude-strategy). Os argumentos em `MIN` podem ser Number, String, Boolean ou NULL. Todos os valores indefinidos serão ignorados.

Ao comparar dados de tipos diferentes, a ordem de prioridade a seguir é usada (em ordem crescente):

- nulo
- boolean
- número
- string

## <a name="next-steps"></a>Próximas etapas

- [Funções matemáticas no Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema no Azure Cosmos DB](sql-query-system-functions.md)
- [Funções de agregação no Azure Cosmos DB](sql-query-aggregate-functions.md)