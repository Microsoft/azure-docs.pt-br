---
title: Unidades de solicitação como uma moeda de taxa de transferência e desempenho no Azure Cosmos DB
description: Saiba mais sobre como especificar e estimar os requisitos da Unidade de Solicitação no Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2020
ms.custom: seo-nov-2020
ms.openlocfilehash: 23401885580a3883dc49eccc97c17bbedd9080ab
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187316"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unidades de Solicitação no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Cosmos DB dá suporte a muitas APIs como SQL, MongoDB, Cassandra, Gremlin e Table. Cada API tem seu próprio conjunto de operações de banco de dados. Essas operações variam de simples leituras e gravações de pontos a consultas complexas. Cada operação de banco de dados consome recursos do sistema com base na complexidade da operação.

O custo de todas as operações do banco de dados é normalizado pelo Azure Cosmos DB e expresso por Unidades de Solicitação (ou RUs, abreviado na sigla em inglês). A unidade de solicitação é uma moeda de desempenho que abstrai os recursos do sistema, como CPU, IOPS e memória, necessários para executar as operações de banco de dados com suporte pelo Azure Cosmos DB.

O custo para fazer uma leitura pontual (ou seja, buscar um único item por sua ID e valor de chave de partição) para um item de 1 KB é 1 unidade de solicitação (ou 1 RU). Todas as outras operações do banco de dados são atribuídas de maneira semelhante a um custo usando RUs. Independentemente da API usada para interagir com o contêiner do Azure Cosmos DB, os custos sempre serão medidos por RUs. Se a operação do banco de dados é uma gravação, uma leitura de ponto ou uma consulta, os custos são sempre medidos em RUs.

A imagem a seguir mostra a ideia de alto nível de RUs:

:::image type="content" source="./media/request-units/request-units.png" alt-text="Operações do banco de dados consomem Unidades de Solicitação" border="false":::

Para gerenciar e planejar a capacidade, o Azure Cosmos DB garante que o número de RUs para uma determinada operação de banco de dados em um determinado conjunto de dados seja determinístico. Você pode examinar o cabeçalho de resposta para rastrear o número de RUs consumidas por qualquer operação de banco de dados. Ao entender os [fatores que afetam os encargos de ru](request-units.md#request-unit-considerations) e os requisitos de taxa de transferência do aplicativo, você pode executar o aplicativo de forma econômica.

O tipo de conta do cosmos do Azure que você está usando determina a maneira como o RUs consumido é cobrado. Há três modos nos quais você pode criar uma conta:

1. **Modo de taxa de transferência provisionado**: nesse modo, você provisiona o número de RUs para seu aplicativo por segundo em incrementos de 100 RUs por segundo. Para dimensionar a taxa de transferência provisionada para seu aplicativo, você pode aumentar ou diminuir o número de RUs a qualquer momento em incrementos ou decrementos de 100 RUs. É possível fazer as alterações de maneira programática ou por meio do portal do Azure. Você é cobrado por hora pela quantidade de RUs por segundo que você provisionou. Para saber mais, confira o artigo [taxa de transferência provisionada](set-throughput.md) .

   Você pode provisionar a taxa de transferência em duas granularidades distintas:

   * **Contêineres**: para obter mais informações, consulte [provisionar taxa de transferência em um contêiner Cosmos do Azure](how-to-provision-container-throughput.md).
   * **Bancos** de dados: para obter mais informações, consulte [provisionar taxa de transferência em um Azure Cosmos](how-to-provision-database-throughput.md).

2. **Modo sem servidor**: nesse modo, você não precisa provisionar nenhuma taxa de transferência ao criar recursos em sua conta do Azure Cosmos. No final do período de cobrança, você será cobrado pela quantidade de unidades de solicitação consumidas pelas operações de banco de dados. Para saber mais, confira o artigo [taxa de transferência sem servidor](serverless.md) . 

3. **Modo de dimensionamento automático**: nesse modo, você pode dimensionar automaticamente e instantaneamente a taxa de transferência (ru/s) do seu banco de dados ou contêiner com base no uso, sem afetar a disponibilidade, a latência, a taxa de transferência ou o desempenho da carga de trabalho. Esse modo é adequado para cargas de trabalho de missão crítica que têm padrões de tráfego variáveis ou imprevisíveis e exigem SLAs em alto desempenho e escala. Para saber mais, confira o artigo [taxa de transferência de dimensionamento automático](provision-throughput-autoscale.md) . 

## <a name="request-unit-considerations"></a>Considerações sobre Unidade de Solicitação

Enquanto você estima o número de RUs consumidas por sua carga de trabalho, considere os seguintes fatores:

* **Tamanho do item**: À medida que o tamanho de um item aumenta, o número de RUs consumidas para ler ou gravar o item também aumenta.

* **Indexação de itens**: Por padrão, cada item é indexado automaticamente. Menos RUs serão consumidas se você optar por não indexar alguns de seus itens em um contêiner.

* **Contagem de propriedades do item**: Assumindo que a indexação padrão é usada em todas as propriedades, o número de RUs consumidas para gravar um item aumentará na medida em que a contagem da propriedade do item aumentar.

* **Propriedades indexadas**: Uma política de índice em cada contêiner determina quais propriedades são indexadas por padrão. Para reduzir o consumo de RU para operações de gravação, limite o número de propriedades indexadas.

* **Consistência de dados**: os níveis de consistência forte e limitado de desatualização consomem aproximadamente duas vezes mais RUs durante a execução de operações de leitura em comparação com a de outros níveis de consistência relaxados.

* **Tipo de leituras**: o ponto de leituras de pontos custa significativamente menos RUs do que as consultas.

* **Padrões de consulta**: A complexidade de uma consulta afeta quantas RUs são consumidas por uma operação. Fatores que afetam o custo das operações de consulta incluem: 
 
  * O número de resultados da consulta
  * O número de predicados
  * A natureza dos predicados
  * O número de funções definidas pelo usuário
  * O tamanho dos dados de origem
  * O tamanho do conjunto de resultados
  * Projeções

  A mesma consulta nos mesmos dados sempre custará o mesmo número de RUs em execuções repetidas.

* **Uso de script**: assim como acontece com consultas, procedimentos armazenados e gatilhos consomem RUs com base na complexidade das operações que são executadas. Conforme você desenvolve seu aplicativo, inspecione o [cabeçalho do preço de solicitação](./optimize-cost-reads-writes.md#measuring-the-ru-charge-of-a-request) para entender melhor a capacidade de RU consumida por operação.

## <a name="request-units-and-multiple-regions"></a>Unidades de solicitação e várias regiões

Se você provisionar RUs *' r '* em um contêiner Cosmos (ou banco de dados), o cosmos DB garantirá que o RUS *' r '* esteja disponível em *cada* região associada à sua conta do cosmos. Você não pode atribuir seletivamente RUs a uma região específica. O RUs provisionado em um contêiner Cosmos (ou banco de dados) é provisionado em todas as regiões associadas à sua conta do cosmos.

Supondo que um contêiner Cosmos esteja configurado com RUs *' R '* e que existam *' n'* regiões associadas à conta Cosmos, o RUS total disponível globalmente no contêiner = *R* x *N*.

Sua escolha de [modelo de consistência](consistency-levels.md) também afeta a taxa de transferência. Você pode obter aproximadamente 2x de taxa de transferência de leitura para os níveis de consistência mais relaxados (por exemplo, *sessão*, *prefixo consistente* e consistência *eventual* ) em comparação com níveis de consistência mais fortes (por exemplo, desatualização *limitada* ou consistência *forte* ).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como [provisionar taxa de transferência para contêineres e bancos de dados do Azure Cosmos DB](set-throughput.md).
- Saiba mais sobre o [Azure Cosmos DB sem servidor](serverless.md).
- Saiba mais sobre [partições lógicas](./partitioning-overview.md).
- Saiba como [provisionar taxa de transferência em um contêiner do Azure Cosmos DB](how-to-provision-container-throughput.md).
- Saiba como [provisionar taxa de transferência em um banco de dados do Azure Cosmos DB](how-to-provision-database-throughput.md).
- Saiba como [localizar o encargo de unidade de solicitação para uma operação](find-request-unit-charge.md).
- Saiba como [otimizar o custo de taxa de transferência provisionada em Azure Cosmos DB](optimize-cost-throughput.md).
- Saiba como [otimizar o custo de leituras e gravações no Azure Cosmos DB](optimize-cost-reads-writes.md).
- Saiba como [otimizar o custo da consulta no Azure Cosmos DB](./optimize-cost-reads-writes.md).
- Saiba como [usar métricas para monitorar a taxa de transferência](use-metrics.md).
