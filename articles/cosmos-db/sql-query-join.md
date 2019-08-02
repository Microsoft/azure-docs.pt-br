---
title: Consultas SQL JOIN para o Azure Cosmos DB
description: Saiba mais sobre a sintaxe SQL JOIN para o Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 408ee11b318143b3128833a741e04dd68f3816ed
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342424"
---
# <a name="joins-in-azure-cosmos-db"></a>Ingressa no Azure Cosmos DB

Em um banco de dados relacional, junções entre tabelas são o padrão lógico para criar esquemas normalizados. Em contraste, a API do SQL usa o modelo de dados desnormalizados de itens sem esquema, que é a lógica equivalente de um *autojunção*.

Junções internas resultam em um produto completo cruzando os conjuntos associados à junção. O resultado de uma junção de N maneiras é um conjunto de tuplas com N elementos, em que cada valor na tupla é associado ao alias do conjunto membro da junção e pode ser acessado pela referência desse alias em outras cláusulas.

## <a name="syntax"></a>Sintaxe

A linguagem dá suporte a sintaxe `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Esta consulta retorna um conjunto de tuplas com `N` valores. Cada tupla possui valores produzidos pela iteração de todos os aliases de contêiner sobre seus respectivos conjuntos. 

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

Os exemplos a seguir mostram como a cláusula junção funciona. No exemplo a seguir, o resultado é vazio, porque o produto cruzado de cada item de origem e um conjunto vazio é vazio:

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

No exemplo a seguir, a junção é um produto cruzado entre dois objetos JSON, a raiz do item `id` e o `children` sub-raiz. O fato de que `children` é uma matriz não é eficaz na junção, como ele lida com uma única raiz que é o `children` matriz. O resultado contém apenas dois resultados, porque o produto cruzado de cada item com a matriz produz exatamente apenas um item.

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

A fonte FROM da cláusula de junção é um iterador. Portanto, o fluxo no exemplo anterior é:  

1. Expanda cada elemento filho `c` na matriz.
2. Aplique um produto cruzado com a raiz do item `f` com cada elemento filho `c` que a primeira etapa nivelado.
3. Por fim, o objeto raiz do projeto `f` `id` propriedade sozinha.

O primeiro item, `AndersenFamily`, contém apenas um `children` elemento, portanto, o conjunto de resultados contém apenas um único objeto. O segundo item, `WakefieldFamily`, contém dois `children`, portanto, o produto cruzado produz dois objetos, um para cada `children` elemento. Os campos raiz em ambos os itens são os mesmos, exatamente como você esperaria em um produto cruzado.

A utilidade real da cláusula de junção é formar tuplas do produto cruzado em uma forma que é difícil ao projeto. O exemplo a seguir filtros na combinação de uma tupla que permite que o usuário escolha uma condição satisfeita pelas tuplas de modo geral.

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

A seguinte extensão do exemplo anterior realiza uma junção dupla. Você pode exibir o produto cruzado como o pseudocódigo a seguir:

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

`AndersenFamily` tem um filho que tem um animal de estimação, portanto, o produto cruzado traz uma linha (1\*1\*1) desta família. `WakefieldFamily` tem dois filhos, apenas um dos quais tem animais de estimação, mas esse filho tem dois animais de estimação. O produto cruzado para essa família resulta em 1\*1\*2 = 2 linhas.

No exemplo a seguir, há um filtro adicional no `pet`, que exclui todas as tuplas em que o nome do Animal não é `Shadow`. Você pode criar tuplas de matrizes, filtrar qualquer um dos elementos da tupla e qualquer combinação dos elementos do projeto.

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

## <a name="next-steps"></a>Próximas etapas

- [Guia de Introdução](sql-query-getting-started.md)
- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Subconsultas](sql-query-subquery.md)