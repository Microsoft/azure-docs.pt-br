---
title: GetCurrentTimestamp na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL GetCurrentTimestamp no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: fa7d1ec2af12065fb7d761073cd982a561cf53c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99524254"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retorna o número de milissegundos decorridos desde 00:00:00 quinta-feira, 1 de janeiro de 1970.
  
## <a name="syntax"></a>Sintaxe
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Tipos de retorno
  
Retorna um valor numérico assinado, o número atual de milissegundos decorridos desde a época do UNIX, ou seja, o número de milissegundos decorridos desde 00:00:00 quinta-feira, 1 de janeiro de 1970.

## <a name="remarks"></a>Comentários

GetCurrentTimestamp () é uma função não determinística. O resultado retornado é UTC (tempo Universal Coordenado).

> [!NOTE]
> Essa função do sistema não usará o índice. Se você precisar comparar valores com a hora atual, obtenha a hora atual antes da execução da consulta e use esse valor de cadeia de caracteres constante na `WHERE` cláusula.

## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como obter o carimbo de data/hora atual usando a função interna GetCurrentTimestamp ().
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Veja um exemplo de conjunto de resultados.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Próximas etapas

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
