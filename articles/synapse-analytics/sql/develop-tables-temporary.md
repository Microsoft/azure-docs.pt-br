---
title: Usar tabelas temporárias no SQL Synapse
description: Diretrizes essenciais para o uso de tabelas temporárias no SQL Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 06faa1da71331c299245a93af96166880e7732de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96451778"
---
# <a name="temporary-tables-in-synapse-sql"></a>Tabelas temporárias no SQL Synapse

Este artigo contém diretrizes essenciais para o uso de tabelas temporárias e realça os princípios de tabelas temporárias em nível de sessão no Synapse SQL. 

O pool SQL dedicado e os recursos do pool SQL sem servidor podem utilizar tabelas temporárias. O pool SQL sem servidor tem limitações que são discutidas no final deste artigo. 

## <a name="temporary-tables"></a>Tabelas temporárias

As tabelas temporárias são úteis durante o processamento de dados - especialmente durante a transformação onde os resultados intermediários são transitórios. Com o Synapse SQL, as tabelas temporárias existem no nível da sessão.  Elas são visíveis apenas para a sessão em que foram criadas. Dessa forma, elas são descartadas automaticamente ao sair da sessão. 

## <a name="temporary-tables-in-dedicated-sql-pool"></a>Tabelas temporárias no pool SQL dedicado

No recurso de pool dedicado do SQL, as tabelas temporárias oferecem um benefício de desempenho porque seus resultados são gravados em local, e não no armazenamento remoto.

### <a name="create-a-temporary-table"></a>Criar uma tabela temporária

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

### <a name="drop-temporary-tables"></a>Soltar tabelas temporárias

Quando uma nova sessão é criada, não deve haver nenhuma tabela temporária.  No entanto, se você estiver chamando o mesmo procedimento armazenado que cria um temporário com o mesmo nome, para garantir que suas `CREATE TABLE` instruções sejam bem-sucedidas, use uma verificação de pré-instalação simples com  `DROP` : 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Para a consistência da codificação, é recomendável usar esse padrão para as tabelas e as tabelas temporárias.  Também é uma boa ideia usar o `DROP TABLE` para remover tabelas temporárias quando você terminar de usá-las.  

No desenvolvimento de procedimento armazenado, é comum ver os comandos de remoção agrupados no fim de um procedimento para garantir que esses objetos sejam limpos.

```sql
DROP TABLE #stats_ddl
```

### <a name="modularize-code"></a>Modularizar código

As tabelas temporárias podem ser usadas em qualquer lugar em uma sessão de usuário. Esse recurso pode ser explorado para ajudá-lo a modularizar o código do aplicativo.  Para demonstrar, o procedimento armazenado a seguir gera DDL para atualizar todas as estatísticas no banco de dados pelo nome da estatística:

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

Nesse estágio, a única ação que ocorreu é a criação de um procedimento armazenado que gera a #stats_ddl tabela temporária.  O procedimento armazenado descartará #stats_ddl se ele já existir. Essa queda garante que ela não falhe se executada mais de uma vez em uma sessão.  

Como não há um `DROP TABLE` no final do procedimento armazenado, quando o procedimento armazenado for concluído, a tabela criada permanecerá e poderá ser lida fora do procedimento armazenado.  

Ao contrário de outros bancos de dados SQL Server, o SQL Synapse permite que você use a tabela temporária fora do procedimento que o criou.  As tabelas temporárias criadas por meio do pool SQL dedicado podem ser usadas **em qualquer lugar** dentro da sessão. Como resultado, você terá um código mais modular e gerenciável, como demonstrado no exemplo a seguir:

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

### <a name="temporary-table-limitations"></a>Limitações da tabela temporária

O pool SQL dedicado tem algumas limitações de implementação para tabelas temporárias:

- Somente tabelas temporárias com escopo de sessão têm suporte.  Não há suporte para as Tabelas Temporárias Globais.
- As exibições não podem ser criadas em tabelas temporárias.
- As tabelas temporárias só podem ser criadas com a distribuição de hash ou round robin.  Não há suporte para a distribuição de tabela temporária replicada. 

## <a name="temporary-tables-in-serverless-sql-pool"></a>Tabelas temporárias no pool SQL sem servidor

As tabelas temporárias no pool SQL sem servidor têm suporte, mas seu uso é limitado. Eles não podem ser usados em consultas que são arquivos de destino. 

Por exemplo, você não pode unir uma tabela temporária com dados de arquivos no armazenamento. O número de tabelas temporárias é limitado a 100, e seu tamanho total é limitado a 100 MB.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como elaborar tabelas, confira o artigo [Criação de tabelas usando os recursos de SQL do Synapse](develop-tables-overview.md).

