---
title: LINQ a tradução de SQL no Azure Cosmos DB
description: Mapeando consultas LINQ para consultas de SQL do Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 057614da8fd29e1208c2788049c5d6d1a985eed5
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342798"
---
# <a name="linq-to-sql-translation"></a>Tradução de LINQ em SQL

O provedor de consulta do Azure Cosmos DB realiza um mapeamento de melhor esforço de uma consulta LINQ para uma consulta SQL do Cosmos DB. A seguinte descrição pressupõe uma familiaridade básica com o LINQ.

O sistema de tipo de provedor de consulta dá suporte a apenas os tipos primitivos de JSON: numérico, booliano, cadeia de caracteres e nulos.

O provedor de consulta dá suporte a expressões escalares a seguir:

- Valores de constantes, incluindo valores de constantes de tipos de dados primitivos no momento da avaliação de consulta.
  
- Expressões de índice de matriz/propriedade se refere à propriedade de um objeto ou um elemento de matriz. Por exemplo:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Expressões aritméticas, inclusive expressões aritméticas comuns em valores numéricos e boolianos. Para obter a lista completa, consulte o [especificação de SQL do Azure Cosmos DB](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Expressões de comparação de cadeia de caracteres, que incluem a comparação de um valor de cadeia de caracteres para algum valor de cadeia de caracteres constante.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Expressões de criação de objeto/matriz, que retornam um objeto do tipo de valor composto ou tipo anônimo ou uma matriz desses objetos. Você pode aninhar esses valores.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a id="SupportedLinqOperators"></a>Operadores LINQ com suporte

O provedor LINQ incluído no SDK do .NET do SQL suporta os seguintes operadores:

- **Selecionar**: As projeções são convertidas para SQL SELECT, incluindo a construção do objeto.
- **Onde**: Filtros de traduzir para SQL WHERE e dão suporte à conversão entre `&&`, `||`, e `!` para os operadores SQL
- **SelectMany**: permite o desenrolamento de matrizes à cláusula SQL JOIN. Use para encadear ou aninhar expressões para filtrar elementos de matriz.
- **OrderBy** e **OrderByDescending**: Traduza para ORDER BY com ASC ou DESC.
- Operadores **Count**, **Sum**, **Min**, **Max** e **Average** para agregação e os seus equivalentes assíncronos, **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** e **AverageAsync**.
- **CompareTo**: é convertido em comparações de intervalo. Normalmente usado para cadeias de caracteres, desde que eles não são comparáveis no .NET.
- **Take**: Se traduz em SQL TOP para limitar os resultados de uma consulta.
- **Funções matemáticas**: Oferece suporte à conversão do .NET `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`, `Sign`, `Sin`, `Sqrt`, `Tan`, e `Truncate` para funções internas do SQL equivalentes.
- **Funções de cadeia de caracteres**: Oferece suporte à conversão do .NET `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd`, e `TrimStart` para funções internas do SQL equivalentes.
- **Funções de matriz**: Oferece suporte à conversão do .NET `Concat`, `Contains`, e `Count` para funções internas do SQL equivalentes.
- **Funções de extensão geoespacial**: Dá suporte à conversão dos métodos stub `Distance`, `IsValid`, `IsValidDetailed`, e `Within` para funções internas do SQL equivalentes.
- **Função de extensão de função definida pelo usuário**: Oferece suporte à conversão do método stub `UserDefinedFunctionProvider.Invoke` à função definida pelo usuário correspondente.
- **Diversos**: Oferece suporte à conversão de `Coalesce` e operadores condicionais. É possível converter a `Contains` para cadeia de caracteres CONTAINS, ARRAY_CONTAINS ou SQL IN, dependendo do contexto.

## <a name="examples"></a>Exemplos

Os exemplos a seguir ilustram como alguns dos operadores de consulta LINQ padrão são convertidas para consultas do Cosmos DB.

### <a name="select-operator"></a>Selecionar operador

A sintaxe é `input.Select(x => f(x))`, em que `f` é uma expressão escalar.

**Selecione o operador de exemplo 1:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Selecione o operador de exemplo 2:** 

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Selecione o operador de exemplo 3:**

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

**Em que operador, exemplo 1:**

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
  
**Em que operador, o exemplo 2:**

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

Você pode compor os operadores anteriores para formar consultas mais poderosas. Como o Cosmos DB oferece suporte a contêineres aninhados, você pode concatenar ou aninhar a composição.

### <a name="concatenation"></a>Concatenação

A sintaxe é `input(.|.SelectMany())(.Select()|.Where())*`. Uma consulta concatenada pode começar com um recurso opcional `SelectMany` consulta, seguida por múltiplos `Select` ou `Where` operadores.

**Concatenação, exemplo 1:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family=>family.parents[0])
          .Where(familyName == "Wakefield");
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

**Concatenação, o exemplo 4:**

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

A sintaxe é `input.SelectMany(x=>x.Q())` onde `Q` é um `Select`, `SelectMany`, ou `Where` operador.

Uma consulta aninhada aplica-se a consulta interna para cada elemento do contêiner externo. Um recurso importante é que a consulta interna pode se referir aos campos dos elementos no contêiner externo, como uma autojunção.

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

**Aninhamento, o exemplo 2:**

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

**Aninhamento, o exemplo 3:**

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

- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Dados de documento de modelo](modeling-data.md)
