---
title: Funções do sistema na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre funções internas e definidas pelo usuário do sistema SQL no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 36ebe80671c77cc83ddba770e3259f6542472e58
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526832"
---
# <a name="system-functions-azure-cosmos-db"></a>Funções do sistema (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 O Cosmos DB fornece muitas funções SQL internas. As categorias de funções internas estão listadas abaixo.  
  
|Grupo de funções|Descrição|Operations|  
|--------------|-----------------|-----------------| 
|[Funções de matriz](sql-query-array-functions.md)|As funções de matriz executam uma operação em um valor de matriz de entrada e retornam um valor numérico, booliano ou um valor de matriz. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md), [ARRAY_LENGTH](sql-query-array-length.md) [ARRAY_SLICE](sql-query-array-slice.md) |
|[Funções de data e hora](sql-query-date-time-functions.md)|As funções de data e hora permitem que você obtenha a data e a hora UTC atuais em duas formas; um carimbo de data/hora numérico cujo valor é a época do UNIX em milissegundos ou como uma cadeia de caracteres que está de acordo com o formato ISO 8601. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md), [GetCurrentTicks](sql-query-getcurrentticks.md) |
|[Funções matemáticas](sql-query-mathematical-functions.md)|As funções matemáticas executam um cálculo, normalmente com base em valores de entrada que são fornecidos como argumentos, e retornam um valor numérico. | [ABS](sql-query-abs.md), [acos](sql-query-acos.md), [Asen](sql-query-asin.md), [ATAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [teto](sql-query-ceiling.md), [cos](sql-query-cos.md), [Cot](sql-query-cot.md), [graus](sql-query-degrees.md), [exp](sql-query-exp.md), [Floor](sql-query-floor.md), [log](sql-query-log.md), [log10](sql-query-log10.md), [PI](sql-query-pi.md), [potência](sql-query-power.md), [radianos](sql-query-radians.md), [Rand](sql-query-rand.md), [redondo](sql-query-round.md), [Sign](sql-query-sign.md), [sin](sql-query-sin.md), [sqrt](sql-query-sqrt.md), [quadrado](sql-query-square.md), [Tan](sql-query-tan.md), [trunc](sql-query-trunc.md) |
|[Funções espaciais](sql-query-spatial-functions.md)|As funções espaciais executam uma operação em um valor de entrada de objeto espacial e retornam um valor numérico ou booliano. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID](sql-query-st-isvalid.md), [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md), [ST_WITHIN](sql-query-st-within.md) |
|[Funções de cadeia de caracteres](sql-query-string-functions.md)|As funções de cadeia de caracteres executam uma operação em um valor de cadeia de caracteres de entrada e retornam uma cadeia de caracteres, um valor numérico ou um valor booliano. | [Concat](sql-query-concat.md), [Contains](sql-query-contains.md), [ENDSWITH](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [esquerda](sql-query-left.md), [comprimento](sql-query-length.md), [menor](sql-query-lower.md), [LTrim](sql-query-ltrim.md), [REGEXMATCH](sql-query-regexmatch.md)[substituir](sql-query-replace.md), [replicar](sql-query-replicate.md), [reverter](sql-query-reverse.md), [direita](sql-query-right.md), [RTrim](sql-query-rtrim.md), [STARTSWITH](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [StringToObject](sql-query-stringtoobject.md), [subcadeia de caracteres](sql-query-substring.md), [ToString](sql-query-tostring.md), [arrumar](sql-query-trim.md), [Upper](sql-query-upper.md) |
|[Funções de verificação de tipo](sql-query-type-checking-functions.md)|As funções de verificação de tipo permitem que você verifique o tipo de uma expressão em consultas SQL. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [is_null](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT](sql-query-is-object.md), [IS_PRIMITIVE](sql-query-is-primitive.md), [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Funções internas versus definidas pelo usuário (UDFs)

Se você estiver usando uma UDF (função definida pelo usuário) para a qual uma função interna agora está disponível, a função interna correspondente será mais rápida de executar e mais eficiente.

## <a name="built-in-versus-ansi-sql-functions"></a>Funções SQL internas versus ANSI

A principal diferença entre Cosmos DB funções e funções ANSI SQL é que Cosmos DB funções são projetadas para funcionar bem com dados de esquema misto e sem esquema. Por exemplo, se uma propriedade estiver ausente ou tiver um valor não numérico como `undefined` , o item será ignorado em vez de retornar um erro.

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções de matriz](sql-query-array-functions.md)
- [Funções de data e hora](sql-query-date-time-functions.md)
- [Funções matemáticas](sql-query-mathematical-functions.md)
- [Funções espaciais](sql-query-spatial-functions.md)
- [Funções de cadeia de caracteres](sql-query-string-functions.md)
- [Funções de verificação de tipo](sql-query-type-checking-functions.md)
- [Funções definidas pelo usuário](sql-query-udfs.md)
- [Agregações](sql-query-aggregate-functions.md)
