---
title: Consultas parametrizadas no Azure Cosmos DB
description: Saiba como as consultas parametrizadas do SQL fornecem manipulação e saída robustas de entrada do usuário e evitam a exposição acidental de dados por meio de injeção de SQL.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: dc32aab89e50b500001fd2267f62e3031154be62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96549150"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Consultas parametrizadas no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB dá suporte a consultas com parâmetros expressos pela conhecida @ Notation. O SQL com parâmetros fornece manipulação e saída robustas de entrada do usuário e impede a exposição acidental de dados por meio de injeção de SQL.

## <a name="examples"></a>Exemplos

Por exemplo, você pode escrever uma consulta que aceite `lastName` e `address.state` como parâmetros e execute-a para vários valores de `lastName` e `address.state` com base na entrada do usuário.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Em seguida, você pode enviar essa solicitação para Azure Cosmos DB como uma consulta JSON parametrizada como a seguinte:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

O exemplo a seguir define o argumento superior com uma consulta parametrizada:

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Os valores de parâmetro podem ser qualquer JSON: cadeias de caracteres, números, Boolianos, nulos, pares ou JSON aninhados válidos. Como Azure Cosmos DB não tem Esquema, os parâmetros não são validados em relação a nenhum tipo.

Aqui estão exemplos de consultas parametrizadas em cada SDK Azure Cosmos DB:

- [SDK .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L195)
- [Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421)
- [Node.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79)
- [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78)

## <a name="next-steps"></a>Próximas etapas

- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dados de documento modelo](modeling-data.md)
