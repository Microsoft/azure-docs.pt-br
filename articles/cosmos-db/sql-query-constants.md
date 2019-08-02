---
title: Constantes SQL no Azure Cosmos DB
description: Saiba mais sobre as constantes SQL no Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: ed337c8330eb8b3e4ad97c92cca1d0c5977f9588
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342579"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Constantes de consulta SQL do Cosmos DB do Azure  

 Uma constante, também conhecida como valor literal ou escalar, é um símbolo que representa um valor de dados específico. O formato de uma constante depende do valor que representa o tipo de dados.  
  
 **Suporte para tipos de dados escalares:**  
  
|**Tipo**|**Ordem de valores**|  
|-|-|  
|**Indefinido**|Valor único: **indefinido**|  
|**Nulo**|Valor único: **nulo**|  
|**Booliano**|Valores: **falso**, **verdadeiro**.|  
|**Número**|Um número de ponto flutuante de precisão dupla, padrão IEEE 754.|  
|**Cadeia de caracteres**|Uma sequência de zero ou mais caracteres Unicode. As cadeias de caracteres devem ser colocadas entre aspas simples ou duplas.|  
|**Matriz**|Uma sequência de zero ou mais elementos. Cada elemento pode ser um valor de qualquer tipo de dados escalares, exceto **Undefined**.|  
|**Objeto**|Um conjunto ordenado de zero ou mais pares de nome/valor. Nome é uma cadeia de caracteres Unicode; o valor pode ser de qualquer tipo de dados escalares, exceto **Indefinido**.|  
  
## <a name="bk_syntax"></a>Sintaxe
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
##  <a name="bk_arguments"></a> Argumentos
  
* `<undefined_constant>; Undefined`  
  
  Representa o valor indefinido do tipo Indefinido.  
  
* `<null_constant>; null`  
  
  Representa o valor **null** do tipo **Nulo**.  
  
* `<boolean_constant>`  
  
  Representa a constante do tipo booliano.  
  
* `false`  
  
  Representa o valor **false** do tipo booliano.  
  
* `true`  
  
  Representa o valor **true** do tipo booliano.  
  
* `<number_constant>`  
  
  Representa uma constante.  
  
* `decimal_literal`  
  
  Os literais decimais são números representados usando notação decimal ou notação científica.  
  
* `hexadecimal_literal`  
  
  Os literais hexadecimais são números representados usando o prefixo '0x' seguido por um ou mais dígitos hexadecimais.  
  
* `<string_constant>`  
  
  Representa uma constante do tipo Cadeia de caracteres.  
  
* `string _literal`  
  
  Literais de cadeia de caracteres são cadeias de caracteres Unicode representadas por uma sequência de zero ou mais caracteres Unicode ou sequências de escape. As literais de cadeia de caracteres são colocadas entre aspas simples (apóstrofe: ') ou aspas duplas (aspas: ").  
  
  As seguintes sequências de escape são permitidas:  
  
|**Sequência de escape**|**Descrição**|**Caractere Unicode**|  
|-|-|-|  
|\\'|apóstrofo (')|U+0027|  
|\\"|aspas (")|U+0022|  
|\\\ |barra invertida (\\)|U+005C|  
|\\/|barra (/)|U+002F|  
|\b|backspace|U+0008|  
|\f|avanço de página|U+000C|  
|\n|alimentação de linha|U+000A|  
|\r|retorno de carro|U+000D|  
|\t|tab|U+0009|  
|\uXXXX|Um caractere Unicode definido por 4 dígitos hexadecimais.|U+XXXX|  

## <a name="next-steps"></a>Próximas etapas

- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Dados de documento de modelo](modeling-data.md)
