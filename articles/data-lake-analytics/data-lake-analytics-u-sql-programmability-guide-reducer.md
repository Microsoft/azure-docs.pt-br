---
title: Guia de programação de redutor definido pelo usuário do U-SQL para Azure Data Lake
description: Saiba mais sobre o guia de programação do UDO do U-SQL-redutor definido pelo usuário.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 52d44685678c3e89dc820042a7925d284500cef8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512502"
---
# <a name="use-user-defined-reducer"></a>Usar redutor definido pelo usuário

## <a name="u-sql-udo-user-defined-reducer"></a>UDO do U-SQL: redutor definido pelo usuário

O U-SQL permite escrever redutores de conjunto de linhas personalizados em C# usando a estrutura de extensibilidade do operador definido pelo usuário e implementando uma interface IReducer.

O redutor definido pelo usuário, ou UDR, pode ser usado para eliminar linhas desnecessárias durante a extração (importação) de dados. Ele também pode ser usado para manipular e avaliar as linhas e colunas. Com base na lógica de programação, ele também pode definir quais linhas precisam ser extraídas.

## <a name="how-to-define-and-use-user-defined-reducer"></a>Como definir e usar redutor definidas pelo usuário
Para definir uma classe UDR, precisamos criar uma interface `IReducer` com um atributo `SqlUserDefinedReducer` opcional.

Essa interface de classe deve conter uma definição para substituição do conjunto de linhas da interface `IEnumerable`.

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

O atributo **SqlUserDefinedReducer** indica que o tipo deve ser registrado como um redutor definido pelo usuário. Essa classe não pode ser herdada.
**SqlUserDefinedReducer** é um atributo opcional para a definição do redutor definido pelo usuário. Ele é usado para definir a propriedade IsRecursive.

* bool     IsRecursive    
* **true** = indica se este Redutor é associativo e comutativo

Os principais objetos de programação são **input** e **output**. O objeto de entrada é usado para enumerar linhas de entrada. Output é usado para definir as linhas de saída como resultado da redução da atividade.

Para enumeração de linhas de entrada, usamos o método `Row.Get`.

```csharp
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

O parâmetro para o método `Row.Get` é uma coluna passada como parte da classe `PRODUCE` da instrução `REDUCE` do script base U-SQL. Precisamos usar o tipo de dados correto aqui também.

Para saída, use o método `output.Set`.

É importante entender que redutor personalizado apenas produz valores que são definidos com a chamada de método `output.Set`.

```csharp
output.Set<string>("mycolumn", guid);
```

A saída real do redutor é disparada chamando `yield return output.AsReadOnly();`.

Abaixo temos um exemplo de redutor:

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

Nesse cenário de caso de uso, o redutor está ignorando linhas com um nome de usuário vazio. Para cada linha no conjunto de linhas, ele lê cada coluna necessária e avalia o comprimento do nome do usuário. Ele só produz a linha real se o comprimento do valor de nome de usuário for maior que 0.

Abaixo temos um script base U-SQL que usa um redutor personalizado:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

## <a name="next-steps"></a>Próximas etapas
* [Guia de programação do U-SQL-visão geral](data-lake-analytics-u-sql-programmability-guide.md)
* [Guia de programação do U-SQL-UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)