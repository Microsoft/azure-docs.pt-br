---
title: Logs de auditoria-banco de dados do Azure para MariaDB
description: Descreve os logs de auditoria disponíveis no banco de dados do Azure para MariaDB e os parâmetros disponíveis para habilitar os níveis de log.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: conceptual
ms.date: 6/24/2020
ms.openlocfilehash: 3032cb8f5ce399822de65c867d1f32da254c9948
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663750"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Logs de auditoria no banco de dados do Azure para MariaDB

No banco de dados do Azure para MariaDB, o log de auditoria está disponível para os usuários. O log de auditoria pode ser usado para acompanhar a atividade no nível do banco de dados e é normalmente usado para conformidade.

## <a name="configure-audit-logging"></a>Configurar o log de auditoria

>[!IMPORTANT]
> É recomendável registrar apenas os tipos de eventos e os usuários necessários para fins de auditoria para garantir que o desempenho do servidor não seja muito afetado.

Por padrão, o log de auditoria é desabilitado. Para habilitá-lo, defina `audit_log_enabled` como ativado.

Outros parâmetros que você pode ajustar incluem:

- `audit_log_events`: controla os eventos a serem registrados. Consulte a tabela abaixo para ver eventos de auditoria específicos.
- `audit_log_include_users`: MariaDB usuários a serem incluídos para registro em log. O valor padrão para esse parâmetro é vazio, o que incluirá todos os usuários para registro em log. Isso tem prioridade mais alta `audit_log_exclude_users` . O comprimento máximo do parâmetro é de 512 caracteres.
- `audit_log_exclude_users`: MariaDB os usuários a serem excluídos do registro em log. Permite no máximo quatro usuários. O comprimento máximo do parâmetro é de 256 caracteres.

> [!Note]
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

## <a name="access-audit-logs"></a>Acesse os logs de auditoria

Os logs de auditoria são integrados aos logs de diagnóstico do Azure Monitor. Depois de habilitar os logs de auditoria no servidor MariaDB, você pode emiti-los para logs do Azure Monitor, hubs de eventos ou armazenamento do Microsoft Azure. Para saber mais sobre como habilitar os logs de diagnóstico no portal do Azure, consulte o [artigo portal de log de auditoria](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Esquemas de logs de diagnóstico

As seções a seguir descrevem o que é resultado de logs de auditoria do MariaDB com base no tipo de evento. Dependendo do método de saída, os campos incluídos e a ordem em que aparecem podem variar.

### <a name="connection"></a>Conexão

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua ID de locatário |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de data/hora quando o log foi gravado, em UTC |
| `Type` | Tipo do log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a assinatura a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos ao qual o servidor pertence |
| `ResourceProvider` | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | ID de conexão exclusiva gerada por MariaDB |
| `host_s` | Em branco |
| `ip_s` | Endereço IP do cliente que se conecta ao MariaDB |
| `user_s` | Nome do usuário executando a consulta |
| `db_s` | Nome do banco de dados conectado a |
| `\_ResourceId` | URI de recurso |

### <a name="general"></a>Geral

O esquema a seguir se aplica aos tipos de evento geral, DML_SELECT, DML_NONSELECT, DML, DDL, DCL e administrador.

> [!NOTE]
> Para `sql_text` , o log será truncado se exceder 2048 caracteres.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua ID de locatário |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de data/hora quando o log foi gravado, em UTC |
| `Type` | Tipo do log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a assinatura a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos ao qual o servidor pertence |
| `ResourceProvider` | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome do servidor |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Segundos de início de consulta no carimbo de data/hora UNIX |
| `error_code_d` | Código de erro se a consulta falhar. `0` significa nenhum erro |
| `thread_id_d` | ID do thread que executou a consulta |
| `host_s` | Em branco |
| `ip_s` | Endereço IP do cliente que se conecta ao MariaDB |
| `user_s` | Nome do usuário executando a consulta |
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

- Listar eventos auditados em todos os servidores MariaDB com logs de diagnóstico habilitados para logs de auditoria

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Próximas etapas

- [Como configurar logs de auditoria no portal do Azure](howto-configure-audit-logs-portal.md)