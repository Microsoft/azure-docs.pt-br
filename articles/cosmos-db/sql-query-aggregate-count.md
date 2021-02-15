---
title: CONTAGEM na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a função de sistema SQL de contagem (contagem) no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 5228558f4bcb146ec08ee5fff45fb1bdf4d56f01
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551355"
---
# <a name="count-azure-cosmos-db"></a>CONTAGEM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Essa função do sistema retorna a contagem dos valores na expressão.
  
## <a name="syntax"></a>Sintaxe
  
```sql
COUNT(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*scalar_expr*  
   É qualquer expressão escalar
  
## <a name="return-types"></a>Tipos de retorno
  
Retorna uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
O exemplo a seguir retorna a contagem total de itens em um contêiner:
  
```sql
SELECT COUNT(1)
FROM c
``` 
A contagem pode usar qualquer expressão escalar como entrada. A consulta abaixo produzirá resultados equivalentes:

```sql
SELECT COUNT(2)
FROM c
```

## <a name="remarks"></a>Comentários

Essa função do sistema se beneficiará de um [índice de intervalo](index-policy.md#includeexclude-strategy) para todas as propriedades no filtro da consulta.

## <a name="next-steps"></a>Próximas etapas

- [Funções matemáticas no Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema no Azure Cosmos DB](sql-query-system-functions.md)
- [Funções de agregação no Azure Cosmos DB](sql-query-aggregate-functions.md)