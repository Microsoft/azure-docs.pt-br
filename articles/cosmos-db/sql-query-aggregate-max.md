---
title: MÁXIMO em linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função de sistema SQL Max (MAX) no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a3d8e3f2687a492461bdbbdd761e26cdb58f9e96
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551356"
---
# <a name="max-azure-cosmos-db"></a>MAX (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Essa função de agregação retorna o máximo dos valores na expressão.
  
## <a name="syntax"></a>Sintaxe
  
```sql
MAX(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argumentos

*scalar_expr*  
   É uma expressão escalar. 
  
## <a name="return-types"></a>Tipos de retorno
  
Retorna uma expressão escalar.  
  
## <a name="examples"></a>Exemplos
  
O exemplo a seguir retorna o valor máximo de `propertyA` :
  
```sql
SELECT MAX(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Comentários

Essa função do sistema se beneficiará de um [índice de intervalo](index-policy.md#includeexclude-strategy). Os argumentos em `MAX` podem ser Number, String, Boolean ou NULL. Todos os valores indefinidos serão ignorados.

Ao comparar dados de tipos diferentes, a ordem de prioridade a seguir é usada (em ordem decrescente):

- cadeia de caracteres
- número
- booleano
- null

## <a name="next-steps"></a>Próximas etapas

- [Funções matemáticas no Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema no Azure Cosmos DB](sql-query-system-functions.md)
- [Funções de agregação no Azure Cosmos DB](sql-query-aggregate-functions.md)