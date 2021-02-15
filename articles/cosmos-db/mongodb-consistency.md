---
title: Mapeando níveis de consistência para Azure Cosmos DB API para MongoDB
description: Mapeando níveis de consistência para Azure Cosmos DB API para MongoDB.
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: e8ac3e376c8d67e82def3a57910707c6b1433912
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333147"
---
# <a name="consistency-levels-for-azure-cosmos-db-and-the-api-for-mongodb"></a>Níveis de consistência para Azure Cosmos DB e a API para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ao contrário do Azure Cosmos DB, o MongoDB nativo não fornece garantias de consistência definidas com precisão. Em vez disso, o MongoDB nativo permite que os usuários configurem as seguintes garantias de consistência: uma preocupação de gravação, uma preocupação de leitura e a diretiva IsMaster – para direcionar as operações de leitura para réplicas primárias ou secundárias para atingir o nível de consistência desejado.

Ao usar a API do Azure Cosmos DB para MongoDB, o driver do MongoDB trata sua região de gravação como a réplica primária e todas as outras regiões são de leitura de réplica. Você pode escolher qual região associada à sua conta do Azure cosmos como uma réplica primária.

> [!NOTE]
> O modelo de consistência padrão para Azure Cosmos DB é Session. Session é um modelo de consistência centrado no cliente que não tem suporte nativo do Cassandra ou do MongoDB. Para obter mais informações sobre qual modelo de consistência escolher, consulte [níveis de consistência no Azure Cosmos DB](consistency-levels.md)

Ao usar a API do Azure Cosmos DB para MongoDB:

* A preocupação de gravação é mapeada para o nível de consistência padrão configurado em sua conta do Azure Cosmos.

* Azure Cosmos DB mapeará dinamicamente a preocupação de leitura especificada pelo driver do cliente MongoDB para um dos níveis de consistência Azure Cosmos DB configurados dinamicamente em uma solicitação de leitura.  

* Você pode anotar uma região específica associada à sua conta do Azure cosmos como "primária", tornando a região como a primeira região gravável. 

## <a name="mapping-consistency-levels"></a>Mapeamento de níveis de consistência

A tabela a seguir ilustra como as preocupações nativas de gravação/leitura do MongoDB são mapeadas para os níveis de consistência do Azure Cosmos ao usar a API do Azure Cosmos DB para MongoDB:

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="Mapeamento de modelo de consistência do MongoDB" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

Se sua conta do Azure Cosmos estiver configurada com um nível de consistência diferente da consistência forte, você poderá descobrir a probabilidade de que seus clientes possam obter leituras fortes e consistentes para suas cargas de trabalho examinando a métrica do PBS (desatualização limitada) do *Probabilistic* . Essa métrica é exposta no portal do Azure, para obter mais informações, consulte [métrica Monitor Probabilistic Bounded Staleness (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

A desatualização limitada probabilística mostra o quão eventual é a sua coerência eventual. Essa métrica fornece uma percepção da frequência com que você pode obter uma consistência mais forte do que o nível de consistência configurado atualmente em sua conta do Azure Cosmos. Em outras palavras, você pode ver a probabilidade (medida em milissegundos) de obter leituras fortemente consistentes para uma combinação de regiões de gravação e leitura.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a distribuição global e os níveis de consistência para Azure Cosmos DB:

* [Visão geral da distribuição global](distribute-data-globally.md)
* [Visão geral do nível de consistência](consistency-levels.md)
* [Alta disponibilidade](high-availability.md)
