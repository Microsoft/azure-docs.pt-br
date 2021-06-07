---
title: Constantes do SQL no Azure Cosmos DB
description: Saiba mais sobre como as constantes de consulta SQL no Azure Cosmos DB são usadas para representar um valor de dados específico
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: b0973dca980b65b85ac66cc1b3c9ed45f0f9c779
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339189"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Constantes de consulta do Azure Cosmos DB SQL  
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Uma constante, também conhecida como uma literal ou um valor escalar, é um símbolo que representa um valor de dados específico. O formato de uma constante depende do tipo de dados do valor que representa.  
  
 **Suporte para tipos de dados escalares:**  
  
|**Tipo**|**Ordem de valores**|  
|-|-|  
|**Indefinido**|Valor único: **indefinido**|  
|**Nulo**|Valor único: **nulo**|  
|**Boolean**|Valores: **falso**, **verdadeiro**.|  
|**Número**|Um número de ponto flutuante de precisão dupla, padrão IEEE 754.|  
|**Cadeia de caracteres**|Uma sequência de zero ou mais caracteres Unicode. As cadeias de caracteres devem ser colocadas entre aspas simples ou duplas.|  
|**Matriz**|Uma sequência de zero ou mais elementos. Cada elemento pode ser um valor de qualquer tipo de dados escalar, exceto **indefinido**.|  
|**Objeto**|Um conjunto ordenado de zero ou mais pares de nome/valor. Nome é uma cadeia de caracteres Unicode; o valor pode ser de qualquer tipo de dados escalares, exceto **Indefinido**.|  
  
## <a name="syntax"></a><a name="bk_syntax"></a>Sintaxe
  
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
  
##  <a name="arguments"></a><a name="bk_arguments"></a> Argumentos
  
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

- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dados de documento modelo](modeling-data.md)
