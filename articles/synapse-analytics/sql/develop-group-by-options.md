---
title: Usar opções de agrupar por no Synapse SQL
description: O Synapse SQL permite desenvolver soluções implementando diferentes opções agrupar por.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: c2e1ddbfb87df40a0e3683e7bca7539c26191a7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671307"
---
# <a name="group-by-options-in-synapse-sql"></a>Opções de agrupar por no Synapse SQL

O Synapse SQL permite desenvolver soluções implementando diferentes opções agrupar por. 

## <a name="what-group-by-does"></a>O que o GROUP BY faz

A cláusula T-SQL [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql?view=azure-sqldw-latest&preserve-view=true) agrega dados a um conjunto de linhas de resumo.

O pool SQL sem servidor dá suporte a todo o intervalo de opções agrupar por. O pool SQL dedicado dá suporte a um número limitado de opções agrupar por.

## <a name="group-by-options-supported-in-dedicated-sql-pool"></a>Opções de agrupar por com suporte no pool SQL dedicado

GROUP BY tem algumas opções que o pool SQL dedicado não dá suporte. Essas opções têm soluções alternativas, que são as seguintes:

* GROUP BY com ROLLUP
* GROUPING SETS
* GROUP BY com CUBE

### <a name="rollup-and-grouping-sets-options"></a>O rollup e o agrupamento definem opções

A opção mais simples aqui é usar UNION ALL para executar o rollup em vez de depender da sintaxe explícita. O resultado é exatamente o mesmo

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

### <a name="cube-options"></a>Opções Cube

É possível criar um GROUP BY com o cubo usando a abordagem UNION ALL. O problema é que o código pode rapidamente se tornar complicado e difícil. Para atenuar esse problema, você pode usar essa abordagem mais avançada.

A primeira etapa é definir o ‘cube’ que define todos os níveis de agregação que desejamos criar. Anote a junção CRUZada das duas tabelas derivadas à medida que ela gera todos os níveis. O restante do código está lá para formatação.

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

A imagem a seguir mostra os resultados de [CREATE TABLE como SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true):

![Agrupar por cubo](./media/develop-group-by-options/develop-group-by-cube.png)

A segunda etapa é especificar uma tabela de destino para armazenar resultados intermediários:

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

A terceira etapa é executar um loop sobre o cubo de colunas que executam a agregação. A consulta será executada uma vez para cada linha na #Cube tabela temporária. Os resultados são armazenados na tabela #Results Temp:

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

Por fim, você pode retornar os resultados lendo na tabela temporária #Results:

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Ao dividir o código em seções e gerar uma construção de loop, o código torna-se mais gerenciável e passível de manutenção.

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](develop-overview.md).
