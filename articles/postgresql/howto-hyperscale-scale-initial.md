---
title: Tamanho inicial do grupo de servidores-Citus (hiperescala)-banco de dados do Azure para PostgreSQL
description: Escolha o tamanho inicial correto para seu caso de uso
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 7e68e9f8caad7d7e4bc44bc4e1e55150a78b4a98
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026396"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Escolha o tamanho inicial para o grupo de servidores de hiperescala (Citus)

O tamanho de um grupo de servidores, seu número de nós e sua capacidade de hardware, é [fácil de alterar](howto-hyperscale-scale-grow.md)). No entanto, você ainda precisa escolher um tamanho inicial para um novo grupo de servidores. Aqui estão algumas dicas para uma escolha razoável.

## <a name="multi-tenant-saas-use-case"></a>Caso de uso de SaaS de vários locatários

Ao migrar para o Citus (subscale) de uma instância de banco de dados PostgreSQL de nó único existente, escolha um cluster em que o número de vCores de trabalho e RAM no total seja igual ao da instância original. Nesses cenários, vimos aprimoramentos de desempenho de 2 a 3, pois a fragmentação melhora a utilização de recursos, permitindo índices menores etc.

A contagem vCore é, na verdade, a única decisão. A alocação de RAM é determinada no momento com base na contagem de vCore, conforme descrito na página [Opções de configuração de hiperescala (Citus)](concepts-hyperscale-configuration-options.md) .
O nó de coordenador não exige tanta RAM quanto os trabalhadores, mas não há como escolher RAM e vCores de forma independente.

## <a name="real-time-analytics-use-case"></a>Caso de uso de análise em tempo real

Total de vCores: quando os dados de trabalho se ajustam à RAM, você pode esperar uma melhoria de desempenho linear em hiperescala (Citus) proporcional ao número de núcleos de trabalho. Para determinar o número certo de vCores para suas necessidades, considere a latência atual para consultas em seu banco de dados de nó único e a latência necessária em hiperescala (Citus). Divida a latência atual pela latência desejada e arredonde o resultado.

RAM de trabalho: o melhor caso seria fornecer memória suficiente para que a maior parte do conjunto de trabalho caiba na memória. O tipo de consultas que seu aplicativo usa afetará os requisitos de memória. Você pode executar explicar analisar em uma consulta para determinar a quantidade de memória necessária. Lembre-se de que vCores e RAM são dimensionados juntos, conforme descrito no artigo [Opções de configuração de hiperescala (Citus)](concepts-hyperscale-configuration-options.md) .

## <a name="next-steps"></a>Próximas etapas

- [Escalar um grupo de servidores](howto-hyperscale-scale-grow.md)
- Saiba mais sobre [as opções de desempenho](concepts-hyperscale-configuration-options.md)do grupo de servidores.
