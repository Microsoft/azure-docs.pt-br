---
title: Função CONTAINS na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre como a função de sistema CONTAINS do SQL no Azure Cosmos DB retorna um booliano que indica se a primeira expressão de cadeia de caracteres contém a segunda
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4b6835b22e5cfa4ca703b95d70e20112b8723def
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339165"
---
# <a name="contains-azure-cosmos-db"></a>CONTAINS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres contém a segunda.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
CONTAINS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É a expressão da cadeia de caracteres a ser pesquisada.  
  
*str_expr2*  
   É a expressão da cadeia de caracteres a ser encontrada.  

*bool_expr* valor opcional para ignorar maiúsculas e minúsculas. Quando definido como True, CONTAINS fará uma pesquisa que não diferencia maiúsculas de minúsculas. Quando não especificado, esse valor é False.
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão booliana.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir verifica se “abc” contém “ab” e se “abc” contém “A”.  
  
```sql
SELECT CONTAINS("abc", "ab", false) AS c1, CONTAINS("abc", "A", false) AS c2, CONTAINS("abc", "A", true) AS c3
```  
  
 Este é o conjunto de resultados.  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>Comentários

Essa função do sistema se beneficiará de um [índice de intervalo](index-policy.md#includeexclude-strategy).

O consumo de RU da função Contains aumentará conforme a cardinalidade da propriedade da função do sistema aumentar. Em outras palavras, se estiver verificando se um valor de propriedade contém uma determinada cadeia de caracteres, a cobrança de RU da consulta dependerá do número de valores possíveis para essa propriedade.

Por exemplo, considere duas propriedades: cidade e país. A cardinalidade de cidade é 5.000 e a cardinalidade de país é 200. A seguir, são apresentados dois exemplos de consultas:

```sql
    SELECT * FROM c WHERE CONTAINS(c.town, "Red", false)
```

```sql
    SELECT * FROM c WHERE CONTAINS(c.country, "States", false)
```

A primeira consulta provavelmente usará mais RUs do que a segunda consulta, pois a cardinalidade da cidade é maior do que a do país.

Se o tamanho da propriedade em contém for maior que 1 KB para alguns documentos, o mecanismo de consulta precisará carregar esses documentos. Nesse caso, o mecanismo de consulta não será capaz de avaliar totalmente Contains com um índice. A carga de RU para Contains será alta se você tiver um grande número de documentos com tamanhos de propriedade mais de 1 KB.

## <a name="next-steps"></a>Próximas etapas

- [Funções de cadeia de caracteres do Azure Cosmos DB](sql-query-string-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
