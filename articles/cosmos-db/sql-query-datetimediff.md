---
title: DateTimeDiff na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL DateTimeDiff no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: aeea2905b6bae094c92bd8b5d46523225c745494
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595627"
---
# <a name="datetimediff-azure-cosmos-db"></a>DateTimeDiff (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
Retorna a contagem (como um valor inteiro assinado) dos limites de DateTimePart especificados cruzados entre o *StartDate* e *EndDate* especificados.
  
## <a name="syntax"></a>Sintaxe
  
```sql
DateTimeDiff (<DateTimePart> , <StartDate> , <EndDate>)
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

*Início*  
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

*Término*  
   Valor de cadeia de caracteres UTC de data e hora do ISO 8601 no formato `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Tipos de retorno

Retorna um valor inteiro assinado.

## <a name="remarks"></a>Comentários

DateTimeDiff retornará `undefined` pelos seguintes motivos:

- O valor de DateTimePart especificado é inválido
- StartDate ou EndDate não é um DateTime ISO 8601 válido

DateTimeDiff sempre retornará um valor inteiro assinado e é uma medida do número de limites de DateTimePart cruzados, não a medição do intervalo de tempo.

## <a name="examples"></a>Exemplos
  
O exemplo a seguir computa o número de limites de dia cruzado entre `2020-01-01T01:02:03.1234527Z` e `2020-01-03T01:02:03.1234567Z` .

```sql
SELECT DateTimeDiff("day", "2020-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInDays
```

```json
[
    {
        "DifferenceInDays": 2
    }
]
```  

O exemplo a seguir computa o número de limites de ano cruzado entre `2028-01-01T01:02:03.1234527Z` e `2020-01-03T01:02:03.1234567Z` .

```sql
SELECT DateTimeDiff("yyyy", "2028-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInYears
```

```json
[
    {
        "DifferenceInYears": -8
    }
]
```

O exemplo a seguir computa o número de limites de hora cruzados entre `2020-01-01T01:00:00.1234527Z` e `2020-01-01T01:59:59.1234567Z` . Embora esses valores DateTime tenham mais de 0,99 horas de distância, `DateTimeDiff` retorna 0 porque nenhum limite de hora foi ultrapassado.

```sql
SELECT DateTimeDiff("hh", "2020-01-01T01:00:00.1234527Z", "2020-01-01T01:59:59.1234567Z") AS DifferenceInHours
```

```json
[
    {
        "DifferenceInHours": 0
    }
]
```

## <a name="next-steps"></a>Próximas etapas

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
