---
title: Funções de agregação no Azure Cosmos DB
description: Saiba mais sobre a sintaxe de função de agregação SQL do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: a6937e9e811ea8e44eda6f2bcb5d2c7d78db4934
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342750"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Funções de agregação no Azure Cosmos DB

Funções agregadas executam um cálculo em um conjunto de valores na cláusula SELECT e retornam um único valor. Por exemplo, a consulta a seguir retorna a contagem de itens dentro de `Families` contêiner:

## <a name="examples"></a>Exemplos

```sql
    SELECT COUNT(1)
    FROM Families f
```

Os resultados são:

```json
    [{
        "$1": 2
    }]
```

Você também pode retornar apenas o valor escalar da agregação usando a palavra-chave do valor. Por exemplo, a consulta a seguir retorna a contagem de valores como um único número:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Os resultados são:

```json
    [ 2 ]
```

Você também pode combinar as agregações com filtros. Por exemplo, a consulta a seguir retorna a contagem de itens com o estado do endereço do `WA`.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Os resultados são:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Tipos de funções de agregação

A API de SQL suporta as seguintes funções de agregação. SUM e AVG operam em valores numéricos e COUNT, MIN e MAX trabalham em números, cadeias de caracteres, boolianos e nulos.

| Função | DESCRIÇÃO |
|-------|-------------|
| COUNT | Retorna o número de itens na expressão. |
| SUM   | Retorna a soma de todos os valores na expressão. |
| MÍN.   | Retorna o valor mínimo na expressão. |
| MÁX.   | Retorna o valor máximo na expressão. |
| AVG   | Retorna a média dos valores na expressão. |

Você também pode agregar os resultados de uma iteração de matriz.

> [!NOTE]
> No Gerenciador de dados do portal do Azure, as consultas de agregação podem agregar resultados parciais em página de apenas uma consulta. O SDK produz um único valor cumulativo em todas as páginas. Para executar consultas de agregação usando o código, você precisa do .NET SDK 1.12.0, .NET Core SDK 1.1.0 ou Java SDK 1.9.5 ou posterior.

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções do sistema](sql-query-system-functions.md)
- [Funções definidas pelo usuário](sql-query-udfs.md)