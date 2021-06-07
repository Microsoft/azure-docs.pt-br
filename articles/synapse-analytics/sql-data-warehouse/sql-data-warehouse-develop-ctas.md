---
title: CREATE TABLE AS SELECT (CTAS)
description: Explicação e exemplos da instrução CREATE TABLE como SELECT (CTAS) no SQL Synapse para desenvolver soluções.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019, azure-synapse
ms.openlocfilehash: 68bab754142538fc6067cf2593ae6244a03a48d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98734807"
---
# <a name="create-table-as-select-ctas"></a>CREATE TABLE AS SELECT (CTAS)

Este artigo explica a instrução T-SQL CREATE TABLE como SELECT (CTAS) no SQL Synapse para desenvolver soluções. O artigo também fornece exemplos de códigos.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

A instrução [CREATE TABLE as Select](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (CTAS) é um dos recursos mais importantes do T-SQL disponíveis. CTAS é uma operação paralela que cria uma nova tabela com base na saída de uma instrução SELECT. O CTAS é a maneira mais simples e rápida de criar e inserir dados em uma tabela com um único comando.

## <a name="selectinto-vs-ctas"></a>SELECIONAR... NO vs. CTAS

CTAS é uma versão mais personalizável do [Select... Instrução INTO](/sql/t-sql/queries/select-into-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .

Veja a seguir um exemplo de uma simples seleção... PORTA

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

SELECIONAR... INTO não permite que você altere o método de distribuição ou o tipo de índice como parte da operação. Você cria `[dbo].[FactInternetSales_new]` usando o tipo de distribuição padrão de ROUND_ROBIN e a estrutura de tabela padrão do índice COLUMNSTORE clusterizado.

Com o CTAS, por outro lado, você pode especificar a distribuição dos dados da tabela, bem como o tipo de estrutura da tabela. Para converter o exemplo anterior em CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
 DISTRIBUTION = ROUND_ROBIN
 ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales];
```

> [!NOTE]
> Se você estiver apenas tentando alterar o índice em sua operação CTAS e a tabela de origem for de hash distribuído, mantenha a mesma coluna de distribuição e tipo de dados. Isso evita a movimentação de dados de distribuição cruzada durante a operação, o que é mais eficiente.

## <a name="use-ctas-to-copy-a-table"></a>Usar CTAS para copiar uma tabela

Talvez um dos usos mais comuns de CTAS seja a criação de uma cópia de uma tabela para alterar o DDL. Digamos que você criou sua tabela originalmente como `ROUND_ROBIN` e agora deseja alterá-la para uma tabela distribuída em uma coluna. CTAS é como você alteraria a coluna de distribuição. Você também pode usar CTAS para alterar o particionamento, a indexação ou os tipos de coluna.

Digamos que você criou essa tabela usando o tipo de distribuição padrão de `ROUND_ROBIN` , não especificando uma coluna de distribuição no `CREATE TABLE` .

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25));
```

Agora você deseja criar uma nova cópia desta tabela, com uma `Clustered Columnstore Index` , para poder aproveitar o desempenho das tabelas Columnstore clusterizadas. Você também deseja distribuir essa tabela no `ProductKey` , pois está prevendo junções nessa coluna e deseja evitar a movimentação de dados durante as junções `ProductKey` . Por fim, você também deseja adicionar o particionamento `OrderDateKey` , para que possa excluir rapidamente dados antigos descartando partições antigas. Aqui está a instrução CTAS, que copia sua tabela antiga em uma nova tabela.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Por fim, você pode renomear suas tabelas, para alternar em sua nova tabela e, em seguida, descartar a tabela antiga.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Use CTAS para contornar recursos sem suporte

Você também pode usar o CTAS para contornar uma série de recursos sem suporte listados abaixo. Esse método pode, muitas vezes, provar útil, pois não apenas o seu código será compatível, mas será executado com frequência no SQL Synapse. Esse desempenho é resultado de seu design totalmente paralelizado. Os cenários incluem:

* ANSI JOINS em instruções UPDATE
* ANSI JOINs em instruções DELETE
* Instrução MERGE

> [!TIP]
> Tente imaginar "CTAS primeiro". Resolver um problema usando CTAS geralmente é uma boa abordagem, mesmo que você esteja escrevendo mais dados como resultado.

## <a name="ansi-join-replacement-for-update-statements"></a>Substituição de junção ANSI para instruções de atualização

Você pode achar que tem uma atualização complexa. A atualização une mais de duas tabelas usando a sintaxe de junção ANSI para executar a atualização ou exclusão.

Imagine que você precisasse atualizar esta tabela:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
( [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
, [CalendarYear]                    SMALLINT        NOT NULL
, [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
);
```

A consulta original pode ter se visto como neste exemplo:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT [EnglishProductCategoryName]
        , [CalendarYear]
        , SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear];
```

O SQL Synapse não dá suporte a junções ANSI na `FROM` cláusula de uma `UPDATE` instrução, portanto, você não pode usar o exemplo anterior sem modificá-lo.

Você pode usar uma combinação de um CTAS e uma junção implícita para substituir o exemplo anterior:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0) AS [EnglishProductCategoryName]
, ISNULL(CAST([CalendarYear] AS SMALLINT),0)  AS [CalendarYear]
, ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)  AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY [EnglishProductCategoryName]
, [CalendarYear];

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]  = AnnualCategorySales.[CalendarYear] ;

--Drop the interim table
DROP TABLE CTAS_acs;
```

## <a name="ansi-join-replacement-for-merge"></a>Substituição de junção ANSI para MESCLAgem 

No Azure Synapse Analytics, a [mesclagem](/sql/t-sql/statements/merge-transact-sql?view=azure-sqldw-latest&preserve-view=true) (visualização) com não correspondente pelo destino requer que o destino seja uma tabela distribuída por hash.  Os usuários podem usar a junção ANSI com [Update](/sql/t-sql/queries/update-transact-sql?view=azure-sqldw-latest&preserve-view=true) ou [delete](/sql/t-sql/statements/delete-transact-sql?view=azure-sqldw-latest&preserve-view=true) como uma solução alternativa para modificar os dados da tabela de destino com base no resultado da junção com outra tabela.  Veja um exemplo.

```sql
CREATE TABLE dbo.Table1   
    (ColA INT NOT NULL, ColB DECIMAL(10,3) NOT NULL);  
GO  
CREATE TABLE dbo.Table2   
    (ColA INT NOT NULL, ColB DECIMAL(10,3) NOT NULL);  
GO  
INSERT INTO dbo.Table1 VALUES(1, 10.0);  
INSERT INTO dbo.Table2 VALUES(1, 0.0);  
GO  
UPDATE dbo.Table2   
SET dbo.Table2.ColB = dbo.Table2.ColB + dbo.Table1.ColB  
FROM dbo.Table2   
    INNER JOIN dbo.Table1   
    ON (dbo.Table2.ColA = dbo.Table1.ColA);  
GO  
SELECT ColA, ColB   
FROM dbo.Table2;

```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>Declarar explicitamente o tipo de dados e a nulidade da saída

Ao migrar o código, você pode achar que é executado por esse tipo de padrão de codificação:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f;
```

Você pode pensar que deve migrar esse código para CTAS e estaria correto. No entanto, há um problema oculto aqui.

O código a seguir não produz o mesmo resultado:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

Observe que a coluna "resultado" transfere os valores de tipo de dados e de nulidade da expressão. A realização do tipo de dados Forward pode levar a variações sutis em valores se você não tiver cuidado.

Experimente este exemplo:

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

O valor armazenado para o resultado é diferente. Como o valor persistente na coluna de resultado é usado em outras expressões, o erro se torna ainda mais significativo.

![Captura de tela dos resultados do CTAS](./media/sql-data-warehouse-develop-ctas/ctas-results.png)

Isso é importante para migrações de dados. Embora a segunda consulta seja, de forma alguma, mais precisa, há um problema. Os dados seriam diferentes em comparação com o sistema de origem e isso leva a perguntas de integridade na migração. Este é um dos raros casos em que a resposta "errada" é, na verdade, a melhor!

O motivo pelo qual vemos uma diparidade entre os dois resultados é devido à conversão de tipo implícito. No primeiro exemplo, a tabela define a definição de coluna. Quando a linha é inserida, ocorre uma conversão implícita de tipo. No segundo exemplo, não há nenhuma conversão implícita de tipo, pois a expressão define o tipo de dados da coluna.

Observe também que a coluna no segundo exemplo foi definida como uma coluna anulável, enquanto que no primeiro exemplo não tem. Quando a tabela foi criada no primeiro exemplo, a nulidade da coluna foi explicitamente definida. No segundo exemplo, ele foi deixado para a expressão e, por padrão, resultaria em uma definição nula.

Para resolver esses problemas, você deve definir explicitamente a conversão de tipo e a nulidade na parte SELECT da instrução CTAS. Você não pode definir essas propriedades em ' CREATE TABLE '.
O exemplo a seguir demonstra como corrigir o código:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Observe o seguinte:

* Você pode usar CAST ou CONVERT.
* Use ISNULL, not enunidas, para forçar a nulidade. Consulte a observação a seguir.
* ISNULL é a função mais externa.
* A segunda parte de ISNULL é uma constante, 0.

> [!NOTE]
> Para que a nulidade seja definida corretamente, é vital usar ISNULL e não adesão. A adesão não é uma função determinística e, portanto, o resultado da expressão sempre será anulável. Com ISNULL, é diferente. É determinístico. Portanto, quando a segunda parte da função ISNULL for uma constante ou um literal, o valor resultante não será nulo.

Garantir a integridade dos cálculos também é importante para a alternância de partição de tabela. Imagine que você tenha essa tabela definida como uma tabela de fatos:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
, [product]   INT     NOT NULL
, [store]     INT     NOT NULL
, [quantity]  INT     NOT NULL
, [price]     MONEY   NOT NULL
, [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
);
```

No entanto, o campo Amount é uma expressão calculada. Ele não faz parte dos dados de origem.

Para criar seu conjunto de espaço de os, você pode desejar usar o seguinte código:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

A consulta seria executada perfeitamente bem. O problema surge quando você tenta fazer a alternância de partição. As definições de tabela não correspondem. Para que as definições de tabela coincidam, modifique o CTAS para adicionar uma `ISNULL` função para preservar o atributo de nulidade da coluna.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
  [date]
, [product]
, [store]
, [quantity]
, [price]
, ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Você pode ver que a consistência de tipo e a manutenção de propriedades de nulidade em um CTAS é uma prática recomendada de engenharia. Ele ajuda a manter a integridade em seus cálculos e também garante que a alternância de partição seja possível.

CTAS é uma das instruções mais importantes no SQL Synapse. Certifique-se compreendê-la totalmente. Consulte a [documentação do CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, confira a [visão geral sobre desenvolvimento](sql-data-warehouse-overview-develop.md).