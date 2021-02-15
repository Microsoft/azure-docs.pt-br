---
title: Funções de agregação no Azure Cosmos DB
description: Saiba mais sobre a sintaxe da função de agregação SQL, tipos de funções de agregação com suporte pelo Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.openlocfilehash: c0d953c8d99582f63744d51b505852b5c44bc409
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551859"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Funções de agregação no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

As funções de agregação executam um cálculo em um conjunto de valores na `SELECT` cláusula e retornam um único valor. Por exemplo, a consulta a seguir retorna a contagem de itens dentro de um contêiner:

```sql
    SELECT COUNT(1)
    FROM c
```

## <a name="types-of-aggregate-functions"></a>Tipos de funções de agregação

A API do SQL dá suporte às funções de agregação a seguir. `SUM` e `AVG` operar em valores numéricos, e `COUNT` , `MIN` e `MAX` trabalhar em números, cadeias de caracteres, Boolianos e nulos.

| Função | Descrição |
|-------|-------------|
| [AVG](sql-query-aggregate-avg.md) | Retorna a média dos valores na expressão. |
| [COUNT](sql-query-aggregate-count.md) | Retorna o número de itens na expressão. |
| [MAX](sql-query-aggregate-max.md) | Retorna o valor máximo na expressão. |
| [MIN](sql-query-aggregate-min.md) | Retorna o valor mínimo na expressão. |
| [SUM](sql-query-aggregate-sum.md) | Retorna a soma de todos os valores na expressão. |


Você também pode retornar apenas o valor escalar da agregação usando a palavra-chave VALUE. Por exemplo, a consulta a seguir retorna a contagem de valores como um único número:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Os resultados são:

```json
    [ 2 ]
```

Você também pode combinar agregações com filtros. Por exemplo, a consulta a seguir retorna a contagem de itens com o estado de endereço de `WA` .

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Os resultados são:

```json
    [ 1 ]
```

## <a name="remarks"></a>Comentários

Essas funções de sistema agregadas se beneficiarão de um [índice de intervalo](index-policy.md#includeexclude-strategy). Se você pretende fazer um `AVG` , `COUNT` , `MAX` , `MIN` ou `SUM` em uma propriedade, deve [incluir o caminho relevante na política de indexação](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções do sistema](sql-query-system-functions.md)
- [Funções definidas pelo usuário](sql-query-udfs.md)