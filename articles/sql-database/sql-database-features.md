---
title: "Visão geral dos recursos do Banco de Dados SQL do Azure | Microsoft Docs"
description: "Esta página fornece uma visão geral dos servidores lógicos e bancos de dados do Banco de Dados SQL do Azure e inclui uma matriz de suporte a recursos com links de cada recurso listado."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/22/2017
ms.author: carlrab
ms.openlocfilehash: 5824347cbfd80ce2d21e256f4ce6e6fe2201792f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sql-database-features"></a>Recursos do Banco de Dados SQL do Azure

O banco de dados do SQL Azure compartilha uma base de código comum com o SQL Server e, no nível do banco de dados, oferece suporte à maioria dos mesmos recursos. As diferenças principais de recursos entre o banco de dados do SQL Azure e SQL Server estão no nível de instância. 

Continuamos a adicionar recursos ao Banco de Dados SQL do Azure. Portanto, recomendamos que você visite a nossa página da Web de atualizações de serviço do Azure e use os seus filtros:

* Filtrado para o [Serviço de Banco de Dados SQL](https://azure.microsoft.com/updates/?service=sql-database).
* Filtrado para disponibilidade geral [Anúncios (GA)](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) para recursos do Banco de Dados SQL.

## <a name="sql-server-and-sql-database-feature-support"></a>Suporte ao recurso Banco de Dados SQL e SQL Server

A tabela a seguir lista os principais recursos do SQL Server e fornece informações sobre se há suporte para cada recurso específico e um link para obter mais informações sobre o recurso. Para ver as diferenças do Transact-SQL a considerar ao migrar uma solução existente do banco de dados, consulte [Resolvendo as diferenças do Transact-SQL durante a migração para o Banco de Dados SQL](sql-database-transact-sql-information.md).


| **Recurso do SQL Server** | **Com suporte no Banco de Dados SQL do Azure** | 
| --- | --- |  
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Sim - veja [Armazenamento de certificados](sql-database-always-encrypted.md) e [Cofre de chaves](sql-database-always-encrypted-azure-key-vault.md)|
| [Grupos de Disponibilidade AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | Não - veja [Grupos de failover e replicação geográfica ativa](sql-database-geo-replication-overview.md) |
| [Anexar um banco de dados](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Não |
| [Funções do aplicativo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Sim |
| [Arquivo BACPAC (exportação)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Sim, veja [Exportação do Banco de Dados SQL](sql-database-export.md) |
| [Arquivo BACPAC (importação)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Sim - veja [Importação de Banco de Dados SQL](sql-database-import.md) |
| [Comando BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Não - veja [Backups automatizados](sql-database-automated-backups.md) |
| [Funções internas](https://docs.microsoft.com/sql/t-sql/functions/functions) | Maioria - veja funções individuais |
| [Alterar captura de dados](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Não |
| [Controle de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Sim |
| [Instruções de agrupamento](https://docs.microsoft.com/sql/t-sql/statements/collations) | Sim |
| [Índices Columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Sim - [somente no Premium Edition](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| [Common language runtime (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Não |
| [Bancos de dados independentes](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Sim |
| [Usuários independentes](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Sim |
| [Controle de palavras-chave da linguagem de fluxo](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Sim |
| [Consultas entre bancos de dados](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | Parcial - consulte [Consultas elásticas](sql-database-elastic-query-overview.md) |
| [Cursores](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Sim | 
| [Compactação de dados](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Sim |
| [Database mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Não |
| [Espelhamento de banco de dados](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Não |
| [Definições de configuração do banco de dados](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Sim |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Não |
| [Instantâneos de banco de dados](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Não |
| [Tipos de dados](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Sim |  
| [Instruções DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | Maioria - veja Instruções individuais |
| [Instruções DDL](https://docs.microsoft.com/sql/t-sql/statements/statements) | Maioria - veja Instruções individuais
| [Gatilhos DDL](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Apenas banco de dados |
| [Transações distribuídas - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Não - veja [transações elásticas](sql-database-elastic-transactions-overview.md) |
| [Instruções DML](https://docs.microsoft.com/sql/t-sql/queries/queries) | Maioria - veja Instruções individuais |
| [Gatilhos DML](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) |
| [DMVs](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Alguns - veja DMVs individuais |
| [Notificações de eventos](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Não - veja [Alertas](sql-database-insights-alerts-portal.md) |
| [Expressões](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Sim |
| [Eventos estendidos](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Alguns - veja eventos individuais |
| [Procedimentos armazenados estendidos](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Não |
| [Arquivos e grupos de arquivos](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Somente o grupo de arquivos primários |
| [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Não |
| [Pesquisa de texto completo](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | Separadores de palavras de terceiros sem suporte |
| [Funções](https://docs.microsoft.com/sql/t-sql/functions/functions) | Maioria - veja funções individuais |
| [Processamento de gráfico](/sql/relational-databases/graphs/sql-graph-overview) | Sim |
| [Otimização na memória](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Sim - [somente no Premium Edition](sql-database-in-memory.md) |
| [Suporte a dados JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | Sim |
| [Elementos de linguagem](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | Maioria - veja elementos individuais |  
| [Servidores vinculados](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Não - veja [Consulta elástica](sql-database-elastic-query-horizontal-partitioning.md) |
| [Envio de logs](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | Não - veja [Grupos de failover e replicação geográfica ativa](sql-database-geo-replication-overview.md) |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Não |
| [Log mínimo na importação em massa](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Não |
| [Modificação dos dados do sistema](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Não |
| [Operações de índice online](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Com suporte - tamanho da transação limitado pela camada de serviço |
| [Operadores](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | Maioria - veja operadores individuais |
| [Restauração pontual de banco de dados](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Sim - veja [Recuperação do Banco de Dados SQL](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Não |
| [Gerenciamento baseado em políticas](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Não |
| [Predicados](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Maioria - veja predicados individuais |
| [R Services](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Versão de teste; consulte [O que há de novo em aprendizado de máquina](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  |
| [Resource governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Não |
| [Instruções RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Não | 
| [Restaurar banco de dados desde o backup](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Somente de backups internos - veja [Recuperação do Banco de Dados SQL](sql-database-recovery-using-backups.md) |
| [Segurança em Nível de Linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Sim |
| [Pesquisa semântica](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Não |
| [Números de sequência](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Sim |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Não |
| [Definições de configuração do servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Não |
| [Instruções Set](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | Maioria - veja Instruções individuais 
| [Espacial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Sim |
| [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Não – consulte [Trabalhos elásticos](sql-database-elastic-jobs-getting-started.md) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Não, veja [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [Auditoria do SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | Não - veja [auditoria do Banco de Dados SQL](sql-database-auditing.md) |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Não - veja [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Sim |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Não - veja [Eventos estendidos](sql-database-xevent-db-diff-from-svr.md) |
| [Replicação do SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Somente assinante de replicação de instantâneo e transacional](sql-database-cloud-migrate.md) |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Não |
| [Procedimentos armazenados](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Sim |
| [Funções armazenadas do sistema](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Maioria - veja funções individuais |
| [Procedimentos armazenados do sistema](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Alguns - veja procedimentos armazenados individuais |
| [Tabelas do sistema](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Alguns - veja tabelas individuais |
| [Exibições do catálogo do sistema](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Alguns - veja exibições individuais |
| [Partições de tabela](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Sim - somente no grupo de arquivos primário |
| [Tabelas temporárias](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | Somente tabelas locais e temporárias globais no escopo do banco de dados |
| [Tabelas temporais](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | Sim |
| [Variáveis](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Sim | 
| [Transparent data encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Sim |
| [Clustering de Failover do Windows Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | Não - veja [Grupos de failover e replicação geográfica ativa](sql-database-geo-replication-overview.md) |
| [Índices XML](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Sim |

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre o serviço do Banco de Dados SQL do Azure, consulte [O que é o Banco de Dados SQL?](sql-database-technical-overview.md)
- Para obter informações sobre o suporte do Transact-SQL e as diferenças, consulte [Resolvendo as diferenças do Transact-SQL durante a migração para o Banco de Dados SQL](sql-database-transact-sql-information.md).
