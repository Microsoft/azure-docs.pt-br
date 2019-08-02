---
title: Consultas parametrizadas no Azure Cosmos DB
description: Saiba mais sobre consultas SQL parametrizada
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 2bfc22346c1dd43d7d3c2937ffc286e48ae774d0
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342802"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Consultas parametrizadas no Azure Cosmos DB

O cosmos DB dá suporte a consultas com parâmetros expressados pela familiar notação @. SQL parametrizado fornece tratamento robusto e escape da entrada do usuário e evita a exposição acidental de dados por meio de injeção de SQL.

## <a name="examples"></a>Exemplos

Por exemplo, você pode escrever uma consulta que demora `lastName` e `address.state` como parâmetros e executá-lo para vários valores de `lastName` e `address.state` com base na entrada do usuário.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Você pode enviar essa solicitação ao Cosmos DB como uma consulta JSON parametrizada semelhante ao seguinte:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

O exemplo a seguir define o argumento TOP com uma consulta parametrizada: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Valores de parâmetro podem ser qualquer JSON válido: cadeias de caracteres, números, boolianos, nulos, ou mesmo matrizes e JSON aninhado. Como o Cosmos DB é sem esquema, os parâmetros não são validados em relação a qualquer tipo.


## <a name="next-steps"></a>Próximas etapas

- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Dados de documento de modelo](modeling-data.md)
