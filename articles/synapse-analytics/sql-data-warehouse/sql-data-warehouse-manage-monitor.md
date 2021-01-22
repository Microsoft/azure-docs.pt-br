---
title: Monitore sua carga de trabalho do pool SQL dedicada usando DMVs
description: Saiba como monitorar sua carga de trabalho do pool SQL dedicada do Azure Synapse Analytics e a execução da consulta usando DMVs.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/24/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: synapse-analytics
ms.openlocfilehash: 62064eaae6aa7fb3438845170497035473227d30
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685201"
---
# <a name="monitor-your-azure-synapse-analytics-dedicated-sql-pool-workload-using-dmvs"></a>Monitorar a carga de trabalho do pool SQL dedicado do Azure Synapse Analytics usando DMVs

Este artigo descreve como usar DMVs (exibições de gerenciamento dinâmico) para monitorar sua carga de trabalho, incluindo investigar a execução da consulta no pool do SQL.

## <a name="permissions"></a>Permissões

Para consultar as DMVs neste artigo, você precisará da permissão **View Database State** ou **Control** . Normalmente, a concessão do **estado do banco de dados de exibição** é a permissão preferida, pois é muito mais restritiva.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Conexões do monitor

Todos os logons em seu data warehouse são registrados em [Sys.dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Essa DMV contém os últimos 10.000 logons.  A session_id é a chave primária e é atribuída em sequência para cada novo logon.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Monitorar a execução de consultas

Todas as consultas executadas no pool SQL são registradas em [Sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Essa DMV contém as últimas 10.000 consultas executadas.  A request_id identifica cada consulta exclusivamente e é a chave primária para essa DMV.  A request_id é atribuída em sequência para cada nova consulta e é prefixada com QID, que representa a ID da consulta.  A consulta a esta DMV para uma determinada session_id mostra todas as consultas para um logon específico.

> [!NOTE]
> Os procedimentos armazenados usam vários request_ids.  As IDs de solicitação são atribuídas em ordem sequencial.

Estas são as etapas para investigar os planos de execução da consulta e as horas para uma consulta específica.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>ETAPA 1: Identificar a consulta que você deseja investigar

```sql
-- Monitor active queries
SELECT *
FROM sys.dm_pdw_exec_requests
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 *
FROM sys.dm_pdw_exec_requests
ORDER BY total_elapsed_time DESC;

```

Nos resultados da consulta anterior, **observe a ID da Solicitação** da consulta que você deseja investigar.

As consultas no estado **suspenso** podem ser enfileiradas devido a um grande número de consultas ativas em execução. Essas consultas também aparecem na consulta [Sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) esperas com um tipo de UserConcurrencyResourceType. Para obter informações sobre limites de simultaneidade, consulte [limites de memória e de simultaneidade](memory-concurrency-limits.md) ou [classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md). As consultas também podem esperar por motivos, como bloqueios.  Se sua consulta estiver aguardando um recurso, confira [Investigar consultas aguardando recursos](#monitor-waiting-queries) mais adiante neste artigo.

Para simplificar a pesquisa de uma consulta na tabela [Sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) , use o [rótulo](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para atribuir um comentário à sua consulta, que pode ser pesquisada na exibição sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

### <a name="step-2-investigate-the-query-plan"></a>ETAPA 2: investigar o plano de consulta

Use a ID da solicitação para recuperar o plano do SQL distribuído da consulta (DSQL) do [Sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Quando um plano DSQL estiver demorando mais do que o esperado, a causa pode ser um plano complexo com muitas etapas de DSQL ou apenas uma etapa demorando muito tempo.  Se o plano tiver muitas etapas com várias operações de movimentação, considere otimizar suas distribuições de tabela para reduzir a movimentação de dados. O artigo [distribuição de tabela](sql-data-warehouse-tables-distribute.md) explica por que os dados devem ser movidos para resolver uma consulta. O artigo também explica algumas estratégias de distribuição para minimizar a movimentação de dados.

Para investigar mais detalhes sobre uma única etapa, verifique a coluna *operation_type* da etapa de consulta de execução longa de consulta e observe o **Índice da etapa**:

* Prossiga com a etapa 3 para **operações do SQL**: OnOperation, RemoteOperation, ReturnOperation.
* Prossiga com a etapa 4 para **operações de movimentação de dados**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3-investigate-sql-on-the-distributed-databases"></a>ETAPA 3: investigar o SQL nos bancos de dados distribuídos

Use a ID da Solicitação e o Índice de Etapas para recuperar os detalhes de [sys.dm_pdw_sql_requests](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), que contém informações sobre a execução da consulta em todos os bancos de dados distribuídos.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Se a consulta estiver em execução, [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) poderá ser usado para recuperar o plano estimado do SQL Server do cache do plano do SQL Server para a etapa em execução em uma distribuição específica.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL pool or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-4-investigate-data-movement-on-the-distributed-databases"></a>ETAPA 4: investigar a movimentação de dados nos bancos de dados distribuídos

Use a ID da Solicitação e o Índice da Etapa para recuperar as informações sobre a etapa de movimentação dos dados em execução em cada distribuição em [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

```sql
-- Find information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Verifique a coluna *total_elapsed_time* para ver se uma distribuição específica está demorando muito mais do que outras para movimentar dados.
* Para a distribuição de longa execução, verifique a coluna *rows_processed* para verificar se o número de linhas sendo movidas dessa distribuição é significativamente maior do que outros. Nesse caso, essa localização pode indicar uma distorção dos dados subjacentes. Uma causa da distorção de dados é distribuir em uma coluna com muitos valores nulos (cujas linhas serão todas esterradas na mesma distribuição). Evite consultas lentas, evitando a distribuição nesses tipos de colunas ou filtrando sua consulta para eliminar nulos quando possível. 

Se a consulta estiver em execução, você poderá usar o [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para recuperar o SQL Server plano estimado do cache do plano de SQL Server para a etapa SQL em execução no momento em uma distribuição específica.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL pool Compute or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Monitorar as consultas em espera

Caso você descubra que sua consulta não está fazendo progresso porque está aguardando um recurso, veja a seguir uma consulta que mostra todos os recursos que uma consulta está aguardando.

```sql
-- Find queries
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Se a consulta estiver ativamente aguardando recursos de outra consulta, o estado será **AcquireResources**.  Se a consulta tiver todos os recursos necessários, o estado será **Concedido**.

## <a name="monitor-tempdb"></a>Monitorar o tempdb

O tempdb é usado para manter resultados intermediários durante a execução da consulta. A alta utilização do banco de dados tempdb pode levar a um desempenho de consulta lento. Para cada DW100c configurado, 399 GB de espaço de tempdb é alocado (o DW1000c teria 3,99 TB de espaço em tempdb total).  Abaixo estão as dicas para monitorar o uso do tempdb e para diminuir o uso do tempdb em suas consultas.

### <a name="monitoring-tempdb-with-views"></a>Monitorando tempdb com exibições

Para monitorar o uso do tempdb, primeiro instale o [Microsoft.vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) exibição do [Microsoft Toolkit para o pool do SQL](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring). Em seguida, você pode executar a consulta a seguir para ver o uso de tempdb por nó para todas as consultas executadas:

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN microsoft.vw_sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa'
ORDER BY sr.request_id;
```

Se você tiver uma consulta que esteja consumindo uma grande quantidade de memória ou tenha recebido uma mensagem de erro relacionada à alocação de tempdb, isso pode ser devido a uma CREATE TABLE muito grande, [como SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) ou a instrução [Insert Select](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) em execução que está falhando na operação de movimentação de dados final. Normalmente, isso pode ser identificado como uma operação ShuffleMove no plano de consulta distribuída logo antes da seleção de inserção final.  Use [Sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para monitorar operações ShuffleMove.

A mitigação mais comum é quebrar sua CTAS ou inserir instrução SELECT em várias instruções Load para que o volume de dados não exceda o limite de 1 TB por nó de tempdb. Você também pode dimensionar o cluster para um tamanho maior que espalhará o tamanho do tempdb em mais nós, reduzindo o tempdb em cada nó individual.

Além das instruções CTAS e INSERT SELECT, as consultas grandes e complexas em execução com memória insuficiente podem ser despejadas no tempdb, causando a falha das consultas.  Considere a execução com uma [classe de recursos](resource-classes-for-workload-management.md) maior para evitar o despejo em tempdb.

## <a name="monitor-memory"></a>Monitorar a memória

A memória pode ser a causa raiz de problemas de desempenho lento e memória insuficiente. Considere a possibilidade de dimensionar o data warehouse se descobrir que o uso de memória do SQL Server está atingindo seus limites durante a execução de consulta.

A seguinte consulta retorna o uso e a demanda de memória do SQL Server por nó:

```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB,
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```

## <a name="monitor-transaction-log-size"></a>Monitorar o tamanho do log de transações

A consulta a seguir retorna o tamanho do log de transações em cada distribuição. Se um dos arquivos de log estiver alcançando 160 GB, você deverá considerar a possibilidade de escalar verticalmente a instância ou limitar o tamanho da transação.

```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id
FROM sys.dm_pdw_nodes_os_performance_counters
WHERE
instance_name like 'Distribution_%'
AND counter_name = 'Log File(s) Used Size (KB)'
```

## <a name="monitor-transaction-log-rollback"></a>Monitorar a reversão do log de transações

Se as consultas estiverem falhando ou demorando muito para continuar, verifique e monitore se há transações sendo revertidas.

```sql
-- Monitor rollback
SELECT
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="monitor-polybase-load"></a>Monitorar o carregamento do polybase

A consulta a seguir fornece uma estimativa aproximada do progresso da carga. A consulta mostra apenas os arquivos que estão sendo processados no momento.

```sql

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre DMVs, consulte [Exibições do sistema](../sql/reference-tsql-system-views.md).
