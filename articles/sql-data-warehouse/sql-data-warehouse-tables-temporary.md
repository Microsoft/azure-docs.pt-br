---
title: Tabelas temporárias no SQL Data Warehouse | Microsoft Docs
description: Guia essencial para usar as tabelas temporárias e destaca os princípios das tabelas temporárias no nível da sessão.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: e43e52e56ec7abbf5d8eb879defef54bd7d50658
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479822"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Tabelas temporárias no SQL Data Warehouse
Este artigo contém as diretrizes essenciais de como usar as tabelas temporárias e destaca os princípios das tabelas temporárias no nível da sessão. Usar as informações neste artigo pode ajudá-lo a modularizar seu código, melhorando a reutilização e a facilidade de manutenção do seu código.

## <a name="what-are-temporary-tables"></a>O que são tabelas temporárias?
As tabelas temporárias são úteis durante o processamento de dados - especialmente durante a transformação onde os resultados intermediários são transitórios. No SQL Data Warehouse, existem tabelas temporárias no nível de sessão.  Elas são visíveis apenas para a sessão na qual foram criadas e são descartadas automaticamente quando a sessão faz logoff.  As tabelas temporárias oferecem um benefício de desempenho, pois seus resultados são gravados no local, em vez do armazenamento remoto.

## <a name="create-a-temporary-table"></a>Criar uma tabela temporária
As tabelas temporárias são criadas simplesmente prefixando o nome da tabela com um `#`.  Por exemplo:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

As tabelas temporárias também podem ser criadas usando `CTAS` com a mesma abordagem:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
``` 

> [!NOTE]
> `CTAS` é um comando potente com a vantagem extra de ser muito eficiente em seu uso do espaço de log das transações. 
> 
> 

## <a name="dropping-temporary-tables"></a>Descartando tabelas temporárias
Quando uma nova sessão é criada, não deve haver nenhuma tabela temporária.  No entanto, se você estiver chamando o mesmo procedimento armazenado, o que cria um temporário com o mesmo nome, para garantir que suas instruções `CREATE TABLE` sejam bem-sucedidas, uma simples verificação da existência com `DROP` pode ser usada como no exemplo abaixo:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Para a consistência da codificação, é recomendável usar esse padrão para as tabelas e as tabelas temporárias.  Também é uma boa prática usar `DROP TABLE` para remover as tabelas temporárias quando você tiver terminado o trabalho com elas no código.  No desenvolvimento de procedimento armazenado, é comum ver os comandos de remoção agrupados no fim de um procedimento para garantir que esses objetos sejam limpos.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularizar o código
Como as tabelas temporárias podem ser vistas em qualquer lugar em uma sessão do usuário, isso pode ser explorado para ajudá-lo a modularizar o código do aplicativo.  Por exemplo, o seguinte procedimento armazenado gera DDL para atualizar todas as estatísticas no banco de dados pelo nome da estatística.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

Nesse estágio, a única ação que ocorreu é a criação de um procedimento armazenado que gera uma tabela temporária, #stats_ddl, com instruções DDL.  Esse procedimento armazenado descartará #stats_ddl se ela já existir para garantir que não falhará se for executada mais de uma vez em uma sessão.  No entanto, já que não há nenhum `DROP TABLE` no final do procedimento armazenado, quando o procedimento armazenado for concluído, ele deixará a tabela criada para que possa ser lida de fora do procedimento armazenado.  No SQL Data Warehouse, ao contrário dos outros bancos de dados do SQL Server, é possível usar a tabela temporária fora do procedimento que a criou.  As tabelas temporárias do SQL Data Warehouse podem ser usadas **em qualquer lugar** na sessão. Isso pode levar a um código mais modular e gerenciável, como no exemplo abaixo:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Limitações da tabela temporária
O SQL Data Warehouse impõe algumas limitações ao implementar a tabelas temporárias.  Atualmente, somente a sessão com o escopo das tabelas temporárias é suportada.  Não há suporte para as Tabelas Temporárias Globais.  E mais, as exibições não podem ser criadas nas tabelas temporárias.  As tabelas temporárias só podem ser criadas com a distribuição hash ou round robin.  Não há suporte para a distribuição de tabela temporária replicada. 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como desenvolver tabelas, consulte a [Visão geral da tabela](sql-data-warehouse-tables-overview.md).

