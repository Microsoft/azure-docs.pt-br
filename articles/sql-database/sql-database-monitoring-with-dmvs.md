---
title: "Monitoramento de Banco de Dados SQL do Azure usando exibições de gerenciamento dinâmico | Microsoft Docs"
description: "Saiba como detectar e diagnosticar problemas de desempenho comuns usando exibições de gerenciamento dinâmico para monitorar o Banco de Dados SQL do Microsoft Azure."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: d08f505f-3c62-47d4-bab7-35c9a834b79b
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: d9b007d29e06e672db71b4a8415673f258c3fd89
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Monitoramento de Banco de Dados SQL usando exibições de gerenciamento dinâmico
O Banco de Dados SQL do Microsoft Azure permite um subconjunto de modos de exibição de gerenciamento dinâmico para diagnosticar problemas de desempenho, que podem ser causados por consultas bloqueadas ou demoradas, afunilamentos de recursos, planos de consulta ruins e assim por diante. Este tópico fornece informações sobre como detectar problemas de desempenho comuns usando exibições de gerenciamento dinâmico.

O Banco de Dados SQL oferece suporte parcial para três categorias de exibições de gerenciamento dinâmico:

* Exibições de gerenciamento dinâmico relacionadas ao banco de dados.
* Exibições de gerenciamento dinâmico relacionadas à execução.
* Exibições de gerenciamento dinâmico relacionadas à transação.

Para obter informações detalhadas sobre exibições de gerenciamento dinâmico, consulte [Funções e exibições (Transact-SQL) de gerenciamento dinâmico](https://msdn.microsoft.com/library/ms188754.aspx) nos Manuais Online do SQL Server.

## <a name="permissions"></a>Permissões
No Banco de Dados SQL, consultar uma visualização de gerenciamento dinâmico requer permissões **VIEW DATABASE STATE** . A permissão **VIEW DATABASE STATE** retorna informações sobre todos os objetos no banco de dados atual.
Para conceder a permissão **VIEW DATABASE STATE** a um usuário específico do banco de dados, execute a seguinte consulta:

```GRANT VIEW DATABASE STATE TO database_user; ```

Em uma instância do SQL Server local, as exibições de gerenciamento dinâmico retornam informações de estado do servidor. Em um Banco de Dados SQL, elas retornam informações relacionadas apenas ao seu banco de dados lógico atual.

## <a name="calculating-database-size"></a>Calculando o tamanho do banco de dados
A seguinte consulta retorna o tamanho do seu banco de dados (em megabytes):

```
-- Calculates the size of the database.
SELECT SUM(reserved_page_count)*8.0/1024
FROM sys.dm_db_partition_stats;
GO
```

A consulta a seguir retorna o tamanho do dos objetos individuais (em megabytes) no seu banco de dados:

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Monitoramento de conexões
É possível usar a exibição [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) para recuperar informações sobre as conexões estabelecidas com um servidor do Banco de dados SQL do Azure específico e os detalhes de cada conexão. Além disso, a exibição [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) é útil ao recuperar informações sobre todas as conexões de usuário e tarefas internas ativas.
A consulta a seguir recupera as informações sobre a conexão atual:

```
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
> 
> 

## <a name="monitoring-query-performance"></a>Monitoramento de desempenho da consulta
Consultas de execução lenta ou longa podem consumir recursos significativos do sistema. Esta seção demonstra como usar exibições de gerenciamento dinâmico para detectar alguns problemas comuns de desempenho de consulta. Uma referência mais antiga, mas ainda útil para solução de problemas, é o artigo [Solucionando problemas de desempenho no SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) no Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Localizando as principais consultas N
O exemplo a seguir retorna informações sobre as cinco principais consultas classificadas pelo tempo médio de CPU. Este exemplo agrega as consultas de acordo com sua hash de consulta, para que as consultas logicamente equivalentes sejam agrupadas em função de seu consumo de recursos cumulativos.

```
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
Consultas lentas ou demoradas podem contribuir para consumo excessivo de recursos e ser a consequência de consultas bloqueadas. A causa do bloqueio pode ser projeto inadequado de aplicativos, planos de consulta incorretos, a falta de índices úteis e assim por diante. Você pode usar o modo de exibição sys.dm_tran_locks para obter informações sobre a atividade de bloqueio atual em seu Banco de dados SQL do Azure. Para ver um exemplo de código, veja [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) nos Manuais Online do SQL Server.

### <a name="monitoring-query-plans"></a>Monitoramento de planos de consulta
Um plano de consulta ineficiente também pode aumentar o consumo de CPU. O exemplo a seguir usa a exibição [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) para determinar qual consulta usa a CPU mais cumulativa.

```
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

## <a name="see-also"></a>Consulte também
[Introdução ao Banco de Dados SQL](sql-database-technical-overview.md)

