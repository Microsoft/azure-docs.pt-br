---
title: Operadores de consulta SQL para Azure Cosmos DB
description: Saiba mais sobre os operadores SQL, como igualdade, comparação e operadores lógicos com suporte pelo Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: c1409bd7f098c24efbb4196d78c6dffb6048119b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93335409"
---
# <a name="operators-in-azure-cosmos-db"></a>Operadores no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo fornece detalhes sobre os vários operadores com suporte pelo Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Operadores de igualdade e comparação

A tabela a seguir mostra o resultado de comparações de igualdade na API do SQL entre dois tipos JSON quaisquer.

| **Parar** | **Indefinido** | **Nulo** | **Boolean** | **Número** | **Cadeia de caracteres** | **Objeto** | **Matriz** |
|---|---|---|---|---|---|---|---|
| **Indefinido** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido |
| **Nulo** | Indefinido | **Okey** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido |
| **Boolean** | Indefinido | Indefinido | **Okey** | Indefinido | Indefinido | Indefinido | Indefinido |
| **Número** | Indefinido | Indefinido | Indefinido | **Okey** | Indefinido | Indefinido | Indefinido |
| **Cadeia de caracteres** | Indefinido | Indefinido | Indefinido | Indefinido | **Okey** | Indefinido | Indefinido |
| **Objeto** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | **Okey** | Indefinido |
| **Matriz** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | **Okey** |

Para operadores de comparação como `>` ,,, `>=` `!=` `<` e `<=` , a comparação entre os tipos ou entre dois objetos ou matrizes produz `Undefined` .  

Se o resultado da expressão escalar for `Undefined` , o item não será incluído no resultado, porque `Undefined` não é igual a `true` .

Por exemplo, a comparação da consulta a seguir entre um número e um valor de cadeia de caracteres produz `Undefined` . Portanto, o filtro não inclui nenhum resultado.

```sql
SELECT *
FROM c
WHERE 7 = 'a'
```

## <a name="logical-and-or-and-not-operators"></a>Operadores lógicos (AND, OR e NOT)

Operadores lógicos funcionam em valores boolianos. As tabelas a seguir mostram as tabelas lógicas da verdade para esses operadores:

**Operador OR**

Retorna `true` quando uma das condições é `true` .

|  | **Verdadeiro** | **Falso** | **Indefinido** |
| --- | --- | --- | --- |
| **True** |True |True |True |
| **Falso** |Verdadeiro |Falso |Indefinido |
| **Indefinido** |Verdadeiro |Indefinido |Indefinido |

**Operador AND**

Retorna `true` quando ambas as expressões são `true` .

|  | **Verdadeiro** | **Falso** | **Indefinido** |
| --- | --- | --- | --- |
| **True** |Verdadeiro |Falso |Indefinido |
| **Falso** |Falso |Falso |Falso |
| **Indefinido** |Indefinido |Falso |Indefinido |

**Operador NOT**

Reverte o valor de qualquer expressão booleana.

|  | **NOT** |
| --- | --- |
| **True** |Falso |
| **Falso** |Verdadeiro |
| **Indefinido** |Indefinido |

**Precedência de operador**

Os operadores lógicos `OR` , `AND` e `NOT` têm o nível de precedência mostrado abaixo:

| **Operador** | **Prioridade** |
| --- | --- |
| **NOT** |1 |
| **AND** |2 |
| **OR** |3 |

## <a name="-operator"></a>Operador *

O operador especial * projeta o item inteiro como está. Quando usado, ele deve ser o único campo projetado. Uma consulta como `SELECT * FROM Families f` é válida, mas `SELECT VALUE * FROM Families f`  `SELECT *, f.id FROM Families f` não é válida.

## <a name="-and--operators"></a>? e?? operadores

Você pode usar os operadores ternário (?) e de União (??) para criar expressões condicionais, como em linguagens de programação, como C# e JavaScript.

Você pode usar o? operador para construir novas propriedades JSON em tempo real. Por exemplo, a consulta a seguir classifica os níveis de nível em `elementary` ou `other` :

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Você também pode aninhar chamadas para o? como na consulta a seguir: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Como ocorre com outros operadores de consulta, o? o operador exclui itens se as propriedades referenciadas estiverem ausentes ou os tipos que estão sendo comparados forem diferentes.

Usar o?? para verificar com eficiência uma propriedade em um item ao consultar dados semiestruturados ou de tipo misto. Por exemplo, a consulta a seguir retorna `lastName` se presente, ou `surname` se `lastName` não estiver presente.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Próximas etapas

- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Palavras-chave](sql-query-keywords.md)
- [Cláusula SELECT](sql-query-select.md)
