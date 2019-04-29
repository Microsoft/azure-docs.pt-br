---
title: Particionamento e escala horizontal no Azure Cosmos DB
description: Saiba mais sobre como o particionamento funciona no Azure Cosmos DB, como configurar o particionamento e chaves de partição e como escolher a chave de partição correta para seu aplicativo.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.openlocfilehash: cf454d6504f0433d7ac7ca883982ae317b14f814
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60928843"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Particionamento e escala horizontal no Azure Cosmos DB

Este artigo explica as partições físicas e lógicas no Azure Cosmos DB. Ele também aborda as práticas recomendadas para dimensionamento e particionamento. 

## <a name="logical-partitions"></a>Partições lógicas

Uma partição lógica consiste em um conjunto de itens que têm a mesma chave de partição. Por exemplo, em um contêiner em que todos os itens contêm uma `City` propriedade, você pode usar `City` como a chave de partição para o contêiner. Grupos de itens que têm valores específicos para `City`, como `London`, `Paris`, e `NYC`, formar partições lógicas distintas. Você não precisa se preocupar sobre como excluir uma partição quando os dados subjacentes são excluídos.

No Azure Cosmos DB, um contêiner é a unidade fundamental de escalabilidade. Dados que são adicionados ao contêiner e a taxa de transferência que você provisiona no contêiner são automaticamente particionado (horizontalmente) em um conjunto de partições lógicas. Taxa de transferência e os dados são particionados com base na chave de partição especificado para o contêiner do Azure Cosmos. Para obter mais informações, consulte [criar um contêiner do Azure Cosmos](how-to-create-container.md).

Uma partição lógica também define o escopo das transações de banco de dados. Você pode atualizar itens dentro de uma partição lógica usando um [transações com isolamento de instantâneo](database-transactions-optimistic-concurrency.md). Quando novos itens são adicionados a um contêiner, as novas partições lógicas transparentemente são criadas pelo sistema.

## <a name="physical-partitions"></a>Partições físicas

Um contêiner do Cosmos do Azure é dimensionado com a distribuição de dados e taxa de transferência em um grande número de partições lógicas. Internamente, uma ou mais partições lógicas são mapeadas para uma partição física que consiste em um conjunto de réplicas, também conhecido como um [ *conjunto de réplicas*](global-dist-under-the-hood.md). Cada réplica definidas hospeda uma instância do mecanismo de banco de dados do Azure Cosmos DB. Um conjunto de réplicas faz com que os dados armazenados dentro da partição física durável, altamente disponível e consistente. A quantidade máxima de unidades de armazenamento e a solicitação (RUs) oferece suporte a uma partição física. Cada réplica que compõe a partição física herda a cota de armazenamento da partição. Todas as réplicas de uma partição física coletivamente dão suporte a taxa de transferência alocada para a partição física. 

A imagem a seguir mostra como as partições lógicas são mapeadas para partições físicas distribuídas globalmente:

![Uma imagem que demonstra o particionamento do Azure Cosmos DB](./media/partition-data/logical-partitions.png)

Taxa de transferência provisionada para um contêiner é dividida igualmente entre partições físicas. Um design de chave de partição que não distribui as solicitações de taxa de transferência uniformemente pode criar partições "hot". Partições ativas podem resultar em limitação de taxa e no uso ineficiente da taxa de transferência provisionada e custos mais altos.

Ao contrário das partições lógicas, as partições físicas são uma implementação interna do sistema. Você não pode controlar o tamanho, o posicionamento ou a contagem de partições físicas, e você não pode controlar o mapeamento entre partições lógicas e partições físicas. No entanto, você pode controlar o número de partições lógicas e a distribuição de dados, a carga de trabalho e a taxa de transferência por [escolhendo a chave de partição lógica correta](partitioning-overview.md#choose-partitionkey).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [escolher uma chave de partição](partitioning-overview.md#choose-partitionkey).
* Saiba mais sobre a [taxa de transferência provisionada no Azure Cosmos DB](request-units.md).
* Saiba mais sobre [distribuição global no Azure Cosmos DB](distribute-data-globally.md).
* Saiba como [provisionar taxa de transferência em um contêiner do Azure Cosmos DB](how-to-provision-container-throughput.md).
* Saiba como [provisionar taxa de transferência em um banco de dados do Azure Cosmos DB](how-to-provision-database-throughput.md).
