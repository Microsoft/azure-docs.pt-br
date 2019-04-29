---
title: Particionamento no BD Cosmos do Azure
description: Visão geral do particionamento no Azure Cosmos DB.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.openlocfilehash: e88be8e7b94566ff94dd94a8679f8ade9d54c0b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60928695"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Particionamento no BD Cosmos do Azure

O Azure Cosmos DB usa o particionamento para dimensionar contêineres individuais em um banco de dados para atender às necessidades de desempenho do seu aplicativo. No particionamento, os itens em um contêiner são divididos em subconjuntos distintos chamados *partições lógicas*. Partições lógicas são formadas com base no valor de uma *chave de partição* que está associado com cada item em um contêiner. Todos os itens em uma partição lógica tem o mesmo valor de chave de partição.

Por exemplo, um contêiner contém itens. Cada item tem um valor exclusivo para o `UserID` propriedade. Se `UserID` serve como a partição de chave para os itens no contêiner e 1.000 são exclusivos `UserID` valores, 1.000 partições lógicas são criadas para o contêiner.

Além de uma chave de partição que determina a partição de lógica do item, cada item em um contêiner tem um *ID do item* (exclusivo dentro de uma partição lógica). Combinando a chave de partição e a ID do item cria o item *índice*, que identifica exclusivamente o item.

[Escolhendo uma chave de partição](partitioning-overview.md#choose-partitionkey) é uma decisão importante que afeta o desempenho do aplicativo.

## <a name="managing-logical-partitions"></a>Gerenciando partições lógicas

O Azure Cosmos DB forma transparente e automática gerencia o posicionamento das partições lógicas em partições físicas para atender com eficiência as necessidades de desempenho e escalabilidade do contêiner. À medida que aumentam os requisitos de taxa de transferência e armazenamento de um aplicativo, o Azure Cosmos DB move partições lógicas para distribuir automaticamente a carga entre um número maior de servidores. 

O Azure Cosmos DB usa o particionamento baseado em hash distribuídas partições lógicas em partições físicas. O Azure Cosmos DB faz o hash do valor de chave de partição de um item. O resultado com hash determina a partição física. Em seguida, Azure Cosmos DB aloca o espaço da chave de partição de hashes de chave uniformemente entre as partições físicas.

As consultas que acessam dados em uma única partição lógica são mais econômicas do que consultas que acessam várias partições. Transações (em procedimentos armazenados ou gatilhos) são permitidas apenas em relação a itens em uma única partição lógica.

Para saber mais sobre como o Azure Cosmos DB gerencia partições, consulte [partições lógicas](partition-data.md). (Ele não é necessário entender os detalhes internos para compilar ou executar seus aplicativos, mas adicionado aqui para um leitor curioso.)

## <a id="choose-partitionkey"></a>Escolhendo uma chave de partição

O exemplo a seguir é uma boa diretriz para escolher uma chave de partição:

* Uma única partição lógica tem um limite de 10 GB de armazenamento.  

* Contêineres do Azure Cosmos tem uma taxa de transferência mínima de 400 unidades de solicitação por segundo (RU/s). As solicitações para a mesma chave de partição não podem exceder a taxa de transferência alocada para uma partição. Se as solicitações excederem a taxa de transferência alocada, as solicitações são limitadas por taxa. Portanto, é importante escolher uma chave de partição que não resulte em "pontos de acesso" em seu aplicativo.

* Escolha uma chave de partição que tenha uma ampla gama de valores e padrões de acesso distribuídos uniformemente em partições lógicas. Isso ajuda a distribuir os dados e a atividade em seu contêiner em um conjunto de partições lógicas, para que os recursos de armazenamento de dados e taxa de transferência podem ser distribuídos em partições lógicas.

* Escolha uma chave de partição que distribui a carga de trabalho uniformemente entre todas as partições e uniformemente ao longo do tempo. Sua escolha de chave de partição deve equilibrar a necessidade de consultas de partição eficiente e transações em relação à meta de distribuição de itens em várias partições para atingir a escalabilidade.

* Candidatos para chaves de partição podem incluir propriedades que aparecem com frequência como um filtro em suas consultas. Consultas podem ser roteadas com eficiência, incluindo a chave de partição no predicado de filtro.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [particionamento e escala horizontal no Azure Cosmos DB](partition-data.md).
* Saiba mais sobre a [taxa de transferência provisionada no Azure Cosmos DB](request-units.md).
* Saiba mais sobre [distribuição global no Azure Cosmos DB](distribute-data-globally.md).
