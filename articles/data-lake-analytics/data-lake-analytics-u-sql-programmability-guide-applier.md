---
title: Guia de programação do aplicador definido pelo usuário do U-SQL para Azure Data Lake
description: Saiba mais sobre o guia de programação do UDO do U-SQL – aplicador definido pelo usuário.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 0842a2cfa021ef8ea45c19ec885c7dec371730de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512504"
---
# <a name="use-user-defined-applier"></a>Usar aplicador definido pelo usuário 

## <a name="u-sql-udo-user-defined-applier"></a>UDO U-SQL: aplicador definido pelo usuário
O aplicador definido pelo usuário do U-SQL permite invocar uma função C# personalizada para cada linha retornada pela expressão de tabela externa de uma consulta. A entrada à direita é avaliada para cada linha a começar da entrada à esquerda e as linhas que são produzidas são combinadas para a saída final. A lista de colunas produzida pelo operador APPLY é a combinação do conjunto de colunas na entrada à esquerda e à direita.


## <a name="how-to-define-and-use-user-defined-applier"></a>Como definir e usar o aplicador definido pelo usuário
O aplicador definido pelo usuário está sendo invocado como parte da expressão SELECT do U-SQL.

A chamada característica ao aplicador definidor pelo usuário se parece com esta:

```usql
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Para saber mais sobre como usar aplicadores na expressão SELECT., veja [U-SQL SELECT selecionando de CROSS APPLY e OUTER APPLY](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply).

A definição de classe base do aplicador definido pelo usuário é a seguinte:

```csharp
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Para definir um aplicador definido pelo usuário, precisamos criar a interface `IApplier` com o atributo [`SqlUserDefinedApplier`], que é opcional para a definição de aplicador definido pelo usuário.

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* Apply é chamado para cada linha da tabela outer. Ele retorna o conjunto de linhas de saída `IUpdatableRow`.
* A classe Constructor é usada para passar parâmetros ao aplicador definido pelo usuário.

**SqlUserDefinedApplier** indica que o tipo deve ser registrado como um aplicador definido pelo usuário. Essa classe não pode ser herdada.

O atributo **SqlUserDefinedApplier** é **opcional** para a definição do aplicador definido pelo usuário.


Os principais objetos de programação são os seguintes:

```csharp
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Os conjuntos de linhas de entrada são passados como entrada `IRow`. As linhas de saída são geradas como a interface de saída `IUpdatableRow`.

Os nomes de coluna individuais podem ser determinados pela chamada do método de esquema `IRow`.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Para obter os valores de dados reais do `IRow` de entrada, usamos o método Get() da interface `IRow`.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Ou, então, usamos o nome de coluna de esquema:

```csharp
row.Get<int>(row.Schema[0].Name)
```

Os valores de saída devem ser definidos com a saída `IUpdatableRow`:

```csharp
output.Set<int>("mycolumn", mycolumn)
```

É importante entender que o aplicador personalizado só produz colunas e valores definidos com a chamada ao método `output.Set`.

A saída real é disparada chamando `yield return output.AsReadOnly();`.

Os parâmetros de aplicador definido pelo usuário podem ser passados para o construtor. O aplicador pode retornar um número variável de colunas que precisa ser definido durante a chamada ao aplicador no script base U-SQL.

```csharp
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Veja abaixo o exemplo do aplicador definido pelo usuário:

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

Abaixo está o script base U-SQL para esse aplicador definido pelo usuário:

```usql
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Nesse cenário de caso de uso, o aplicador definido pelo usuário atua como um analisador de valor delimitado por vírgula para as propriedades de frota de carros. As linhas do arquivo de entrada têm a seguinte aparência:

```text
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Trata-se de um típico arquivo TSV delimitado por tab com a coluna de propriedades contendo propriedades do carro, como fabricante e modelo. Essas propriedades precisam ser analisadas pelas colunas da tabela. O aplicador fornecido também permite gerar um número dinâmico de propriedades no conjunto de linhas de resultados, com base no parâmetro que é passado. Você pode gerar todas as propriedades ou somente um conjunto específico de propriedades.

```text
...USQL_Programmability.ParserApplier ("all")
...USQL_Programmability.ParserApplier ("make")
...USQL_Programmability.ParserApplier ("make&model")
```

O aplicador definido pelo usuário pode ser chamado como uma nova instância do objeto do aplicador:

```usql
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

Ou com a invocação de um método de fábrica wrapper:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```


## <a name="next-steps"></a>Próximas etapas
* [Guia de programação do U-SQL-visão geral](data-lake-analytics-u-sql-programmability-guide.md)
* [Guia de programação do U-SQL-UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)