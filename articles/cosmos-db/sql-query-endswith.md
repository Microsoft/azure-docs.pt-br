---
title: EndsWith na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a função de sistema ENDSWITH do SQL no Azure Cosmos DB para retornar um booliano que indica se a primeira expressão de cadeia de caracteres termina com a segunda
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0cc93fee8aacc711a797925cb2e2808b73cafd1
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93338806"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres termina com a segunda.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É uma expressão de cadeia de caracteres.  
  
*str_expr2*  
   É uma expressão de cadeia de caracteres a ser comparada ao final de *str_expr1*.

*bool_expr* valor opcional para ignorar maiúsculas e minúsculas. Quando definido como true, ENDSWITH fará uma pesquisa que não diferencia maiúsculas de minúsculas. Quando não especificado, esse valor é false.
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão booliana.  
  
## <a name="examples"></a>Exemplos
  
O exemplo a seguir verifica se a cadeia de caracteres "abc" termina com "b" e "bC".  
  
```sql
SELECT ENDSWITH("abc", "b", false) AS e1, ENDSWITH("abc", "bC", false) AS e2, ENDSWITH("abc", "bC", true) AS e3
```  
  
 Este é o conjunto de resultados.  
  
```json
[
    {
        "e1": false,
        "e2": false,
        "e3": true
    }
]
```  

## <a name="remarks"></a>Comentários

Essa função do sistema se beneficiará de um [índice de intervalo](index-policy.md#includeexclude-strategy).

O consumo do EndsWith de RU aumentará conforme a cardinalidade da propriedade do sistema aumentar. Em outras palavras, se estiver verificando se um valor de propriedade termina com uma determinada cadeia de caracteres, a cobrança de RU da consulta dependerá do número de valores possíveis para essa propriedade.

Por exemplo, considere duas propriedades: cidade e país. A cardinalidade de cidade é 5.000 e a cardinalidade de país é 200. A seguir, são apresentados dois exemplos de consultas:

```sql
    SELECT * FROM c WHERE ENDSWITH(c.town, "York", false)
```

```sql
    SELECT * FROM c WHERE ENDSWITH(c.country, "States", false)
```

A primeira consulta provavelmente usará mais RUs do que a segunda consulta, pois a cardinalidade da cidade é maior do que a do país.

Se o tamanho da propriedade em EndsWith for maior que 1 KB para alguns documentos, o mecanismo de consulta precisará carregar esses documentos. Nesse caso, o mecanismo de consulta não conseguirá avaliar totalmente o EndsWith com um índice. O encargo de RU para EndsWith será alto se você tiver um grande número de documentos com tamanhos de propriedade maiores que 1 KB.

## <a name="next-steps"></a>Próximas etapas

- [Funções de cadeia de caracteres do Azure Cosmos DB](sql-query-string-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
