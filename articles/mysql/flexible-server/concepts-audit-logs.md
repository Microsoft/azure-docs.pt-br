---
title: Logs de auditoria-banco de dados do Azure para MySQL-servidor flexível
description: Descreve os logs de auditoria disponíveis no banco de dados do Azure para MySQL servidor flexível.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 1232a0753c988f5a28ebba28f9819aa67ce28603
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718736"
---
# <a name="track-database-activity-with-audit-logs-in-azure-database-for-mysql-flexible-server"></a>Acompanhar atividade do banco de dados com logs de auditoria no banco de dados do Azure para MySQL servidor flexível

> [!IMPORTANT] 
> Atualmente, o Servidor Flexível do Banco de Dados do Azure para MySQL está em versão prévia pública

O banco de dados do Azure para MySQL servidor flexível fornece aos usuários a capacidade de configurar logs de auditoria. Os logs de auditoria podem ser usados para acompanhar a atividade no nível do banco de dados, incluindo eventos de conexão, administração, DDL e DML. Esses tipos de logs são comumente usados para fins de conformidade.

## <a name="configure-audit-logging"></a>Configurar o log de auditoria

>[!IMPORTANT]
> É recomendável registrar apenas os tipos de eventos e os usuários necessários para fins de auditoria para garantir que o desempenho do servidor não seja muito afetado.

Por padrão, os logs de auditoria são desabilitados. Para habilitá-los, defina o `audit_log_enabled` parâmetro de servidor como *ativado*. Isso pode ser configurado usando o portal do Azure ou CLI do Azure <!-- add link to server parameter-->. 

Outros parâmetros que você pode ajustar para controlar o comportamento do log de auditoria incluem:

- `audit_log_events`: controla os eventos a serem registrados. Consulte a tabela abaixo para ver eventos de auditoria específicos.
- `audit_log_include_users`: Usuários do MySQL a serem incluídos para registro em log. O valor padrão para esse parâmetro é vazio, o que incluirá todos os usuários para registro em log. Isso tem prioridade mais alta `audit_log_exclude_users` . O comprimento máximo do parâmetro é de 512 caracteres.
- `audit_log_exclude_users`: Os usuários do MySQL serão excluídos do registro em log. O comprimento máximo do parâmetro é de 512 caracteres.

> [!NOTE]
> `audit_log_include_users` tem prioridade mais alta `audit_log_exclude_users` . Por exemplo, se `audit_log_include_users`  =  `demouser` e `audit_log_exclude_users`  =  `demouser` , o usuário será incluído nos logs de auditoria porque `audit_log_include_users` tem prioridade mais alta.

| **Evento** | **Descrição** |
|---|---|
| `CONNECTION` | -Início da conexão (bem-sucedido ou malsucedido) <br> -Reautenticação do usuário com usuário/senha diferente durante a sessão <br> -Terminação de conexão |
| `DML_SELECT`| Consultas SELECT |
| `DML_NONSELECT` | INSERIR/excluir/atualizar consultas |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Consultas como "remover banco de dados" |
| `DCL` | Consultas como "conceder permissão" |
| `ADMIN` | Consultas como "mostrar STATUS" |
| `GENERAL` | Tudo em DML_SELECT, DML_NONSELECT, DML, DDL, DCL e administrador |
| `TABLE_ACCESS` | -Instruções de leitura de tabela, como selecionar ou inserir em... Não <br> -Instruções DELETE de tabela, como DELETE ou TRUNCATE TABLE <br> -Instruções INSERT de tabela, como INSERT ou REPLACE <br> -Instruções de atualização de tabela, como UPDATE |

## <a name="access-audit-logs"></a>Acesse os logs de auditoria

Os logs de auditoria são integrados às configurações de diagnóstico Azure Monitor. Depois de habilitar os logs de auditoria no servidor flexível do MySQL, você pode emiti-los para Azure Monitor logs, hubs de eventos ou armazenamento do Azure. Para saber mais sobre as configurações de diagnóstico, consulte a [documentação dos logs de diagnóstico](../../azure-monitor/essentials/platform-logs-overview.md). Para saber mais sobre como habilitar as configurações de diagnóstico no portal do Azure, consulte o [artigo portal de log de auditoria](how-to-configure-audit-logs-portal.md#set-up-diagnostics).

As seções a seguir descrevem a saída dos logs de auditoria do MySQL com base no tipo de evento. Dependendo do método de saída, os campos incluídos e a ordem em que aparecem podem variar.

### <a name="connection"></a>Conexão

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua ID de locatário |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de data/hora quando o log foi gravado, em UTC |
| `Type` | Tipo do log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a assinatura a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos ao qual o servidor pertence |
| `ResourceProvider` | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome do servidor |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT`, `CHANGE USER` |
| `connection_id_d` | ID de conexão exclusiva gerada pelo MySQL |
| `host_s` | Em branco |
| `ip_s` | Endereço IP do cliente que se conecta ao MySQL |
| `user_s` | Nome do usuário executando a consulta |
| `db_s` | Nome do banco de dados conectado a |
| `\_ResourceId` | URI de recurso |

### <a name="general"></a>Geral

O esquema a seguir se aplica aos tipos de evento geral, DML_SELECT, DML_NONSELECT, DML, DDL, DCL e administrador.

> [!NOTE]
> Para `sql_text_s` , o log será truncado se exceder 2048 caracteres.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua ID de locatário |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de data/hora quando o log foi gravado, em UTC |
| `Type` | Tipo do log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a assinatura a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos ao qual o servidor pertence |
| `ResourceProvider` | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome do servidor |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR` , `RESULT` (disponível somente para MySQL 5,6) |
| `event_time` | Hora de início da consulta no carimbo de data/hora UTC |
| `error_code_d` | Código de erro se a consulta falhar. `0` significa nenhum erro |
| `thread_id_d` | ID do thread que executou a consulta |
| `host_s` | Em branco |
| `ip_s` | Endereço IP do cliente que se conecta ao MySQL |
| `user_s` | Nome do usuário executando a consulta |
| `sql_text_s` | Texto de consulta completo |
| `\_ResourceId` | URI de recurso |

### <a name="table-access"></a>Acesso à tabela

> [!NOTE]
> Para `sql_text_s` , o log será truncado se exceder 2048 caracteres.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua ID de locatário |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de data/hora quando o log foi gravado, em UTC |
| `Type` | Tipo do log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a assinatura a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos ao qual o servidor pertence |
| `ResourceProvider` | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome do servidor |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE` ou `DELETE` |
| `connection_id_d` | ID de conexão exclusiva gerada pelo MySQL |
| `db_s` | Nome do banco de dados acessado |
| `table_s` | Nome da tabela acessada |
| `sql_text_s` | Texto de consulta completo |
| `\_ResourceId` | URI de recurso |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analisar logs em logs de Azure Monitor

Depois que os logs de auditoria são canalizados para Azure Monitor logs por meio de logs de diagnóstico, você pode executar uma análise adicional dos eventos auditados. Abaixo estão algumas consultas de exemplo para ajudá-lo a começar. Certifique-se de atualizar o abaixo com o nome do servidor.

- Listar eventos gerais em um servidor específico

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- Listar eventos de conexão em um servidor específico

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Resumir eventos auditados em um servidor específico

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- Grafo a distribuição de tipo de evento de auditoria em um servidor específico

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- Listar eventos auditados em todos os servidores MySQL com logs de diagnóstico habilitados para logs de auditoria

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [logs de consulta lentos](concepts-slow-query-logs.md)
- Configurar logs de consulta de auditoria do [portal do Azure](how-to-configure-audit-logs-portal.md)
<!-- - [How to configure audit logs in the Azure portal](howto-configure-audit-logs-portal.md)-->
