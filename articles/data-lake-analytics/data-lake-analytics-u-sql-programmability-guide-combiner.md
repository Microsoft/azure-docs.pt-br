---
title: Guia de programação do combinador do U-SQL definido pelo usuário para Azure Data Lake
description: Saiba mais sobre o guia de programação do UDO do U-SQL – combinador definido pelo usuário.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: a6c560cf4ec11197183711656d69024591e7008c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512513"
---
# <a name="use-user-defined-combiner"></a>Usar combinador definido pelo usuário

## <a name="u-sql-udo-user-defined-combiner"></a>UDO U-SQL: combinador definido pelo usuário
O combinador definido pelo usuário, ou UDC, permite combinar linhas dos conjuntos de linhas esquerdo e direito, com base em lógica personalizada. O Combinador Definido pelo Usuário é usado com a expressão COMBINE.

## <a name="how-to-define-and-use-user-defined-combiner"></a>Como definir e usar o combinador definido pelo usuário

Um combinador está sendo invocado com a expressão COMBINE, que fornece as informações necessárias sobre os conjuntos de linhas de entrada, as colunas de agrupamento, o esquema de resultado esperado e as informações adicionais.

Para chamar um combinador em um script base U-SQL, usamos a seguinte sintaxe:

```usql
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Para saber mais, veja [Expressão COMBINE (U-SQL)](/u-sql/statements-and-expressions/combine-expression).

Para definir um combinador definido pelo usuário, precisamos criar a interface `ICombiner` com o atributo [`SqlUserDefinedCombiner`], que é opcional para a definição do combinador definido pelo usuário.

Definição da classe `ICombiner` base:

```csharp
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

A implementação personalizada de uma interface `ICombiner` deve conter a definição para uma substituição Combine `IEnumerable<IRow>`.

```csharp
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

O atributo **SqlUserDefinedCombiner** indica que o tipo deve ser registrado como um combinador definido pelo usuário. Essa classe não pode ser herdada.

**SqlUserDefinedCombiner** é usado para definir a propriedade do modo Combinador. É um atributo opcional para a definição do combinador definido pelo usuário.

Modo CombinerMode

A enumeração CombinerMode pode usar os seguintes valores:

* Completo (0) Cada linha de saída depende potencialmente de todas as linhas de entrada da esquerda e direita com o mesmo valor de chave.

* Esquerda (1) Cada linha de saída depende de uma única linha de entrada da esquerda (e potencialmente de todas as linhas da direita com o mesmo valor de chave).

* Direita (2) Cada linha de saída depende de uma única linha de entrada da direita (e potencialmente de todas as linhas da esquerda com o mesmo valor de chave).

* Interno (3) Cada linha de saída depende de uma única linha de entrada da esquerda e direita com o mesmo valor.

Exemplo:    [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


Os principais objetos de programação são:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Os conjuntos de linhas de entrada são passados como o tipo **esquerdo** e **direito** `IRowset` da interface. Ambos os conjuntos de linhas precisam ser enumerados para processamento. Você só pode enumerar cada interface uma vez e, portanto, temos que enumerá-las e armazená-las se necessário.

Para fins de cache, podemos criar um \<T\> tipo de lista de estrutura de memória como resultado de uma execução de consulta LINQ, especificamente listar<`IRow`>. O tipo de dados anônimo também pode ser usado durante a enumeração.

Consulte [introdução às consultas LINQ (C#)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) para obter mais informações sobre consultas LINQ e [a \<T\> interface IEnumerable](/dotnet/api/system.collections.generic.ienumerable-1) para obter mais informações sobre a \<T\> interface IEnumerable.

Para obter os valores de dados reais do `IRowset` de entrada, usamos o método Get() da interface `IRow`.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Os nomes de coluna individuais podem ser determinados pela chamada do método de esquema `IRow`.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Ou usando o nome de coluna de esquema:

```csharp
c# row.Get<int>(row.Schema[0].Name)
```

A enumeração geral com o LINQ se parece com este exemplo:

```csharp
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Depois de enumerar os dois conjuntos de linhas, vamos executar um loop por todas as linhas. Para cada linha no conjunto de linhas à esquerda, vamos localizar todas as linhas que satisfazem a condição de nosso combinador.

Os valores de saída devem ser definidos com a saída `IUpdatableRow`.

```csharp
output.Set<int>("mycolumn", mycolumn)
```

A saída real é disparada chamando `yield return output.AsReadOnly();`.

Veja abaixo um exemplo do combinador:

```csharp
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

Nesse cenário de caso de uso, estamos criando um relatório de análise para o varejista. O objetivo é encontrar todos os produtos que custam mais de US$ 20,000 e são vendidos por meio de sites mais rapidamente do que pelo varejista comum durante determinado período.

Veja abaixo o script base U-SQL. Você pode comparar a lógica entre JOIN regular e um combinador:

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

O combinador definido pelo usuário pode ser chamado como uma nova instância do objeto do aplicador:

```csharp
USING new MyNameSpace.MyCombiner();
```


Ou com a invocação de um método de fábrica wrapper:

```csharp
USING MyNameSpace.MyCombiner();
```

## <a name="next-steps"></a>Próximas etapas
* [Guia de programação do U-SQL-visão geral](data-lake-analytics-u-sql-programmability-guide.md)
* [Guia de programação do U-SQL-UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)