---
title: Formato de Log de auditoria de banco de dados SQL | Microsoft Docs
description: Entenda como os logs de auditoria de banco de dados SQL são estruturados.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 0fefe01e413e30e4aa3c1fa90de77cbdece39c38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61417381"
---
# <a name="sql-database-audit-log-format"></a>Formato de Log de auditoria de banco de dados SQL

[A auditoria de banco de dados SQL do Azure](sql-database-auditing.md) rastreia eventos de banco de dados e os grava em uma auditoria de log em sua conta de armazenamento do Azure ou envia ao Hub de eventos ou Log Analytics para análise e processamento de downstream.

## <a name="naming-conventions"></a>Convenções de nomenclatura

### <a name="blob-audit"></a>Auditoria de blob

Logs de auditoria armazenados no armazenamento de Blob são armazenados em um contêiner denominado `sqldbauditlogs` na conta de armazenamento do Azure. A hierarquia de diretórios dentro do contêiner está no formato `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`. É o formato de nome de arquivo de Blob `<CreationTime>_<FileNumberInSession>.xel`, onde `CreationTime` é em UTC `hh_mm_ss_ms` formato, e `FileNumberInSession` é um execução de índice no caso de sessão faz spans em vários arquivos de Blob.

Por exemplo, para o banco de dados `Database1` em `Server1` a seguir está um caminho válido possíveis:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

### <a name="event-hub"></a>Hub de evento

Eventos de auditoria são gravados para o namespace e hub de eventos que foi definido durante a configuração de auditoria e é capturado no corpo da [Apache Avro](https://avro.apache.org/) eventos e armazenados usando a formatação JSON com codificação UTF-8. Para ler os logs de auditoria, você pode usar as [Ferramentas Avro](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) ou ferramentas similares que processam esse formato.

### <a name="log-analytics"></a>Log Analytics

Eventos de auditoria são gravados ao espaço de trabalho do Log Analytics definido durante a configuração de auditoria para o `AzureDiagnostics` tabela com a categoria `SQLSecurityAuditEvents`. Para obter informações úteis adicionais sobre o idioma e os comandos de pesquisa do Log Analytics, consulte [Referência de pesquisa do Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search).

## <a id="subheading-1"></a>Campos de Log de auditoria

| Nome (Blob) | Nome (evento Hubs/Log Analytics) | DESCRIÇÃO | Tipo de Blob | Tipo do evento Hubs/Log Analytics |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | ID da ação | varchar(4) | string |
| action_name | action_name_s | Nome da ação | N/D | string |
| additional_information | additional_information_s | Informações adicionais sobre o evento, armazenado como XML | nvarchar(4000) | string |
| affected_rows | affected_rows_d | Número de linhas afetadas pela consulta | bigint | int |
| application_name | application_name_s| Nome do aplicativo cliente | nvarchar(128) | string |
| audit_schema_version | audit_schema_version_d | Sempre 1 | int | int |
| class_type | class_type_s | Tipo de entidade auditável no qual a auditoria ocorre | varchar(2) | string |
| class_type_desc | class_type_description_s | Descrição da entidade auditável no qual a auditoria ocorre | N/D | string |
| client_ip | client_ip_s | IP do aplicativo cliente de origem | nvarchar(128) | string |
| connection_id | N/D | ID da conexão no servidor | GUID | N/D |
| data_sensitivity_information | data_sensitivity_information_s | Tipos de informações e rótulos de confidencialidade retornados pela consulta auditada, com base nas colunas confidenciais no banco de dados. Saiba mais sobre [banco de dados do SQL Azure descobrir dados e classificação](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | string |
| database_name | database_name_s | O contexto de banco de dados no qual ocorreu a ação | sysname | string |
| database_principal_id | database_principal_id_d | ID do contexto do usuário de banco de dados que a ação é executada em | int | int |
| database_principal_name | database_principal_name_s | Nome do contexto do usuário de banco de dados no qual a ação é executada. | sysname | string |
| duration_milliseconds | duration_milliseconds_d | Duração da execução de consulta em milissegundos | bigint | int |
| event_time | event_time_t | Data e hora quando a ação auditável é acionada | datetime2 | datetime |
| host_name | N/D | Nome de host do cliente | string | N/D |
| is_column_permission | is_column_permission_s | Sinalizador que indica se esta é uma permissão de nível de coluna. 1 = true, 0 = false | bit | string |
| N/D | is_server_level_audit_s | Sinalizador que indica se essa auditoria é no nível do servidor | N/D | string |
| object_id | object_id_d | A ID da entidade na qual a auditoria ocorreu. Isso inclui o: objetos de servidor, bancos de dados, objetos de banco de dados e objetos de esquema. 0 se a entidade é o próprio servidor ou se a auditoria não for executada em nível de objeto | int | int |
| object_name | object_name_s | O nome da entidade na qual a auditoria ocorreu. Isso inclui o: objetos de servidor, bancos de dados, objetos de banco de dados e objetos de esquema. 0 se a entidade é o próprio servidor ou se a auditoria não for executada em nível de objeto | sysname | string |
| permission_bitmask | permission_bitmask_s | Quando aplicável, mostra as permissões que foram concedidas, negadas ou revogadas | varbinary(16) | string |
| response_rows | response_rows_d | Número de linhas retornadas no conjunto de resultados | bigint | int |
| schema_name | schema_name_s | O contexto do esquema no qual ocorreu a ação. NULL para auditorias ocorrendo fora de um esquema | sysname | string |
| N/D | securable_class_type_s | Objeto protegível que é mapeado para o class_type que está sendo auditado | N/D | string |
| sequence_group_id | sequence_group_id_g | Identificador exclusivo | varbinary | GUID |
| sequence_number | sequence_number_d | Rastreia a sequência de registros dentro de um único registro de auditoria que era muito grande para caber no buffer de gravação das auditorias | int | int |
| server_instance_name | server_instance_name_s | Nome da instância do servidor em que a auditoria ocorreu | sysname | string |
| server_principal_id | server_principal_id_d | ID do contexto de logon na qual a ação é executada. | int | int |
| server_principal_name | server_principal_name_s | Logon atual | sysname | string |
| server_principal_sid | server_principal_sid_s | SID do logon atual | varbinary | string |
| session_id | session_id_d | ID da sessão na qual o evento ocorreu | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Entidade de segurança de servidor para a sessão | sysname | string |
| Instrução | statement_s | Instrução T-SQL que foi executada (se houver) | nvarchar(4000) | string |
| obteve êxito | succeeded_s | Indica se a ação que acionou o evento foi bem-sucedida. Para eventos diferentes de lote e de logon, reporta somente se a verificação de permissão foi bem-sucedida ou falha, não a operação. 1 = êxito, 0 = falha | bit | string |
| target_database_principal_id | target_database_principal_id_d | A entidade de segurança do banco de dados que a operação GRANT/DENY/REVOKE é executada em. 0 se não aplicável | int | int |
| target_database_principal_name | target_database_principal_name_s | Usuário de destino da ação. NULL se não aplicável | string | string |
| target_server_principal_id | target_server_principal_id_d | Entidade de servidor em que a operação GRANT/DENY/REVOKE é executada em. Retorna 0 se não aplicável | int | int |
| target_server_principal_name | target_server_principal_name_s | Logon de destino da ação. NULL se não aplicável | sysname | string |
| target_server_principal_sid | target_server_principal_sid_s | SID do logon de destino. NULL se não aplicável | varbinary | string |
| transaction_id | transaction_id_d | SQL Server somente (começando com 2016) - 0 para BD SQL do Azure | bigint | int |
| user_defined_event_id | user_defined_event_id_d | Id do evento passado como um argumento para sp_audit_write definido pelo usuário. NULL para eventos do sistema (padrão) e diferente de zero para evento definido pelo usuário. Para obter mais informações, consulte [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | As informações passadas como um argumento ao sp_audit_write definido pelo usuário. NULL para eventos do sistema (padrão) e diferente de zero para evento definido pelo usuário. Para obter mais informações, consulte [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | string |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [auditoria de banco de dados SQL](sql-database-auditing.md).