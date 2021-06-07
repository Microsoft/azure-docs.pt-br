---
title: Dimensionar grupo de servidores-hiperescala (Citus)-banco de dados do Azure para PostgreSQL
description: Ajuste os recursos de memória do grupo de servidores, disco e CPU para lidar com o aumento de carga
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 59e6e73c99569b0a35c56d65c1a7ccdfcb394c0f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026413"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Dimensionar um grupo de servidores de hiperescala (Citus)

Banco de dados do Azure para PostgreSQL-Citus (hiperescala) fornece dimensionamento de autoatendimento para lidar com o aumento da carga. A portal do Azure facilita a adição de novos nós de trabalho e o aumento da vCores dos nós existentes. Adicionar nós não causa nenhum tempo de inatividade e até mesmo mover fragmentos para os novos nós (chamados de [rebalanceamento de fragmentos](howto-hyperscale-scale-rebalance.md)) ocorre sem interromper as consultas.

## <a name="add-worker-nodes"></a>Adicionar nós de trabalho

Para adicionar nós, vá para a guia **computação + armazenamento** em seu grupo de servidores de hiperescala (Citus).  Arrastar o controle deslizante para **contagem de nós de trabalho** altera o valor.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="Controles deslizantes de recursos":::

Clique no botão **salvar** para fazer o valor alterado entrar em vigor.

> [!NOTE]
> Após aumentar e salvar os nós de trabalho, a quantidade deles não poderá ser reduzida com o controle deslizante.

> [!NOTE]
> Para aproveitar os nós recém-adicionados, você deve [reequilibrar os fragmentos de tabela distribuída](howto-hyperscale-scale-rebalance.md), o que significa mover alguns [fragmentos](concepts-hyperscale-distributed-data.md#shards) de nós existentes para os novos.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Aumentar ou diminuir vCores em nós

Além de adicionar nós, será possível aumentar as funcionalidades dos nós existentes. Ajustar a capacidade de computação para cima e para baixo pode ser útil para testes de desempenho, bem como alterações de curto ou longo prazo em demandas de tráfego.

Para alterar o vCores de todos os nós de trabalho, ajuste o controle deslizante **vCores** em **configuração (por nó de trabalho)**. O vCores do nó de coordenador pode ser ajustado de forma independente. Ajuste o controle deslizante **vCores** em  **configuração (nó de coordenador)**.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [as opções de desempenho](concepts-hyperscale-configuration-options.md)do grupo de servidores.
- [Rebalancear fragmentos de tabela distribuída](howto-hyperscale-scale-rebalance.md) para que todos os nós de trabalho possam participar de consultas paralelas
