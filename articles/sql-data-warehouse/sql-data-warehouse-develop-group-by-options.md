---
title: Usando o grupo por opções no SQL Data Warehouse do Azure | Microsoft Docs
description: Dicas para implementar o agrupamento por opções no SQL Data Warehouse do Azure para desenvolver soluções.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 2f6614f32c31338c9cf4f00307c475db4e02f553
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479649"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Agrupar por opções de SQL Data Warehouse
Dicas para implementar o agrupamento por opções no SQL Data Warehouse do Azure para desenvolver soluções.

## <a name="what-does-group-by-do"></a>O que GROUP BY faz?

A cláusula T-SQL [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql) agrega dados a um conjunto de linhas de resumo. GROUP BY tem algumas opções que a SQL Data Warehouse não oferece suporte. Essas opções têm soluções alternativas.

Essas opções são

* GROUP BY com ROLLUP
* GROUPING SETS
* GROUP BY com CUBE

## <a name="rollup-and-grouping-sets-options"></a>O rollup e o agrupamento definem opções
A opção mais simples é usar UNION ALL ao invés de executar o rollup do que contar com a sintaxe explícita. O resultado é exatamente o mesmo

O exemplo a seguir usa a instrução GROUP BY com a opção ROLLUP:
```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Usando ROLLUP, o exemplo anterior solicita as agregações a seguir:

* país e região
* País
* Grande Total

Para substituir o ROLLUP e retornar os mesmos resultados, você pode usar UNION ALL e especificar explicitamente as agregações necessárias:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Para substituir GROUPING SETS, o princípio de exemplo se aplica. Você só precisa criar seções UNION ALL para os níveis de agregação que você deseja ver.

## <a name="cube-options"></a>Opções Cube
É possível criar uma instrução GROUP BY WITH CUBE usando a abordagem UNION ALL. O problema é que o código pode rapidamente se tornar complicado e difícil. Para atenuar isso, você pode usar essa abordagem mais avançada.

Vamos usar o exemplo anterior.

A primeira etapa é definir o ‘cube’ que define todos os níveis de agregação que desejamos criar. É importante que você observe o CROSS JOIN das duas tabelas derivadas. Isso gera todos os níveis. O restante do código está lá para formatação.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

O exemplo a seguir mostra os resultados do CTAS:

![Agrupar por cubo](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

A segunda etapa é especificar uma tabela de destino para armazenar os resultados intermediários:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

A terceira etapa é executar um loop sobre o cubo de colunas realizando a agregação. A consulta será executada uma vez para cada linha na tabela temporária #Cube e armazenará os resultados na tabela temporária #Results

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Por fim, você pode retornasr os resultados apenas lendo da tabela temporária #Results

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Dividir o código em seções e gerar uma construção de loop, torna o código mais gerenciável e sustentável.

## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).

