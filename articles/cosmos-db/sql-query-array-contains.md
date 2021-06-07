---
title: ARRAY_CONTAINS na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre como a matriz contém a função do sistema SQL em Azure Cosmos DB retorna um valor booleano que indica se a matriz contém o valor especificado
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ff88d66ee6d1ceee4f1cf4e7b6501ab323dbc79e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93332652"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Retorna um valor booliano que indica se a matriz contém o valor especificado. Você pode verificar uma correspondência parcial ou completa de um objeto usando uma expressão booliana no comando. 

## <a name="syntax"></a>Sintaxe
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Argumentos
  
*arr_expr*  
   É a expressão de matriz a ser pesquisada.  
  
*expr*  
   É a expressão a ser encontrada.  

*bool_expr*  
   É uma expressão booliana. Se for avaliada como ' true ' e se o valor de pesquisa especificado for um objeto, o comando verificará se há uma correspondência parcial (o objeto de pesquisa é um subconjunto de um dos objetos). Se ele for avaliado como ' false ', o comando verificará se há uma correspondência completa de todos os objetos dentro da matriz. O valor padrão, se não especificado, é false. 
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna um valor booliano.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir como verificar a associação em uma matriz usando `ARRAY_CONTAINS` .  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"b1": true, "b2": false}]  
```  

O exemplo a seguir mostra como verificar em busca de correspondência parcial de um JSON em uma matriz usando ARRAY_CONTAINS.  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Este é o conjunto de resultados.  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}]
```

## <a name="remarks"></a>Comentários

Essa função do sistema se beneficiará de um [índice de intervalo](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Próximas etapas

- [Funções de matriz Azure Cosmos DB](sql-query-array-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
