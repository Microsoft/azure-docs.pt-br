---
title: Níveis de consistência e APIs do Azure Cosmos DB
description: Noções básicas dos níveis de coerência entre as APIs no Azure Cosmos DB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.reviewer: sngun
ms.openlocfilehash: 7a8617ae2b01fc89a4c957b8610164a2b53a16f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60892448"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Níveis de consistência e APIs do Azure Cosmos DB

O Azure Cosmos DB oferece suporte nativo para transmissão compatível com o protocolo de APIs para bancos de dados populares. Eles incluem armazenamento Apache Cassandra, MongoDB, Gremlin e tabelas do Azure. Esses bancos de dados não oferecem precisamente os modelos de consistência definidos ou garantias com suporte de SLA para os níveis de consistência. Fornecem normalmente apenas um subconjunto dos cincos modelos de consistência oferecidos pelo Azure Cosmos DB. 

Ao usar a API do SQL, API do Gremlin e API de tabela, o nível de consistência padrão configurado na conta de Cosmos do Azure é usado. 

Ao usar a API Cassandra API ou o Azure Cosmos DB para MongoDB, os aplicativos obter um conjunto completo de níveis de consistência oferecidos pelo Apache Cassandra e MongoDB, respectivamente, com ainda mais forte garantias de consistência e durabilidade. Este documento mostra os níveis de consistência do Azure Cosmos DB correspondentes para o Apache Cassandra e MongoDB níveis de consistência.


## <a id="cassandra-mapping"></a>O mapeamento entre os níveis de consistência do Apache Cassandra e o Azure Cosmos DB

Diferentemente AzureCosmos DB, Apache Cassandra não fornece garantias de consistência definidos com precisão.  Em vez disso, o Apache Cassandra fornece um nível de consistência de gravação e um nível de consistência de leitura, para habilitar as compensações de alta disponibilidade, consistência e latência. Ao usar a API do Cassandra do Azure Cosmos DB: 

* O nível de consistência de gravação do Apache Cassandra é mapeado para o nível de consistência padrão configurado em sua conta do Cosmos do Azure. 

* O Azure Cosmos DB mapeará dinamicamente o nível de consistência de leitura especificado pelo driver do cliente do Cassandra para um dos níveis de consistência do Azure Cosmos DB configurados dinamicamente em uma solicitação de leitura. 

A tabela a seguir ilustra como os níveis de consistência de Cassandra nativos são mapeados para níveis de consistência do Azure Cosmos DB ao usar a API do Cassandra:  

[![Mapeamento do modelo de consistência de Cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>Mapeamento entre os níveis de consistência do MongoDB e o Azure Cosmos DB

Ao contrário do Azure Cosmos DB, o MongoDB nativo não oferece garantias de consistência definidos com precisão. Em vez disso, o MongoDB nativo permite aos usuários configurar as seguintes garantias de consistência: uma preocupação de gravação, uma preocupação de leitura e a diretiva isMaster - para direcionar as operações de leitura para réplicas primárias ou secundárias para atingir o nível desejado de consistência. 

Ao usar a API do Azure Cosmos DB para MongoDB, o driver do MongoDB trata sua região de gravação como a réplica primária e todas as outras regiões são lidas a réplica. Você pode escolher qual região associada à sua conta do Azure Cosmos como uma réplica primária. 

Ao usar a API do Azure Cosmos DB para MongoDB:

* A preocupação de gravação é mapeada para o nível de consistência padrão configurado em sua conta do Cosmos do Azure.
 
* O Azure Cosmos DB dinamicamente mapeará a preocupação de leitura especificada pelo driver do cliente MongoDB para um dos níveis de consistência do Azure Cosmos DB que está configurado dinamicamente em uma solicitação de leitura. 

* É possível anotar uma região específica associada com sua conta do Azure Cosmos como "Mestre", tornando a região como a primeira região gravável. 

A tabela a seguir ilustra como o MongoDB nativo leitura/gravação preocupações são mapeados para os níveis de consistência do Azure Cosmos ao usar a API do Azure Cosmos DB para MongoDB:

[![Mapeamento de modelos de consistência do MongoDB](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre os níveis de coerência e a compatibilidade entre as APIs do Azure Cosmos DB com as APIs de código aberto. Confira os seguintes artigos:

* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)
* [Recursos do MongoDB com suporte da API do MongoDB no Azure Cosmos DB](mongodb-feature-support.md)
* [Recursos do Apache Cassandra suportados pela API do Cassandra do Azure Cosmos DB](cassandra-support.md)