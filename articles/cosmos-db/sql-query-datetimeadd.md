---
title: DateTimeAdd na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL DateTimeAdd no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: c04c63a5ec72f08807b1702f74db39e00662656f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597650"
---
# <a name="datetimeadd-azure-cosmos-db"></a>DateTimeAdd (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Retorna o valor da cadeia de caracteres DateTime resultante da adição de um valor de número especificado (como um inteiro assinado) a uma cadeia de caracteres DateTime especificada  
  
## <a name="syntax"></a>Sintaxe
  
```sql
DateTimeAdd (<DateTimePart> , <numeric_expr> ,<DateTime>)
```

## <a name="arguments"></a>Argumentos
  
*DateTimePart*  
   A parte da data à qual o DateTimeAdd adiciona um número inteiro. Esta tabela lista todos os argumentos DateTimePart válidos:

| DateTimePart | abreviações        |
| ------------ | -------------------- |
| Year         | "Year", "yyyy", "AA" |
| Mês        | "month", "mm", "m"   |
| Dia          | "Day", "dd", "d"     |
| Hora         | "hora", "HH"         |
| Minuto       | "minuto", "mi", "n"  |
| Segundo       | "segundo", "SS", "s"  |
| Milissegundos  | "milissegundo", "MS"  |
| Microssegundos  | "microssegundo", "MCS" |
| 100 nanossegundos   | "nanossegundos", "NS"   |

*numeric_expr*  
   É um valor inteiro assinado que será adicionado ao DateTimePart do DateTime especificado

*DateTime*  
   Valor de cadeia de caracteres UTC de data e hora do ISO 8601 no formato `YYYY-MM-DDThh:mm:ss.fffffffZ` em que:
  
|Formatar|Descrição|
|-|-|
|AAAA|ano de quatro dígitos|
|MM|mês de dois dígitos (01 = Janeiro, etc.)|
|DD|dia de dois dígitos do mês (01 a 31)|
|T|signifier para o início dos elementos de hora|
|hh|hora de dois dígitos (00 a 23)|
|MM|minutos de dois dígitos (00 a 59)|
|ss|segundos de dois dígitos (00 a 59)|
|. fffffff|segundos fracionários de sete dígitos|
|Z|Designador UTC (tempo Universal Coordenado)|
  
  Para obter mais informações sobre o formato ISO 8601, consulte [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="return-types"></a>Tipos de retorno

Retorna um valor de cadeia de caracteres do UTC de data e hora do ISO 8601 no formato `YYYY-MM-DDThh:mm:ss.fffffffZ` em que:
  
|Formatar|Descrição|
|-|-|
|AAAA|ano de quatro dígitos|
|MM|mês de dois dígitos (01 = Janeiro, etc.)|
|DD|dia de dois dígitos do mês (01 a 31)|
|T|signifier para o início dos elementos de hora|
|hh|hora de dois dígitos (00 a 23)|
|MM|minutos de dois dígitos (00 a 59)|
|ss|segundos de dois dígitos (00 a 59)|
|. fffffff|segundos fracionários de sete dígitos|
|Z|Designador UTC (tempo Universal Coordenado)|

## <a name="remarks"></a>Comentários

DateTimeAdd retornará `undefined` pelos seguintes motivos:

- O valor de DateTimePart especificado é inválido
- O numeric_expr especificado não é um inteiro válido
- O DateTime no argumento ou resultado não é um DateTime ISO 8601 válido.

## <a name="examples"></a>Exemplos
  
O exemplo a seguir adiciona 1 mês ao DateTime: `2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("mm", 1, "2020-07-09T23:20:13.4575530Z") AS OneMonthLater
```

```json
[
    {
        "OneMonthLater": "2020-08-09T23:20:13.4575530Z"
    }
]
```  

O exemplo a seguir subtrai 2 horas do DateTime: `2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("hh", -2, "2020-07-09T23:20:13.4575530Z") AS TwoHoursEarlier
```

```json
[
    {
        "TwoHoursEarlier": "2020-07-09T21:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>Próximas etapas

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
