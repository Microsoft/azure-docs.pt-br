---
title: TimestampToDateTime na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL TimestampToDateTime no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: e9a77ab0ac32d627d59e2cb0fa4a680f174a6833
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587229"
---
# <a name="timestamptodatetime-azure-cosmos-db"></a>TimestampToDateTime (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Converte o valor de carimbo de data/hora especificado em um DateTime.
  
## <a name="syntax"></a>Sintaxe
  
```sql
TimestampToDateTime (<Timestamp>)
```

## <a name="arguments"></a>Argumentos

*Timestamp*  

Um valor numérico assinado, o número atual de milissegundos decorridos desde a época do UNIX. Em outras palavras, o número de milissegundos decorridos desde 00:00:00 quinta-feira, 1 de janeiro de 1970.

## <a name="return-types"></a>Tipos de retorno

Retorna o valor de cadeia de caracteres do UTC de data e hora do ISO 8601 no formato `YYYY-MM-DDThh:mm:ss.fffffffZ` em que:
  
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

TimestampToDateTime retornará `undefined` se o valor de carimbo de data/hora especificado for inválido.

## <a name="examples"></a>Exemplos
  
O exemplo a seguir converte o carimbo de data/hora em um DateTime:

```sql
SELECT TimestampToDateTime(1594227912345) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-08T17:05:12.3450000Z"
    }
]
```  

## <a name="next-steps"></a>Próximas etapas

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
