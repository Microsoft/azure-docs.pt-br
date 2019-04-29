---
title: Status do cenário de migração de banco de dados | Microsoft Docs
description: Saiba mais sobre o status dos cenários de migração com suporte pelo serviço de migração de banco de dados do Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/04/2019
ms.openlocfilehash: 4159b2e7af83030f46d5aca150ef99a1380e711f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473001"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Status de cenários de migração com suporte pelo serviço de migração de banco de dados do Azure
Serviço de migração de banco de dados do Azure foi projetado para dar suporte a diferentes cenários de migração (pares de origem/destino) para ambos off-line (uma vez) e migrações on-line (sincronização contínua). A cobertura de cenário fornecida pelo serviço de migração de banco de dados do Azure está sendo estendida ao longo do tempo. Novos cenários são adicionados regularmente. Este artigo identifica os cenários de migração com suporte no momento pelo serviço de migração de banco de dados do Azure e o status (versão prévia privada, visualização pública ou disponibilidade geral) para cada cenário.

## <a name="offline-versus-online-migrations"></a>Migrações offline versus online
Com o serviço de migração de banco de dados do Azure, você pode fazer uma migração online ou offline. Nas migrações *offline*, o tempo de inatividade do aplicativo começa quando a migração inicia. Para limitar o tempo de inatividade para o tempo necessário para transferir para o novo ambiente quando a migração for concluída, use uma *online* migração. É recomendável para testar uma migração offline para determinar se o tempo de inatividade é aceitável; Caso contrário, fazer uma migração online.

## <a name="migration-scenario-status"></a>Status do cenário de migração
O status dos cenários de migração com suporte pelo serviço de migração de banco de dados do Azure varia de acordo com o tempo. Em geral, os cenários são liberados primeiro no **versão prévia privada**. Participação no modo de visualização particular exige que os clientes enviar uma indicação por meio de [site de visualização de DMS](https://aka.ms/dms-preview). Após a visualização privada, o status de cenário muda para **visualização pública**. Os usuários do serviço de migração de banco de dados do Azure podem testar os cenários de migração em visualização pública diretamente da interface do usuário. Inscreva-se de não é necessária.  No entanto, os cenários de migração em visualização pública podem não estar disponíveis em todas as regiões e poderão sofrer alterações adicionais antes do lançamento final. Após a visualização pública, o status de cenário muda para **geralmente disponibilidade**. Disponibilidade geral (GA) é o status da versão final e a funcionalidade está concluída e acessível a todos os usuários.

## <a name="migration-scenario-support"></a>Suporte para o cenário de migração
As tabelas a seguir mostram quais cenários de migração são suportados ao usar o serviço de migração de banco de dados do Azure.

> [!NOTE]
> Se um cenário está listado como tendo suporte abaixo, mas não aparece na interface do usuário, entre em contato com a [Equipe de migração de dados](mailto:datamigrationteam@microsoft.com) para saber mais.

> [!IMPORTANT]
> Para exibir todos os cenários com suporte no momento pelo serviço de migração de banco de dados do Azure na versão prévia privada, consulte o [site de visualização de DMS](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Suporte para a migração offline (única)
A tabela a seguir mostra o suporte para o Serviço de Migração de Banco de Dados do Azure em migrações offline.

| Destino  | Fonte | Suporte | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **BD SQL do Azure** | SQL Server | ✔ | GA |
|   | SQL para RDS |  |  |
|   | Oracle |  |  |
| **MI do BD SQL do Azure** | SQL Server | ✔ | GA |
|   | SQL para RDS |  |  |
|   | Oracle |  |   |
| **VM do SQL do Azure** | SQL Server | ✔ | GA |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | Versão prévia pública |
| **BD do Azure para MySQL** | MySQL |   |   |
|   | MySQL para RDS |   |   |
| **Banco de Dados do Azure para PostgreSQL** | PostgreSQL |  |
|  | PostgreSQL para RDS |   |   |

### <a name="online-continuous-sync-migration-support"></a>Suporte à migração online (sincronização contínua)
A tabela a seguir mostra o suporte para o Serviço de Migração de Banco de Dados do Azure em migrações online.

| Destino  | Fonte | Suporte | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **BD SQL do Azure** | SQL Server | ✔ | GA |
|   | SQL para RDS | ✔ | GA |
|   | Oracle |  |  |
| **MI do BD SQL do Azure** | SQL Server | ✔ | GA |
|   | SQL para RDS | ✔ | GA |
|   | Oracle | ✔ | Visualização particular |
| **VM do SQL do Azure** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | Versão prévia pública |
| **BD do Azure para MySQL** | MySQL | ✔ | GA |
|   | MySQL para RDS | ✔ | GA |
| **Banco de Dados do Azure para PostgreSQL** | PostgreSQL | ✔ | GA |
|   | PostgreSQL para RDS | ✔ | GA |
|   | Oracle | ✔ | Visualização particular |

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral do serviço de migração de banco de dados do Azure e disponibilidade regional, consulte o artigo [o que é o serviço de migração de banco de dados do Azure](dms-overview.md).
