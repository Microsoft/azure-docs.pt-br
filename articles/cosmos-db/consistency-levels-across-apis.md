---
title: Níveis de consistência e APIs do Azure Cosmos DB
description: Noções básicas dos níveis de coerência entre as APIs no Azure Cosmos DB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 26cea6243a8b6d06c132325f0b2fe830c4030e9d
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467768"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Níveis de consistência e APIs do Azure Cosmos DB

O Azure Cosmos DB fornece suporte nativo para APIs compatíveis com protocolo de conexão para bancos de dados populares. Isso inclui o MongoDB, o Apache Cassandra, o Gremlin e o armazenamento de tabelas do Azure. Esses bancos de dados não oferecem modelos de consistência definidos com precisão ou garantias com suporte de SLA para os níveis de consistência. Fornecem normalmente apenas um subconjunto dos cincos modelos de consistência oferecidos pelo Azure Cosmos DB. 

Ao usar a API do SQL, a API do Gremlin e o API de Tabela, o nível de consistência padrão configurado na conta do Azure cosmos é usado. 

Ao usar a API do API do Cassandra ou do Azure Cosmos DB para o MongoDB, os aplicativos obtêm um conjunto completo de níveis de consistência oferecidos pelo Apache Cassandra e pelo MongoDB, respectivamente, com uma consistência ainda mais forte e garantias de durabilidade. Este documento mostra os níveis de consistência de Azure Cosmos DB correspondentes para os níveis de consistência do Apache Cassandra e do MongoDB.


## <a id="cassandra-mapping"></a>O mapeamento entre os níveis de consistência do Apache Cassandra e o Azure Cosmos DB

Ao contrário do AzureCosmos DB, o Apache Cassandra não fornece nativamente garantias de consistência definidas com precisão.  Em vez disso, o Apache Cassandra fornece um nível de consistência de gravação e um nível de consistência de leitura para habilitar a alta disponibilidade, consistência e compensações de latência. Ao usar o API do Cassandra do Azure Cosmos DB: 

* O nível de consistência de gravação do Apache Cassandra é mapeado para o nível de consistência padrão configurado em sua conta do Azure Cosmos. 

* Azure Cosmos DB mapeará dinamicamente o nível de consistência de leitura especificado pelo driver de cliente Cassandra para um dos níveis de consistência Azure Cosmos DB configurados dinamicamente em uma solicitação de leitura. 

A tabela a seguir ilustra como os níveis de consistência Cassandra nativos são mapeados para os níveis de consistência do Azure Cosmos DB ao usar o API do Cassandra:  

[![Mapeamento de modelo de consistência Cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>Mapeamento entre os níveis de consistência do MongoDB e do Azure Cosmos DB

Ao contrário do Azure Cosmos DB, o MongoDB nativo não fornece garantias de consistência definidas com precisão. Em vez disso, o MongoDB nativo permite que os usuários configurem as seguintes garantias de consistência: uma preocupação de gravação, uma preocupação de leitura e a diretiva IsMaster – para direcionar as operações de leitura para réplicas primárias ou secundárias para atingir o nível de consistência desejado. 

Ao usar a API do Azure Cosmos DB para MongoDB, o driver do MongoDB trata sua região de gravação como a réplica primária e todas as outras regiões são de leitura de réplica. Você pode escolher qual região associada à sua conta do Azure cosmos como uma réplica primária. 

Ao usar a API do Azure Cosmos DB para MongoDB:

* A preocupação de gravação é mapeada para o nível de consistência padrão configurado em sua conta do Azure Cosmos.
 
* Azure Cosmos DB mapeará dinamicamente a preocupação de leitura especificada pelo driver do cliente MongoDB para um dos níveis de consistência Azure Cosmos DB configurados dinamicamente em uma solicitação de leitura. 

* Você pode anotar uma região específica associada à sua conta do Azure cosmos como "mestre", tornando a região como a primeira região gravável. 

A tabela a seguir ilustra como as preocupações nativas de gravação/leitura do MongoDB são mapeadas para os níveis de consistência do Azure Cosmos ao usar a API do Azure Cosmos DB para MongoDB:

[![Mapeamento de modelo de consistência do MongoDB](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre os níveis de coerência e a compatibilidade entre as APIs do Azure Cosmos DB com as APIs de código aberto. Confira os seguintes artigos:

* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)
* [Recursos do MongoDB com suporte da API do MongoDB no Azure Cosmos DB](mongodb-feature-support.md)
* [Recursos do Apache Cassandra suportados pela API do Cassandra do Azure Cosmos DB](cassandra-support.md)