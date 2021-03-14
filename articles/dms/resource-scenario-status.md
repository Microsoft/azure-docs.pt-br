---
title: Status do cenário de migração de banco de dados
titleSuffix: Azure Database Migration Service
description: Saiba mais sobre o status dos cenários de migração com suporte pelo serviço de migração de banco de dados do Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 07/08/2020
ms.openlocfilehash: 719dcecf3091e03fbcbe6a82d4c618f54243c4e1
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102215333"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Status dos cenários de migração com suporte pelo serviço de migração de banco de dados do Azure

O serviço de migração de banco de dados do Azure foi projetado para dar suporte a diferentes cenários de migração (pares de origem/destino) para migrações offline (de uma vez) e online (sincronização contínua). A cobertura do cenário fornecida pelo serviço de migração de banco de dados do Azure está sendo estendida ao longo do tempo. Novos cenários são adicionados regularmente. Este artigo identifica os cenários de migração com suporte no momento pelo serviço de migração de banco de dados do Azure e o status (visualização privada, visualização pública ou disponibilidade geral) para cada cenário.

## <a name="offline-versus-online-migrations"></a>Migrações offline versus online

Com o serviço de migração de banco de dados do Azure, você pode fazer uma migração online ou offline. Nas migrações *offline*, o tempo de inatividade do aplicativo começa quando a migração inicia. Para limitar o tempo de inatividade ao horário necessário para passar para o novo ambiente quando a migração for concluída, use uma migração *online* . É recomendável testar uma migração offline para determinar se o tempo de inatividade é aceitável; caso contrário, faça uma migração online.

## <a name="migration-scenario-status"></a>Status do cenário de migração

O status dos cenários de migração com suporte pelo serviço de migração de banco de dados do Azure varia com o tempo. Em geral, os cenários são lançados pela primeira vez em versão **prévia privada**. A participação em visualização privada exige que os clientes enviem uma indicação por meio do [site de visualização do DMS](https://aka.ms/dms-preview). Após a visualização privada, o status do cenário muda para **Visualização pública**. Os usuários do serviço de migração de banco de dados do Azure podem experimentar cenários de migração na visualização pública diretamente da interface do usuário. Nenhuma inscrição é necessária.  No entanto, os cenários de migração na visualização pública podem não estar disponíveis em todas as regiões e podem sofrer alterações adicionais antes da versão final. Após a visualização pública, o status do cenário muda para a **disponibilidade geral**. GA (disponibilidade geral) é o status da versão final e a funcionalidade está completa e acessível a todos os usuários.

## <a name="migration-scenario-support"></a>Suporte para o cenário de migração

As tabelas a seguir mostram quais cenários de migração têm suporte ao usar o serviço de migração de banco de dados do Azure.

> [!NOTE]
> Se um cenário listado como com suporte abaixo não aparecer na interface do usuário, entre em contato com o alias [pedir migrações de banco de dados do Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com) para obter informações adicionais.

> [!IMPORTANT]
> Para exibir todos os cenários com suporte no momento pelo serviço de migração de banco de dados do Azure em versão prévia privada, consulte o [site de visualização do DMS](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Suporte para a migração offline (única)

A tabela a seguir mostra o suporte para o Serviço de Migração de Banco de Dados do Azure em migrações offline.

| Destino  | Fonte | Suporte | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **BD SQL do Azure** | SQL Server | ✔ | GA |
|   | SQL para RDS | X |  |
|   | Oracle | X |  |
| **SQL do Azure para MI do BD** | SQL Server | ✔ | GA |
|   | SQL para RDS | X |  |
|   | Oracle | X |   |
| **VM do SQL do Azure** | SQL Server | ✔ | GA |
|   | Oracle | X |   |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Banco de dados do Azure para MySQL** | MySQL | X |   |
|   | MySQL para RDS | X |   |
| **BD do Azure para PostgreSQL-servidor único** | PostgreSQL | X |
|  | PostgreSQL para RDS | X |   |
| **BD do Azure para PostgreSQL-Citus (hiperescala)** | PostgreSQL | X |
|  | PostgreSQL para RDS | X |   |

### <a name="online-continuous-sync-migration-support"></a>Suporte à migração online (sincronização contínua)

A tabela a seguir mostra o suporte para o Serviço de Migração de Banco de Dados do Azure em migrações online.

| Destino  | Fonte | Suporte | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **BD SQL do Azure** | SQL Server | X | GA |
|   | SQL para RDS | X | GA |
|   | Oracle | X |  |
| **SQL do Azure para MI do BD** | SQL Server | ✔ | GA |
|   | SQL para RDS | X | GA |
|   | Oracle | X |  |
| **VM do SQL do Azure** | SQL Server | X |   |
|   | Oracle  | X |  |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Banco de dados do Azure para MySQL** | MySQL | ✔ | GA |
|   | MySQL para RDS | ✔ | GA |
| **BD do Azure para PostgreSQL-servidor único** | PostgreSQL | ✔ | GA |
|   | BD do Azure para PostgreSQL-servidor único | ✔ | GA |
|   | PostgreSQL para RDS | ✔ | GA |
|   | Oracle | ✔ | Visualização pública (a ser preterida após 1º de maio de 2021) |
| **BD do Azure para PostgreSQL-Citus (hiperescala)** | PostgreSQL | ✔ | GA |
|   | PostgreSQL para RDS | ✔ | GA |

> [!IMPORTANT]
> O cenário de migração "Oracle para banco de dados do Azure para PostgreSQL" (atualmente em visualização) não estará mais disponível após 1º de maio de 2021. Continuaremos a fornecer suporte por meio de ferramentas alternativas (como Ora2pg) e proporcionar a melhor experiência de migração para migrações Oracle para PostgreSQL. Para obter as práticas recomendadas de migração, consulte [guia de migração do Oracle para o banco de dados do Azure para PostgreSQL] ( https://aka.ms/OracletoPGguide) .


## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral do serviço de migração de banco de dados do Azure e da disponibilidade regional, consulte o artigo o [que é o serviço de migração de banco de dados](dms-overview.md)
