---
title: Níveis de consistência do Apache Cassandra e Azure Cosmos DB
description: Os níveis de consistência do Apache Cassandra e Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 3107610215d5b37c43124ce4129b2eb5437e3b62
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97516844"
---
# <a name="apache-cassandra-and-azure-cosmos-db-cassandra-api-consistency-levels"></a>API do Cassandra níveis de consistência do Apache Cassandra e Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Ao contrário de Azure Cosmos DB, o Apache Cassandra não fornece nativamente garantias de consistência definidas com precisão. Em vez disso, o Apache Cassandra fornece um nível de consistência de gravação e um nível de consistência de leitura para habilitar a alta disponibilidade, consistência e compensações de latência. Ao usar o API do Cassandra do Azure Cosmos DB:

* O nível de consistência de gravação do Apache Cassandra é mapeado para o nível de consistência padrão configurado em sua conta do Azure Cosmos. A consistência de uma operação de gravação (CL) não pode ser alterada em uma base por solicitação.

* Azure Cosmos DB mapeará dinamicamente o nível de consistência de leitura especificado pelo driver de cliente Cassandra para um dos níveis de consistência Azure Cosmos DB configurados dinamicamente em uma solicitação de leitura.

## <a name="multi-region-writes-vs-single-region-writes"></a>Gravações de várias regiões versus gravações de região única

O banco de dados Apache Cassandra é um sistema de vários mestres por padrão e não fornece uma opção pronta para uso para gravações de região única com replicação de várias regiões para leituras. No entanto, Azure Cosmos DB fornece a capacidade de ter uma única região ou configurações de gravação de [várias regiões](how-to-multi-master.md) . Uma das vantagens de ser capaz de escolher uma única configuração de gravação de região em várias regiões é a eliminação de cenários de conflito entre regiões e a opção de manter uma consistência forte em várias regiões. 

Com gravações de região única, você pode manter uma consistência forte e ainda manter um nível de alta disponibilidade entre regiões com [failover automático](high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage). Nessa configuração, você ainda pode explorar a localidade dos dados para reduzir a latência de leitura por meio do downgrade para a consistência eventual em uma base de solicitação. Além desses recursos, a plataforma de Azure Cosmos DB também fornece a capacidade de habilitar a [redundância de zona](high-availability.md#availability-zone-support) ao selecionar uma região. Portanto, diferentemente do Apache Cassandra nativo, Azure Cosmos DB permite que você navegue pelo [espectro](consistency-levels.md#rto) da teorema de compensação de ponta com mais granularidade.

## <a name="mapping-consistency-levels"></a>Mapeamento de níveis de consistência

A plataforma de Azure Cosmos DB fornece um conjunto de cinco configurações de consistência orientadas a casos de uso de negócios bem definidas com relação à replicação e às compensações definidas pelo [Cap teorema](https://en.wikipedia.org/wiki/CAP_theorem) e [PACLC teorema](https://en.wikipedia.org/wiki/PACELC_theorem). Como essa abordagem difere significativamente do Apache Cassandra, recomendamos que você reveja e entenda Azure Cosmos DB configurações de consistência em nossa [documentação](consistency-levels.md)ou assista a este breve guia de [vídeo](https://www.youtube.com/watch?v=t1--kZjrG-o) para entender as configurações de consistência na plataforma Azure Cosmos DB.

A tabela a seguir ilustra os possíveis mapeamentos entre o Apache Cassandra e os níveis de consistência Azure Cosmos DB ao usar o API do Cassandra. Isso mostra as configurações para uma única região, leituras de várias regiões com gravações de região única e gravações de várias regiões.

> [!NOTE]
> Esses não são mapeamentos exatos. Em vez disso, fornecemos os análogos mais próximos ao Apache Cassandra e desambiguamos as diferenças qualitativas na coluna mais à direita. Conforme mencionado acima, é recomendável revisar [as configurações de consistência](consistency-levels.md)de Azure Cosmos DB. 

:::image type="content" source="./media/cassandra-consistency/account.png" alt-text="Mapeamento de nível de conta de consistência Cassandra" lightbox="./media/cassandra-consistency/account.png" :::

:::image type="content" source="./media/cassandra-consistency/dynamic.png" alt-text="Mapeamento dinâmico de consistência do Cassandra" lightbox="./media/cassandra-consistency/dynamic.png" :::

Se sua conta do Azure Cosmos estiver configurada com um nível de consistência diferente da consistência forte, você poderá descobrir a probabilidade de que seus clientes possam obter leituras fortes e consistentes para suas cargas de trabalho examinando a métrica do PBS (desatualização limitada) do *Probabilistic* . Essa métrica é exposta no portal do Azure, para obter mais informações, consulte [métrica Monitor Probabilistic Bounded Staleness (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

A desatualização limitada probabilística mostra o quão eventual é a sua coerência eventual. Essa métrica fornece uma percepção da frequência com que você pode obter uma consistência mais forte do que o nível de consistência configurado atualmente em sua conta do Azure Cosmos. Em outras palavras, você pode ver a probabilidade (medida em milissegundos) de obter leituras fortemente consistentes para uma combinação de regiões de gravação e leitura.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a distribuição global e os níveis de consistência para Azure Cosmos DB:

* [Visão geral da distribuição global](distribute-data-globally.md)
* [Visão geral do nível de consistência](consistency-levels.md)
* [Alta disponibilidade](high-availability.md)
