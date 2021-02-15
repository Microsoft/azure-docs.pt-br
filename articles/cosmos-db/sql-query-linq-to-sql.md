---
title: Conversão de LINQ to SQL em Azure Cosmos DB
description: Conheça os operadores LINQ com suporte e como as consultas LINQ são mapeadas para consultas SQL no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: tisande
ms.openlocfilehash: 122c95fe9ac017ad7a6957dcdb8323837be34f21
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96545376"
---
# <a name="linq-to-sql-translation"></a>Tradução de LINQ em SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O provedor de consulta Azure Cosmos DB executa um mapeamento de melhor esforço de uma consulta LINQ em uma consulta SQL Cosmos DB. Se você quiser obter a consulta SQL que é convertida do LINQ, use o `ToString()` método no objeto gerado `IQueryable` . A descrição a seguir pressupõe uma familiaridade básica com o [LINQ](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries). Além do LINQ, o Azure Cosmos DB também dá suporte a [Entity Framework Core](/ef/core/providers/cosmos/?tabs=dotnet-core-cli) que funciona com a API do SQL.

O sistema do tipo de provedor de consulta dá suporte apenas aos tipos primitivos JSON: Numeric, Boolean, String e NULL.

O provedor de consultas oferece suporte às seguintes expressões escalares:

- Valores constantes, incluindo valores constantes dos tipos de dados primitivos no tempo de avaliação da consulta.
  
- Expressões de índice de matriz/propriedade que se referem à propriedade de um objeto ou de um elemento de matriz. Por exemplo:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Expressões aritméticas, incluindo expressões aritméticas comuns em valores numéricos e boolianos. Para obter a lista completa, consulte a [especificação de Azure Cosmos DB SQL](sql-query-aggregate-functions.md).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Expressões de comparação de cadeia de caracteres, que incluem a comparação de um valor de cadeia de caracteres com um valor de cadeia de caracteres  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Expressões de criação de objeto/matriz, que retornam um objeto de tipo de valor composto ou tipo anônimo, ou uma matriz desses objetos. Você pode aninhar esses valores.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="using-linq"></a>Usando o LINQ

Você pode criar uma consulta LINQ com `GetItemLinqQueryable` . Este exemplo mostra a geração de consulta LINQ e a execução assíncrona com um `FeedIterator` :

```csharp
using (FeedIterator<Book> setIterator = container.GetItemLinqQueryable<Book>()
                      .Where(b => b.Title == "War and Peace")
                      .ToFeedIterator<Book>())
 {
     //Asynchronous query execution
     while (setIterator.HasMoreResults)
     {
         foreach(var item in await setIterator.ReadNextAsync()){
         {
             Console.WriteLine(item.cost);
         }
       }
     }
 }
```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>Operadores LINQ com suporte

O provedor LINQ incluído no SDK do .NET do SQL dá suporte aos seguintes operadores:

- **Select**: projeções traduzir para [selecionar](sql-query-select.md), incluindo a construção de objetos.
- **Onde**: os filtros são convertidos em [Where](sql-query-where.md)e dão suporte à conversão entre `&&` , `||` e `!` para os operadores SQL
- **SelectMany**: permite o desenrolamento de matrizes para a cláusula [Join](sql-query-join.md) . Use para encadear ou aninhar expressões para filtrar elementos de matriz.
- **OrderBy** e **OrderByDescending**: translate para [order by](sql-query-order-by.md) com ASC ou Desc.
- Operadores **Count**, **sum**, **min**, **Max** e **Average** para [agregação](sql-query-aggregate-functions.md)e seus equivalentes assíncronos **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** e **AverageAsync**.
- **CompareTo**: é convertido em comparações de intervalo. Comumente usado para cadeias de caracteres, pois não são comparáveis em .NET.
- **Skip** e **Take**: está relacionado ao [deslocamento e ao limite](sql-query-offset-limit.md) para limitar os resultados de uma consulta e fazer a paginação.
- **Funções matemáticas**: oferece suporte à conversão de .net,,,,,,,,,,,,, `Abs` `Acos` `Asin` `Atan` `Ceiling` ,, `Cos` `Exp` `Floor` `Log` `Log10` `Pow` `Round` `Sign` `Sin` `Sqrt` `Tan` e `Truncate` para as [funções matemáticas internas](sql-query-mathematical-functions.md)equivalentes.
- **Funções de cadeia de caracteres**: dá suporte à conversão de .net,,,,,,, `Concat` `Contains` `Count` `EndsWith` `IndexOf` `Replace` `Reverse` `StartsWith` ,,, `SubString` `ToLower` `ToUpper` , `TrimEnd` e `TrimStart` para as [funções de cadeia de caracteres internas](sql-query-string-functions.md)equivalentes.
- **Funções de matriz**: oferece suporte à conversão do .NET `Concat` , `Contains` `Count` ao e às funções de [matriz internas](sql-query-array-functions.md)equivalentes.
- **Funções de extensão geoespaciais**: oferece suporte à conversão de métodos stub `Distance` , `IsValid` ,, `IsValidDetailed` e `Within` às [funções geoespaciais internas](sql-query-geospatial-query.md)equivalentes.
- **Função de extensão de função definida pelo usuário**: dá suporte à conversão do método stub `UserDefinedFunctionProvider.Invoke` para a [função definida pelo usuário](sql-query-udfs.md)correspondente.
- **Miscelânea**: dá suporte à conversão de `Coalesce` [operadores](sql-query-operators.md)condicionais e condicional. Pode converter `Contains` em cadeia de caracteres contém, ARRAY_CONTAINS ou no, dependendo do contexto.

## <a name="examples"></a>Exemplos

Os exemplos a seguir ilustram como alguns dos operadores de consulta LINQ padrão são convertidos em consultas no Azure Cosmos DB.

### <a name="select-operator"></a>Selecionar operador

A sintaxe é `input.Select(x => f(x))`, em que `f` é uma expressão escalar. O `input` , nesse caso, seria um `IQueryable` objeto.

**Selecione operador, exemplo 1:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Selecione operador, exemplo 2:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Selecione operador, exemplo 3:**

- **Expressão lambda do LINQ**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

### <a name="selectmany-operator"></a>Operador SelectMany

A sintaxe é `input.SelectMany(x => f(x))`, em que `f` é uma expressão escalar que retorna um tipo de contêiner.

- **Expressão lambda do LINQ**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Operador Where

A sintaxe é `input.Where(x => f(x))`, em que `f` é uma expressão escalar que retorna um valor booliano.

**Operador where, exemplo 1:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Operador where, exemplo 2:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

## <a name="composite-sql-queries"></a>Consultas SQL composta

Você pode compor os operadores anteriores para formar consultas mais poderosas. Como Cosmos DB dá suporte a Contêineres aninhados, você pode concatenar ou aninhar a composição.

### <a name="concatenation"></a>Concatenação

A sintaxe é `input(.|.SelectMany())(.Select()|.Where())*`. Uma consulta concatenada pode começar com uma `SelectMany` consulta opcional, seguida por vários `Select` `Where` operadores or.

**Concatenação, exemplo 1:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Concatenação, exemplo 2:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**Concatenação, exemplo 3:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**Concatenação, exemplo 4:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

### <a name="nesting"></a>Aninhamento

A sintaxe é `input.SelectMany(x=>x.Q())` Where `Q` é um `Select` `SelectMany` operador, ou `Where` .

Uma consulta aninhada aplica a consulta interna a cada elemento do contêiner externo. Um recurso importante é que a consulta interna pode se referir aos campos dos elementos no contêiner externo, como uma auto-associação.

**Aninhamento, exemplo 1:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**Aninhamento, exemplo 2:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**Aninhamento, exemplo 3:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```

## <a name="next-steps"></a>Próximas etapas

- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dados de documento modelo](modeling-data.md)