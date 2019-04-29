---
title: Dimensionar a taxa de transferência no Azure Cosmos DB
description: Este artigo descreve como o Azure Cosmos DB dimensiona a taxa de transferência de maneira elástica
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: b645fe210e7eeb073380dcadefead3e1b4d7ccc0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926373"
---
# <a name="globally-scale-provisioned-throughput"></a>Taxa de transferência provisionada para dimensionamento global 

No Azure Cosmos DB, taxa de transferência provisionada é representada como solicitação unidades/segundo (RU/s ou a forma plural RUs). As RUs medem o custo das operações de leitura e de gravação em seu contêiner do Cosmos como mostrado nesta imagem:

![Unidades de solicitação](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Você pode provisionar RUs em um contêiner ou em um banco de dados do Cosmos. RUs provisionadas em um contêiner são exclusivamente disponíveis para as operações executadas no contêiner. RUs provisionadas em um banco de dados são compartilhadas entre todos os contêineres do banco de dados (exceto por contêineres com RUs atribuídas de maneira exclusiva).

Para dimensionar elasticamente a taxa de transferência provisionada, você pode aumentar ou diminuir a RU/s provisionado a qualquer momento. Para obter mais informações, consulte [explicativos provisionar a produtividade](set-throughput.md) e dimensione de contêineres do Cosmos e bancos de dados. Para dimensionar globalmente a taxa de transferência, você pode adicionar ou remover regiões da sua conta do Cosmos a qualquer momento. Para obter mais informações, consulte [Adicionar ou remover regiões de sua conta de banco de dados](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Associação de várias regiões com uma conta de Cosmos é importante em cenários de muitos - para conseguir baixa latência e [alta disponibilidade](high-availability.md) em todo o mundo.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Como a taxa de transferência provisionada é distribuída entre regiões

Se você provisionar *'R'* RUs em um Cosmos contêiner (ou banco de dados), o Cosmos DB garante que *'R'* RUs estão disponíveis na *cada* região associada à sua conta do Cosmos. Cada vez que você adicionar uma nova região à sua conta do Cosmos DB provisiona automaticamente *'R'* RUs na região recém-adicionado. As operações executadas em relação a seu contêiner do Cosmos têm garantia de obter *'R'* RUs em cada região. Você não pode atribuir seletivamente RUs a uma região específica. O RUs provisionadas em um contêiner do Cosmos (ou banco de dados) é provisionado em todas as regiões associadas à sua conta do Cosmos.

Supondo que um contêiner do Cosmos é configurado com *'R'* RUs e há *' n'* regiões associadas à conta Cosmos, então:

- Se a conta de Cosmos é configurada com uma região de gravação única, o total de RUs disponíveis globalmente no contêiner = *R* x *N*.

- Se a conta de Cosmos é configurada com várias regiões de gravação, o total de RUs disponíveis globalmente no contêiner = *R* x (*N*+ 1). Adicional *R* RUs são automaticamente provisionadas para conflitos de atualização de processo e o tráfego de antiem entropia todas as regiões.

Sua escolha de [modelo de consistência](consistency-levels.md) também afeta a taxa de transferência. Você pode obter aproximadamente 2 vezes a taxa de transferência de leitura para os níveis de consistência reduzidos (por exemplo, *sessão*, *prefixo consistente* e *eventual* consistência) em comparação com níveis de consistência mais fortes (por exemplo, *desatualização* ou *forte* consistência).

## <a name="next-steps"></a>Próximas etapas

Em seguida, você pode aprender como configurar a taxa de transferência em um contêiner ou banco de dados:

* [Obter e definir a taxa de transferência para contêineres e bancos de dados](set-throughput.md) 

