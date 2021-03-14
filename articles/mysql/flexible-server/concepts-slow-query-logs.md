---
title: Logs de consulta lentos-banco de dados do Azure para MySQL-servidor flexível
description: Descreve os logs de consulta lento disponíveis no banco de dados do Azure para MySQL servidor flexível.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: d311ea3158e1f9d53c51fe239103039849597d11
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579189"
---
# <a name="slow-query-logs-in-azure-database-for-mysql-flexible-server-preview"></a>Logs de consulta lentos no banco de dados do Azure para MySQL servidor flexível (versão prévia)

> [!IMPORTANT] 
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

No banco de dados do Azure para servidor flexível do MySQL, o log de consulta lenta está disponível para os usuários configurarem e acessarem. Logs de consulta lentos são desabilitados por padrão e podem ser habilitados para auxiliar na identificação de gargalos de desempenho durante a solução de problemas.

Para obter mais informações sobre o log de consultas lentas do MySQL, consulte a [seção log de consultas lentas](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) na documentação do mecanismo MySQL.

## <a name="configure-slow-query-logging"></a>Configurar o log de consultas lentas 
Por padrão, o log de consultas lentas está desabilitado. Para habilitar os logs, defina o `slow_query_log` parâmetro de servidor como *ativado*. Isso pode ser configurado usando o portal do Azure ou CLI do Azure <!-- add link to server parameter-->. 

Outros parâmetros que você pode ajustar para controlar o comportamento de log de consulta lento incluem:

- **long_query_time**: Registre uma consulta se demorar mais do que `long_query_time` (em segundos) para ser concluída. O padrão é 10 segundos.
- **log_slow_admin_statements**: determina se instruções administrativas (ex. `ALTER_TABLE`, `ANALYZE_TABLE` ) são registrados.
- **log_queries_not_using_indexes**: determina se as consultas que não usam índices são registradas.
- **log_throttle_queries_not_using_indexes**: limita o número de consultas não indexadas que podem ser gravadas no log de consultas lentas. Esse parâmetro entra em vigor quando `log_queries_not_using_indexes` é definido como *on*

> [!IMPORTANT]
> Se as tabelas não estiverem indexadas, definir `log_queries_not_using_indexes` os `log_throttle_queries_not_using_indexes` parâmetros e como **on** pode afetar o desempenho do MySQL, pois todas as consultas em execução nessas tabelas não indexadas serão gravadas no log de consultas lentas.

Consulte a [documentação de log de consulta lenta](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) MySQL para descrições completas dos parâmetros de log de consultas lentas.

## <a name="access-slow-query-logs"></a>Acessar logs de consulta lentos

Logs de consulta lentos são integrados com Azure Monitor configurações de diagnóstico. Depois de habilitar os logs de consulta lentos no servidor flexível do MySQL, você pode emiti-los para Azure Monitor logs, hubs de eventos ou armazenamento do Azure. Para saber mais sobre as configurações de diagnóstico, consulte a [documentação dos logs de diagnóstico](../../azure-monitor/essentials/platform-logs-overview.md). Para saber mais sobre como habilitar as configurações de diagnóstico no portal do Azure, consulte o [artigo portal de log de consultas lentas](how-to-configure-slow-query-logs-portal.md#set-up-diagnostics).

A tabela a seguir descreve a saída do log de consultas lentas. Dependendo do método de saída, os campos incluídos e a ordem em que aparecem podem variar.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua ID de locatário |
| `SourceSystem` | `Azure` |
| `TimeGenerated` HORÁRIO | Carimbo de data/hora quando o log foi gravado, em UTC |
| `Type` | Tipo do log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a assinatura a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos ao qual o servidor pertence |
| `ResourceProvider` | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nome do servidor |
| `start_time_t` HORÁRIO | Horário em que a consulta começou |
| `query_time_s` | Tempo total em segundos que a consulta levou para ser executada |
| `lock_time_s` | Tempo total em segundos em que a consulta foi bloqueada |
| `user_host_s` | Nome de usuário |
| `rows_sent_s` | Número de linhas enviadas |
| `rows_examined_s` | Número de linhas verificadas |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/5.7/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Inserir ID |
| `sql_text_s` | Consulta completa |
| `server_id_s` | A ID do servidor |
| `thread_id_s` | ID do thread |
| `\_ResourceId` | URI de recurso |

> [!Note]
> Para `sql_text_s` , o log será truncado se exceder 2048 caracteres.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analisar logs em logs de Azure Monitor

Depois que os logs de consulta lentos são canalizados para Azure Monitor logs por meio de logs de diagnóstico, você pode executar uma análise adicional de suas consultas lentas. Abaixo estão algumas consultas de exemplo para ajudá-lo a começar. Certifique-se de atualizar o abaixo com o nome do servidor.

- Consultas com mais de 10 segundos em um servidor específico

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Listar as 5 principais consultas mais longas em um servidor específico

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Resumir consultas lentas pelo mínimo, máximo, média e tempo de consulta de desvio padrão em um servidor específico

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Grafar a distribuição de consulta lenta em um servidor específico

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Exibir consultas com mais de 10 segundos em todos os servidores MySQL com logs de diagnóstico habilitados

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [os logs de auditoria](concepts-audit-logs.md)
- Configurar logs de consulta lentos no [portal do Azure](how-to-configure-slow-query-logs-portal.md)
<!-- - [How to configure slow query logs from the Azure CLI](howto-configure-server-logs-in-cli.md). -->