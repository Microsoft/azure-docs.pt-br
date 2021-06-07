---
title: Monitorar o desempenho usando DMVs
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Saiba como detectar e diagnosticar problemas comuns de desempenho usando exibições de gerenciamento dinâmico para monitorar Banco de Dados SQL do Microsoft Azure e Instância Gerenciada do SQL do Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 03/15/2021
ms.openlocfilehash: 5c0de2c1589bfa495ab6ad287b998c403041674c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592139"
---
# <a name="monitoring-microsoft-azure-sql-database-and-azure-sql-managed-instance-performance-using-dynamic-management-views"></a>Monitoramento do desempenho do Banco de Dados SQL do Microsoft Azure e da Instância Gerenciada de SQL usando DMV
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Banco de Dados SQL do Microsoft Azure e o SQL do Azure Instância Gerenciada permitem um subconjunto de exibições de gerenciamento dinâmico para diagnosticar problemas de desempenho, que podem ser causados por consultas bloqueadas ou de execução longa, afunilamentos de recursos, planos de consulta insatisfatórios e assim por diante. Este artigo fornece informações sobre como detectar problemas comuns de desempenho usando exibições de gerenciamento dinâmico.

O Banco de Dados SQL do Microsoft Azure e o SQL Instância Gerenciada do Azure dão suporte parcial a três categorias de exibições de gerenciamento dinâmico:

- Exibições de gerenciamento dinâmico relacionadas ao banco de dados.
- Exibições de gerenciamento dinâmico relacionadas à execução.
- Exibições de gerenciamento dinâmico relacionadas à transação.

Para obter informações detalhadas sobre exibições de gerenciamento dinâmico, consulte [funções e exibições de gerenciamento dinâmico (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

## <a name="monitor-with-sql-insights"></a>Monitorar com o SQL insights

[Azure monitor insights do SQL](../../azure-monitor/insights/sql-insights-overview.md) é uma ferramenta para monitorar instâncias gerenciadas do Azure SQL, bancos de dados SQL do Azure e instâncias de SQL Server em VMs do Azure SQL. Esse serviço usa um agente remoto para capturar dados de DMVs (exibições de gerenciamento dinâmico) e roteia os dados para o Azure Log Analytics, onde podem ser monitorados e analisados. Você pode exibir esses dados de [Azure monitor](../../azure-monitor/overview.md) nos modos de exibição fornecidos ou acessar os dados de log diretamente para executar consultas e analisar tendências. Para começar a usar o Azure Monitor o SQL insights, consulte [habilitar insights do SQL](../../azure-monitor/insights/sql-insights-enable.md).

## <a name="permissions"></a>Permissões

No banco de dados SQL do Azure, consultar uma exibição de gerenciamento dinâmico requer permissões **View Database State** . A permissão **VIEW DATABASE STATE** retorna informações sobre todos os objetos no banco de dados atual.
Para conceder a permissão **VIEW DATABASE STATE** a um usuário específico do banco de dados, execute a seguinte consulta:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

No Azure SQL Instância Gerenciada, consultar uma exibição de gerenciamento dinâmico requer permissões **View Server State** . Para obter mais informações, consulte [exibições de gerenciamento dinâmico do sistema](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views#required-permissions).

Em uma instância do SQL Server e no SQL Instância Gerenciada do Azure, as exibições de gerenciamento dinâmico retornam informações de estado do servidor. No banco de dados SQL do Azure, eles retornam informações sobre o banco de dados lógico atual somente.

Este artigo contém uma coleção de consultas DMV que você pode executar usando SQL Server Management Studio ou Azure Data Studio para detectar os seguintes tipos de problemas de desempenho de consulta:

- [Identificando consultas relacionadas ao consumo excessivo de CPU](#identify-cpu-performance-issues)
- [PAGELATCH_ * e WRITE_LOG esperas relacionadas aos afunilamentos de e/s](#identify-io-performance-issues)
- [PAGELATCH_ * esperas causaram contenção de bytTempDB](#identify-tempdb-performance-issues)
- [RESOURCE_SEMAHPORE esperas causadas por problemas de espera de concessão de memória](#identify-memory-grant-wait-performance-issues)
- [Identificando tamanhos de banco de dados e objeto](#calculating-database-and-objects-sizes)
- [Recuperando informações sobre sessões ativas](#monitoring-connections)
- [Recuperar informações de uso de recursos de todo o sistema e de banco de dados](#monitor-resource-use)
- [Recuperando informações de desempenho de consulta](#monitoring-query-performance)

## <a name="identify-cpu-performance-issues"></a>Identificar problemas de desempenho da CPU

Se o consumo de CPU estiver acima de 80% por longos períodos de tempo, considere as seguintes etapas de solução de problemas:

### <a name="the-cpu-issue-is-occurring-now"></a>O problema de CPU está ocorrendo agora

Se o problema está ocorrendo no momento, há dois cenários possíveis:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Muitas consultas individuais que consomem cumulativamente alta utilização da CPU

Use a consulta a seguir para identificar os hashes de consulta top:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM (SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
    FROM (SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
                CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
    GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>Consultas de longa execução que consomem CPU ainda estão em execução

Use a consulta a seguir para identificar os hashes de consulta:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
    CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>O problema da CPU ocorreu no passado

Se o problema ocorreu no passado e você deseja analisar a causa raiz, use o[Repositório de Consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store). Os usuários com acesso de banco de dados podem usar o T-SQL para consultar os dados dos Repositório de Consultas. As configurações padrão dos Repositório de Dados de Consultas usam uma granularidade de 1 hora. Use a consulta a seguir para examinar a atividade para consultas de consumo de CPU alta. Essa consulta retorna as consultas de consumo de CPU 15 principais. Lembre-se de alterar `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()`:

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

Depois de identificar as consultas com problemas, é hora de ajustar as consultas para reduzir a utilização da CPU.  Se você não tiver tempo para ajustar as consultas, você também pode optar por atualizar o SLO do banco de dados para contornar o problema.

## <a name="identify-io-performance-issues"></a>Identificar problemas de desempenho de e/s

Ao identificar problemas de desempenho de e/s, os tipos de espera superior associados a problemas de e/s são:

- `PAGEIOLATCH_*`

  Para problemas de e/s de arquivo de dados (incluindo `PAGEIOLATCH_SH`, `PAGEIOLATCH_EX`, `PAGEIOLATCH_UP`).  Se o nome do tipo de espera tiver **e/s** , ele apontará para um problema de e/s. Se não houver nenhuma **e/s** no nome de espera da trava de página, ela apontará para um tipo diferente de problema (por exemplo, contenção de tempdb).

- `WRITE_LOG`

  Para problemas de e/s de log de transações.

### <a name="if-the-io-issue-is-occurring-right-now"></a>Se o problema de e/s estiver ocorrendo no momento

Use o [. DM exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) ou [DM os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) para ver os `wait_type` e `wait_time`.

#### <a name="identify-data-and-log-io-usage"></a>Identificar os dados e registrar o uso de e/s

Use a seguinte consulta para identificar os dados e uso de e/s de log. Se a e/s de dados ou de log estiver acima de 80%, isso significará que os usuários usaram a e/s para a camada de serviço do SQL Database.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Se tiver sido atingido o limite de e/s, você tem duas opções:

- Opção 1: Atualizar o tamanho da computação ou camada de serviço
- Opção 2: Identificar e ajustar as consultas que consomem a maioria das e/s.

#### <a name="view-buffer-related-io-using-the-query-store"></a>Visualizar e/s relacionada ao buffer usando o Repositório de Consultas

Para a opção 2, você pode usar a consulta a seguir em relação ao Repositório de Dados de Consultas para e/s relacionada ao buffer para exibir as últimas duas horas de atividade controlada:

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>Exibir o log total e/s para WRITELOG espera

Se o tipo de espera for `WRITELOG`, use a seguinte consulta para exibir e/s de log total pela instrução:

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>Identificar `tempdb` problemas de desempenho

Ao identificar problemas de desempenho de e/s, os principais tipos de espera de associado `tempdb` é de problemas `PAGELATCH_*` (não `PAGEIOLATCH_*`). No entanto, `PAGELATCH_*` esperas sempre significam que você tem `tempdb` contenção.  Essa espera também pode significar que você tenha a contenção de página de dados de objeto de usuário devido a solicitações simultâneas, visando a mesma página de dados. Para confirmar ainda mais a `tempdb` contenção, use [Sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) para confirmar que o valor de wait_resource começa com `2:x:y` onde 2 é `tempdb` a ID do banco de dados, `x` é a ID do arquivo e `y` é a ID da página.  

Para contenção de tempdb, um método comum é reduzir ou reescrever o código do aplicativo que se baseia `tempdb` .  Áreas de uso comum `tempdb` incluem:

- Tabelas temporárias
- Variáveis de tabela
- Parâmetros com valor de tabela
- Uso do repositório de versão (associado a transações de longa execução)
- Consultas que têm planos de consulta que usam classificações e junções de hash spools

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>Principais consultas que usam variáveis de tabela e tabelas temporárias

Use a consulta a seguir para identificar as principais consultas que usam tabelas temporárias e variáveis de tabela:

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>Identificar as transações de longa execução

Use a consulta a seguir para identificar transações de execução longas. Transações de longa execução impedem a limpeza do repositório de versão.

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>Identificar problemas de desempenho de espera de concessão de memória

Se o seu tipo de espera superior for `RESOURCE_SEMAHPORE` e você não tiver um problema de uso de CPU alto, você pode ter um problema de concessão de memória em espera.

### <a name="determine-if-a-resource_semahpore-wait-is-a-top-wait"></a>Determinar se um `RESOURCE_SEMAHPORE` espera for uma espera superior

Use a seguinte consulta para determinar se uma `RESOURCE_SEMAHPORE` espera é uma espera superior

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identify-high-memory-consuming-statements"></a>Identificar altas instruções que consomem memória

Use a consulta a seguir para identificar instruções altas consumo de memória:

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>Identificar as 10 principais concessão de memória

Use a consulta a seguir para identificar 10 principais concessão de memória:

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>Calcular os tamanhos de banco de dados e objetos

A seguinte consulta retorna o tamanho do seu banco de dados (em megabytes):

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

A consulta a seguir retorna o tamanho do dos objetos individuais (em megabytes) no seu banco de dados:

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Monitoramento de conexões

Você pode usar a exibição [Sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql) para recuperar informações sobre as conexões estabelecidas com um servidor específico e uma instância gerenciada e os detalhes de cada conexão. Além disso, a exibição [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) é útil ao recuperar informações sobre todas as conexões de usuário e tarefas internas ativas.

A consulta a seguir recupera as informações sobre a conexão atual:

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> Ao executar as exibições **sys.dm_exec_requests** e **sys.dm_exec_sessions**, se você tiver a permissão **EXIBIR ESTADO DO BANCO DE DADOS** no banco de dados, verá todas as sessões em execução no banco de dados; caso contrário, verá apenas a sessão atual.

## <a name="monitor-resource-use"></a>Monitorar o uso de recursos

Você pode monitorar o uso de recursos do banco de dados SQL usando o [banco de dados sql análise de desempenho de consultas](query-performance-insight-use.md). Para o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada, você pode monitorar usando [repositório de consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

Você também pode monitorar o uso usando estas exibições:

- Banco de dados SQL do Azure: [Sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- Instância Gerenciada do SQL do Azure: [Sys.server_resource_stats](/sql/relational-databases/system-catalog-views/sys-server-resource-stats-azure-sql-database)
- Banco de dados SQL do Azure e Azure SQL Instância Gerenciada: [Sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)

### <a name="sysdm_db_resource_stats"></a>sys.dm_db_resource_stats

Você pode usar a exibição [Sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) em cada banco de dados. A exibição **sys.dm_db_resource_stats** mostra dados de uso de recursos recentes em relação à camada de serviço. A porcentagem média de CPU, E/S de dados, gravações de log e memória é registrada a cada 15 segundos e armazenada por 1 hora.

Como essa exibição oferece uma visão mais granular do uso de recursos, use **sys.dm_db_resource_stats** primeiro para qualquer análise de estado atual ou para solução de problemas. Por exemplo, esta consulta mostra o uso médio e máximo dos recursos do banco de dados atual durante a última hora:

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

Para outras consultas, consulte os exemplos em [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database).

### <a name="sysserver_resource_stats"></a>sys.server_resource_stats

Você pode usar [Sys.server_resource_stats](/sql/relational-databases/system-catalog-views/sys-server-resource-stats-azure-sql-database) para retornar dados de uso de CPU, e/s e armazenamento para uma instância gerenciada do SQL do Azure. Os dados são coletados e agregados em intervalos de cinco minutos. Há uma linha para cada relatório de 15 segundos. Os dados retornados incluem uso de CPU, tamanho de armazenamento, utilização de e/s e SKU de instância gerenciada. Os dados históricos são retidos por aproximadamente 14 dias.

```sql
DECLARE @s datetime;  
DECLARE @e datetime;  
SET @s= DateAdd(d,-7,GetUTCDate());  
SET @e= GETUTCDATE();  
SELECT resource_name, AVG(avg_cpu_percent) AS Average_Compute_Utilization
FROM sys.server_resource_stats
WHERE start_time BETWEEN @s AND @e  
GROUP BY resource_name  
HAVING AVG(avg_cpu_percent) >= 80;
```

### <a name="sysresource_stats"></a>sys. resource_stats

A exibição de [Sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) no banco de dados **mestre** tem informações adicionais que podem ajudá-lo a monitorar o desempenho do banco de dados em sua camada de serviço e tamanho de computação específicos. Os dados são coletados a cada 5 minutos e são mantidos por aproximadamente 14 dias. Essa exibição é útil para uma análise histórica de longo prazo de como seu banco de dados usa recursos.

O grafo a seguir mostra o uso de recursos da CPU para um banco de dados Premium com o tamanho da computação P2 para cada hora em uma semana. Esse grafo começa em uma segunda-feira, mostra cinco dias úteis e, em seguida, mostra um fim de semana, quando ocorre muito menos no aplicativo.

![Uso de recursos do banco de dados](./media/monitoring-with-dmvs/sql_db_resource_utilization.png)

Segundo os dados, atualmente esse banco de dados tem um pico de carga de CPU de pouco mais de 50% de uso da CPU em relação ao tamanho da computação P2 (meio-dia de terça-feira). Se a CPU for o fator dominante no perfil de recurso do aplicativo, você poderá decidir que P2 é o tamanho de computação certo para garantir que a carga de trabalho sempre caiba. Se você espera que um aplicativo cresça com o passar do tempo, é uma boa ideia ter um buffer de recursos adicional para que o aplicativo nunca atinja o limite do nível de desempenho. Aumentando o tamanho da computação, você pode ajudar a evitar erros visíveis para os clientes, que podem ocorrer quando um banco de dados não tem capacidade suficiente para processar solicitações de modo eficaz, especialmente em ambientes sensíveis à latência. Um exemplo é um banco de dados que dá suporte a um aplicativo que pinta páginas da Web com base nos resultados de chamadas de banco de dados.

Outros tipos de aplicativos podem interpretar o mesmo grafo de forma diferente. Por exemplo, se um aplicativo tentar processar dados de folha de pagamento por dia e incluir o mesmo gráfico, esse tipo de modelo de "trabalho em lotes" poderá funcionar bem em um tamanho da computação P1. O tamanho da computação P1 tem 100 DTUs, comparadas a 200 DTUs do tamanho da computação P2. O tamanho da computação P1 fornece que metade do desempenho do P2. Portanto, 50% de uso da CPU em P2 equivale a 100% de uso da CPU em P1. Se o aplicativo não tiver tempo limite, talvez não faça diferença se um trabalho demora 2 horas ou 2,5 horas para ser concluído, desde que seja concluído hoje. Um aplicativo dessa categoria provavelmente pode usar um tamanho da computação P1. Você pode tirar proveito do fato de que há períodos do dia em que o uso de recursos é menor, o que significa que um "pico grande" pode ser extrapolado para um dos ciclos mais tarde. O tamanho da computação P1 pode ser bom para esse tipo de aplicativo (e economiza dinheiro), desde que os trabalhos possam ser concluídos no horário todos os dias.

O mecanismo de banco de dados expõe informações de recursos consumidas para cada banco de dados ativo na exibição **Sys.resource_stats** do banco de dados **mestre** em cada servidor. Os dados na tabela são agregados em intervalos de cinco minutos. Com as camadas de serviço Básica, Standard e Premium, os dados podem demorar mais de 5 minutos para aparecer na tabela, o que significa que esses dados são mais úteis para análise histórica do que para análise em tempo real. Consulte a exibição **sys.resource_stats** para ver o histórico recente de um banco de dados e validar se a reserva escolhida ofereceu o desempenho desejado quando necessário.

> [!NOTE]
> No banco de dados SQL do Azure, você deve estar conectado ao banco de dados **mestre** para consultar **Sys.resource_stats** nos exemplos a seguir.

Este exemplo mostra como os dados nesse modo de exibição são expostos:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC;
```

![Exibição do catálogo sys.resource_stats](./media/monitoring-with-dmvs/sys_resource_stats.png)

O exemplo a seguir mostra diferentes maneiras pelas quais você pode usar a exibição de catálogo **Sys.resource_stats** para obter informações sobre como seu banco de dados usa recursos:

1. Para examinar o uso de recursos da semana passada para o banco de dados userdb1, você pode executar esta consulta:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. Para avaliar como sua carga de trabalho se ajusta ao tamanho da computação, você precisa fazer uma pesquisa detalhada de cada aspecto das métricas de recursos: CPU, leituras, gravações, número de trabalhadores e número de sessões. Veja uma consulta revisada usando **sys.resource_stats** para relatar os valores médio e máximo dessas métricas de recursos:

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. Com essas informações sobre os valores médio e máximo de cada métrica de recurso, você pode avaliar como sua carga de trabalho se ajusta ao tamanho da computação escolhido. Normalmente, os valores médios de **sys.resource_stats** oferecem uma boa linha de base a ser usada em comparação ao tamanho de destino. Deve ser seu cartão de medida principal. Por exemplo, você pode estar usando a camada de serviço Standard com o tamanho da computação S2. Os percentuais médios de uso da CPU e leituras e gravações de E/S estão abaixo de 40%, o número médio de trabalhadores está abaixo de 50 e o número médio de sessões é inferior a 200. Talvez a carga de trabalho se enquadre no tamanho da computação S1. É fácil ver se o banco de dados se encaixa nos limites de sessão e de trabalho. Para ver se um banco de dados se encaixa em um tamanho de computação inferior em relação à CPU, às leituras e às gravações, divida o número de DTU do tamanho de computação inferior pelo número de DTU do seu tamanho de computação atual e multiplique o resultado por 100:

    `S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40`

    O resultado é a diferença de desempenho relativa entre os dois tamanhos da computação em percentual. Se seu uso de recursos não ultrapassar esse valor, sua carga de trabalho poderá se enquadrar no tamanho da computação inferior. No entanto, você precisa examinar todos os intervalos de valores de uso de recursos e determinar, pelo percentual, com que frequência a carga de trabalho do banco de dados se enquadraria no tamanho da computação inferior. A consulta a seguir produz o percentual de ajuste por dimensão de recurso, com base no limite de 40% que calculamos neste exemplo:

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Com base na camada de serviço do banco de dados, você pode decidir se sua carga de trabalho se enquadra no tamanho da computação inferior. Se a meta de carga de trabalho do banco de dados é de 99,9% e a consulta anterior retorna valores superiores a 99,9% para as três dimensões de recursos, é provável que sua carga de trabalho se enquadre no tamanho da computação inferior.

    A avaliação do percentual de ajuste também traz informações que indicam se você deve ir ao próximo tamanho da computação para chegar à sua meta. Por exemplo, userdb1 mostra o seguinte uso da CPU para a semana passada:

   | Percentual médio da CPU | Percentual máximo da CPU |
   | --- | --- |
   | 24,5 |100,00 |

    A média da CPU é de aproximadamente um quarto do limite do tamanho da computação, o que se ajustaria bem ao tamanho da computação do banco de dados. No entanto, o valor máximo mostra que o banco de dados atinge o limite do tamanho da computação. Você precisa ir para o próximo tamanho da computação? Avalie quantas vezes sua carga de trabalho atinge 100% e compará-la à meta de carga de trabalho de seu banco de dados.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Se essa consulta retornar um valor menor que 99,9 por cento para qualquer uma das três dimensões de recurso, considere mover para o próximo tamanho de computação mais alto ou usar técnicas de ajuste de aplicativo para reduzir a carga no banco de dados.

4. Este exercício também leva em consideração o aumento projetado da carga de trabalho no futuro.

Para pools elásticos, você pode monitorar bancos de dados autônomos no pool com as técnicas descritas nesta seção. Mas você também pode monitorar o pool como um todo. Para saber mais, veja [Monitorar e gerenciar um pool elásticos](elastic-pool-overview.md).

### <a name="maximum-concurrent-requests"></a>Máximo de solicitações simultâneas

Para ver o número de solicitações simultâneas, execute esta consulta Transact-SQL em seu banco de dados:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R;
```

Para analisar a carga de trabalho de um banco de dados SQL Server, modifique essa consulta para filtrar o banco de dados específico que você deseja analisar. Por exemplo, se você tiver um banco de dados local chamado MyDatabase, esta consulta Transact-SQL retornará a contagem de solicitações simultâneas nesse banco de dados:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R
INNER JOIN sys.databases D ON D.database_id = R.database_id
AND D.name = 'MyDatabase';
```

Isso é apenas um instantâneo em um único ponto no tempo. Para entender melhor sua carga de trabalho e seus requisitos de solicitações simultâneas, você precisará coletar muitas amostras durante algum tempo.

### <a name="maximum-concurrent-logins"></a>Máximo de logons simultâneos

Você pode analisar seus padrões de usuário e de aplicativo para ter uma ideia da frequência de logons. Você também pode executar cargas reais em um ambiente de teste para ter certeza de que não está atingindo esse ou outros limites mencionados neste artigo. Não há uma única consulta ou DMV (exibição de gerenciamento dinâmico) que possa mostrar o histórico ou contagens de logon simultâneas.

Se vários clientes usarem a mesma cadeia de conexão, o serviço autenticará cada logon. Se 10 usuários se conectassem simultaneamente a um banco de dados usando o mesmo nome de usuário e senha, existiriam 10 logons simultâneos. Esse limite é aplicado apenas durante o logon e a autenticação. Se os mesmos 10 usuários se conectassem ao banco de dados sequencialmente, o número de logons simultâneos nunca seria maior do que 1.

> [!NOTE]
> Atualmente, esse limite não se aplica a bancos de dados em pools elásticos.

### <a name="maximum-sessions"></a>Máximo de sessões

Para ver o número de sessões ativas atuais, execute esta consulta Transact-SQL em seu banco de dados:

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections;
```

Se você estiver analisando uma carga de trabalho SQL Server, modifique a consulta para se concentrar em um banco de dados específico. Essa consulta ajuda a determinar possíveis necessidades de sessão para o banco de dados se você estiver pensando em movê-lo para o Azure.

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections C
INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
INNER JOIN sys.databases D ON (D.database_id = S.database_id)
WHERE D.name = 'MyDatabase';
```

Novamente, essas consultas retornam uma contagem pontual. Se você coletar vários exemplos ao longo do tempo, terá a melhor compreensão do uso da sessão.

Você pode obter estatísticas históricas em sessões consultando a exibição de [Sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) e revisando a coluna **active_session_count** .

## <a name="monitoring-query-performance"></a>Monitoramento de desempenho da consulta

Consultas de execução lenta ou longa podem consumir recursos significativos do sistema. Esta seção demonstra como usar exibições de gerenciamento dinâmico para detectar alguns problemas comuns de desempenho de consulta.

### <a name="finding-top-n-queries"></a>Localizando as principais consultas N

O exemplo a seguir retorna informações sobre as cinco principais consultas classificadas pelo tempo médio de CPU. Este exemplo agrega as consultas de acordo com sua hash de consulta, para que as consultas logicamente equivalentes sejam agrupadas em função de seu consumo de recursos cumulativos.

```sql
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
     MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
FROM sys.dm_exec_query_stats AS QS
CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Monitoramento de consultas bloqueadas

Consultas lentas ou demoradas podem contribuir para consumo excessivo de recursos e ser a consequência de consultas bloqueadas. A causa do bloqueio pode ser projeto inadequado de aplicativos, planos de consulta incorretos, a falta de índices úteis e assim por diante. Você pode usar a exibição sys.dm_tran_locks para obter informações sobre a atividade de bloqueio atual no banco de dados. Para obter um exemplo de código, consulte [Sys.dm_tran_locks (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql). Para obter mais informações sobre como solucionar problemas de bloqueio, consulte [entender e resolver problemas de bloqueio do SQL do Azure](understand-resolve-blocking.md).

### <a name="monitoring-query-plans"></a>Monitoramento de planos de consulta

Um plano de consulta ineficiente também pode aumentar o consumo de CPU. O exemplo a seguir usa a exibição [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql) para determinar qual consulta usa a CPU mais cumulativa.

```sql
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Confira também

[Introdução ao banco de dados SQL do Azure e Azure SQL Instância Gerenciada](sql-database-paas-overview.md)