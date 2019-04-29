---
title: Unidades de Solicitação e taxa de transferência no Azure Cosmos DB
description: Saiba mais sobre como especificar e estimar os requisitos da Unidade de Solicitação no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: 72ec8332a3363d5336fb84b3390d5e44ced0b2e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60925964"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unidades de Solicitação no Azure Cosmos DB

Com o Azure Cosmos DB, você paga pela taxa de transferência provisionada e pelo armazenamento que consome por hora. Taxa de transferência deve ser provisionada para garantir que os recursos de sistema suficientes estejam disponíveis para seu banco de dados Cosmos do Azure em todos os momentos. Você precisa de recursos suficientes para atender ou exceder a [SLAs do Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

O Azure Cosmos DB dá suporte a muitas APIs como SQL, MongoDB, Cassandra, Gremlin e Table. Cada API tem seu próprio conjunto de operações de banco de dados. Essas operações variam de simples leituras e gravações de pontos a consultas complexas. Cada operação de banco de dados consome recursos do sistema com base na complexidade da operação. 

O custo de todas as operações de banco de dados estão normalizado pelo Azure Cosmos DB e é expressa por *unidades de solicitação* (ou RUs, de forma abreviada). É possível pensar em RUs por segundo como a moeda para a taxa de transferência. RUs por segundo é uma moeda baseada em taxa. Elas extraem os recursos do sistema como CPU, IOPS e memória que são necessários para executar as operações do banco de dados com suporte no Azure Cosmos DB. 

O custo para ler um 1 KB que o item é 1 unidade de solicitação (ou 1 RU). Todas as outras operações de banco de dados da mesma forma são atribuídas a um custo que usam o RUs. Independentemente da API usada para interagir com o contêiner do Azure Cosmos DB, os custos sempre serão medidos por RUs. Independentemente da operação do banco de dados ser uma gravação, leitura ou consulta, os custos sempre serão medidos em RUs.

A imagem a seguir mostra a ideia de alto nível de RUs:

![Operações do banco de dados consomem Unidades de Solicitação](./media/request-units/request-units.png)

Para gerenciar e planejar a capacidade, o Azure Cosmos DB garante que o número de RUs para uma determinada operação de banco de dados em um determinado conjunto de dados seja determinístico. Você pode examinar o cabeçalho de resposta para controlar o número de RUs consumidos por qualquer operação de banco de dados. Quando você entende os [fatores que afetam os encargos de RU](request-units.md#request-unit-considerations) e requisitos de taxa de transferência do seu aplicativo, você pode executar seu aplicativo de forma econômico.

Você provisiona o número de RUs para o aplicativo em uma base por segundo em incrementos de 100 RUs por segundo. Para dimensionar a taxa de transferência provisionada ao aplicativo, você poderá aumentar ou diminuir o número de RUs a qualquer momento. Você pode dimensionar em aumenta ou diminui de 100 RUs. É possível fazer as alterações programaticamente ou usando o portal do Azure. Você será cobrado por hora.

Você pode provisionar a taxa de transferência em duas granularidades distintas: 

* **Contêineres**: Para obter mais informações, consulte [Provisionar taxa de transferência em um contêiner do Azure Cosmos DB](how-to-provision-container-throughput.md).
* **Bancos de dados**: Para obter mais informações, consulte [Provisionar taxa de transferência em um banco de dados do Azure Cosmos DB](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Considerações sobre Unidade de Solicitação

Enquanto você estima o número de RUs por segundo para provisionar, considere os seguintes fatores:

* **Tamanho do item**: Na medida em que o tamanho de um item aumentar, o número de RUs consumidas para leitura ou gravação do item também aumentará.

* **Indexação de itens**: Por padrão, cada item é indexado automaticamente. Menos RUs serão consumidas se você optar por não indexar alguns dos itens em um contêiner.

* **Contagem de propriedades do item**: Supondo que a indexação padrão está em todas as propriedades, o número de RUs consumidas para gravar que um item aumenta à medida que os aumentos de contagem de propriedade do item.

* **Propriedades indexadas**: Uma política de índice em cada contêiner determina quais propriedades são indexadas por padrão. Para reduzir o consumo de RU para operações de gravação, limite o número de propriedades indexadas.

* **Consistência de dados**: Os níveis de consistência de desatualização limitada e forte consomem aproximadamente duas vezes mais RUs durante a execução de operações de leitura, quando comparados com outros níveis de consistência flexíveis.

* **Padrões de consulta**: A complexidade de uma consulta afetará quantas RUs serão consumidas por uma operação. Fatores que afetam o custo das operações de consulta incluem: 
    
    - O número de resultados da consulta
    - O número de predicados
    - A natureza dos predicados
    - O número de funções definidas pelo usuário
    - O tamanho da fonte de dados
    - O tamanho do conjunto de resultados
    - Projeções

  O Azure Cosmos DB garante que a mesma consulta nos mesmos dados sempre custa o mesmo número de RUs em execuções repetidas.

* **Uso de script**: Assim como acontece com as consultas, os procedimentos armazenados e gatilhos consomem RUs com base na complexidade das operações que são executadas. À medida que você desenvolve seu aplicativo, inspecione a [cabeçalho de solicitação de encargo](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) para compreender quanta capacidade de RU consome cada operação.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como [provisionar taxa de transferência para contêineres e bancos de dados do Azure Cosmos DB](set-throughput.md).
* Saiba mais sobre [partições lógicas](partition-data.md).
* Saiba mais sobre como [dimensionar taxa de transferência provisionada globalmente](scaling-throughput.md).
* Saiba como [provisionar taxa de transferência em um contêiner do Azure Cosmos DB](how-to-provision-container-throughput.md).
* Saiba como [provisionar taxa de transferência em um banco de dados do Azure Cosmos DB](how-to-provision-database-throughput.md).
* Saiba como [localizar o encargo de unidades de solicitação para uma operação](find-request-unit-charge.md).
* Saiba como [otimizar o custo de taxa de transferência provisionada no Azure Cosmos DB](optimize-cost-throughput.md).
* Saiba como [otimizar leituras e gravações de custo no Azure Cosmos DB](optimize-cost-reads-writes.md).
* Saiba como [otimizar o custo da consulta no Azure Cosmos DB](optimize-cost-queries.md).
