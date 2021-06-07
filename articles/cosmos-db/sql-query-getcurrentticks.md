---
title: GetCurrentTicks na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL GetCurrentTicks no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 16004e6e471094c99229c32a63396ac3b0490905
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99524265"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Retorna o número de tiques 100-nanossegundos que foram decorridos desde 00:00:00 quinta-feira, 1 de janeiro de 1970.
  
## <a name="syntax"></a>Sintaxe
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Tipos de retorno

Retorna um valor numérico assinado, o número atual de tiques de 100-nanossegundos que foram decorridos desde a época do UNIX. Em outras palavras, GetCurrentTicks retorna o número de tiques de 100 nanossegundos decorridos desde 00:00:00 quinta-feira, 1 de janeiro de 1970.

## <a name="remarks"></a>Comentários

GetCurrentTicks () é uma função não determinística. O resultado retornado é UTC (tempo Universal Coordenado).

> [!NOTE]
> Essa função do sistema não usará o índice. Se você precisar comparar valores com a hora atual, obtenha a hora atual antes da execução da consulta e use esse valor de cadeia de caracteres constante na `WHERE` cláusula.

## <a name="examples"></a>Exemplos

Veja um exemplo que retorna a hora atual, medida em tiques:

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>Próximas etapas

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
