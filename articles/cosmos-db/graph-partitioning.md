---
title: Particionamento de dados na API Gremlin do Azure Cosmos DB
description: Saiba como usar um gráfico particionado no Azure Cosmos DB. Este artigo também descreve os requisitos e as melhores práticas para um gráfico particionado.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 4d76bdcb385ed2fe4b8a697f24187b8e3d5addbc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99988759"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Usando um gráfico particionado no Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Um dos principais recursos da API do Gremlin no Azure Cosmos DB é a capacidade de lidar com gráficos em grande escala por meio de dimensionamento horizontal. Os contêineres podem ser dimensionados independentemente em termos de armazenamento e taxa de transferência. É possível criar contêineres no Azure Cosmos DB que podem ser dimensionados automaticamente para armazenar dados de gráfico. Os dados são balanceados automaticamente com base na **chave de partição** especificada.

O particionamento é feito internamente se espera-se que o contêiner armazene mais de 20 GB de tamanho ou se você deseja alocar mais de 10.000 unidades de solicitação por segundo (RUs). Os dados são particionados automaticamente com base na chave de partição que você especificar. A chave de partição será necessária se você criar contêineres de grafo do portal do Azure ou das versões 3. x ou superior dos drivers Gremlin. A chave de partição não será necessária se você usar 2. x ou versões inferiores de drivers Gremlin.

Os mesmos princípios gerais do [Azure Cosmos DB mecanismo de particionamento](partitioning-overview.md) se aplicam a algumas otimizações específicas de grafo descritas abaixo.

:::image type="content" source="./media/graph-partitioning/graph-partitioning.png" alt-text="Particionamento de grafo." border="false":::

## <a name="graph-partitioning-mechanism"></a>Mecanismo de particionamento de grafo

As diretrizes a seguir descrevem como a estratégia de particionamento no Azure Cosmos DB Opera:

- **Os vértices e as bordas são armazenados como documentos JSON**.

- **Vértices exigem uma chave de partição**. Essa chave determina em qual partição o vértice será armazenado por meio de um algoritmo de hash. O nome da propriedade de chave de partição é definido ao criar um novo contêiner e ele tem um formato: `/partitioning-key-name` .

- **Bordas serão armazenadas com o vértice de origem**. Em outras palavras, para cada vértice, sua chave de partição definirá o local em que ele será armazenado junto com suas bordas de saída. Essa otimização é feita para evitar consultas entre partições ao usar a `out()` cardinalidade em consultas de grafo.

- **As bordas contêm referências aos vértices para os quais apontam**. Todas as bordas são armazenadas com as chaves de partição e as IDs dos vértices aos quais estão apontando. Essa computação faz com que todas as `out()` consultas de direção sempre sejam uma consulta particionada com escopo e não uma consulta de partição cruzada.

- **Consultas de gráfico precisam especificar uma chave de partição**. Para aproveitar ao máximo o particionamento horizontal no Azure Cosmos DB, a chave de partição deve ser especificada quando um único vértice for selecionado, sempre que possível. A seguir estão as consultas para selecionar os vários vértices em um gráfico particionado:

    - `/id` e `/label` não têm suporte como chaves de partição para um contêiner na API do Gremlin.


    - Selecionar um vértice por ID, em seguida, **usar a `.has()` etapa para especificar a propriedade de chave de partição**:

        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```

    - Selecionar um vértice por **especificação de uma tupla, incluindo o valor da chave de partição e a ID**:

        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```

    - Especificar uma **matriz de tuplas de valores de chave de partição e IDs**:

        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```

    - Selecionar um conjunto de vértices com suas IDs e **especificar uma lista de valores de chave de partição**:

        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Usando a **estratégia de partição** no início de uma consulta e especificando uma partição para o escopo do restante da consulta Gremlin:

        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Melhores práticas ao usar um gráfico particionado

Use as diretrizes a seguir para garantir o desempenho e a escalabilidade ao usar gráficos particionados com contêineres ilimitados:

- **Sempre especifique o valor de chave de partição ao consultar um vértice**. Obter o vértice de uma partição conhecida é uma maneira de alcançar o desempenho. Todas as operações de adjacência subsequentes sempre terão o escopo definido para uma partição, uma vez que as bordas contêm a ID de referência e a chave de partição para seus vértices de destino.

- **Use a direção de saída ao consultar as bordas sempre que possível**. Conforme mencionado acima, as bordas são armazenadas com seus vértices de origem na direção de saída. Dessa forma, as chances de reclassificação para consultas entre partições são minimizadas quando os dados e as consultas são criadas com esse padrão em mente. Ao contrário, a `in()` consulta será sempre uma consulta de Fan-out cara.

- **Escolha uma chave de partição que distribuirá de forma uniforme os dados entre partições**. Essa decisão depende muito do modelo de dados da solução. Leia mais sobre a criação de uma chave de partição apropriada em [Particionamento e escala no Azure Cosmos DB](partitioning-overview.md).

- **Otimize as consultas para obter dados dentro dos limites de uma partição**. Uma estratégia de particionamento ideal seria alinhada com os padrões de consulta. Consultas que obtêm dados de uma única partição fornecem o melhor desempenho possível.

## <a name="next-steps"></a>Próximas etapas

Em seguida, você poderá ler os artigos a seguir:

* Saiba [Como particionar e dimensionar no Azure Cosmos DB](partitioning-overview.md).
* Saiba mais sobre o [Suporte do Gremlin na API do Gremlin](gremlin-support.md).
* Saiba mais sobre [Introdução à API do Gremlin](graph-introduction.md).
