---
title: RegexMatch na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL RegexMatch no Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 7f5b2831f45e902b312636e4133557a16ee7ec95
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341630"
---
# <a name="regexmatch-azure-cosmos-db"></a>REGEXMATCH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Fornece recursos de expressão regular. As expressões regulares são uma notação concisa e flexível para localizar padrões de texto. Azure Cosmos DB usa [expressões regulares compatíveis com Perl (PCRE)](http://www.pcre.org/). 

## <a name="syntax"></a>Sintaxe
  
```sql
RegexMatch(<str_expr1>, <str_expr2>, [, <str_expr3>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É a expressão da cadeia de caracteres a ser pesquisada.  
  
*str_expr2*  
   É a expressão regular.

*str_expr3*  
   É a cadeia de caracteres dos modificadores selecionados a serem usados com a expressão regular. Esse valor de cadeia de caracteres é opcional. Se você quiser executar RegexMatch sem modificadores, você pode adicionar uma cadeia de caracteres vazia ou omitir totalmente. 

Você pode aprender sobre [a sintaxe para criar expressões regulares no Perl](https://perldoc.perl.org/perlre). 

O Azure Cosmos DB dá suporte aos quatro modificadores a seguir:

| Modificador | Description |
| ------ | ----------- |
| `m` | Trate a expressão de cadeia de caracteres a ser pesquisada como várias linhas. Sem essa opção, "^" e "$" corresponderão no início ou no final da cadeia de caracteres e não em cada linha individual. |
| `s` | Permitir que "." corresponda a qualquer caractere, incluindo um caractere de nova linha. | 
| `i` | Ignorar maiúsculas e minúsculas quando houver correspondência de padrões. |
| `x` | Ignore todos os caracteres de espaço em branco. |

## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão booliana. Retorna indefinido se a expressão de cadeia de caracteres a ser pesquisada, a expressão regular ou os modificadores selecionados são inválidos.
  
## <a name="examples"></a>Exemplos
  
O exemplo de RegexMatch simples a seguir verifica a cadeia de caracteres "abcd" para correspondência de expressão regular usando alguns modificadores diferentes.
  
```sql
SELECT RegexMatch ("abcd", "ABC", "") AS NoModifiers, 
RegexMatch ("abcd", "ABC", "i") AS CaseInsensitive, 
RegexMatch ("abcd", "ab.", "") AS WildcardCharacter,
RegexMatch ("abcd", "ab c", "x") AS IgnoreWhiteSpace, 
RegexMatch ("abcd", "aB c", "ix") AS CaseInsensitiveAndIgnoreWhiteSpace 
```  
  
 Este é o conjunto de resultados.  
  
```json
[
    {
        "NoModifiers": false,
        "CaseInsensitive": true,
        "WildcardCharacter": true,
        "IgnoreWhiteSpace": true,
        "CaseInsensitiveAndIgnoreWhiteSpace": true
    }
]
```

Com o RegexMatch, você pode usar metacaracteres para fazer pesquisas de cadeias de caracteres mais complexas que, de outra forma, seriam possíveis com as funções de sistema StartsWith, EndsWith, CONTAINS ou StringEquals. Aqui estão alguns exemplos adicionais, que você pode executar usando o conjunto de dados nutrição disponível por meio do [playground para consultas de Azure Cosmos DB](https://www.documentdb.com/sql/demo). 

> [!NOTE] 
> Se você precisar usar um metacaractere em uma expressão regular e não quiser que ele tenha um significado especial, você deverá escapar do metacaractere usando `\` .

**Verifique os itens que têm uma descrição que contém a palavra "Salt" exatamente uma vez:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "salt{1}","")
```

**Verifique os itens que têm uma descrição que contém um número entre 0 e 99:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "[0-99]","")
```

**Verifique os itens que têm uma descrição que contém quatro palavras de letras começando com "S" ou "s":**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, " s... ","i")
```

## <a name="remarks"></a>Comentários

Essa função do sistema se beneficiará de um [índice de intervalo](index-policy.md#includeexclude-strategy) se a expressão regular puder ser dividida em funções de sistema StartsWith, EndsWith, CONTAINS ou StringEquals.

## <a name="next-steps"></a>Próximas etapas

- [Funções de cadeia de caracteres do Azure Cosmos DB](sql-query-string-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
