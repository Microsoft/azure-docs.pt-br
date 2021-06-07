---
title: O que é o Serviço de Migração de Banco de Dados do Azure?
description: Visão geral do Serviço de Migração de Banco de Dados do Azure, que fornece migrações coesas de várias fontes de banco de dados para as plataformas de dados do Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.topic: overview
ms.date: 02/20/2020
ms.openlocfilehash: 328c29afee3752ecb11b83f22d67f20aa3a2c93e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94963004"
---
# <a name="what-is-azure-database-migration-service"></a>O que é o Serviço de Migração de Banco de Dados do Azure?

O Serviço de Migração de Banco de Dados do Azure é um serviço totalmente gerenciado projetado para permitir migrações coesas de várias fontes de banco de dados para as plataformas de dados do Azure com tempo de inatividade mínimo (migrações online).

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Migrar bancos de dados para Azure com ferramentas clássicas

O Serviço de Migração de Banco de Dados do Azure integra algumas funcionalidades dos nossos serviço e ferramentas existentes. Ele fornece aos clientes uma solução abrangente e altamente disponível. O serviço usa o [Data Migration Assistant](/sql/dma/dma-overview) para gerar relatórios de avaliação que fornecem recomendações para orientar você durante as alterações necessárias antes de executar uma migração. Cabe a você executar qualquer correção necessária. Quando você estiver pronto para iniciar o processo de migração, o Serviço de Migração de Banco de Dados do Azure executará todas as etapas necessárias. Você pode acioná-lo e ficar tranquilo quanto aos seus projetos de migração, com a certeza de que o processo aproveita as melhores práticas determinadas pela Microsoft. 

> [!NOTE]
> Usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração online exige a criação de uma instância com base no tipo de preço Premium.

## <a name="regional-availability"></a>Disponibilidade regional

Para obter informações atualizadas sobre a disponibilidade regional do Serviço de Migração de Banco de Dados do Azure, confira [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration).

## <a name="pricing"></a>Preços

Para obter informações atualizadas sobre os preços do serviço de migração de banco de dados do Azure, consulte [preços do serviço de migração de banco de dados do Azure](https://azure.microsoft.com/pricing/details/database-migration/).

## <a name="next-steps"></a>Próximas etapas

* [Status dos cenários de migração compatíveis com o Serviço de Migração de Banco de Dados do Azure](resource-scenario-status.md).
* [Crie uma instância do Serviço de Migração de Banco de Dados do Azure usando o Portal do Azure](quickstart-create-data-migration-service-portal.md).
* [Migre do SQL Server para o Banco de Dados SQL do Azure](tutorial-sql-server-to-azure-sql.md).
* [Visão geral dos pré-requisitos para uso do Serviço de Migração de Banco de Dados do Azure](pre-reqs.md).
* [Perguntas Frequentes sobre como usar o Serviço de Migração de Banco de Dados do Azure](faq.md).
* [Serviços e ferramentas disponíveis para cenários de migração de dados](dms-tools-matrix.md).