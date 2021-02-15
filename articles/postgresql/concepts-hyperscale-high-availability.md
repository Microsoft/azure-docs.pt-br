---
title: Alta disponibilidade – Citus (hiperescala) – banco de dados do Azure para PostgreSQL
description: Conceitos de alta disponibilidade e recuperação de desastre
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 406b4f2ada665d65ee0452579e24744d1f7cfc63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91314846"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Alta disponibilidade no banco de dados do Azure para PostgreSQL – Citus (hiperescala)

A HA (alta disponibilidade) evita o tempo de inatividade do banco de dados mantendo réplicas em espera de cada nó em um grupo de servidores. Se um nó falhar, o Citus (hiperescala) alternará as conexões de entrada do nó com falha para o seu estado de espera. O failover ocorre em alguns minutos e os nós promovidos sempre têm dados atualizados por meio da replicação de streaming síncrona do PostgreSQL.

Para aproveitar a HA no nó de coordenador, os aplicativos de banco de dados precisam detectar e repetir conexões descartadas e transações com falha. O coordenador recém-promovido será acessível com a mesma cadeia de conexão.

A recuperação pode ser dividida em três estágios: detecção, failover e recuperação completa.  O Citus (hiperscale) executa verificações de integridade periódicas em cada nó e, após quatro verificações com falha, ele determina que um nó está inoperante. O Citus (subscale), em seguida, promove um estado de espera para o status do nó primário (failover) e provisiona um novo em espera.
A replicação de streaming começa, colocando o novo nó atualizado.  Quando todos os dados tiverem sido replicados, o nó terá atingido a recuperação completa.

### <a name="next-steps"></a>Próximas etapas

- Saiba como [habilitar a alta disponibilidade](howto-hyperscale-high-availability.md) em um grupo de servidores Citus (hiperescala).
