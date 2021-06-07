---
title: Entender e resolver problemas de bloqueio do SQL do Azure
titleSuffix: Azure SQL Database
description: Uma visão geral dos tópicos específicos do banco de dados SQL do Azure sobre bloqueio e solução de problemas.
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 3/02/2021
ms.openlocfilehash: 3d64336184450514d52095097343a4588213f111
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034890"
---
# <a name="understand-and-resolve-azure-sql-database-blocking-problems"></a>Entender e resolver problemas de bloqueio do banco de dados SQL do Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="objective"></a>Objetivo

O artigo descreve o bloqueio em bancos de dados SQL do Azure e demonstra como solucionar problemas e resolver o bloqueio. 

Neste artigo, o termo conexão refere-se a uma única sessão conectada do banco de dados. Cada conexão aparece como uma ID de sessão (SPID) ou session_id em muitos DMVs. Cada um desses SPIDs é geralmente conhecido como um processo, embora não seja um contexto de processo separado no sentido normal. Em vez disso, cada SPID consiste nos recursos do servidor e nas estruturas de dados necessárias para atender às solicitações de uma única conexão de um determinado cliente. Um único aplicativo cliente pode ter uma ou mais conexões. Da perspectiva do banco de dados SQL do Azure, não há nenhuma diferença entre várias conexões de um único aplicativo cliente em um único computador cliente e várias conexões de vários aplicativos cliente ou vários computadores cliente; Eles são atômicos. Uma conexão pode bloquear outra conexão, independentemente do cliente de origem.

> [!NOTE]
> **Este conteúdo está concentrado no banco de dados SQL do Azure.** O banco de dados SQL do Azure é baseado na versão estável mais recente do mecanismo de banco de dados Microsoft SQL Server, portanto, grande parte do conteúdo é semelhante, embora as opções e as ferramentas de solução de problemas possam ser diferentes. Para obter mais informações sobre o bloqueio no SQL Server, consulte [entender e resolver problemas de bloqueio de SQL Server](/troubleshoot/sql/performance/understand-resolve-blocking).

## <a name="understand-blocking"></a>Entender o bloqueio 
 
O bloqueio é uma característica inevitável e por design de qualquer RDBMS (sistema de gerenciamento de banco de dados relacional) com simultaneidade baseada em bloqueio. Como mencionado anteriormente, em SQL Server, o bloqueio ocorre quando uma sessão mantém um bloqueio em um recurso específico e um segundo SPID tenta adquirir um tipo de bloqueio conflitante no mesmo recurso. Normalmente, o intervalo de tempo para o qual o primeiro SPID bloqueia o recurso é pequeno. Quando a sessão proprietária libera o bloqueio, a segunda conexão é liberada para adquirir seu próprio bloqueio no recurso e continuar o processamento. Esse comportamento é normal e pode ocorrer muitas vezes durante o curso de um dia sem efeito perceptível no desempenho do sistema.

O contexto de duração e de transação de uma consulta determina por quanto tempo seus bloqueios são mantidos e, assim, seus efeitos em outras consultas. Se a consulta não for executada em uma transação (e nenhuma dica de bloqueio for usada), os bloqueios para instruções SELECT só serão mantidos em um recurso no momento em que estiver sendo lido, e não durante a consulta. Para instruções INSERT, UPDATE e DELETE, os bloqueios são mantidos durante a consulta, tanto para a consistência de dados quanto para permitir que a consulta seja revertida, se necessário.

Para consultas executadas em uma transação, a duração para a qual os bloqueios são mantidos é determinada pelo tipo de consulta, o nível de isolamento da transação e se as dicas de bloqueio são usadas na consulta. Para obter uma descrição de bloqueio, dicas de bloqueio e níveis de isolamento da transação, consulte os seguintes artigos:

* [Bloqueio no Mecanismo de Banco de Dados](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [Personalizando bloqueio e controle de versão de linha](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#customizing-locking-and-row-versioning)
* [Modos de bloqueio](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_modes)
* [Compatibilidade de bloqueio](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_compatibility)
* [Transações](/sql/t-sql/language-elements/transactions-transact-sql)

Quando o bloqueio e o bloqueio persistem até o ponto em que há um efeito prejudicial no desempenho do sistema, isso ocorre devido a um dos seguintes motivos:

* Um SPID mantém bloqueios em um conjunto de recursos por um longo período de tempo antes de liberá-los. Esse tipo de bloqueio resolve a si mesmo ao longo do tempo, mas pode causar degradação do desempenho.

* Um SPID mantém bloqueios em um conjunto de recursos e nunca os libera. Esse tipo de bloqueio não se resolve e impede o acesso aos recursos afetados indefinidamente.

No primeiro cenário, a situação pode ser muito fluida, pois os SPIDs diferentes causam o bloqueio em diferentes recursos ao longo do tempo, criando um destino de movimentação. Essas situações são difíceis de solucionar problemas usando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) para restringir o problema a consultas individuais. Por outro lado, a segunda situação resulta em um estado consistente que pode ser mais fácil de diagnosticar.

## <a name="applications-and-blocking"></a>Aplicativos e bloqueio

Pode haver uma tendência de se concentrar em problemas de plataforma e de ajuste no lado do servidor ao enfrentar um problema de bloqueio. No entanto, a atenção paga apenas ao banco de dados pode não levar a uma resolução e pode absorver o tempo e a energia mais bem direcionados para examinar o aplicativo cliente e as consultas que ele envia. Independentemente do nível de visibilidade que o aplicativo expõe em relação às chamadas de banco de dados sendo feitas, um problema de bloqueio, no entanto, freqüentemente requer a inspeção das instruções SQL exatas enviadas pelo aplicativo e o comportamento exato do aplicativo em relação ao cancelamento de consulta, ao gerenciamento de conexão, à busca de todas as linhas de resultado e assim por diante. Se a ferramenta de desenvolvimento não permitir o controle explícito sobre o gerenciamento de conexão, o cancelamento de consulta, o tempo limite de consulta, a busca de resultados e assim por diante, os problemas de bloqueio poderão não ser resolvidos. Esse potencial deve ser examinado de forma minuciosa antes de selecionar uma ferramenta de desenvolvimento de aplicativos para o banco de dados SQL do Azure, especialmente para ambientes OLTP sensíveis ao desempenho. 

Preste atenção ao desempenho do banco de dados durante a fase de design e construção do banco de dados e do aplicativo. Em particular, o consumo de recursos, o nível de isolamento e o comprimento do caminho da transação devem ser avaliados para cada consulta. Cada consulta e transação devem ser as mais leves possíveis. Uma boa disciplina de gerenciamento de conexão deve ser exercitada, sem ele, o aplicativo pode parecer ter desempenho aceitável em um número baixo de usuários, mas o desempenho pode degradar significativamente à medida que o número de usuários é dimensionado para cima. 

Com o design adequado de aplicativos e consultas, o banco de dados SQL do Azure é capaz de dar suporte a muitos milhares de usuários simultâneos em um único servidor, com pouco bloqueio.

> [!Note]
> Para obter mais diretrizes de desenvolvimento de aplicativos, consulte [Solucionando problemas de conectividade e outros erros com o banco de dados SQL do Azure e o Azure sql instância gerenciada](troubleshoot-common-errors-issues.md) e [tratamento de falhas transitórias](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling).

## <a name="troubleshoot-blocking"></a>Solucionar problemas de bloqueio

Independentemente da situação de bloqueio em que estamos, a metodologia para solucionar problemas de bloqueio é a mesma. Essas separações lógicas são o que determinará o restante da composição deste artigo. O conceito é localizar o bloqueador de cabeçalho e identificar o que a consulta está fazendo e por que está bloqueando. Depois que a consulta problemática for identificada (ou seja, o que está mantendo bloqueios pelo período prolongado), a próxima etapa será analisar e determinar por que o bloqueio está ocorrendo. Depois de entendermos o porquê, podemos fazer alterações remodelando a consulta e a transação.

Etapas na solução de problemas:

1. Identificar a sessão de bloqueio principal (bloqueador de cabeçalho)

2. Localizar a consulta e a transação que está causando o bloqueio (o que está mantendo bloqueios por um período prolongado)

3. Analisar/entender por que o bloqueio prolongado ocorre

4. Resolver o problema de bloqueio remodelando a consulta e a transação

Agora, vamos nos aprofundar para discutir como identificar a sessão de bloqueio principal com uma captura de dados apropriada.

## <a name="gather-blocking-information"></a>Coletar informações de bloqueio

Para neutralizar a dificuldade de solucionar problemas de bloqueio, um administrador de banco de dados pode usar scripts SQL que monitoram constantemente o estado de bloqueio e bloqueio no banco de dados SQL do Azure. Para coletar esses dados, há basicamente dois métodos. 

A primeira é consultar DMOs (Dynamic Management Objects) e armazenar os resultados para comparação ao longo do tempo. Alguns objetos referenciados neste artigo são DMVs (exibições de gerenciamento dinâmico) e alguns são DMFs (funções de gerenciamento dinâmico). O segundo método é usar XEvents para capturar o que está em execução. 


## <a name="gather-information-from-dmvs"></a>Coletar informações de DMVs

Referenciar DMVs para solucionar problemas de bloqueio tem o objetivo de identificar o SPID (ID de sessão) no início da cadeia de bloqueios e na instrução SQL. Procure os SPIDs de vítima que estão sendo bloqueados. Se qualquer SPID estiver sendo bloqueado por outro SPID, investigue o SPID que possui o recurso (o SPID de bloqueio). O proprietário SPID está sendo bloqueado também? Você pode percorrer a cadeia para localizar o bloqueador de cabeçalho e, em seguida, investigar por que ele está mantendo seu bloqueio.

Lembre-se de executar cada um desses scripts no banco de dados SQL do Azure de destino.

* Os comandos sp_who e sp_who2 são comandos mais antigos para mostrar todas as sessões atuais. O sys.dm_exec_sessions DMV retorna mais dados em um conjunto de resultados que é mais fácil de consultar e filtrar. Você encontrará sys.dm_exec_sessions no núcleo de outras consultas. 

* Se você já tiver uma sessão específica identificada, poderá usar `DBCC INPUTBUFFER(<session_id>)` para localizar a última instrução que foi enviada por uma sessão. Resultados semelhantes podem ser retornados com o sys.dm_exec_input_buffer a DMF (função de gerenciamento dinâmico), em um conjunto de resultados que é mais fácil de consultar e filtrar, fornecendo o session_id e o request_id. Por exemplo, para retornar a consulta mais recente enviada por session_id 66 e request_id 0:

```sql
SELECT * FROM sys.dm_exec_input_buffer (66,0);
```

* Consulte a sys.dm_exec_requests e referencie a coluna blocking_session_id. Quando blocking_session_id = 0, uma sessão não está sendo bloqueada. Embora sys.dm_exec_requests liste somente as solicitações em execução no momento, qualquer conexão (ativa ou não) será listada em sys.dm_exec_sessions. Crie nessa junção comum entre sys.dm_exec_requests e sys.dm_exec_sessions na próxima consulta.

* Execute esta consulta de exemplo para localizar as consultas em execução ativamente e o texto do lote SQL atual ou o texto do buffer de entrada, usando as DMVs [Sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) ou [Sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) . Se os dados retornados pelo `text` campo de sys.dm_exec_sql_text forem nulos, a consulta não estará em execução no momento. Nesse caso, o `event_info` campo de sys.dm_exec_input_buffer conterá a última cadeia de caracteres de comando passada para o mecanismo do SQL. Essa consulta também pode ser usada para identificar sessões que bloqueiam outras sessões, incluindo uma lista de session_ids bloqueadas por session_id. 

```sql
WITH cteBL (session_id, blocking_these) AS 
(SELECT s.session_id, blocking_these = x.blocking_these FROM sys.dm_exec_sessions s 
CROSS APPLY    (SELECT isnull(convert(varchar(6), er.session_id),'') + ', '  
                FROM sys.dm_exec_requests as er
                WHERE er.blocking_session_id = isnull(s.session_id ,0)
                AND er.blocking_session_id <> 0
                FOR XML PATH('') ) AS x (blocking_these)
)
SELECT s.session_id, blocked_by = r.blocking_session_id, bl.blocking_these
, batch_text = t.text, input_buffer = ib.event_info, * 
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
INNER JOIN cteBL as bl on s.session_id = bl.session_id
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) t
OUTER APPLY sys.dm_exec_input_buffer(s.session_id, NULL) AS ib
WHERE blocking_these is not null or r.blocking_session_id > 0
ORDER BY len(bl.blocking_these) desc, r.blocking_session_id desc, r.session_id;
```

* Execute esta consulta de exemplo elaborada, fornecida pelo Suporte da Microsoft, para identificar o cabeçalho de uma cadeia de bloqueio de sessão múltipla, incluindo o texto de consulta das sessões envolvidas em uma cadeia de bloqueio.

```sql
WITH cteHead ( session_id,request_id,wait_type,wait_resource,last_wait_type,is_user_process,request_cpu_time
,request_logical_reads,request_reads,request_writes,wait_time,blocking_session_id,memory_usage
,session_cpu_time,session_reads,session_writes,session_logical_reads
,percent_complete,est_completion_time,request_start_time,request_status,command
,plan_handle,sql_handle,statement_start_offset,statement_end_offset,most_recent_sql_handle
,session_status,group_id,query_hash,query_plan_hash) 
AS ( SELECT sess.session_id, req.request_id, LEFT (ISNULL (req.wait_type, ''), 50) AS 'wait_type'
    , LEFT (ISNULL (req.wait_resource, ''), 40) AS 'wait_resource', LEFT (req.last_wait_type, 50) AS 'last_wait_type'
    , sess.is_user_process, req.cpu_time AS 'request_cpu_time', req.logical_reads AS 'request_logical_reads'
    , req.reads AS 'request_reads', req.writes AS 'request_writes', req.wait_time, req.blocking_session_id,sess.memory_usage
    , sess.cpu_time AS 'session_cpu_time', sess.reads AS 'session_reads', sess.writes AS 'session_writes', sess.logical_reads AS 'session_logical_reads'
    , CONVERT (decimal(5,2), req.percent_complete) AS 'percent_complete', req.estimated_completion_time AS 'est_completion_time'
    , req.start_time AS 'request_start_time', LEFT (req.status, 15) AS 'request_status', req.command
    , req.plan_handle, req.[sql_handle], req.statement_start_offset, req.statement_end_offset, conn.most_recent_sql_handle
    , LEFT (sess.status, 15) AS 'session_status', sess.group_id, req.query_hash, req.query_plan_hash
    FROM sys.dm_exec_sessions AS sess
    LEFT OUTER JOIN sys.dm_exec_requests AS req ON sess.session_id = req.session_id
    LEFT OUTER JOIN sys.dm_exec_connections AS conn on conn.session_id = sess.session_id 
    )
, cteBlockingHierarchy (head_blocker_session_id, session_id, blocking_session_id, wait_type, wait_duration_ms,
wait_resource, statement_start_offset, statement_end_offset, plan_handle, sql_handle, most_recent_sql_handle, [Level])
AS ( SELECT head.session_id AS head_blocker_session_id, head.session_id AS session_id, head.blocking_session_id
    , head.wait_type, head.wait_time, head.wait_resource, head.statement_start_offset, head.statement_end_offset
    , head.plan_handle, head.sql_handle, head.most_recent_sql_handle, 0 AS [Level]
    FROM cteHead AS head
    WHERE (head.blocking_session_id IS NULL OR head.blocking_session_id = 0)
    AND head.session_id IN (SELECT DISTINCT blocking_session_id FROM cteHead WHERE blocking_session_id != 0)
    UNION ALL
    SELECT h.head_blocker_session_id, blocked.session_id, blocked.blocking_session_id, blocked.wait_type,
    blocked.wait_time, blocked.wait_resource, h.statement_start_offset, h.statement_end_offset,
    h.plan_handle, h.sql_handle, h.most_recent_sql_handle, [Level] + 1
    FROM cteHead AS blocked
    INNER JOIN cteBlockingHierarchy AS h ON h.session_id = blocked.blocking_session_id and h.session_id!=blocked.session_id --avoid infinite recursion for latch type of blocking
    WHERE h.wait_type COLLATE Latin1_General_BIN NOT IN ('EXCHANGE', 'CXPACKET') or h.wait_type is null
    )
SELECT bh.*, txt.text AS blocker_query_or_most_recent_query 
FROM cteBlockingHierarchy AS bh 
OUTER APPLY sys.dm_exec_sql_text (ISNULL ([sql_handle], most_recent_sql_handle)) AS txt;
```

* Para capturar transações de execução longa ou não confirmadas, use outro conjunto de DMVs para exibir as transações abertas atuais, incluindo [Sys.dm_tran_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql), [Sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql), [Sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql)e sys.dm_exec_sql_text. Há vários DMVs associados a transações de rastreamento, consulte mais [DMVs em transações](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql) aqui. 

```sql
SELECT [s_tst].[session_id],
[database_name] = DB_NAME (s_tdt.database_id),
[s_tdt].[database_transaction_begin_time], 
[sql_text] = [s_est].[text] 
FROM sys.dm_tran_database_transactions [s_tdt]
INNER JOIN sys.dm_tran_session_transactions [s_tst] ON [s_tst].[transaction_id] = [s_tdt].[transaction_id]
INNER JOIN sys.dm_exec_connections [s_ec] ON [s_ec].[session_id] = [s_tst].[session_id]
CROSS APPLY sys.dm_exec_sql_text ([s_ec].[most_recent_sql_handle]) AS [s_est];
```

* [Sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) de referência que está na camada de thread/tarefa do SQL. Isso retorna informações sobre qual tipo de espera SQL a solicitação está experimentando no momento. Assim como sys.dm_exec_requests, somente as solicitações ativas são retornadas por sys.dm_os_waiting_tasks. 

> [!Note]
> Para obter muito mais sobre os tipos de espera, incluindo estatísticas de espera agregadas ao longo do tempo, consulte o [Sys.DM_DB_WAIT_STATS](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)DMV. Essa DMV retorna estatísticas de espera de agregação somente para o banco de dados atual.

* Use o DMV [Sys.dm_tran_locks](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql) para obter informações mais granulares sobre quais bloqueios foram colocados por consultas. Essa DMV pode retornar grandes quantidades de dados em uma SQL Server de produção e é útil para diagnosticar quais bloqueios estão sendo mantidos no momento. 

Devido à junção interna em sys.dm_os_waiting_tasks, a consulta a seguir restringe a saída de sys.dm_tran_locks somente para solicitações atualmente bloqueadas, seu status de espera e seus bloqueios:

```sql
SELECT table_name = schema_name(o.schema_id) + '.' + o.name
, wt.wait_duration_ms, wt.wait_type, wt.blocking_session_id, wt.resource_description
, tm.resource_type, tm.request_status, tm.request_mode, tm.request_session_id
FROM sys.dm_tran_locks AS tm
INNER JOIN sys.dm_os_waiting_tasks as wt ON tm.lock_owner_address = wt.resource_address
LEFT OUTER JOIN sys.partitions AS p on p.hobt_id = tm.resource_associated_entity_id
LEFT OUTER JOIN sys.objects o on o.object_id = p.object_id or tm.resource_associated_entity_id = o.object_id
WHERE resource_database_id = DB_ID()
AND object_name(p.object_id) = '<table_name>';
```

* Com DMVs, armazenar os resultados da consulta ao longo do tempo fornecerá pontos de dados que permitirão que você examine o bloqueio em um intervalo de tempo especificado para identificar as tendências ou o bloqueio persistente. 

## <a name="gather-information-from-extended-events"></a>Coletar informações de eventos estendidos

Além das informações anteriores, geralmente é necessário capturar um rastreamento das atividades no servidor para investigar minuciosamente um problema de bloqueio no banco de dados SQL do Azure. Por exemplo, se uma sessão executar várias instruções em uma transação, somente a última instrução que foi enviada será representada. No entanto, uma das instruções anteriores pode ser o motivo pelo qual os bloqueios ainda estão sendo mantidos. Um rastreamento permitirá que você veja todos os comandos executados por uma sessão dentro da transação atual.

Há duas maneiras de capturar rastreamentos no SQL Server; Eventos estendidos (XEvents) e rastreamentos do criador de perfil. No entanto, [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) é uma tecnologia de rastreamento preterida sem suporte para o banco de dados SQL do Azure. Os [eventos estendidos](/sql/relational-databases/extended-events/extended-events) são a tecnologia de rastreamento mais recente que permite mais versatilidade e menos impacto no sistema observado, e sua interface é integrada ao SSMS (SQL Server Management Studio). 

Consulte o documento que explica como usar o assistente para [nova sessão de eventos estendidos](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) no SSMS. No entanto, para bancos de dados SQL do Azure, o SSMS fornece uma subpasta eventos estendidos em cada banco de dados no Pesquisador de objetos. Use um assistente de sessão de eventos estendidos para capturar esses eventos úteis: 

-   Erros de categoria:
    -   Attention
    -   Error_reported  
    -   Execution_warning

-   Avisos de categoria: 
    -   Missing_join_predicate

-   Execução da categoria:
    -   Rpc_completed
    -   Rpc_starting 
    -   Sql_batch_completed
    -   Sql_batch_starting

-   Bloqueio
    -   Lock_deadlock

-   Session
    -   Existing_connection
    -   Logon
    -   Logout

## <a name="identify-and-resolve-common-blocking-scenarios"></a>Identificar e resolver cenários de bloqueio comuns

Ao examinar as informações anteriores, você pode determinar a causa da maioria dos problemas de bloqueio. O restante deste artigo é uma discussão de como usar essas informações para identificar e resolver alguns cenários comuns de bloqueio. Esta discussão pressupõe que você usou os scripts de bloqueio (referenciados anteriormente) para capturar informações sobre os SPIDs de bloqueio e ter capturado a atividade do aplicativo usando uma sessão XEvent.

## <a name="analyze-blocking-data"></a>Analisar dados de bloqueio 

* Examine a saída das DMVs sys.dm_exec_requests e sys.dm_exec_sessions para determinar os cabeçotes das cadeias de bloqueio, usando blocking_these e session_id. Isso identificará de forma mais clara quais solicitações são bloqueadas e quais estão bloqueando. Examine ainda mais as sessões bloqueadas e bloqueadas. Há uma raiz ou comum para a cadeia de bloqueio? Eles provavelmente compartilham uma tabela comum, e uma ou mais sessões envolvidas em uma cadeia de bloqueio está executando uma operação de gravação. 

* Examine a saída das DMVs sys.dm_exec_requests e sys.dm_exec_sessions para obter informações sobre os SPIDs no início da cadeia de bloqueio. Procure os seguintes campos: 

    -    `sys.dm_exec_requests.status`  
    Esta coluna mostra o status de uma solicitação específica. Normalmente, um status de suspensão indica que o SPID concluiu a execução e está aguardando o aplicativo enviar outra consulta ou lote. Um status executável ou em execução indica que o SPID está processando uma consulta no momento. A tabela a seguir fornece breves explicações dos vários valores de status.

    | Status | Significado |
    |:-|:-|
    | Tela de fundo | O SPID está executando uma tarefa em segundo plano, como detecção de deadlock, gravador de log ou ponto de verificação. |
    | Hibernando | O SPID não está em execução no momento. Isso geralmente indica que o SPID está aguardando um comando do aplicativo. |
    | Executando | O SPID está sendo executado no momento em um Agendador. |
    | Executável | O SPID está na fila executável de um Agendador e aguardando para obter o tempo do Agendador. |
    | Suspenso | O SPID está aguardando um recurso, como um bloqueio ou uma trava. | 
                       
    -    `sys.dm_exec_sessions.open_transaction_count`  
    Este campo informa o número de transações abertas nesta sessão. Se esse valor for maior que 0, o SPID estará dentro de uma transação aberta e poderá estar mantendo os bloqueios adquiridos por qualquer instrução dentro da transação.

    -    `sys.dm_exec_requests.open_transaction_count`  
    Da mesma forma, esse campo informa o número de transações abertas nesta solicitação. Se esse valor for maior que 0, o SPID estará dentro de uma transação aberta e poderá estar mantendo os bloqueios adquiridos por qualquer instrução dentro da transação.

    -   `sys.dm_exec_requests.wait_type`, `wait_time`, e `last_wait_type`  
    Se o  `sys.dm_exec_requests.wait_type`   for nulo, a solicitação não está aguardando nada e o  `last_wait_type`   valor indica o último  `wait_type`   que a solicitação encontrou. Para obter mais informações sobre  `sys.dm_os_wait_stats` o e uma descrição dos tipos de espera mais comuns, consulte [Sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql). O  `wait_time`   valor pode ser usado para determinar se a solicitação está progredindo. Quando uma consulta na tabela sys.dm_exec_requests retorna um valor na  `wait_time`   coluna menor que o  `wait_time`   valor de uma consulta anterior de sys.dm_exec_requests, isso indica que o bloqueio anterior foi adquirido e liberado e agora está aguardando um novo bloqueio (supondo que seja diferente de zero `wait_time` ). Isso pode ser verificado por meio da comparação `wait_resource`   entre sys.dm_exec_requests saída, que exibe o recurso para o qual a solicitação está aguardando.

    -   `sys.dm_exec_requests.wait_resource` Esse campo indica o recurso em que uma solicitação bloqueada está aguardando. A tabela a seguir lista os  `wait_resource`   formatos comuns e seus significados:

    | Recurso | Formato | Exemplo | Explicação | 
    |:-|:-|:-|:-|
    | Tabela | DatabaseID: ObjectID: IndexID | GUIA: 5:261575970:1 | Nesse caso, a ID de banco de dados 5 é o banco de dados de exemplo pubs e a ID de objeto 261575970 é a tabela de títulos e 1 é o índice clusterizado. |
    | ? | DatabaseID: arquivo de dados: PageId | PÁGINA: 5:1:104 | Nesse caso, a ID de banco de dados 5 é pubs, a ID de arquivo 1 é o arquivo primário e a página 104 é uma página que pertence à tabela de títulos. Para identificar a object_id à qual a página pertence, use a função de gerenciamento dinâmico [Sys.dm_db_page_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql), passando o DatabaseID, o FileID, o PageId do `wait_resource` . | 
    | Chave | DatabaseID: Hobt_id (valor de hash para chave de índice) | CHAVE: 5:72057594044284928 (3300a4f361aa) | Nesse caso, a ID de banco de dados 5 é pubs, Hobt_ID 72057594044284928 corresponde a index_id 2 para object_id 261575970 (tabela de títulos). Use a exibição de catálogo sys. partitions para associar o hobt_id a um index_id e object_id específicos. Não é possível refazer o hash do hash de chave de índice para um valor de chave específico. |
    | Linha | DatabaseID: arquivo de dados: PageId: slot (linha) | RID: 5:1:104:3 | Nesse caso, a ID de banco de dados 5 é pubs, a ID do arquivo 1 é o arquivo principal, a página 104 é uma página que pertence à tabela de títulos e o slot 3 indica a posição da linha na página. |
    | Compilar  | DatabaseID: arquivo de dados: PageId: slot (linha) | RID: 5:1:104:3 | Nesse caso, a ID de banco de dados 5 é pubs, a ID do arquivo 1 é o arquivo principal, a página 104 é uma página que pertence à tabela de títulos e o slot 3 indica a posição da linha na página. |

    -    `sys.dm_tran_active_transactions` A DMV [Sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql) contém dados sobre transações abertas que podem ser unidas a outras DMVs para obter uma imagem completa das transações que estão aguardando confirmação ou reversão. Use a consulta a seguir para retornar informações sobre transações abertas, Unidas a outras DMVs, incluindo [Sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql). Considere o estado atual de uma transação, `transaction_begin_time` e outros dados de situação para avaliar se ela pode ser uma fonte de bloqueio.

    ```sql
    SELECT tst.session_id, [database_name] = db_name(s.database_id)
    , tat.transaction_begin_time
    , transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime()) 
    , transaction_type = CASE tat.transaction_type  WHEN 1 THEN 'Read/write transaction'
                                                    WHEN 2 THEN 'Read-only transaction'
                                                    WHEN 3 THEN 'System transaction'
                                                    WHEN 4 THEN 'Distributed transaction' END
    , input_buffer = ib.event_info, tat.transaction_uow     
    , transaction_state  = CASE tat.transaction_state    
                WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                WHEN 1 THEN 'The transaction has been initialized but has not started.'
                WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                WHEN 4 THEN 'The commit process has been initiated on the distributed transaction.'
                WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                WHEN 6 THEN 'The transaction has been committed.'
                WHEN 7 THEN 'The transaction is being rolled back.'
                WHEN 8 THEN 'The transaction has been rolled back.' END 
    , transaction_name = tat.name, request_status = r.status
    , azure_dtc_state = CASE tat.dtc_state 
                        WHEN 1 THEN 'ACTIVE'
                        WHEN 2 THEN 'PREPARED'
                        WHEN 3 THEN 'COMMITTED'
                        WHEN 4 THEN 'ABORTED'
                        WHEN 5 THEN 'RECOVERED' END
    , tst.is_user_transaction, tst.is_local
    , session_open_transaction_count = tst.open_transaction_count  
    , s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
    FROM sys.dm_tran_active_transactions tat 
    INNER JOIN sys.dm_tran_session_transactions tst  on tat.transaction_id = tst.transaction_id
    INNER JOIN Sys.dm_exec_sessions s on s.session_id = tst.session_id 
    LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
    CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib;
    ```

    -   Outras colunas

        As colunas restantes em [Sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) e [Sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) também podem fornecer informações sobre a raiz de um problema. Sua utilidade varia dependendo das circunstâncias do problema. Por exemplo, você pode determinar se o problema ocorre apenas de determinados clientes (nome de host), em determinadas bibliotecas de rede (net_library), quando o último lote enviado por um SPID estava `last_request_start_time` em sys.dm_exec_sessions, por quanto tempo uma solicitação esteve sendo executada usando `start_time` em sys.dm_exec_requests e assim por diante.


## <a name="common-blocking-scenarios"></a>Cenários comuns de bloqueio

A tabela a seguir mapeia sintomas comuns a suas causas prováveis.  

As `wait_type` `open_transaction_count` colunas, e `status` referem-se às informações retornadas por [Sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql), outras colunas podem ser retornadas por [Sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql). O "resolve?" coluna indica se o bloqueio será ou não resolvido por conta própria ou se a sessão deve ser eliminada por meio do `KILL` comando. Para obter mais informações, consulte [Kill (Transact-SQL)](/sql/t-sql/language-elements/kill-transact-sql).

| Cenário | Colunas waittype | Open_Tran | Status | Resolve? | Outros sintomas |  
|:-|:-|:-|:-|:-|:-|--|
| 1 | NOT NULL | >= 0 | executável | Sim, quando a consulta é concluída. | Em sys.dm_exec_sessions, as colunas **leituras**, **cpu_time** e/ou **memory_usage** aumentarão ao longo do tempo. A duração da consulta será alta quando for concluída. |
| 2 | NULO | \>0 | dormindo | Não, mas SPID pode ser eliminado. | Um sinal de atenção pode ser visto na sessão de evento estendido para esse SPID, indicando que um tempo limite de consulta ou cancelamento ocorreu. |
| 3 | NULO | \>= 0 | executável | Não. Não será resolvido até que o cliente busque todas as linhas ou feche a conexão. O SPID pode ser eliminado, mas pode levar até 30 segundos. | Se open_transaction_count = 0, e o SPID mantiver bloqueios enquanto o nível de isolamento da transação for padrão (COMMMITTED de leitura), essa é uma causa provável. |  
| 4 | Varia | \>= 0 | executável | Não. Não será resolvido até que o cliente cancele consultas ou feche as conexões. Os SPIDs podem ser eliminados, mas podem levar até 30 segundos. | A coluna **hostname** em sys.dm_exec_sessions para o SPID no início de uma cadeia de bloqueio será a mesma que uma das SPIDs que está bloqueando. |  
| 5 | NULO | \>0 | reversão | Sim. | Um sinal de atenção pode ser visto na sessão de eventos estendidos para esse SPID, indicando que o tempo limite da consulta ou cancelamento ocorreu ou simplesmente uma instrução ROLLBACK foi emitida. |  
| 6 | NULO | \>0 | dormindo | Eventualmente. Quando o Windows NT determina que a sessão não está mais ativa, a conexão do banco de dados SQL do Azure será interrompida. | O `last_request_start_time` valor em sys.dm_exec_sessions é muito anterior à hora atual. |

## <a name="detailed-blocking-scenarios"></a>Cenários de bloqueio detalhados

1.  Bloqueio causado por uma consulta normalmente em execução com um tempo de execução longo

    **Resolução**: a solução para esse tipo de problema de bloqueio é procurar maneiras de otimizar a consulta. Na verdade, essa classe de problema de bloqueio pode ser apenas um problema de desempenho e exigir que você a busque como tal. Para obter informações sobre como solucionar problemas de uma consulta de execução lenta específica, consulte [como solucionar problemas de consultas de execução lenta no SQL Server](/troubleshoot/sql/performance/troubleshoot-slow-running-queries). Para obter mais informações, confira [Monitorar e ajustar o desempenho](/sql/relational-databases/performance/monitor-and-tune-for-performance). 

    Os relatórios da [repositório de consultas](/sql/relational-databases/performance/best-practice-with-the-query-store) no SSMS também são uma ferramenta valiosa e altamente recomendada para identificar as consultas mais dispendiosas, planos de execução de qualidade inferior. Examine também a seção de [desempenho inteligente](intelligent-insights-overview.md) do portal do Azure para o banco de dados SQL do Azure, incluindo [análise de desempenho de consultas](query-performance-insight-use.md).

    Se você tiver uma consulta de execução longa que está bloqueando outros usuários e não puder ser otimizada, considere movê-lo de um ambiente OLTP para um sistema de relatórios dedicado, uma [réplica somente leitura síncrona do banco de dados](read-scale-out.md).

1.  Bloqueio causado por um SPID em suspensão que tem uma transação não confirmada

    Esse tipo de bloqueio geralmente pode ser identificado por um SPID que está em suspensão ou aguardando um comando, mas cujo nível de aninhamento `@@TRANCOUNT` de transação (, `open_transaction_count` de sys.dm_exec_requests) é maior que zero. Isso pode ocorrer se o aplicativo apresentar um tempo limite de consulta ou emitir um cancelamento sem emitir também o número necessário de instruções de reversão e/ou confirmação. Quando um SPID recebe um tempo limite de consulta ou um cancelamento, ele encerra a consulta e o lote atuais, mas não reverte ou confirma a transação automaticamente. O aplicativo é responsável por isso, pois o banco de dados SQL do Azure não pode supor que uma transação inteira deve ser revertida porque uma única consulta está sendo cancelada. O tempo limite da consulta ou o cancelamento aparecerá como um evento de sinal de atenção para o SPID na sessão de evento estendido.

    Para demonstrar uma transação explícita não confirmada, emita a seguinte consulta:

    ```sql
    CREATE TABLE #test (col1 INT);
    INSERT INTO #test SELECT 1;
    BEGIN TRAN
    UPDATE #test SET col1 = 2 where col1 = 1;
    ```

    Em seguida, execute essa consulta na mesma janela:
    ```sql
    SELECT @@TRANCOUNT;
    ROLLBACK TRAN
    DROP TABLE #test;
    ```

    A saída da segunda consulta indica que o nível de aninhamento de transação é um. Todos os bloqueios adquiridos na transação ainda serão mantidos até que a transação seja confirmada ou revertida. Se os aplicativos abrirem e confirmarem explicitamente as transações, uma comunicação ou outro erro poderá deixar a sessão e sua transação em um estado aberto. 

    Use o script anteriormente neste artigo com base em sys.dm_tran_active_transactions para identificar transações não confirmadas no momento em toda a instância.

    **Resoluções**:

     -   Além disso, essa classe de problema de bloqueio também pode ser um problema de desempenho e exigir que você o busque como tal. Se o tempo de execução da consulta puder ser diminuído, o tempo limite da consulta ou cancelamento não ocorrerá. É importante que o aplicativo seja capaz de lidar com os cenários de tempo limite ou cancelamento caso eles surjam, mas você também pode se beneficiar com a análise do desempenho da consulta. 
     
     -    Os aplicativos devem gerenciar corretamente os níveis de aninhamento de transações ou podem causar um problema de bloqueio após o cancelamento da consulta dessa maneira. Considere o seguinte:  

            *    No manipulador de erros do aplicativo cliente, execute `IF @@TRANCOUNT > 0 ROLLBACK TRAN` após qualquer erro, mesmo que o aplicativo cliente não acredite que uma transação está aberta. A verificação de transações abertas é necessária, pois um procedimento armazenado chamado durante o lote pode ter iniciado uma transação sem o conhecimento do aplicativo cliente. Determinadas condições, como cancelar a consulta, impedem que o procedimento seja executado após a instrução atual, portanto, mesmo que o procedimento tenha lógica para verificar `IF @@ERROR <> 0` e anular a transação, esse código de reversão não será executado nesses casos.  
            *    Se o pool de conexões estiver sendo usado em um aplicativo que abre a conexão e executa um pequeno número de consultas antes de liberar a conexão de volta ao pool, como um aplicativo baseado na Web, desabilitar temporariamente o pool de conexões pode ajudar a aliviar o problema até que o aplicativo cliente seja modificado para manipular os erros adequadamente. Ao desabilitar o pool de conexões, a liberação da conexão causará uma desconexão física da conexão do banco de dados SQL do Azure, fazendo com que o servidor reverta todas as transações abertas.  
            *    Use `SET XACT_ABORT ON` para a conexão ou em quaisquer procedimentos armazenados que iniciem transações e não estão limpando após um erro. No caso de um erro de tempo de execução, essa configuração anulará todas as transações abertas e retornará o controle para o cliente. Para obter mais informações, consulte [SET XACT_ABORT (Transact-SQL)](/sql/t-sql/statements/set-xact-abort-transact-sql).

    > [!NOTE]
    > A conexão não é redefinida até que seja reutilizada do pool de conexões, portanto, é possível que um usuário possa abrir uma transação e, em seguida, liberar a conexão para o pool de conexões, mas ela pode não ser reutilizada por vários segundos, durante o qual a transação permaneceria aberta. Se a conexão não for reutilizada, a transação será anulada quando a conexão atingir o tempo limite e for removida do pool de conexões. Portanto, é ideal que o aplicativo cliente anule as transações em seu manipulador de erros ou use `SET XACT_ABORT ON` para evitar esse atraso potencial.

    > [!CAUTION]
    > `SET XACT_ABORT ON`A seguir, instruções T-SQL seguindo uma instrução que causa um erro não serão executadas. Isso pode afetar o fluxo pretendido de código existente.

1.  Bloqueio causado por um SPID cujo aplicativo cliente correspondente não buscou todas as linhas de resultado para conclusão

    Depois de enviar uma consulta para o servidor, todos os aplicativos devem buscar imediatamente todas as linhas de resultado para conclusão. Se um aplicativo não buscar todas as linhas de resultado, os bloqueios poderão ser deixados nas tabelas, bloqueando outros usuários. Se você estiver usando um aplicativo que envia de forma transparente instruções SQL para o servidor, o aplicativo deve buscar todas as linhas de resultado. Se não for (e se não puder ser configurado para fazer isso), talvez você não consiga resolver o problema de bloqueio. Para evitar o problema, você pode restringir o mau desempenho de aplicativos a um relatório ou a um banco de dados de suporte a decisões, separado do banco de dados OLTP principal.
    
    > [!NOTE]
    > Consulte as [diretrizes para lógica de repetição](./troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) para aplicativos que se conectam ao banco de dados SQL do Azure. 
    
    **Resolução**: o aplicativo deve ser reescrito para buscar todas as linhas do resultado para conclusão. Isso não regra o uso de [offset e FETCH na cláusula order by](/sql/t-sql/queries/select-order-by-clause-transact-sql#using-offset-and-fetch-to-limit-the-rows-returned) de uma consulta para executar a paginação do servidor.

1.  Bloqueio causado por uma sessão em um estado de reversão

    Uma consulta de modificação de dados que é encerrada ou cancelada fora de uma transação definida pelo usuário será revertida. Isso também pode ocorrer como um efeito colateral da desconexão da sessão de rede do cliente ou quando uma solicitação é selecionada como a vítima do deadlock. Geralmente, isso pode ser identificado observando a saída de sys.dm_exec_requests, que pode indicar o **comando** Rollback, e a **coluna percent_complete** pode mostrar o progresso. 

    Graças ao [recurso de recuperação de banco de dados acelerado](../accelerated-database-recovery.md) introduzido em 2019, as reversões longas devem ser raras.
    
    **Resolução**: Aguarde até que o SPID termine de reverter as alterações feitas. 

    Para evitar essa situação, não execute operações de gravação em lote grandes ou operações de manutenção ou de criação de índice durante horas ocupadas em sistemas OLTP. Se possível, execute essas operações durante períodos de baixa atividade.

1.  Bloqueio causado por uma conexão órfã

    Se o aplicativo cliente interceptar erros ou a estação de trabalho cliente for reiniciada, a sessão de rede para o servidor poderá não ser cancelada imediatamente em algumas condições. Da perspectiva do banco de dados SQL do Azure, o cliente ainda parece estar presente e todos os bloqueios adquiridos ainda podem ser retidos. Para obter mais informações, consulte [como solucionar problemas de conexões órfãs no SQL Server](/sql/t-sql/language-elements/kill-transact-sql#remarks). 

    **Resolução**: se o aplicativo cliente tiver se desconectado sem a limpeza adequada de seus recursos, você poderá encerrar o SPID usando o `KILL` comando. O `KILL` comando usa o valor SPID como entrada. Por exemplo, para eliminar SPID 99, emita o seguinte comando:

    ```sql
    KILL 99
    ```

## <a name="see-also"></a>Veja também

* [Monitoramento e ajuste de desempenho no Banco de Dados SQL do Azure e da Instância Gerenciada de SQL do Azure](./monitor-tune-overview.md)
* [Monitorando o desempenho usando o Repositório de Consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [Guia de Controle de Versão de Linha e Bloqueio de Transações](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [SET TRANSACTION ISOLATION LEVEL](/sql/t-sql/statements/set-transaction-isolation-level-transact-sql)
* [Início Rápido: Eventos estendidos no SQL Server](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
* [Intelligent Insights usando IA para monitorar e solucionar problemas de desempenho de banco de dados](intelligent-insights-overview.md)

## <a name="learn-more"></a>Saiba mais

* [Banco de dados SQL do Azure: melhorando o ajuste de desempenho com ajuste automático](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Improving-Performance-Tuning-with-Automatic-Tuning)
* [Melhorar o desempenho do banco de dados SQL do Azure com ajuste automático](https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning)
* [Fornecer desempenho consistente com o SQL Azure](/learn/modules/azure-sql-performance/)
* [Solucionar problemas de conectividade e outros erros com o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada](troubleshoot-common-errors-issues.md)
* [Tratamento de falhas transitórias](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)
