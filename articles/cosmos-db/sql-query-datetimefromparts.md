---
title: DateTimeFromParts na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL DateTimeFromParts no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: b0e7996b71e68db371201da1f0f5c93486ae4e29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592081"
---
# <a name="datetimefromparts-azure-cosmos-db"></a>DateTimeFromParts (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Retorna um valor DateTime da cadeia de caracteres construído a partir de valores de entrada.
  
## <a name="syntax"></a>Sintaxe
  
```sql
DateTimeFromParts(<numberYear>, <numberMonth>, <numberDay> [, numberHour]  [, numberMinute]  [, numberSecond] [, numberOfFractionsOfSecond])
```

## <a name="arguments"></a>Argumentos
  
*numberYear* Valor inteiro para o ano no formato `YYYY`

*numberMonth*  
   Valor inteiro para o mês no formato `MM`

*numberDay*  
   Valor inteiro para o dia no formato `DD`

*numberHour* (opcional) valor inteiro para a hora no formato `hh`

*numberMinute* (opcional) valor inteiro para o minuto no formato `mm`

*numberSecond* (opcional) valor inteiro para o segundo no formato `ss`

*numberOfFractionsOfSecond* (opcional) valor inteiro para a fração de um segundo no formato `.fffffff`

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
  
 Para obter mais informações sobre o formato ISO 8601, consulte [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Comentários

Se os inteiros especificados criarem um DateTime inválido, DateTimeFromParts retornará `undefined` .

Se um argumento opcional não for especificado, seu valor será 0.

## <a name="examples"></a>Exemplos

Aqui está um exemplo que inclui apenas os argumentos necessários para construir um DateTime:

```sql
SELECT DateTimeFromParts(2020, 9, 4) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T00:00:00.0000000Z"
    }
]
```

Veja outro exemplo que também usa alguns argumentos opcionais para construir um DateTime:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:00.0000000Z"
    }
]
```

Veja outro exemplo que também usa todos os argumentos opcionais para construir um DateTime:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52, 12, 3456789) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:12.3456789Z"
    }
]
```

## <a name="next-steps"></a>Próximas etapas

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
