---
title: Consultas do SQL JOIN para Azure Cosmos DB
description: Saiba como unir várias tabelas em Azure Cosmos DB para consultar os dados
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: tisande
ms.openlocfilehash: cb7b2e62a9fabeeca675edb8e6aa356213e0999e
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98011366"
---
# <a name="joins-in-azure-cosmos-db"></a>Junções no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Em um banco de dados relacional, as junções entre as tabelas são o registro lógico da criação de esquemas normalizados. Por outro lado, a API do SQL usa o modelo de dados desnormalizado de itens sem esquema, que é o equivalente lógico de uma *auto-associação*.

As junções resultam em um produto cruzado completo dos conjuntos que participam da junção. O resultado de uma junção de N maneiras é um conjunto de tuplas com N elementos, em que cada valor na tupla é associado ao alias do conjunto membro da junção e pode ser acessado pela referência desse alias em outras cláusulas.

## <a name="syntax"></a>Syntax

O idioma dá suporte à sintaxe `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>` . Essa consulta retorna um conjunto de tuplas com `N` valores. Cada tupla possui valores produzidos pela iteração de todos os aliases de contêiner sobre seus respectivos conjuntos. 

Vamos começar com a seguinte cláusula FROM: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Permite que cada fonte defina `input_alias1, input_alias2, …, input_aliasN`. Essa cláusula FROM retorna um conjunto de tuplas N (tupla com valores N). Cada tupla possui valores produzidos pela iteração de todos os aliases de contêiner sobre seus respectivos conjuntos.  
  
**Exemplo 1** - 2 fontes  
  
- Permite que `<from_source1>` tenha escopo de contêiner e represente o conjunto {A, B, C}.  
  
- Permite que `<from_source2>` tenha escopo de documento, referenciando input_alias1 e represente os conjuntos:  
  
    {1,2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4,5} para`input_alias1 = C,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2>` resultará nas tuplas abaixo:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Exemplo 2** - 3 fontes  
  
- Permite que `<from_source1>` tenha escopo de contêiner e represente o conjunto {A, B, C}.  
  
- Permite que `<from_source2>` tenha escopo de documento, referenciando `input_alias1` e represente os conjuntos:  
  
    {1,2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4,5} para`input_alias1 = C,`  
  
- Permite que `<from_source3>` tenha escopo de documento, referenciando `input_alias2` e represente os conjuntos:  
  
    {100, 200} para`input_alias2 = 1,`  
  
    {300} para `input_alias2 = 3,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` resultará nas tuplas abaixo:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Falta de tuplas para outros valores de `input_alias1`, `input_alias2`, para os quais `<from_source3>` não retornou nenhum valor.  
  
**Exemplo 3** - 3 fontes  
  
- Permite que <from_source1> tenha escopo de contêiner e represente o conjunto {A, B, C}.  
  
- Permite que `<from_source1>` tenha escopo de contêiner e represente o conjunto {A, B, C}.  
  
- Permite que <from_source2> tenha escopo de documento, referenciando input_alias1 e represente os conjuntos:  
  
    {1,2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4,5} para`input_alias1 = C,`  
  
- Permite que `<from_source3>` tenha escopo `input_alias1` e represente os conjuntos:  
  
    {100, 200} para`input_alias2 = A,`  
  
    {300} para `input_alias2 = C,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` resultará nas tuplas abaixo:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200),  (C, 4, 300) , (C, 5, 300)  
  
  > [!NOTE]
  > Isso resultou em produto cruzado entre `<from_source2>` e `<from_source3>` porque ambos têm o escopo para a mesma `<from_source1>`.  Isso resultou em 4 (2 x 2) tuplas com valor A, 0 tuplas com valor B (1 x 0) e 2 (2 x 1) tuplas com valor C.  
  
## <a name="examples"></a>Exemplos

Os exemplos a seguir mostram como a cláusula junção funciona. Antes de executar esses exemplos, carregue os [dados da família](sql-query-getting-started.md#upload-sample-data)de exemplo. No exemplo a seguir, o resultado está vazio, pois o produto cruzado de cada item da origem e um conjunto vazio está vazio:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

O resultado é:

```json
    [{
    }]
```

No exemplo a seguir, a junção é um produto cruzado entre dois objetos JSON, a raiz do item `id` e a `children` subraiz. O fato de que `children` uma matriz não é eficaz na junção, porque lida com uma única raiz que é a `children` matriz. O resultado contém apenas dois resultados, porque o produto cruzado de cada item com a matriz produz exatamente apenas um item.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

Os resultados são:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

O exemplo a seguir mostra uma junção mais convencional:

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

Os resultados são:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

A origem da cláusula de junção é um iterador. Portanto, o fluxo no exemplo anterior é:  

1. Expanda cada elemento filho `c` na matriz.
2. Aplique um produto cruzado com a raiz do item a `f` cada elemento filho `c` que a primeira etapa achatada.
3. Por fim, projeto apenas a propriedade do objeto raiz `f` `id` .

O primeiro item, `AndersenFamily` , contém apenas um `children` elemento, portanto, o conjunto de resultados contém apenas um único objeto. O segundo item, `WakefieldFamily` , contém dois `children` , portanto, o produto cruzado produz dois objetos, um para cada `children` elemento. Os campos raiz em ambos os itens são os mesmos, exatamente como você esperaria em um produto cruzado.

O verdadeiro utilitário da cláusula JOIN é formar tuplas do produto cruzado em uma forma que seja difícil de projetar. O exemplo a seguir filtra na combinação de uma tupla que permite que o usuário escolha uma condição satisfeita pelas tuplas como um todo.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

Os resultados são:

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

A seguinte extensão do exemplo anterior executa uma junção dupla. Você pode exibir o produto cruzado como o seguinte pseudocódigo:

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily` tem um filho que tem um animal de estimação, portanto, o produto cruzado produz uma linha (1 \* 1 \* 1) dessa família. `WakefieldFamily` tem dois filhos, apenas um de quem tem animais de estimação, mas esse filho tem dois animais de estimação. O produto cruzado para esta família gera 1 \* 1 \* 2 = 2 linhas.

No próximo exemplo, há um filtro adicional em `pet` , que exclui todas as tuplas em que o nome do animal de estimação não é `Shadow` . Você pode criar tuplas de matrizes, filtrar em qualquer um dos elementos da tupla e projetar qualquer combinação dos elementos.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

Os resultados são:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

Se sua consulta tiver uma junção e filtros, você poderá reescrever parte da consulta como uma [subconsulta](sql-query-subquery.md#optimize-join-expressions) para melhorar o desempenho.

## <a name="next-steps"></a>Próximas etapas

- [Guia de Introdução](sql-query-getting-started.md)
- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Subconsultas](sql-query-subquery.md)