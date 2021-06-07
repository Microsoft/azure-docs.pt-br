---
title: StringToNull na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL StringToNull no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ee91ca3a32bacbf6590877f49bf74499fc00a8f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339573"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retorna a expressão convertida em NULL. Se a expressão não puder ser convertida, retornará indefinido.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cadeia de caracteres a ser analisada como uma expressão nula.
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão nula ou indefinido.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como o `StringToNull` se comporta entre diferentes tipos. 

Veja a seguir exemplos de entrada válida.

 O espaço em branco é permitido somente antes ou depois de "NULL".

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

Veja a seguir exemplos de entrada inválida.

NULL diferencia maiúsculas de minúsculas e deve ser escrito com todos os caracteres minúsculos, ou seja, "NULL".

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Este é o conjunto de resultados.  
  
```json
[{}]
```  

A expressão passada será analisada como uma expressão nula; essas entradas não são avaliadas como tipo NULL e, portanto, retornam indefinidamente.

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 Este é o conjunto de resultados.  
  
```json
[{}]
```  

## <a name="remarks"></a>Comentários

Essa função do sistema não usará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções de cadeia de caracteres do Azure Cosmos DB](sql-query-string-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
