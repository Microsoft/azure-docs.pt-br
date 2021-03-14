---
title: Migração do SSIS com o Azure SQL Instância Gerenciada como o destino da carga de trabalho do banco de dados
description: Migração do SSIS com o Azure SQL Instância Gerenciada como o destino da carga de trabalho do banco de dados.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 3d2bc60f8ba7120f8d962500c06be50e905c11a5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373582"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>Migração do SSIS com o Azure SQL Instância Gerenciada como o destino da carga de trabalho do banco de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ao migrar cargas de trabalho de banco de dados de uma instância SQL Server para o SQL do Azure Instância Gerenciada, você deve estar familiarizado com o [serviço de migração do Azure data](../dms/dms-overview.md)(DMS) e as [topologias de rede para SQL instância gerenciada migrações usando DMS](../dms/resource-network-topologies.md).

Este artigo se concentra na migração de pacotes do SSIS (serviço de integração do SQL Server) armazenados no catálogo do SSIS (SSISDB) e em trabalhos de SQL Server Agent que agendam execuções de pacotes SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrar catálogo do SSIS (SSISDB)

A migração do SSISDB pode ser feita usando DMS, conforme descrito no artigo: [migrar pacotes do SSIS para o SQL instância gerenciada](../dms/how-to-migrate-ssis-packages-managed-instance.md).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>Trabalhos do SSIS para o SQL Instância Gerenciada Agent

O SQL Instância Gerenciada tem um Agendador nativo de primeira classe, assim como SQL Server Agent local.  Você pode [executar pacotes do SSIS por meio do Azure SQL instância gerenciada Agent](how-to-invoke-ssis-package-managed-instance-agent.md).

Como uma ferramenta de migração para trabalhos do SSIS ainda não está disponível, eles precisam ser migrados do SQL Server Agent local para o SQL Instância Gerenciada Agent por meio de scripts/cópia manual.

## <a name="additional-resources"></a>Recursos adicionais

- [Azure Data Factory](./introduction.md)
- [Integration Runtime do Azure-SSIS](./create-azure-ssis-integration-runtime.md)
- [Serviço de Migração de Banco de Dados do Azure](../dms/dms-overview.md)
- [Topologias de rede para migrações do SQL Instância Gerenciada usando DMS](../dms/resource-network-topologies.md)
- [Migrar pacotes do SSIS para um SQL Instância Gerenciada](../dms/how-to-migrate-ssis-packages-managed-instance.md)

## <a name="next-steps"></a>Próximas etapas

- [Conectar-se ao SSISDB no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Executar pacotes do SSIS implantados no Azure](/sql/integration-services/lift-shift/ssis-azure-run-packages)