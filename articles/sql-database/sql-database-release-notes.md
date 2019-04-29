---
title: Notas de versão do banco de dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre os novos recursos e melhorias no serviço de banco de dados SQL e na documentação do banco de dados SQL
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: carlrab
ms.openlocfilehash: e6d702c7a3194f07f9d04139acbc9b6101b296ea
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759461"
---
# <a name="sql-database-release-notes"></a>Notas de versão do banco de dados SQL

Este artigo lista os novos recursos e melhorias no serviço de banco de dados SQL e na documentação do banco de dados SQL. Para aprimoramentos de serviço de banco de dados SQL, consulte também [atualizações de serviço de banco de dados SQL](https://azure.microsoft.com/updates/?product=sql-database). Para encontrar melhorias para outros serviços do Azure, consulte [atualizações de serviço](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Recursos em visualização pública

| Recurso | Detalhes |
| ---| --- |
| Trabalhos de banco de dados elástico | Para obter informações, consulte [criar, configurar e gerenciar trabalhos Elásticos](elastic-jobs-overview.md) |
| Transações elásticas | [Transações distribuídas entre bancos de dados na nuvem](sql-database-elastic-transactions-overview.md) |
| Consultas elásticas | Para obter informações, consulte [visão geral de consulta elástica](sql-database-elastic-query-overview.md) |
| Replicação de instâncias gerenciadas |Para obter informações, consulte [configurar a replicação em um banco de dados de instância gerenciada do banco de dados SQL](replication-with-sql-database-managed-instance.md)|
| Agrupamento de instância com instâncias gerenciadas |Para obter informações, consulte [usar o PowerShell com o modelo do Resource Manager para criar uma instância gerenciada SQL do Azure](./scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)|
| R services / machine learning com bancos de dados únicos e pools Elásticos |Para obter informações, consulte [serviços do Machine Learning no Azure SQL Database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)|
| Recuperação de banco de dados acelerada com bancos de dados únicos e pools Elásticos | Para obter informações, consulte [recuperação acelerada de banco de dados](sql-database-accelerated-database-recovery.md)|
| Descoberta de dados e classificação  |Para obter informações, consulte [descoberta de dados do banco de dados SQL e SQL Data Warehouse e classificação](sql-database-data-discovery-and-classification.md)|
| Transparent data encryption (TDE) com Traga sua própria chave (BYOK) com instâncias gerenciadas |Para obter informações, consulte [Azure SQL Transparent Data Encryption com chaves gerenciadas pelo cliente no Azure Key Vault: Oferecer suporte de sua própria chave](transparent-data-encryption-byok-azure-sql.md)|
| Recriar bancos de dados descartados com instâncias gerenciadas |Para obter informações, consulte [recriar bancos de dados descartados na instância gerenciada do SQL do Azure](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266)|
| Detecção de ameaças com instâncias gerenciadas |Para obter informações, consulte [instância gerenciada de configurar a detecção de ameaças no banco de dados SQL](sql-database-managed-instance-threat-detection.md)|
| Camadas de serviço em hiperescala com bancos de dados únicos |Para obter informações, consulte [camada de serviço em hiperescala para até 100 TB](sql-database-service-tier-hyperscale.md)|
| Editor de consultas no portal do Azure |Para obter informações, consulte [usar o editor de consultas SQL do portal do Azure para conectar e consultar dados](sql-database-connect-query-portal.md)|
|Distinção de contagem aproximada|Para obter informações, consulte [distinção de contagem aproximada](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)|
|Modo de lote em Rowstore (no nível de compatibilidade 150)|Para obter informações, consulte [modo em lotes em Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)|
|Comentários de concessão de memória (modo de linha) (em nível de compatibilidade 150)|Para obter informações, consulte [comentários de concessão de memória (modo de linha)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)|
|Tabela variável adiada compilação (no nível de compatibilidade 150)|Para obter informações, consulte [compilação adiada variável da tabela](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)|
|Análise de SQL|Para obter informações, consulte [análise de SQL do Azure](../azure-monitor/insights/azure-sql.md)|
| Suporte a fuso horário para instâncias gerenciadas|Para obter mais informações, consulte [fuso horário no banco de dados de instância gerenciada do SQL](sql-database-managed-instance-timezone.md)|
|||

## <a name="april-2019"></a>Abril de 2019

### <a name="service-improvements"></a>Melhorias nos serviços

| Melhorias nos serviços | Detalhes |
| --- | --- |
| Pontos de extremidade públicos para a instância gerenciada | Para obter mais informações, consulte [usando o Azure SQL Database managed instância forma segura com o ponto de extremidade público](sql-database-managed-instance-public-endpoint-securely.md)
| Suporte a fuso horário para a instância gerenciada | Para obter mais informações, consulte [fuso horário no banco de dados de instância gerenciada do SQL (versão prévia)](sql-database-managed-instance-timezone.md)

### <a name="documentation-improvements"></a>Melhorias na documentação

| Melhorias na documentação | Detalhes |
| --- | --- |
| Pontos de extremidade públicos para a instância gerenciada | Para obter mais informações, consulte [usando o Azure SQL Database managed instância forma segura com o ponto de extremidade público](sql-database-managed-instance-public-endpoint-securely.md)
| Suporte a fuso horário para a instância gerenciada | Para obter mais informações, consulte [fuso horário no banco de dados de instância gerenciada do SQL (versão prévia)](sql-database-managed-instance-timezone.md)

## <a name="march-2019"></a>Março de 2019

### <a name="service-improvements"></a>Melhorias nos serviços

| Melhorias nos serviços | Detalhes |
| --- | --- |
| Disponibilidade geral: Suporte de expansão de leitura para Banco de Dados SQL do Azure | Para obter mais informações, consulte [expansão de leitura](sql-database-read-scale-out.md)|
| &nbsp; |

### <a name="documentation-improvements"></a>Melhorias na documentação

| Melhorias na documentação | Detalhes |
| --- | --- |
| Suporte a fuso horário para instâncias gerenciadas|Para obter mais informações, consulte [fuso horário no banco de dados de instância gerenciada do SQL](sql-database-managed-instance-timezone.md)|
| Adicionado log limites para bancos de dados únicos|Para obter mais informações, consulte [limites de recursos de vCore do banco de dados individual](sql-database-vcore-resource-limits-single-databases.md).|
| Adicionado log limites para pools Elásticos e bancos de dados em pool|Para obter mais informações, consulte [vCore limites de recursos dos pools Elásticos](sql-database-vcore-resource-limits-elastic-pools.md).|
| Governança de taxa de log de transação adicionada| Adicionado novo conteúdo para [governança de taxa de log de transação](sql-database-resource-limits-database-server.md#transaction-log-rate-governance).|
| Exemplos atualizados do PowerShell para bancos de dados únicos e pools Elásticos para usar o módulo az.sql | Para obter mais informações, consulte [amostras do PowerShell para bancos de dados únicos e pools Elásticos](sql-database-powershell-samples.md#single-database-and-elastic-pools).|
| &nbsp; |

## <a name="february-2019"></a>fevereiro de 2019

### <a name="service-improvements"></a>Melhorias nos serviços

| Melhorias nos serviços | Detalhes |
| --- | --- |
|Criando um índice online retomável agora está geralmente disponível| Para obter mais informações, consulte [Create Index](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql).|
|Suporte à instância gerenciada para tabelas de rotas aprimorado| Para obter mais informações, consulte [requisitos de rede](sql-database-managed-instance-connectivity-architecture.md#network-requirements).|
|Renomeação do banco de dados com suporte na instância gerenciada | Para obter mais detalhes, consulte o [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) e [sp_rename](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql) sintaxe.|
|Banco de dados SQL como uma fonte de dados de referência para o Stream Analytics. | Para obter mais informações, consulte [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/).|
|Assistência de migração de dados adiciona suporte para a instância gerenciada. |Para obter mais informações, consulte [o que há de novo no DMA](https://docs.microsoft.com/sql/dma/dma-whatsnew).|
|SQL Server Migration Assistant adiciona suporte para avaliação de prontidão de destino para a instância gerenciada. | Para obter mais informações, consulte [SQL Server Migration Assistant](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant).
|Serviço de migração de dados oferece suporte à migração de RDS do Amazon para a instância gerenciada | Para obter mais informações, confira [Tutorial: Migrar RDS SQL Server para o banco de dados SQL ou uma instância gerenciada do Azure SQL Database online usando o DMS](../dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md).|
| &nbsp; |

### <a name="documentation-improvements"></a>Melhorias na documentação

| Melhorias na documentação | Detalhes |
| --- | --- |
|Adicionando gerenciados esclarecimentos de opção de implantação de instância|Atualizado muitos artigos para esclarecer a aplicabilidade para o banco de dados individual, pool Elástico e opções de implantação de instância gerenciada. |
|Tamanhos de tempdb atualizado para o modelo de compra baseado em DTU | Para obter mais informações, consulte [banco de dados Tempdb no banco de dados SQL](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
|Atualizado de importação e exportação com o arquivo bacpac para o suporte da instância gerenciada| Para obter mais informações, consulte [importação de BACPAC](sql-database-import.md) e [exportar para o BACPAC](sql-database-export.md). |
| &nbsp; |


## <a name="january-2019"></a>Janeiro de 2019

### <a name="service-improvements"></a>Melhorias nos serviços

| Melhorias nos serviços | Detalhes |
| --- | --- |
| Opções de granularidade adicional para os recursos de computação | Camadas para de serviço de uso geral e comercialmente crítico [bancos de dados únicos](sql-database-vcore-resource-limits-single-databases.md) e [pools Elásticos](sql-database-vcore-resource-limits-elastic-pools.md) agora tem mais opções de computação refinado.|
| Exibindo registros de auditoria para a instância gerenciada no portal do Azure | Exibindo [registros para instâncias gerenciadas de auditoria](sql-database-managed-instance-auditing.md) no Azure portal agora tem suporte.|
| Recurso de detecção de ameaças avançadas renomeado para segurança avançada de dados | O recurso de detecção de ameaças avançadas renomeado para [avançadas de segurança de dados](sql-advanced-threat-protection.md) para instâncias gerenciadas, pools Elásticos e bancos de dados individuais. |
| &nbsp; |

### <a name="documentation-improvements"></a>Melhorias na documentação

| Melhorias na documentação | Detalhes |
| --- | --- |
| Replicação transacional e instâncias gerenciadas | Adicionado artigo sobre como usar [replicação transacional com instâncias gerenciadas](replication-with-sql-database-managed-instance.md) |
| Adição do Azure AD com o tutorial de instância gerenciada | Isso [Azure AD com a instância gerenciada](sql-database-managed-instance-aad-security-tutorial.md) tutorial mostra que você precisa configurar e testar o gerenciado de segurança da instância usando logons do AD do Azure. |
| Conteúdo atualizado para a automação de trabalho usando scripts Transact-SQL | Atualizado e esclareceu o conteúdo para o uso [automação de trabalho usando scripts Transact-SQL](sql-database-job-automation-overview.md) para instâncias gerenciadas, pools Elásticos e bancos de dados únicos |
| Conteúdo de segurança para instâncias gerenciadas atualizado | Atualizado e esclareceu o conteúdo para o [modelo de segurança para instâncias gerenciadas](sql-database-security-overview.md), comparações e contrastes com o modelo de segurança para bancos de dados únicos e pools Elásticos |
| Atualizado todos os guias de início rápido e tutoriais | Todos os guias de início rápido e tutoriais do [documentação](https://docs.microsoft.com/azure/sql-database) foram atualizadas e atualizada para coincidir com as alterações no portal do Azure |
| Guias de visão geral de início rápido adicionado | Adicionado um guia de visão geral do guia de início rápido para [bancos de dados únicos](sql-database-quickstart-guide.md) e para [instâncias gerenciadas](sql-database-managed-instance-quickstart-guide.md) |
| Glossário de banco de dados SQL adicionado de termos | Isso [Glossário de termos](sql-database-glossary-terms.md) artigo fornece uma lista definitiva de termos de banco de dados SQL e links para a página principal conceitual que explica o termo no contexto. |
| &nbsp; |

## <a name="contribute-to-content-improvement"></a>Contribuir com conteúdo de melhoria

O conjunto de documentação do SQL Azure é um software livre. Trabalhar com software livre oferece várias vantagens:

- Planejamento de repositórios de código-fonte aberto em obter comentários sobre quais documentos são mais necessários.
- Análise de repositórios de código-fonte aberto em publicar o conteúdo mais útil em nossa primeira versão.
- Atualização de repositórios de código-fonte aberto no aberto para tornar mais fácil melhorar continuamente o conteúdo.

Para contribuir com conteúdo de documentação do banco de dados SQL, consulte a [visão geral do guia de Colaborador de Microsoft Docs](https://docs.microsoft.com/contribute/). A experiência do usuário no [docs.microsoft.com](https://docs.microsoft.com/) integra [GitHub](https://github.com/) fluxos de trabalho diretamente para facilitar ainda mais. Comece [editando o documento que você está exibindo](https://docs.microsoft.com/contribute/#quick-edits-to-existing-documents). Ou então, ajude [analisando novos tópicos](https://docs.microsoft.com/contribute/#review-open-prs), ou [criar problemas de qualidade](https://docs.microsoft.com/contribute/#create-quality-issues).
