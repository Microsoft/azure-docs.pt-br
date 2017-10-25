# Visão geral

## [O que é o SQL Data Warehouse?](sql-data-warehouse-overview-what-is.md)
## [Carga de trabalho do data warehouse](sql-data-warehouse-overview-workload.md)
## [Dados distribuídos](sql-data-warehouse-distributed-data.md)
## [Perguntas frequentes](sql-data-warehouse-overview-faq.md)

# Introdução

## [Tutorial para iniciantes](sql-data-warehouse-get-started-tutorial.md)
## [Tutorial de consulta elástica](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)
## [práticas recomendadas](sql-data-warehouse-best-practices.md)
## [Gerenciar](sql-data-warehouse-overview-manage.md)



# Como

## Backup e restauração

### [Visão geral do Backup](sql-data-warehouse-backups.md)
### [Visão geral de Restauração](sql-data-warehouse-restore-database-overview.md)
#### [Portal do Azure](sql-data-warehouse-restore-database-portal.md)
#### [PowerShell do Azure](sql-data-warehouse-restore-database-powershell.md)
#### [REST](sql-data-warehouse-restore-database-rest-api.md)

## Connect

### [Visão geral](sql-data-warehouse-connect-overview.md)
### [SSMS](sql-data-warehouse-query-ssms.md)
### [Visual Studio](sql-data-warehouse-query-visual-studio.md)
### [Instalar Visual Studio](sql-data-warehouse-install-visual-studio.md)
### [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
### [Cadeias de conexão](sql-data-warehouse-connection-strings.md)

## Criação
### [Portal do Azure](sql-data-warehouse-get-started-provision.md)
### [PowerShell do Azure](sql-data-warehouse-get-started-provision-powershell.md)
### [T-SQL](sql-data-warehouse-get-started-create-database-tsql.md)

## Desenvolver

### [Visão geral](sql-data-warehouse-overview-develop.md)

### Tabelas

#### [Visão geral](sql-data-warehouse-tables-overview.md)
#### [CTAS](sql-data-warehouse-develop-ctas.md)
#### [Tipos de dados](sql-data-warehouse-tables-data-types.md)
#### [Tabelas distribuídas](sql-data-warehouse-tables-distribute.md)
#### [Índices](sql-data-warehouse-tables-index.md)
#### [Identidade](sql-data-warehouse-tables-identity.md)
#### [Partições](sql-data-warehouse-tables-partition.md)
#### [Tabelas replicadas](design-guidance-for-replicated-tables.md)
#### [Estatísticas](sql-data-warehouse-tables-statistics.md)
#### [Temporário](sql-data-warehouse-tables-temporary.md)

### Consultas

#### [SQL dinâmico](sql-data-warehouse-develop-dynamic-sql.md)
#### [Agrupar por opções](sql-data-warehouse-develop-group-by-options.md)
#### [Rótulos](sql-data-warehouse-develop-label.md)

### Elementos da linguagem T-SQL

#### [Loops](sql-data-warehouse-develop-loops.md)
#### [Procedimentos armazenados](sql-data-warehouse-develop-stored-procedures.md)
#### [Transações](sql-data-warehouse-develop-transactions.md)
#### [Práticas recomendadas de Transações](sql-data-warehouse-develop-best-practices-transactions.md)
#### [Esquemas definidos pelo usuário](sql-data-warehouse-develop-user-defined-schemas.md)
#### [Atribuição de variável](sql-data-warehouse-develop-variable-assignment.md)
#### [Modos de exibição](sql-data-warehouse-develop-views.md)

## Integração

### [Visão geral](sql-data-warehouse-overview-integrate.md)
### [Fábrica de dados](sql-data-warehouse-integrate-azure-data-factory.md)
### [Machine Learning](sql-data-warehouse-integrate-azure-machine-learning.md)
### [Tutorial de Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [Power BI](sql-data-warehouse-integrate-power-bi.md)
### [Visualização do Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
### [Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md)

### [Consulta elástica com Banco de Dados SQL e SQL Data Warehouse](how-to-use-elastic-query-with-sql-data-warehouse.md)

## Carregar

### Conceitos
#### [Visão geral](sql-data-warehouse-overview-load.md)
#### [Diretrizes do PolyBase](sql-data-warehouse-load-polybase-guide.md)

### Tutoriais
#### [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)

### Guias de instruções
#### [Dados de amostra](sql-data-warehouse-load-sample-databases.md)
#### [Repositório Azure Data Lake](sql-data-warehouse-load-from-azure-data-lake-store.md)
#### [BCP](sql-data-warehouse-load-with-bcp.md)
#### [Fábrica de dados](sql-data-warehouse-load-with-data-factory.md)
#### [PolyBase do armazenamento de blobs](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
#### [PolyBase do SQL Server](sql-data-warehouse-load-from-sql-server-with-polybase.md)
#### [RedGate](sql-data-warehouse-load-with-redgate.md)
#### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)

## Migrar

### [Visão geral](sql-data-warehouse-overview-migrate.md)
### [Utilitário de migração](sql-data-warehouse-migrate-migration-utility.md)
### [Migrar o esquema](sql-data-warehouse-migrate-schema.md)
### [Migrar o código](sql-data-warehouse-migrate-code.md)
### [Migrar dados](sql-data-warehouse-migrate-data.md)
### [Migrar para o Armazenamento Premium](sql-data-warehouse-migrate-to-premium-storage.md)

## Gerenciar a computação

### [Visão geral](sql-data-warehouse-manage-compute-overview.md)
### [Portal do Azure](sql-data-warehouse-manage-compute-portal.md)
### [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
### [API REST](sql-data-warehouse-manage-compute-rest-api.md)
### [T-SQL](sql-data-warehouse-manage-compute-tsql.md)

## Desempenho

### [Visão geral](sql-data-warehouse-overview-manage-user-queries.md)
### [Compactação columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
### [Monitorar](sql-data-warehouse-manage-monitor.md)
### [Carga de trabalho](sql-data-warehouse-develop-concurrency.md)

## Segurança

### [Visão geral](sql-data-warehouse-overview-manage-security.md)
### [Auditoria](sql-data-warehouse-auditing-overview.md)
### [Auditoria para clientes de nível inferior](sql-data-warehouse-auditing-downlevel-clients.md)
### [Autenticação](sql-data-warehouse-authentication.md)
### [Criptografia](sql-data-warehouse-encryption-tde.md)
### [Criptografia com o T-SQL](sql-data-warehouse-encryption-tde-tsql.md)
### [Detecção de ameaças](sql-data-warehouse-security-threat-detection.md)

## Solucionar problemas
### [Solucionar problemas](sql-data-warehouse-troubleshoot.md)

# Referência

## [Limites de capacidade](sql-data-warehouse-service-capacity-limits.md)
## T-SQL
### [Referência completa](https://docs.microsoft.com/sql/t-sql/language-reference/)
### [Elementos da linguagem SQL DW](sql-data-warehouse-reference-tsql-language-elements.md)
### [Instruções do SQL DW](sql-data-warehouse-reference-tsql-statements.md)
## [Exibições do sistema](sql-data-warehouse-reference-tsql-system-views.md)
## [Cmdlets do PowerShell](sql-data-warehouse-reference-powershell-cmdlets.md)

# Recursos
## [Roteiro do Azure](https://azure.microsoft.com/roadmap/?category=databases)
## [Fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSQLDataWarehouse)
## [Preços](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)
## [Calculadora de preço](https://azure.microsoft.com/pricing/calculator/)
## [Solicitações de recursos](https://feedback.azure.com/forums/307516-sql-data-warehouse/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=sql-data-warehouse)
## [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/)
## [Suporte](sql-data-warehouse-get-started-create-support-ticket.md)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

## Parceiros
### [Business intelligence](sql-data-warehouse-partner-business-intelligence.md)
### [Integração de dados](sql-data-warehouse-partner-data-integration.md)
### [Gerenciamento de dados](sql-data-warehouse-partner-data-management.md)
