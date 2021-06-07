---
title: EXP na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função de sistema SQL do expoente (EXP) no Azure Cosmos DB para retornar o valor exponencial da expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: adf80d36edbe8f9a5535e8fa04501918a62ea7ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93335599"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retorna o valor exponencial da expressão numérica especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão numérica.  
  
## <a name="remarks"></a>Comentários
  
  A constante **e** (2,718281...), é a base dos logaritmos naturais.  
  
  O expoente de um número é a constante **e** elevado à potência do número. Por exemplo, EXP(1.0) = e^1.0 = 2.71828182845905 e EXP(10) = e^10 = 22026.4657948067.  
  
  O exponencial do logaritmo natural de um número é o número em si: EXP (LOG (n)) = n. E o logaritmo natural do exponencial de um número é o número em si: LOG (EXP (n)) = n.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir declara uma variável e retorna o valor exponencial da variável especificada (10).  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 O exemplo a seguir retorna o valor exponencial do logaritmo natural de 20 e o logaritmo natural do exponencial de 20. Como essas funções são funções inversas uma da outra, o valor retornado com o arredondamento para a matemática do ponto flutuante em ambos os casos é 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>Próximas etapas

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
