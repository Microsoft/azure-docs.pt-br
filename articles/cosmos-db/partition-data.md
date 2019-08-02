---
title: Particionamento e escala horizontal no Azure Cosmos DB
description: Saiba mais sobre como o particionamento funciona no Azure Cosmos DB, como configurar o particionamento e as chaves de partição e como escolher a chave de partição correta para seu aplicativo.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 1b624270fe22004a6ae64affe87b2fc22a2e9a66
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467693"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Particionamento e escala horizontal no Azure Cosmos DB

Este artigo explica as partições físicas e lógicas no Azure Cosmos DB. Ele também aborda as práticas recomendadas para o dimensionamento e o particionamento. 

## <a name="logical-partitions"></a>Partições lógicas

Uma partição lógica consiste em um conjunto de itens que têm a mesma chave de partição. Por exemplo, em um contêiner em que todos os itens `City` contêm uma propriedade, você `City` pode usar como a chave de partição para o contêiner. Grupos de itens que têm valores específicos para `City`, `London`como `Paris`, e `NYC`, formam partições lógicas distintas. Você não precisa se preocupar em excluir uma partição quando os dados subjacentes são excluídos.

No Azure Cosmos DB, um contêiner é a unidade fundamental de escalabilidade. Os dados que são adicionados ao contêiner e a taxa de transferência que você provisiona no contêiner são automaticamente (horizontalmente) particionados em um conjunto de partições lógicas. Os dados e a taxa de transferência são particionados com base na chave de partição especificada para o contêiner Cosmos do Azure. Para obter mais informações, consulte [criar um contêiner Cosmos do Azure](how-to-create-container.md).

Uma partição lógica também define o escopo das transações de banco de dados. Você pode atualizar itens em uma partição lógica usando uma [transação com isolamento de instantâneo](database-transactions-optimistic-concurrency.md). Quando novos itens são adicionados a um contêiner, novas partições lógicas são criadas de forma transparente pelo sistema.

## <a name="physical-partitions"></a>Partições físicas

Um contêiner Cosmos do Azure é dimensionado pela distribuição de dados e taxa de transferência em um grande número de partições lógicas. Internamente, uma ou mais partições lógicas são mapeadas para uma partição física que consiste em um conjunto de réplicas, também conhecido como um [*conjunto*](global-dist-under-the-hood.md)de réplicas. Cada conjunto de réplicas hospeda uma instância do mecanismo de banco de dados Azure Cosmos DB. Um conjunto de réplicas torna os dados armazenados na partição física durável, altamente disponível e consistente. Uma partição física dá suporte à quantidade máxima de armazenamento e de unidades de solicitação (RUs). Cada réplica que compõe a partição física herda a cota de armazenamento da partição. Todas as réplicas de uma partição física dão suporte coletiva à taxa de transferência alocada para a partição física. 

A imagem a seguir mostra como as partições lógicas são mapeadas para partições físicas distribuídas globalmente:

![Uma imagem que demonstra Azure Cosmos DB particionamento](./media/partition-data/logical-partitions.png)

A taxa de transferência provisionada para um contêiner é dividida uniformemente entre as partições físicas. Um design de chave de partição que não distribui as solicitações de taxa de transferência pode, uniformemente, criar partições "ativas". As partições quentes podem resultar em limitação de taxa e em uso ineficiente da taxa de transferência provisionada e em custos mais altos.

Ao contrário das partições lógicas, as partições físicas são uma implementação interna do sistema. Você não pode controlar o tamanho, o posicionamento ou a contagem de partições físicas e não pode controlar o mapeamento entre partições lógicas e partições físicas. No entanto, você pode controlar o número de partições lógicas e a distribuição de dados, carga de trabalho e taxa de transferência [escolhendo a chave de partição lógica correta](partitioning-overview.md#choose-partitionkey).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como [escolher uma chave de partição](partitioning-overview.md#choose-partitionkey).
* Saiba mais sobre a [taxa de transferência provisionada no Azure Cosmos DB](request-units.md).
* Saiba mais sobre [distribuição global no Azure Cosmos DB](distribute-data-globally.md).
* Saiba como [provisionar taxa de transferência em um contêiner do Azure Cosmos DB](how-to-provision-container-throughput.md).
* Saiba como [provisionar taxa de transferência em um banco de dados do Azure Cosmos DB](how-to-provision-database-throughput.md).
