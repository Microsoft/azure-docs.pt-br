---
title: StringToArray na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL StringToArray no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7ae1f69e92e890daae528eb1f4dfb95f76560043
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93337975"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retorna a expressão convertida em uma matriz. Se a expressão não puder ser convertida, retornará indefinido.  
  
## <a name="syntax"></a>Sintaxe
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cadeia de caracteres a ser analisada como uma expressão de matriz JSON. 
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão de matriz ou indefinido. 
  
## <a name="remarks"></a>Comentários
  Valores de cadeia de caracteres aninhados devem ser gravados com aspas duplas para serem JSON válidos. Para obter detalhes sobre o formato JSON, consulte [JSON.org](https://json.org/). Essa função do sistema não usará o índice.
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como o `StringToArray` se comporta entre diferentes tipos. 
  
 Veja a seguir exemplos de entrada válida.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Este é o conjunto de resultados.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Veja a seguir um exemplo de entrada inválida. 
   
 Aspas simples dentro da matriz não são JSON válidos.
Mesmo que eles sejam válidos em uma consulta, eles não serão analisados em matrizes válidas. As cadeias de caracteres da matriz devem ser de escape "[ \\ " \\ "]" ou a aspa ao redor deve ser única "[" "]".

```sql
SELECT
    StringToArray("['5','6','7']")
```

Este é o conjunto de resultados.

```json
[{}]
```

Veja a seguir exemplos de entrada inválida.
   
 A expressão passada será analisada como uma matriz JSON; o seguinte não é avaliado para a matriz de tipos e, portanto, retorna indefinido.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

Este é o conjunto de resultados.

```json
[{}]
```

## <a name="next-steps"></a>Próximas etapas

- [Funções de cadeia de caracteres do Azure Cosmos DB](sql-query-string-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
