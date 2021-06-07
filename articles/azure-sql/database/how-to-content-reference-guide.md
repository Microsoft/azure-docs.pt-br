---
title: Configurar & gerenciar a referência de conteúdo
description: Encontre uma referência de conteúdo que ensina a configurar e gerenciar o banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 01/14/2020
ms.openlocfilehash: 19cec1deeef344ea5897061db0fec88066b90b05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92791759"
---
# <a name="configure-and-manage-content-reference---azure-sql-database"></a>Configurar e gerenciar a referência de conteúdo-banco de dados SQL do Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste artigo, você pode encontrar uma referência de conteúdo de vários guias, scripts e explicações que podem ajudá-lo a gerenciar e configurar seu banco de dados SQL do Azure. 

## <a name="load-data"></a>Carregar dados

- [Migrar para o Banco de Dados SQL](migrate-to-database-from-sql-server.md)
- Saiba como [gerenciar o banco de dados SQL após a migração](manage-data-after-migrating-to-database.md).
- [Copiar um banco de dados](database-copy.md)
- [Importar um BD de um BACPAC](database-import.md)
- [Exportar um BD de um BACPAC](database-export.md)
- [Carregar dados com o BCP](../load-from-csv-with-bcp.md)
- [Carregar dados com o ADF](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="configure-features"></a>Configurar recursos

- [Configurar a autenticação do Azure Active Directory (Azure AD)](authentication-aad-configure.md)
- [Configurar o acesso condicional](conditional-access-configure.md)
- [Autenticação multifator do Azure AD](authentication-mfa-ssms-overview.md)
- [Configurar a autenticação multifator](authentication-mfa-ssms-configure.md)
- [Configurar a política de retenção temporal](temporal-tables-retention-policy.md)
- [Configurar TDE com BYOK](transparent-data-encryption-byok-configure.md)
- [Girar chaves BYOK da TDE](transparent-data-encryption-byok-key-rotation.md)
- [Remover protetor da TDE](transparent-data-encryption-byok-remove-tde-protector.md)
- [Configurar OLTP na memória](../in-memory-oltp-configure.md)
- [Configurar Automação do Azure](automation-manage.md)
- [Configurar a replicação transacional](replication-to-sql-database.md) para replicar sua data entre bancos de dados.
- [Configurar a detecção de ameaças](threat-detection-configure.md) para permitir que o Banco de Dados SQL do Azure identifique atividades suspeitas como Injeção de SQL ou acesso de locais suspeitos.
- [Configurar a máscara de dados dinâmicos](dynamic-data-masking-configure-portal.md) para proteger seus dados confidenciais.
- [Configurar a retenção de backup](long-term-backup-retention-configure.md) para um banco de dados manter os backups no Armazenamento de Blobs do Azure. 
- [Configurar a replicação geográfica](active-geo-replication-overview.md) para manter uma réplica do banco de dados em outra região.
- [Configure a segurança para réplicas geográficas](active-geo-replication-security-configure.md).

## <a name="monitor-and-tune-your-database"></a>Monitorar e ajustar o banco de dados

- [Ajuste manual](performance-guidance.md)
- [Utilizar DMVs para monitorar o desempenho](monitoring-with-dmvs.md)
- [Usar o Repositório de Consultas para monitorar o desempenho](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Habilitar o ajuste automático](automatic-tuning-enable.md) para permitir que o Banco de Dados SQL do Azure otimize o desempenho da sua carga de trabalho.
- [Habilitar as notificações por email para ajuste automático](automatic-tuning-email-notifications-configure.md) para obter informações sobre recomendações de ajuste.
- [Aplicar as recomendações de desempenho](database-advisor-find-recommendations-portal.md) e otimizar seu banco de dados.
- [Criar alertas](alerts-insights-configure-portal.md) para receber notificações do Banco de Dados SQL do Azure.
- [Solucionar problemas de conectividade](troubleshoot-common-errors-issues.md) se perceber alguns problemas de conectividade entre os aplicativos e o banco de dados. Você também pode usar o [Resource Health para problemas de conectividade](resource-health-to-troubleshoot-connectivity.md).
- [Solucionar problemas de desempenho com o Intelligent Insights](intelligent-insights-troubleshoot-performance.md)
- [Gerenciar o espaço de arquivo](file-space-manage.md) para monitorar o uso de armazenamento no banco de dados.
- [Usar o log de diagnóstico do Intelligent Insights](intelligent-insights-use-diagnostics-log.md)
- [Monitorar o espaço OLTP na memória](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Eventos estendidos

- [Eventos estendidos](xevent-db-diff-from-svr.md)
- [Armazenar eventos estendidos no arquivo de evento](xevent-code-event-file.md)
- [Armazenar eventos estendidos no buffer de anel](xevent-code-ring-buffer.md)

## <a name="query-distributed-data"></a>Consulta de dados distribuídos

- [Consultar dados particionados verticalmente](elastic-query-getting-started-vertical.md) entre vários bancos de dados.
- [Relatório na camada de dados escalada](elastic-query-horizontal-partitioning.md)horizontalmente.
- [Consulte entre tabelas com esquemas diferentes](elastic-query-vertical-partitioning.md).

### <a name="data-sync"></a>Sincronização de dados

- [Sincronização de Dados SQL](sql-data-sync-data-sql-server-sql-database.md)
- [Agente de Sincronização de Dados](sql-data-sync-agent-overview.md)
- [Replicar alterações de esquema](sql-data-sync-update-sync-schema.md)
- [Monitorar com o OMS](./monitor-tune-overview.md)
- [Práticas recomendadas para a Sincronização de Dados](sql-data-sync-best-practices.md)
- [Solucionar problemas da Sincronização de Dados](sql-data-sync-troubleshoot.md)

## <a name="elastic-database-jobs"></a>Trabalhos de Banco de Dados elástico

- [Criar e gerenciar](elastic-jobs-powershell-create.md) Trabalhos de banco de dados elástico usando o PowerShell.
- [Criar e gerenciar](elastic-jobs-tsql-create-manage.md) trabalhos do Banco de Dados Elástico usando o Transact-SQL.
- [Migrar de trabalhos Elásticos antigos](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Fragmentação do banco de dados

- [Atualizar biblioteca de cliente do banco de dados elástico](elastic-scale-upgrade-client-library.md).
- [Criar aplicativo fragmentado](elastic-scale-get-started.md).
- [Consultar dados fragmentados horizontalmente](elastic-query-getting-started.md).
- Executar [Consultas com vários fragmentos](elastic-scale-multishard-querying.md).
- [Mover dados fragmentados](elastic-scale-configure-deploy-split-and-merge.md).
- [Configurar a segurança](elastic-scale-split-merge-security-configuration.md) em fragmentos de banco de dados.
- [Adicionar um fragmento](elastic-scale-add-a-shard.md) ao conjunto atual de fragmentos de bancos de dados.
- [Corrigir problemas de mapa de fragmentos](elastic-database-recovery-manager.md).
- [Migre o banco de BD fragmentado](elastic-convert-to-use-elastic-tools.md).
- [Criar contadores](elastic-database-perf-counters.md).
- [Usar o Entity Framework](elastic-scale-use-entity-framework-applications-visual-studio.md) para consultar dados fragmentados.
- [Usar a estrutura de Dapper](elastic-scale-working-with-dapper.md) para consultar dados fragmentados.

## <a name="develop-applications"></a>Desenvolver aplicativos

- [Conectividade](connect-query-content-reference-guide.md#libraries)
- [Usar o Conector do Spark](spark-connector.md)
- [Autenticar aplicativo](application-authentication-get-client-id-keys.md)
- [Usar o envio em lote para obter melhor desempenho](../performance-improve-use-batching.md)
- [Diretrizes de conectividade](troubleshoot-common-connectivity-issues.md)
- [Aliases de DNS](dns-alias-overview.md)
- [Configurar PowerShell de alias do DNS](dns-alias-powershell-create.md)
- [Portas - ADO.NET](adonet-v12-develop-direct-route-ports.md)
- [C e C ++](develop-cplusplus-simple.md)
- [Excel](connect-excel.md)

## <a name="design-applications"></a>Design de aplicativos

- [Design para a recuperação de desastres](designing-cloud-solutions-for-disaster-recovery.md)
- [Design para pools elásticos](disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Design para atualizações do aplicativo](manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-software-as-a-service-saas-applications"></a>Criar aplicativos SaaS (software como serviço) de vários locatários

- [Padrões de design do SaaS](saas-tenancy-app-design-patterns.md)
- [Indexador de vídeo de SaaS](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Segurança de aplicativo de SaaS](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [guias de instruções para o Azure SQL instância gerenciada](../managed-instance/how-to-content-reference-guide.md)