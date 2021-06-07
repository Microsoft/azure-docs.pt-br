---
title: Subcadeia de caracteres na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a subcadeia de caracteres da função do sistema SQL no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 17888ccd8fc51ed96f7fc92a0f9275d2c8cb56f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340816"
---
# <a name="substring-azure-cosmos-db"></a>Subcadeia de caracteres (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retorna parte de uma expressão de cadeia de caracteres começando na posição baseada em zero do caractere especificado e continua até o comprimento especificado ou até o final da cadeia de caracteres.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cadeia de caracteres.
  
*num_expr1*  
   É uma expressão numérica para denotar o caractere inicial. Um valor de 0 é o primeiro caractere de *str_expr*.
  
*num_expr2*  
   É uma expressão numérica para denotar o número máximo de caracteres de *str_expr* a serem retornados. Um valor de 0 ou menos resultados em uma cadeia de caracteres vazia.

## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão de cadeia de caracteres.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir retorna a subcadeia de caracteres de "abc" começando em 1 e com o comprimento de 1 caractere.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Comentários

Essa função do sistema se beneficiará de um [índice de intervalo](index-policy.md#includeexclude-strategy) se a posição inicial for `0` .

## <a name="next-steps"></a>Próximas etapas

- [Funções de cadeia de caracteres do Azure Cosmos DB](sql-query-string-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
