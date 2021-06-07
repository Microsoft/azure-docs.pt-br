---
title: Matriz de ferramentas do serviço de migração de banco de dados Azure
description: Conheça os serviços e ferramentas disponíveis para migrar bancos de dados e prestar suporte a várias fases do processo de migração.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: reference
ms.date: 03/03/2020
ms.openlocfilehash: 07cf2695f4d6256e717c7ce1a456d32b25c34ec9
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643751"
---
# <a name="services-and-tools-available-for-data-migration-scenarios"></a>Serviços e ferramentas disponíveis para cenários de migração de dados

Este artigo fornece uma matriz dos serviços e ferramentas da Microsoft e de terceiros disponíveis para ajudá-lo nos vários cenários de migração de banco de dados e dados e tarefas especiais.

As tabelas a seguir identificam os serviços e as ferramentas que você pode usar para planejar com êxito a migração de dados e concluir suas várias fases.

> [!NOTE]
> Nas tabelas a seguir, os itens marcados com um asterisco (*) representam ferramentas de terceiros.

## <a name="business-justification-phase"></a>Fase de justificativa de negócios

| Fonte | Destino | Descobrir /<br/>Inventário | Recomendação de<br/>recomendação | TCO/ROI e<br/>Caso comercial |
| --- | --- | --- | --- | --- |
| SQL Server | BD SQL do Azure | [MAP Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Migrações para Azure](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMA](/sql/dma/dma-overview)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Calculadora de TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
 SQL Server | SQL do Azure para MI do BD | [MAP Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Migrações para Azure](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMA](/sql/dma/dma-overview)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Calculadora de TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | VM de SQL do Azure | [MAP Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Migrações para Azure](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Calculadora de TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Azure Synapse Analytics | [MAP Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Migrações para Azure](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) |  | [Calculadora de TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL para RDS | SQL do Azure para BD, MI, VM |  | [DMA](/sql/dma/dma-overview) | [Calculadora de TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oracle | SQL do Azure para BD, MI, VM | [MAP Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Migrações para Azure](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[MigVisor*](https://www.migvisor.com/) |  |
| Oracle | Azure Synapse Analytics | [MAP Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Migrações para Azure](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Oracle | BD do Azure para PostgreSQL-<br/>Servidor único | [MAP Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Migrações para Azure](https://azure.microsoft.com/services/azure-migrate/) |  |  |
| MongoDB | Cosmos DB | [Cloudamize*](https://www.cloudamize.com/) | [Cloudamize*](https://www.cloudamize.com/) |  |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | SQL do Azure para BD, MI, VM | [Migrações para Azure](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/) | [Calculadora de TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| MySQL | Banco de dados do Azure para MySQL | [Migrações para Azure](https://azure.microsoft.com/services/azure-migrate/) |  | [Calculadora de TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| MySQL para RDS | Banco de dados do Azure para MySQL |  |  | [Calculadora de TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| PostgreSQL | BD do Azure para PostgreSQL-<br/>Servidor único | [Migrações para Azure](https://azure.microsoft.com/services/azure-migrate/) |  | [Calculadora de TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| PostgreSQL para RDS | BD do Azure para PostgreSQL-<br/>Servidor único |  |  | [Calculadora de TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| DB2 | SQL do Azure para BD, MI, VM | [Migrações para Azure](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Access | SQL do Azure para BD, MI, VM | [Migrações para Azure](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Sybase-SAP ASE | SQL do Azure para BD, MI, VM | [Migrações para Azure](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Sybase-SAP IQ | SQL do Azure para BD, MI, VM |  |  |  |
| | | | | |

## <a name="pre-migration-phase"></a>Fase de pré-migração

| Fonte | Destino | Acesso a dados de aplicativos<br/>Avaliação da camada | Banco de dados<br/>Avaliação | Desempenho<br/>Avaliação |
| --- | --- | --- | --- | --- |
| SQL Server | BD SQL do Azure | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [DMA](/sql/dma/dma-overview) | [DMA](/sql/dma/dma-overview)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | SQL do Azure para MI do BD | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [DMA](/sql/dma/dma-overview) | [DMA](/sql/dma/dma-overview)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | VM de SQL do Azure | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [DMA](/sql/dma/dma-overview) | [DMA](/sql/dma/dma-overview)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Azure Synapse Analytics | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) |  |  |
| SQL para RDS | SQL do Azure para BD, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [DMA](/sql/dma/dma-overview) | [DMA](/sql/dma/dma-overview) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090) |
| Oracle | SQL do Azure para BD, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](/sql/ssma/sql-server-migration-assistant) | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Oracle | Azure Synapse Analytics | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](/sql/ssma/sql-server-migration-assistant) | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Oracle | BD do Azure para PostgreSQL-<br/>Servidor único |  | [Ora2Pg*](http://ora2pg.darold.net/start.html) |  |
| MongoDB | Cosmos DB |  | [Cloudamize*](https://www.cloudamize.com/) | [Cloudamize*](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | SQL do Azure para BD, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](/sql/ssma/sql-server-migration-assistant) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/) |  |
| MySQL | Banco de dados do Azure para MySQL |  |  |  |
| MySQL para RDS | Banco de dados do Azure para MySQL |  |  |  |
| PostgreSQL | BD do Azure para PostgreSQL-<br/>Servidor único |  |  |  |
| PostgreSQL para RDS | BD do Azure para PostgreSQL-<br/>Servidor único |  |  |  |
| DB2 | SQL do Azure para BD, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](/sql/ssma/sql-server-migration-assistant) | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Access | SQL do Azure para BD, MI, VM |  | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Sybase-SAP ASE | SQL do Azure para BD, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](/sql/ssma/sql-server-migration-assistant) | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Sybase-SAP IQ | SQL do Azure para BD, MI, VM |  | |  |
| | | | | |

## <a name="migration-phase"></a>Fase de migração

| Fonte | Destino | Esquema | Dados<br/>Está | Dados<br/>Conectar |
| --- | --- | --- | --- | --- |
| SQL Server | BD SQL do Azure | [DMA](/sql/dma/dma-overview)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | SQL do Azure para MI do BD | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | VM de SQL do Azure | [DMA](/sql/dma/dma-overview)<br/>[DMS](https://azure.microsoft.com/services/database-migration/)<br>[Cloudamize*](https://www.cloudamize.com/) | [DMA](/sql/dma/dma-overview)<br/>[DMS](https://azure.microsoft.com/services/database-migration/)<br>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Azure Synapse Analytics |  |  |  |
| SQL para RDS | SQL do Azure para BD, MI, VM | [DMA](/sql/dma/dma-overview) | [DMA](/sql/dma/dma-overview)<br/>[DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | SQL do Azure para BD, MI, VM | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[*](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[*](https://www.ispirer.com/solutions) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Azure Synapse Analytics | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[*](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[*](https://www.ispirer.com/solutions) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | BD do Azure para PostgreSQL-<br/>Servidor único | [*](https://www.ispirer.com/solutions) | [*](https://www.ispirer.com/solutions) | [DMS](https://azure.microsoft.com/services/database-migration/) |
| MongoDB | Cosmos DB | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis Data*](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/talena-inc.talena-solution-template?tab=Overview) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis Data*](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/talena-inc.talena-solution-template?tab=Overview) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis Data*](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/talena-inc.talena-solution-template?tab=Overview)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Cassandra | Cosmos DB | [Imanis Data*](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/talena-inc.talena-solution-template?tab=Overview) | [Imanis Data*](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/talena-inc.talena-solution-template?tab=Overview) | [Imanis Data*](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/talena-inc.talena-solution-template?tab=Overview) |
| MySQL | SQL do Azure para BD, MI, VM | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[*](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[*](https://www.ispirer.com/solutions) | [Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| MySQL | Banco de dados do Azure para MySQL | [Despejo do MySQL*](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| MySQL para RDS | Banco de dados do Azure para MySQL | [Despejo do MySQL*](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| PostgreSQL | BD do Azure para PostgreSQL-<br/>Servidor único | [Despejo do PG*](https://www.postgresql.org/docs/11/static/app-pgdump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| PostgreSQL para RDS | BD do Azure para PostgreSQL-<br/>Servidor único | [Despejo do PG*](https://www.postgresql.org/docs/11/static/app-pgdump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| DB2 | SQL do Azure para BD, MI, VM | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[*](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[*](https://www.ispirer.com/solutions) | [Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Access | SQL do Azure para BD, MI, VM | [SSMA](/sql/ssma/sql-server-migration-assistant) | [SSMA](/sql/ssma/sql-server-migration-assistant) | [SSMA](/sql/ssma/sql-server-migration-assistant) |
| Sybase-SAP ASE | SQL do Azure para BD, MI, VM | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[*](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[*](https://www.ispirer.com/solutions) | [Attunity](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Sybase-SAP IQ | SQL do Azure para BD, MI, VM | [*](https://www.ispirer.com/solutions) | [*](https://www.ispirer.com/solutions) | |
| | | | | |

## <a name="post-migration-phase"></a>Fase de pós-migração

| Fonte | Destino | Otimizar |
| --- | --- | --- |
| SQL Server | BD SQL do Azure | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | SQL do Azure para MI do BD | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | VM de SQL do Azure | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Azure Synapse Analytics |  |
| SQL para RDS | SQL do Azure para BD, MI, VM |  |
| Oracle | SQL do Azure para BD, MI, VM |  |
| Oracle | Azure Synapse Analytics |  |
| Oracle | BD do Azure para PostgreSQL-<br/>Servidor único |  |
| MongoDB | Cosmos DB | [Cloudamize*](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |
| MySQL | SQL do Azure para BD, MI, VM |  |
| MySQL | Banco de dados do Azure para MySQL |  |
| MySQL para RDS | Banco de dados do Azure para MySQL |  |
| PostgreSQL | BD do Azure para PostgreSQL-<br/>Servidor único |  |
| PostgreSQL para RDS | BD do Azure para PostgreSQL-<br/>Servidor único |  |
| DB2 | SQL do Azure para BD, MI, VM |  |
| Access | SQL do Azure para BD, MI, VM |  |
| Sybase-SAP ASE | SQL do Azure para BD, MI, VM |  |
| Sybase-SAP IQ | SQL do Azure para BD, MI, VM |  |
| | | |

## <a name="next-steps"></a>Próximas etapas

Para uma visão geral do serviço de migração de banco de dados do Azure, consulte o artigo o [que é o serviço de migração de banco de dados do Azure](dms-overview.md).