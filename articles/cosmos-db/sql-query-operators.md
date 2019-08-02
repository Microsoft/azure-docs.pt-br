---
title: Operadores de consulta SQL do Azure Cosmos DB
description: Saiba mais sobre operadores do SQL do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: eecc1522f8c260286c7dd7fc4c2e58d5d8caa8fb
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342528"
---
# <a name="operators-in-azure-cosmos-db"></a>Operadores no Azure Cosmos DB

Este artigo fornece detalhes sobre os vários operadores com suporte do Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Operadores de comparação e igualdade

A tabela a seguir mostra o resultado de comparações de igualdade na API do SQL entre dois tipos JSON quaisquer.

| **Op** | **Indefinido** | **Nulo** | **Booliano** | **Número** | **Cadeia de caracteres** | **Objeto** | **Matriz** |
|---|---|---|---|---|---|---|---|
| **Indefinido** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido |
| **Nulo** | Indefinido | **Ok** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido |
| **Booliano** | Indefinido | Indefinido | **Ok** | Indefinido | Indefinido | Indefinido | Indefinido |
| **Número** | Indefinido | Indefinido | Indefinido | **Ok** | Indefinido | Indefinido | Indefinido |
| **Cadeia de caracteres** | Indefinido | Indefinido | Indefinido | Indefinido | **Ok** | Indefinido | Indefinido |
| **Objeto** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | **Ok** | Indefinido |
| **Matriz** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | **Ok** |

Para operadores de comparação, como `>`, `>=`, `!=`, `<`, e `<=`, comparação em todos os tipos ou entre dois objetos ou matrizes produz `Undefined`.  

Se o resultado da expressão escalar `Undefined`, o item não está incluído no resultado, porque `Undefined` não é igual a `true`.

## <a name="logical-and-or-and-not-operators"></a>Operadores lógicos (AND, OR e NOT)

Operadores lógicos funcionam em valores boolianos. As tabelas a seguir mostram as tabelas de verdade lógicas desses operadores:

**Operador OU**

| OU | True | Falso | Indefinido |
| --- | --- | --- | --- |
| True |True |True |True |
| Falso |True |Falso |Indefinido |
| Indefinido |True |Indefinido |Indefinido |

**E o operador**

| E | True | Falso | Indefinido |
| --- | --- | --- | --- |
| True |True |Falso |Indefinido |
| Falso |Falso |Falso |Falso |
| Indefinido |Indefinido |Falso |Indefinido |

**NÃO operador**

| NÃO |  |
| --- | --- |
| True |Falso |
| Falso |True |
| Indefinido |Indefinido |


## <a name="-operator"></a>* operador

O operador especial * projetos todo o item como está. Quando usado, ele deve ser o único campo projetado. Uma consulta como `SELECT * FROM Families f` é válido, mas `SELECT VALUE * FROM Families f` e `SELECT *, f.id FROM Families f` não são válidos.

## <a name="-and--operators"></a>? e?? Operadores

Você pode usar o Ternário (?) e de união (?) operadores para construir expressões condicionais, como na programação de linguagens, como C# e JavaScript. 

Você pode usar o? operador para construir novas propriedades JSON em tempo real. Por exemplo, a consulta a seguir classifica os níveis de nível empresarial em `elementary` ou `other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Você também pode aninhar chamadas para o? operador, como a consulta a seguir: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Assim como acontece com outros operadores de consulta, o? operador exclui itens se as propriedades de referência estão ausentes ou os tipos que estão sendo comparados forem diferentes.

Use a?? operador para verificar uma propriedade em um item com eficiência ao consultar em relação aos dados semi-estruturados ou tipo misto. Por exemplo, a seguinte consulta retorna `lastName` se estiver presente, ou `surname` se `lastName` não estiver presente.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Próximas etapas

- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Palavras-chave](sql-query-keywords.md)
- [Cláusula SELECT](sql-query-select.md)
