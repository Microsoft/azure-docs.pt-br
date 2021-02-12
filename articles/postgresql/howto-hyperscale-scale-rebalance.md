---
title: Rebalancear fragmentos-Citus (hiperescala)-banco de dados do Azure para PostgreSQL
description: Distribua os fragmentos uniformemente entre servidores para melhorar o desempenho
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: b69c4fc3ff16cf30181a3529f61af7126b92950b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026379"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Reequilibrar fragmentos no grupo de servidores Citus (hiperescala)

Para aproveitar os nós recém-adicionados, você deve reequilibrar os [fragmentos](concepts-hyperscale-distributed-data.md#shards)de tabela distribuída, o que significa mover alguns fragmentos de nós existentes para os novos. Primeiro, verifique se os novos trabalhadores concluíram com êxito o provisionamento. Em seguida, inicie o rebalanceador de fragmento, conectando-se ao nó de coordenador de cluster com psql e executando:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

A função [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) reequilibra todas as tabelas no grupo de [colocalização](concepts-hyperscale-colocation.md) da tabela nomeada em seu argumento. Portanto, você não precisa chamar a função para cada tabela distribuída, basta chamá-la em uma tabela representativa de cada grupo de colocalização.

**Próximas etapas**


- Saiba mais sobre [as opções de desempenho](concepts-hyperscale-configuration-options.md)do grupo de servidores.
- [Reduzir ou reduzir verticalmente um grupo de servidores](howto-hyperscale-scale-grow.md)
- Consulte o material de referência do [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards)
