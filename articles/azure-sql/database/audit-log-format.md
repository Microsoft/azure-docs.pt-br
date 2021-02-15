---
title: Formato do log de auditoria do banco de dados SQL
description: Entenda como os logs de auditoria do banco de dados SQL do Azure são estruturados.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: reference
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 06/03/2020
ms.openlocfilehash: f4da14c1fbdaf71018e62b0f97e288a66edef5c8
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677284"
---
# <a name="sql-database-audit-log-format"></a>Formato do log de auditoria do banco de dados SQL

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

A [auditoria do banco de dados SQL do Azure](auditing-overview.md) rastreia eventos de banco de dados e os grava em um log de auditoria em sua conta de armazenamento do Azure ou os envia ao Hub de eventos ou log Analytics para análise e processamento de downstream.

## <a name="naming-conventions"></a>Convenções de nomenclatura

### <a name="blob-audit"></a>Auditoria de BLOB

Os logs de auditoria armazenados no armazenamento de BLOBs do Azure são armazenados em um contêiner chamado `sqldbauditlogs` na conta de armazenamento do Azure. A hierarquia de diretórios dentro do contêiner está no formato `<ServerName>/<DatabaseName>/<AuditName>/<Date>/` . O formato de nome de arquivo de blob é `<CreationTime>_<FileNumberInSession>.xel` , em que `CreationTime` está no `hh_mm_ss_ms` formato UTC e `FileNumberInSession` é um índice em execução em casos de logs de sessão abrangedos em vários arquivos de BLOB.

Por exemplo, para o banco de dados, `Database1` `Server1` é possível um caminho válido a seguir:

`Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel`

Os logs de auditoria de [réplicas somente leitura](read-scale-out.md) são armazenados no mesmo contêiner. A hierarquia de diretórios dentro do contêiner está no formato `<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/` . O nome do arquivo de blob compartilha o mesmo formato. Os logs de auditoria de réplicas somente leitura são armazenados no mesmo contêiner.


### <a name="event-hub"></a>Hub de evento

Os eventos de auditoria são gravados no namespace e no Hub de eventos que foi definido durante a configuração de auditoria e são capturados no corpo de eventos do [Apache Avro](https://avro.apache.org/) e armazenados usando a formatação JSON com codificação UTF-8. Para ler os logs de auditoria, você pode usar as [Ferramentas Avro](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) ou ferramentas similares que processam esse formato.

### <a name="log-analytics"></a>Log Analytics

Os eventos de auditoria são gravados no espaço de trabalho Log Analytics definido durante a configuração de auditoria, para a `AzureDiagnostics` tabela com a categoria `SQLSecurityAuditEvents` . Para obter informações úteis adicionais sobre o idioma e os comandos de pesquisa do Log Analytics, consulte [Referência de pesquisa do Log Analytics](../../azure-monitor/log-query/log-query-overview.md).

## <a name="audit-log-fields"></a><a id="subheading-1"></a>Campos do log de auditoria

| Nome (BLOB) | Nome (hubs de eventos/Log Analytics) | Descrição | Tipo de blob | Tipo de Log Analytics/hubs de eventos |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | ID da ação | varchar(4) | string |
| action_name | action_name_s | Nome da ação | N/D | string |
| additional_information | additional_information_s | Quaisquer informações adicionais sobre o evento, armazenadas como XML | nvarchar(4000) | string |
| affected_rows | affected_rows_d | Número de linhas afetadas pela consulta | BIGINT | INT |
| application_name | application_name_s| Nome do aplicativo cliente | nvarchar(128) | string |
| audit_schema_version | audit_schema_version_d | Sempre 1 | INT | INT |
| class_type | class_type_s | Tipo de entidade auditável na qual a auditoria ocorre | varchar(2) | string |
| class_type_desc | class_type_description_s | Descrição da entidade auditável na qual a auditoria ocorre | N/D | string |
| client_ip | client_ip_s | IP de origem do aplicativo cliente | nvarchar(128) | string |
| connection_id | N/D | ID da conexão no servidor | GUID | N/D |
| data_sensitivity_information | data_sensitivity_information_s | Tipos de informações e rótulos de sensibilidade retornados pela consulta auditada, com base nas colunas classificadas no banco de dados. Saiba mais sobre a [descoberta e a classificação de dados do banco SQL do Azure](data-discovery-and-classification-overview.md) | nvarchar(4000) | string |
| database_name | database_name_s | O contexto do banco de dados no qual a ação ocorreu | sysname | string |
| database_principal_id | database_principal_id_d | ID do contexto de usuário do banco de dados no qual a ação é executada | INT | INT |
| database_principal_name | database_principal_name_s | Nome do contexto de usuário do banco de dados no qual a ação é executada | sysname | string |
| duration_milliseconds | duration_milliseconds_d | Duração da execução da consulta em milissegundos | BIGINT | INT |
| event_time | event_time_t | Data e hora em que a ação auditável é acionada | datetime2 | DATETIME |
| host_name | N/D | Nome do host do cliente | string | N/D |
| is_column_permission | is_column_permission_s | Sinalizador que indica se esta é uma permissão no nível de coluna. 1 = true, 0 = false | bit | string |
| N/D | is_server_level_audit_s | Sinalizador indicando se essa auditoria está no nível do servidor | N/D | string |
| object_id | object_id_d | A ID da entidade na qual a auditoria ocorreu. Isso inclui os objetos de servidor:, bancos de dados, objetos de banco e objetos de esquema. 0 se a entidade for o próprio servidor ou se a auditoria não for executada em um nível de objeto | INT | INT |
| object_name | object_name_s | O nome da entidade na qual a auditoria ocorreu. Isso inclui os objetos de servidor:, bancos de dados, objetos de banco e objetos de esquema. 0 se a entidade for o próprio servidor ou se a auditoria não for executada em um nível de objeto | sysname | string |
| permission_bitmask | permission_bitmask_s | Quando aplicável, mostra as permissões concedidas, negadas ou revogadas | varbinary(16) | string |
| response_rows | response_rows_d | Número de linhas retornadas no conjunto de resultados | BIGINT | INT |
| schema_name | schema_name_s | O contexto do esquema no qual a ação aconteceu. NULL para auditorias que ocorrem fora de um esquema | sysname | string |
| N/D | securable_class_type_s | Objeto protegível que mapeia para o class_type que está sendo auditado | N/D | string |
| sequence_group_id | sequence_group_id_g | Identificador exclusivo | varbinary | GUID |
| sequence_number | sequence_number_d | Controla a sequência de registros em um único registro de auditoria que era muito grande para caber no buffer de gravação para auditorias | INT | INT |
| server_instance_name | server_instance_name_s | Nome da instância do servidor em que ocorreu a auditoria | sysname | string |
| server_principal_id | server_principal_id_d | ID do contexto de logon no qual a ação é executada | INT | INT |
| server_principal_name | server_principal_name_s | Logon atual | sysname | string |
| server_principal_sid | server_principal_sid_s | SID de logon atual | varbinary | string |
| session_id | session_id_d | ID da sessão na qual o evento ocorreu | SMALLINT | INT |
| session_server_principal_name | session_server_principal_name_s | Entidade de segurança do servidor para sessão | sysname | string |
| instrução | statement_s | Instrução T-SQL que foi executada (se houver) | nvarchar(4000) | string |
| bem-sucedido | succeeded_s | Indica se a ação que disparou o evento foi bem-sucedida. Para eventos diferentes de logon e lote, isso apenas relata se a verificação de permissão foi bem-sucedida ou falhou, não a operação. 1 = êxito, 0 = falha | bit | string |
| target_database_principal_id | target_database_principal_id_d | O banco de dados principal no qual a operação GRANT/DENY/REVOKE é executada. 0 se não for aplicável | INT | INT |
| target_database_principal_name | target_database_principal_name_s | Usuário de destino da ação. NULL se não for aplicável | string | string |
| target_server_principal_id | target_server_principal_id_d | Diretor de Servidor que o GRANT/operação do DENY/REVOKE é executada em. Retorna 0 se não aplicável | INT | INT |
| target_server_principal_name | target_server_principal_name_s | Logon de destino da ação. NULL se não for aplicável | sysname | string |
| target_server_principal_sid | target_server_principal_sid_s | SID do logon de destino. NULL se não for aplicável | varbinary | string |
| transaction_id | transaction_id_d | Somente SQL Server (começando com 2016)-0 para o banco de dados SQL do Azure | BIGINT | INT |
| user_defined_event_id | user_defined_event_id_d | ID do evento definido pelo usuário passada como um argumento para sp_audit_write. NULL para eventos do sistema (padrão) e diferente de zero para o evento definido pelo usuário. Para obter mais informações, consulte [sp_audit_write (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | SMALLINT | INT |
| user_defined_information | user_defined_information_s | Informações definidas pelo usuário passadas como um argumento para sp_audit_write. NULL para eventos do sistema (padrão) e diferente de zero para o evento definido pelo usuário. Para obter mais informações, consulte [sp_audit_write (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | string |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [auditoria do banco de dados SQL do Azure](auditing-overview.md).