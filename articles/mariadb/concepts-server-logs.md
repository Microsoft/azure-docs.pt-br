---
title: Logs de consulta lentos-banco de dados do Azure para MariaDB
description: Descreve os logs disponíveis no Banco de Dados do Azure para MariaDB para os parâmetros disponíveis para habilitar níveis de log diferentes.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: conceptual
ms.date: 11/6/2020
ms.openlocfilehash: 2d3c3c92cc4d0da38f18c7895f4f7f3f3385c653
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664156"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Logs de consulta lentos no banco de dados do Azure para MariaDB
No Banco de Dados do Azure para MariaDB, o log de consultas lentas está disponível para os usuários. No entanto, não há suporte para acesso ao log de transação. O log de consultas lentas pode ser usado para identificar gargalos de desempenho para solução de problemas.

Para obter mais informações sobre o log de consulta lenta, leia a documentação do MariaDB para [log de consulta lenta](https://mariadb.com/kb/en/library/slow-query-log-overview/).

Quando o [repositório de consultas](concepts-query-store.md) estiver habilitado no servidor, você poderá ver as consultas como " `CALL mysql.az_procedure_collect_wait_stats (900, 30);` " registradas em log em seus logs de consulta lentos. Esse comportamento é esperado, pois o recurso de Repositório de Consultas coleta estatísticas sobre suas consultas. 

## <a name="configure-slow-query-logging"></a>Configurar o log de consultas lentas
Por padrão, o log de consultas lentas está desabilitado. Para habilitá-lo, defina `slow_query_log` como ativado. Isso pode ser habilitado usando o portal do Azure ou CLI do Azure. 

Outros parâmetros que você pode ajustar incluem:

- **long_query_time**: se uma consulta demorar mais do que long_query_time (em segundos), essa consulta será registrada. O padrão é 10 segundos.
- **log_slow_admin_statements**: se ON inclui instruções administrativas como ALTER_TABLE e ANALYZE_TABLE nas instruções gravadas para o slow_query_log.
- **log_queries_not_using_indexes**: determina se as consultas que não usam índices são ou não registradas para o slow_query_log
- **log_throttle_queries_not_using_indexes**: este parâmetro limita o número de consultas que não são de índice que podem ser gravadas no log de consultas lentas. Esse parâmetro tem efeito quando log_queries_not_using_indexes está definido como ON.
- **log_output**: se "File", permite que o log de consultas lentas seja gravado no armazenamento do servidor local e em Azure monitor logs de diagnóstico. Se for "None", o log de consultas lentas só será gravado em logs de diagnóstico do Azure Monitor. 

> [!IMPORTANT]
> Se as tabelas não estiverem indexadas, definir `log_queries_not_using_indexes` os `log_throttle_queries_not_using_indexes` parâmetros e como on pode afetar o desempenho do MariaDB, pois todas as consultas em execução nessas tabelas não indexadas serão gravadas no log de consultas lentas.<br><br>
> Se você planeja registrar em log consultas lentas por um longo período de tempo, é recomendável definir `log_output` como "nenhum". Se definido como "File", esses logs são gravados no armazenamento do servidor local e podem afetar o desempenho do MariaDB. 

Consulte a [documentação de log de consulta lenta](https://mariadb.com/kb/en/library/slow-query-log-overview/) do MariaDB para ver descrições completas dos parâmetros de log de consulta lenta.

## <a name="access-slow-query-logs"></a>Acessar logs de consulta lentos
Há duas opções para acessar logs de consulta lentos no banco de dados do Azure para MariaDB: armazenamento de servidor local ou logs de diagnóstico de Azure Monitor. Isso é definido usando o `log_output` parâmetro.

Para o armazenamento do servidor local, você pode listar e baixar logs de consulta lentos usando o portal do Azure ou o CLI do Azure. Na portal do Azure, navegue até o servidor na portal do Azure. Sob o título **Monitoramento**, selecione a página **Logs do Servidor**. Para obter mais informações sobre a CLI do Azure, consulte [Configurar e acessar logs de servidor usando a CLI do Azure](howto-configure-server-logs-cli.md). 

Azure Monitor logs de diagnóstico permite canalizar logs de consulta lentos para Azure Monitor logs (Log Analytics), armazenamento do Azure ou hubs de eventos. Consulte [abaixo](concepts-server-logs.md#diagnostic-logs) para obter mais informações.

## <a name="local-server-storage-log-retention"></a>Retenção de log de armazenamento do servidor local
Ao fazer logon no armazenamento local do servidor, os logs ficam disponíveis por até sete dias a partir de sua criação. Se o tamanho total dos logs disponíveis exceder 7 GB, os arquivos mais antigos serão excluídos até que haja espaço disponível.

Logs são reciclados a cada 24 horas ou 7 GB, o que ocorrer primeiro.

> [!Note]
> A retenção de log acima não se aplica a logs que são canalizados usando Azure Monitor logs de diagnóstico. Você pode alterar o período de retenção dos coletores de dados que estão sendo emitidos para (ex. Armazenamento do Azure).

## <a name="diagnostic-logs"></a>Logs de diagnóstico
O banco de dados do Azure para MariaDB é integrado a logs de diagnóstico Azure Monitor. Depois de habilitar os logs de consulta lentos em seu servidor MariaDB, você pode optar por que eles sejam emitidos para Azure Monitor logs, hubs de eventos ou armazenamento do Azure. Para saber mais sobre como ativar logs de diagnóstico, consulte o como parte da [documentação registros de diagnóstico](../azure-monitor/platform/platform-logs-overview.md).

A tabela a seguir descreve o que está em cada log. Dependendo do método de saída, os campos incluídos e a ordem em que aparecem podem variar.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua ID de locatário |
| `SourceSystem` | `Azure` |
| `TimeGenerated` HORÁRIO | Carimbo de data/hora quando o log foi gravado, em UTC |
| `Type` | Tipo do log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a assinatura a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos ao qual o servidor pertence |
| `ResourceProvider` | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nome do servidor |
| `start_time_t` HORÁRIO | Horário em que a consulta começou |
| `query_time_s` | Tempo total que a consulta levou para executar |
| `lock_time_s` | Tempo total em que a consulta foi bloqueada |
| `user_host_s` | Nome de Usuário |
| `rows_sent_s` | Número de linhas enviadas |
| `rows_examined_s` | Número de linhas verificadas |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Inserir ID |
| `sql_text_s` | Consulta completa |
| `server_id_s` | ID de servidor |
| `thread_id_s` | ID do thread |
| `\_ResourceId` | URI de recurso |

> [!Note]
> Para `sql_text` , o log será truncado se exceder 2048 caracteres.

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

- Exibir consultas com mais de 10 segundos em todos os servidores MariaDB com logs de diagnóstico habilitados

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Próximas etapas
- [Como configurar logs de consulta lentos no portal do Azure](howto-configure-server-logs-portal.md)
- [Como configurar logs de consulta lentos no CLI do Azure](howto-configure-server-logs-cli.md)
