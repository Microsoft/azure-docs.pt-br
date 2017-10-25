---
title: "Otimização de transações para o SQL Data Warehouse | Microsoft Docs"
description: "Diretrizes de práticas recomendadas sobre como gravar atualizações de transação eficientes no SQL Data Warehouse do Azure"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 6f326f26-8a54-49df-a482-9c96a58db371
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: f9f19d75a37351b3562ce8c2f3629df14c5437c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="optimizing-transactions-for-sql-data-warehouse"></a>Otimização de transações para o SQL Data Warehouse
Este artigo explica como otimizar o desempenho do seu código transacional ao minimizar o risco para reversões longas.

## <a name="transactions-and-logging"></a>Transações e registro em log
As transações são um componente importante de um mecanismo de banco de dados relacional. O SQL Data Warehouse usa transações durante a modificação de dados. Essas transações podem ser implícitas ou explícitas. As instruções individuais `INSERT`, `UPDATE` e `DELETE` são exemplos de transações implícitas. As transações explícitas são escritas explicitamente por um desenvolvedor usando `BEGIN TRAN`, `COMMIT TRAN` ou `ROLLBACK TRAN` e costumam ser usadas quando é preciso agrupar várias instruções de modificação em uma única unidade atômica. 

O SQL Data Warehouse do Azure confirma as alterações no banco de dados usando os logs de transação. Cada distribuição tem seu próprio log de transações. As gravações de log de transações são automáticas. Não é necessária nenhuma configuração. No entanto, apesar desse processo garantir a gravação, ele introduz uma sobrecarga no sistema. Você pode minimizar esse impacto ao escrever um código transacionalmente eficiente. De modo geral, um código transacionalmente eficiente se enquadra em duas categorias.

* Aproveitar constructos mínimos de registro em log quando possível
* Processar dados usando lotes com escopo para evitar transações de longa execução singulares
* Adotar um padrão de alternância de partições para grandes modificações de uma determinada partição

## <a name="minimal-vs-full-logging"></a>Registro mínimo em log vs. registro total em log
Ao contrário de operações totalmente registradas em log, que usam o log de transações para acompanhar cada linha alterada, as operações minimamente registradas em log controlam apenas as alocações de extensão e as alterações de metadados. Portanto, o registro mínimo em log só envolve o registro das informações necessárias para reverter a transação no caso de uma falha ou de uma solicitação explícita (`ROLLBACK TRAN`). Como muito menos informações são rastreadas no log de transações, uma operação minimamente registrada em log tem um desempenho melhor do que uma operação totalmente registrada em log de tamanho similar. Além disso, como ocorrem menos gravações no log de transações, uma quantidade menor de dados de log será gerada e, portanto, é mais eficiente em relação à E/S.

Os limites de segurança de transação só se aplicam às operações totalmente registradas em log.

> [!NOTE]
> As operações minimamente registradas em log podem participar de transações explícitas. Como todas as alterações nas estruturas de alocação são rastreadas, é possível reverter operações minimamente registradas em log. É importante entender que a alteração é registrada "minimamente" em log, ou seja, não tem o log cancelado.
> 
> 

## <a name="minimally-logged-operations"></a>Operações minimamente registradas em log
As seguintes operações podem ser minimamente registradas em log:

* CREATE TABLE AS SELECT ([CTAS][CTAS])
* INSERT..SELECT
* CREATE INDEX
* ALTER INDEX REBUILD
* DROP INDEX
* TRUNCATE TABLE
* DROP TABLE
* ALTER TABLE SWITCH PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> As operações de movimentação de dados internos (como `BROADCAST` e `SHUFFLE`) não são afetadas pelo limite de segurança de transação.
> 
> 

## <a name="minimal-logging-with-bulk-load"></a>Registro mínimo em log com carregamento em massa
`CTAS` e `INSERT...SELECT` são ambos operações de carregamento em massa. No entanto, ambas são influenciadas pela definição da tabela de destino e dependem do cenário de carga. A seguir, uma tabela que explica se a operação em massa será total ou minimamente registrada em log:  

| Índice principal | Cenário de carga | Modo de registro em log |
| --- | --- | --- |
| Heap |Qualquer |**Mínimo** |
| Índice clusterizado |Tabela de destino vazia |**Mínimo** |
| Índice clusterizado |As linhas carregadas não se sobrepõem às páginas existentes no destino |**Mínimo** |
| Índice clusterizado |Linhas carregadas se sobrepõem com páginas existentes no destino |Completo |
| Índice columnstore clusterizado |Tamanho do lote >= 102.400 por distribuição alinhada por partição |**Mínimo** |
| Índice columnstore clusterizado |Tamanho do lote < 102.400 por distribuição alinhada por partição |Completo |

Vale a pena observar que todas as gravações para atualizar índices secundários ou não clusterizados sempre serão operações com log completo.

> [!IMPORTANT]
> O SQL Data Warehouse possui 60 distribuições. Portanto, supondo que todas as linhas são distribuídas uniformemente e em uma única partição, o seu lote deverá conter 6.144.000 linhas ou mais para ser minimamente registrado ao gravar em um Índice Columnstore Clusterizado. Se a tabela estiver particionada e as linhas que estiverem sendo inseridas se estenderem pelos limites de partição, você precisará de 6.144.000 linhas por limite de partição, considerando uma distribuição uniforme de dados. Cada partição em cada distribuição deve exceder independentemente o limite de 102.400 linhas para a inserção ser minimamente registrada em log para a distribuição.
> 
> 

Carregar dados em uma tabela não vazia com um índice clusterizado pode, muitas vezes, conter uma combinação de linhas total e minimamente registradas em log. Um índice clusterizado é uma árvore balanceada (árvore b) das páginas. Se a página que estiver sendo gravada já contiver linhas de outra transação, então essas gravações serão totalmente registradas em log. No entanto, se a página estiver vazia, a gravação para essa página será minimamente registrada em log.

## <a name="optimizing-deletes"></a>Otimizando exclusões
`DELETE` é uma operação totalmente registrada em log.  Se você precisar excluir uma grande quantidade de dados de uma tabela ou uma partição, geralmente fará mais sentido `SELECT` (selecionar) os dados que deseja manter, que podem ser executados como uma operação minimamente registrada em log.  Para isso, crie uma nova tabela com [CTAS][CTAS].  Depois de criada, use [RENAME][RENAME] para alternar sua tabela antiga com a recentemente criada.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT     *
FROM     [dbo].[FactInternetSales]
WHERE    [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Otimizando atualizações
`UPDATE` é uma operação totalmente registrada em log.  Se você precisar atualizar um grande número de linhas em uma tabela ou em uma partição, no geral, poderá ser muito mais proveitoso usar uma operação minimamente registrada em log, tal como [CTAS][CTAS].

No exemplo abaixo, uma atualização completa de tabela foi convertida em um `CTAS` para permitir o registro mínimo em log.

Nesse caso, adiciona-se retrospectivamente um valor de desconto às vendas na tabela:

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(    CLUSTERED INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [!NOTE]
> A recriação de tabelas grandes pode se beneficiar do uso de recursos de gerenciamento de carga de trabalho do SQL Data Warehouse. Para obter mais detalhes, confira a seção de gerenciamento de carga de trabalho no artigo sobre [simultaneidade][concurrency].
> 
> 

## <a name="optimizing-with-partition-switching"></a>Otimizando com alternância de partição
Quando houver modificações em larga escala dentro de uma [partição da tabela][table partition], fará mais sentido considerar um padrão de troca de partições. Se a modificação de dados for significativa e se estender por várias partições, a simples iteração nas partições terá o mesmo resultado.

As etapas para executar uma alternância de partições são as seguintes:

1. Criar uma partição de saída vazia
2. Executar a “atualização” como um CTAS
3. Alternar os dados existentes na tabela de saída
4. Inserir os novos dados
5. Limpar os dados

No entanto, para ajudar a identificar as partições a serem alternadas, primeiro precisamos criar um procedimento auxiliar, como mostrado abaixo. 

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,    @table_name               NVARCHAR(128)
,    @boundary_value           INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
WITH CTE
AS
(
SELECT     s.name                            AS [schema_name]
,        t.name                            AS [table_name]
,         p.partition_number                AS [ptn_nmbr]
,        p.[rows]                        AS [ptn_rows]
,        CAST(r.[value] AS INT)            AS [boundary_value]
FROM        sys.schemas                    AS s
JOIN        sys.tables                    AS t    ON  s.[schema_id]        = t.[schema_id]
JOIN        sys.indexes                    AS i    ON     t.[object_id]        = i.[object_id]
JOIN        sys.partitions                AS p    ON     i.[object_id]        = p.[object_id] 
                                                AND i.[index_id]        = p.[index_id] 
JOIN        sys.partition_schemes        AS h    ON     i.[data_space_id]    = h.[data_space_id]
JOIN        sys.partition_functions        AS f    ON     h.[function_id]        = f.[function_id]
LEFT JOIN    sys.partition_range_values    AS r     ON     f.[function_id]        = r.[function_id] 
                                                AND r.[boundary_id]        = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT    *
FROM    CTE
WHERE    [schema_name]        = @schema_name
AND        [table_name]        = @table_name
AND        [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Esse procedimento maximiza a reutilização de código e deixa o exemplo de alternância de partições mais compacto.

O código a seguir demonstra as cinco etapas mencionadas acima para obter uma rotina de alternância de partições completa.

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE    OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]    SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))    +' TO [dbo].[FactInternetSales_out] PARTITION '    +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))    +' TO [dbo].[FactInternetSales] PARTITION '        +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Minimizar o registro em log com pequenos lotes
Para grandes operações de modificação de dados, talvez faça sentido dividir a operação em partes ou em lotes para abranger a unidade de trabalho.

Um exemplo funcional é fornecido abaixo. O tamanho do lote foi definido como um número trivial para realçar a técnica. Na realidade, o tamanho do lote seria significativamente maior. 

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
SELECT    ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,        SalesOrderNumber
,        SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE    [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE    @seq_start        INT = 1
,        @batch_iterator    INT = 1
,        @batch_size        INT = 50
,        @max_seq_nmbr    INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE    @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,        @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE    @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT    1
            FROM    #t t
            WHERE    seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND        FactInternetSales.SalesOrderNumber        = t.SalesOrderNumber
            AND        FactInternetSales.SalesOrderLineNumber    = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Diretrizes de pausa e dimensionamento
O SQL Data Warehouse do Azure permite pausar, retomar e dimensionar seu data warehouse sob demanda. Quando você pausa ou dimensiona o SQL Data Warehouse, deve entender que todas as transações em trânsito serão encerradas imediatamente, fazendo com que qualquer transação aberta seja revertida. Se sua carga de trabalho tiver emitido uma modificação de dados de longa duração e incompleta antes de a operação de dimensionamento ou pausa, o trabalho precisará ser desfeito. Isso pode afetar o tempo necessário para pausar ou dimensionar seu banco de dados do Azure SQL Data Warehouse. 

> [!IMPORTANT]
> As operações `UPDATE` e `DELETE` são totalmente registradas em log e, portanto, essas operações de desfazer/refazer podem demorar significativamente mais do que as operações equivalentes minimamente registradas em log. 
> 
> 

O melhor cenário é permitir que as transações de modificação de dados em trânsito sejam concluídas antes da pausa ou do dimensionamento do SQL Data Warehouse. No entanto, isso pode não sempre ser prático. Para reduzir o risco de uma longa reversão, considere uma das seguintes opções:

* Gravar novamente as operações de longa duração usando [CTAS][CTAS]
* Divida a operação em partes, trabalhando com um subconjunto de linhas

## <a name="next-steps"></a>Próximas etapas
Confira [Transações no SQL Data Warehouse][Transactions in SQL Data Warehouse] para saber mais sobre os níveis de isolamento e os limites transacionais.  Para obter uma visão geral de outras práticas recomendadas, confira [Práticas recomendadas para o Azure SQL Data Warehouse][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Transactions in SQL Data Warehouse]: ./sql-data-warehouse-develop-transactions.md
[table partition]: ./sql-data-warehouse-tables-partition.md
[Concurrency]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[alter index]:https://msdn.microsoft.com/library/ms188388.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx

<!-- Other web references -->

