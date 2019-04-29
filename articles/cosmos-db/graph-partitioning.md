---
title: Particionamento de dados na API Gremlin do Azure Cosmos DB
description: Saiba como usar um gráfico particionado no Azure Cosmos DB. Este artigo também descreve os requisitos e as melhores práticas para um gráfico particionado.
author: rockboyfor
ms.author: v-yeche
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
origin.date: 12/06/2018
ms.date: 03/18/2019
ms.custom: seodec18
ms.openlocfilehash: f1e486a302b440d819e15ef86f8d76ea5e50d201
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888395"
---
<!--Verify sucessfully-->
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Usando um gráfico particionado no Azure Cosmos DB

Um dos principais recursos da API do Gremlin no Azure Cosmos DB é a capacidade de lidar com gráficos em grande escala por meio de dimensionamento horizontal. O dimensionamento horizontal é alcançada por meio do [particionamento das funcionalidades no Azure Cosmos DB](partition-data.md). Os contêineres podem ser dimensionados independentemente em termos de armazenamento e taxa de transferência. É possível criar contêineres no Azure Cosmos DB que podem ser dimensionados automaticamente para armazenar dados de gráfico. Os dados são balanceados automaticamente com base na **chave de partição** especificada.

Neste documento, as informações específicas sobre como os bancos de dados do gráfico são particionados serão descritas junto com suas implicações para os vértices (ou nós) e bordas.

## <a name="requirements-for-partitioned-graph"></a>Requisitos para o gráfico particionado

A seguir estão os detalhes que precisam ser compreendidos ao se criar um contêiner de gráfico particionado:

- **O particionamento será necessário** se o contêiner tiver que armazenar mais de 10 GB em tamanho ou se você quiser alocar mais de 10.000 RUs (unidades de solicitação) por segundo.

- **Os vértices e as bordas são armazenados como documentos JSON**.

- **Vértices exigem uma chave de partição**. Essa chave determina em qual partição o vértice será armazenado por meio de um algoritmo de hash. O nome dessa chave de partição é uma cadeia de caracteres de uma única palavra sem espaços ou caracteres especiais. A chave de partição é definida durante a criação de um novo contêiner e tem um formato: `/partitioning-key-name`.

- **Bordas serão armazenadas com o vértice de origem**. Em outras palavras, para cada vértice, sua chave de partição definirá o local em que ele será armazenado junto com suas bordas de saída. Isso é feito para evitar consultas entre partições ao usar a cardinalidade de `out()` em consultas de gráfico.

- **Consultas de gráfico precisam especificar uma chave de partição**. Para aproveitar ao máximo o particionamento horizontal no Azure Cosmos DB, a chave de partição deve ser especificada quando um único vértice for selecionado, sempre que possível. A seguir estão as consultas para selecionar os vários vértices em um gráfico particionado:

    - `/id` e `/label` não têm suporte como chaves de partição para um contêiner na API do Gremlin.

    - Selecionar um vértice por ID, em seguida, **usar a `.has()` etapa para especificar a propriedade de chave de partição**: 

        ```
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```

    - Selecionar um vértice por **especificação de uma tupla, incluindo o valor da chave de partição e a ID**: 

        ```
        g.V(['partitionKey_value', 'vertex_id'])
        ```

    - Especificar uma **matriz de tuplas de valores de chave de partição e IDs**:

        ```
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```

    - Selecionar um conjunto de vértices e **especificar uma lista de valores de chave de partição**: 

        ```
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Melhores práticas ao usar um gráfico particionado

Use as diretrizes a seguir para garantir o desempenho e a escalabilidade ao usar gráficos particionados com contêineres ilimitados:

- **Sempre especifique o valor de chave de partição ao consultar um vértice**. Obter o vértice de uma partição conhecida é uma maneira de alcançar o desempenho.

- **Use a direção de saída ao consultar as bordas sempre que possível**. Conforme mencionado acima, as bordas são armazenadas com seus vértices de origem na direção de saída. Dessa forma, as chances de reclassificação para consultas entre partições são minimizadas quando os dados e as consultas são criadas com esse padrão em mente.

- **Escolha uma chave de partição que distribuirá uniformemente dados entre as partições**. Essa decisão depende muito do modelo de dados da solução. Leia mais sobre a criação de uma chave de partição apropriada em [Particionamento e escala no Azure Cosmos DB](partition-data.md).

- **Otimize as consultas para obter dados dentro dos limites de uma partição**. Uma estratégia de particionamento ideal seria alinhada com os padrões de consulta. Consultas que obtêm dados de uma única partição fornecem o melhor desempenho possível.

## <a name="next-steps"></a>Próximas etapas

Em seguida, você poderá ler os artigos a seguir:

* Saiba [Como particionar e dimensionar no Azure Cosmos DB](partition-data.md).
* Saiba mais sobre o [Suporte do Gremlin na API do Gremlin](gremlin-support.md).
* Saiba mais sobre [Introdução à API do Gremlin](graph-introduction.md).

<!--Update_Description: new articles on  -->
<!--ms.date: 03/18/2019-->