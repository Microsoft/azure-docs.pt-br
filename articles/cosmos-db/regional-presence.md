---
title: Presença regional com Azure Cosmos DB
description: Este artigo explica sobre a presença regional do Azure Cosmos DB e diferentes ambientes de nuvem.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 775ca8c57a9a444df2f7d0a4df4224b7eba9d677
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384031"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Presença regional com Azure Cosmos DB

Azure Cosmos DB é um serviço fundamental no Azure, e, por padrão, está sempre disponível em todas as regiões, onde o Azure está disponível. Atualmente, o Azure está disponível em [54 regiões](https://azure.microsoft.com/global-infrastructure/regions/) em todo o mundo. 

[![Regiões onde o Azure Cosmos DB está disponível](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

O Cosmos DB está disponível em todos os cinco ambientes distintos de nuvem do Azure disponíveis para os clientes:

* **Nuvem pública do Azure**, que está disponível globalmente.

* O **Azure China 21vianet** está disponível por meio de uma parceria exclusiva entre a Microsoft e a 21vianet, um dos maiores provedores de Internet do país na China.

* **Azure Alemão** fornece serviços sob um modelo de truste de dados, que garante que os dados de clientes permaneçam na Alemanha sob o controle da T-Systems International GmbH, uma subsidiária da Deutsche Telecom, atuando como depositária de dados alemã.

* **Azure Governamental** está disponível em quatro regiões nos Estados Unidos para agências do governo dos EUA e seus parceiros. 

* O **Azure governamental para o departamento de defesa (DoD)** está disponível em duas regiões do Estados Unidos para o departamento de defesa dos EUA.

## <a name="regional-presence-with-global-distribution"></a>Presença regional com a distribuição global

Todas as APIs expostas por Azure Cosmos DB (incluindo SQL, MongoDB, Cassandra, Gremlin e Table) estão disponíveis em todas as regiões do Azure por padrão. Por exemplo, você pode ter APIs MongoDB e Cassandra expostas por Azure Cosmos DB não apenas em todas as regiões globais do Azure, mas também em nuvens soberanass como China, Alemanha, governo e as regiões do departamento de defesa (DoD).

Azure Cosmos DB é um serviço de banco de dados [distribuído globalmente](distribute-data-globally.md) . Você pode associar qualquer número de regiões do Azure à sua conta do Azure Cosmos e seus dados serão replicados de maneira automática e transparente. Você pode adicionar ou remover uma região da sua conta do Azure Cosmos a qualquer momento. Com o recurso de distribuição global turnkey e o protocolo de replicação multi-masterizado, o Azure Cosmos DB oferece latências de leitura e gravação inferiores a 10 ms no 99º percentil, 99,999 leitura e disponibilidade de gravação e capacidade de dimensionar de forma elástica o throughput provisionado para leituras e gravações em todos os regiões associadas à sua conta do Azure Cosmos. O Azure Cosmos DB também oferece cinco modelos de consistência bem definidos e você pode optar por aplicar um modelo de consistência específico aos seus dados. Por fim, Azure Cosmos DB é o único serviço de banco de dados do setor que fornece um [SLA (contrato de nível de serviço)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) abrangente que abrange a taxa de transferência provisionada, latência no 99 º percentil, alta disponibilidade e consistência. Os recursos acima estão disponíveis em todas as nuvens do Azure.

## <a name="next-steps"></a>Próximas etapas

Agora você pode aprender sobre os principais conceitos do Azure Cosmos DB com os seguintes artigos:

* [Distribuição de dados globais](distribute-data-globally.md)
* [Como gerenciar uma conta do Azure Cosmos DB](manage-account.md)
* [Taxa de transferência de provisão para contêineres do Cosmos do Azure e bancos de dados](set-throughput.md)
* [SLA do Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
