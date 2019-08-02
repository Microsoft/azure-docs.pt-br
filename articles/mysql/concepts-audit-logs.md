---
title: Logs de auditoria do banco de dados do Azure para MySQL
description: Descreve os logs de auditoria disponíveis no banco de dados do Azure para MySQL e os parâmetros disponíveis para habilitar níveis de log.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 86750cea5e7f0d4726f3e0e9a03795ef2a602d8b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443852"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Logs de auditoria no banco de dados do Azure para MySQL

No banco de dados do Azure para MySQL, o log de auditoria está disponível aos usuários. O log de auditoria pode ser usado para rastrear a atividade de nível de banco de dados e normalmente é usado para fins de conformidade.

> [!IMPORTANT]
> Funcionalidade de log de auditoria está atualmente em visualização.

## <a name="configure-audit-logging"></a>Configurar o log de auditoria

Por padrão, o log de auditoria está desabilitado. Para habilitá-lo, defina `audit_log_enabled` como ON.

Outros parâmetros que você pode ajustar incluem:

- `audit_log_events`: controla os eventos a serem registrados. Consulte a tabela a seguir para eventos de auditoria específicos.
- `audit_log_exclude_users`: Usuários do MySQL a serem excluídos do log. Permite no máximo quatro usuários. O comprimento máximo do parâmetro é de 256 caracteres.

| **Evento** | **Descrição** |
|---|---|
| `CONNECTION` | -Iniciação de Conexão (bem-sucedidas ou não) <br> -Reautenticação do usuário com usuário/senha diferente durante a sessão <br> -Encerramento da Conexão |
| `DML_SELECT`| Consultas SELECT |
| `DML_NONSELECT` | INSERT/DELETE/UPDATE queries |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Consultas como "DROP DATABASE" |
| `DCL` | Consultas como "CONCEDER permissão de" |
| `ADMIN` | Consultas como "Mostrar STATUS" |
| `GENERAL` | Tudo em DML_SELECT, DML_NONSELECT, DML, DDL, DCL e administração |
| `TABLE_ACCESS` | -Só estará disponível para o MySQL 5.7 <br> -Tabela leia as instruções como SELECT ou INSERT INTO... SELECIONAR <br> -Instruções delete table, como DELETE ou TRUNCATE TABLE <br> -Instruções insert table, como inserção ou substituição <br> -Instruções update table, como atualização |

## <a name="access-audit-logs"></a>Acesse os logs de auditoria

Os logs de auditoria são integrados com os Logs de diagnóstico do Azure Monitor. Depois de habilitar os logs de auditoria no servidor MySQL, você pode emitir os logs do Azure Monitor, os Hubs de eventos ou armazenamento do Azure. Para saber mais sobre como habilitar os logs de diagnóstico no portal do Azure, consulte a [artigo portal do log de auditoria](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Esquemas de Logs de diagnóstico

As seções a seguir descrevem o que é a saída de logs de auditoria do MySQL com base no tipo de evento. Dependendo do método de saída, os campos incluídos e a ordem em que aparecem podem variar.

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
| `event_subclass_s` | `CONNECT`, `DISCONNECT`, `CHANGE USER` (disponível apenas para o MySQL 5.7) |
| `connection_id_d` | ID de conexão exclusiva gerada pelo MySQL |
| `host_s` | Em branco |
| `ip_s` | Endereço IP do cliente conectando-se ao MySQL |
| `user_s` | Nome do usuário que executa a consulta |
| `db_s` | Nome do banco de dados conectado |
| `\_ResourceId` | URI de recurso |

### <a name="general"></a>Geral

Esquema abaixo se aplica a tipos de eventos gerais, DML_SELECT, DML_NONSELECT, DML, DDL, DCL e administrador.

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
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` (disponível apenas para MySQL 5.6) |
| `event_time` | Consulta inicie segundos no carimbo de hora do UNIX |
| `error_code_d` | Código de erro se a falha na consulta. `0` significa que nenhum erro |
| `thread_id_d` | ID do thread que executou a consulta |
| `host_s` | Em branco |
| `ip_s` | Endereço IP do cliente conectando-se ao MySQL |
| `user_s` | Nome do usuário que executa a consulta |
| `sql_text_s` | Texto da consulta completa |
| `\_ResourceId` | URI de recurso |

### <a name="table-access"></a>Acesso à tabela

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
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE`, ou `DELETE` |
| `connection_id_d` | ID de conexão exclusiva gerada pelo MySQL |
| `db_s` | Nome do banco de dados acessado |
| `table_s` | Nome da tabela acessada |
| `sql_text_s` | Texto da consulta completa |
| `\_ResourceId` | URI de recurso |

## <a name="next-steps"></a>Próximas etapas

- [Como configurar logs de auditoria no portal do Azure](howto-configure-audit-logs-portal.md)