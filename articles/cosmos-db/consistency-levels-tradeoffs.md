---
title: Compromissos de disponibilidade e desempenho para vários níveis de consistência no Azure Cosmos DB
description: Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência no Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: a76e277bf56861bcaefb5bf7f8b3b3bc03ad1164
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60894021"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Compensações de consistência, disponibilidade e desempenho 

Bancos de dados distribuídos que dependem de replicação para alta disponibilidade, baixa latência ou ambos devem fazer compensações. As compensações estão entre consistência de leitura versus a disponibilidade, latência e taxa de transferência.

O Azure Cosmos DB aborda a coerência de dados como um espectro de opções. Essa abordagem inclui mais opções que os dois extremos de consistência forte e eventual. Você pode escolher entre cinco modelos bem definidos no espectro de consistência. Do mais forte ao mais fraco, os modelos são:

- *Forte*
- *Desatualização Limitada*
- *Sessão*
- *Prefixo coerente*
- *Eventual*

Cada modelo fornece disponibilidade e compensações de desempenho e é respaldo por SLAs abrangentes.

## <a name="consistency-levels-and-latency"></a>Níveis de coerência e latência

A latência de leitura para todos os níveis de coerência é sempre asseguradamente menor que 10 milissegundos no 99º percentil contando com suporte do SLA. A latência de leitura é garantida por SLA. A latência de leitura média, no 50º %, é normalmente 2 milissegundos ou menos. Contas do Cosmos do Azure que abrangem várias regiões e são configuradas com coerência forte são uma exceção a essa garantia.

A latência de gravação para todos os níveis de consistência sempre é garantida para ser menor que 10 milissegundos no percentil 99. A latência de gravação é garantida por SLA. A latência média de gravação, 50 º percentil, geralmente é 5 milissegundos ou menos.

Para contas do Azure Cosmos configuradas com consistência forte com mais de uma região, a latência de gravação é garantido que seja menor que duas vezes tempo ida e volta (RTT) entre qualquer uma das duas regiões mais distantes, além de 10 milissegundos no percentil 99.

A latência RTT é uma função de distância à velocidade da luz e a topologia de rede exata do Azure. A Rede do Azure não fornece nenhum SLA de latência para o RTT entre nenhum par de regiões do Azure. Para sua conta do Azure Cosmos, latências de replicação são exibidas no portal do Azure. Você pode usar o portal do Azure (vá para a folha de métricas) para monitorar as latências de replicação entre várias regiões que estão associados com sua conta do Cosmos do Azure.

## <a name="consistency-levels-and-throughput"></a>Níveis de coerência e taxa de transferência

- Para o mesmo número de unidades de solicitação, a sessão, o prefixo consistente e os níveis de consistência eventuais fornecem uma taxa de leitura de aproximadamente 2X quando comparados com a rigidez forte e limitada.

- Para um determinado tipo de operação de gravação, como inserir, substituir, submeter, excluir, a taxa de transferência de gravação para unidades de solicitação é idêntica para todos os níveis de consistência.

## <a id="rto"></a>Níveis de consistência e durabilidade de dados

Em um ambiente de banco de dados distribuído globalmente, há uma relação direta entre a durabilidade dos dados e o nível de consistência no caso de uma interrupção em toda a região. À medida que você vai desenvolvendo o plano de continuidade dos negócios, precisará saber qual é o tempo máximo aceitável antes que o aplicativo se recupere completamente após um evento de interrupção. O tempo necessário para um aplicativo se recupere totalmente é conhecido como **objetivo de tempo de recuperação** (**RTO**). Também é necessário saber o período máximo de atualizações de dados recentes que o aplicativo pode perder sem maiores problemas durante a recuperação após um evento de interrupção. O período de tempo das atualizações que você pode pode perder é conhecido como **objetivo de ponto de recuperação** (**RPO**).

A tabela a seguir define a relação entre a durabilidade de dados e o modelo de consistência na presença de interrupção ampla de região. É importante observar que em um sistema distribuído, mesmo com coerência forte, é impossível ter um banco de dados distribuído com um RPO e RTO de zero devido ao Teorema de CAP. Para saber mais sobre o motivo, consulte [níveis de consistência no Azure Cosmos DB](consistency-levels.md).

|**Regiões**|**Modo de replicação**|**Nível de coerência**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Único ou vários mestres|Qualquer nível de consistência|< 240 minutos|< 1 semana|
|> 1|Único mestre|Sessão, Prefixo Consistente, Eventual|< 15 minutos|< 15 minutos|
|> 1|Único mestre|Bounded staleness|*K* & *T*|< 15 minutos|
|> 1|Vários mestres|Sessão, Prefixo Consistente, Eventual|< 15 minutos|0|
|> 1|Vários mestres|Bounded staleness|*K* & *T*|0|
|> 1|Único ou vários mestres|Strong|0|< 15 minutos|

*K* = o número de *"K"* versões (ou seja, as atualizações) de um item.

*T* = o intervalo de tempo *"T"* desde a última atualização.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a distribuição global e compensações de consistência geral nos sistemas distribuídos. Confira os seguintes artigos:

- [Consistency tradeoffs in modern distributed database systems design](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k) (Compensações de coerência no projeto de sistemas de bancos de dados modernos distribuídos)
- [Alta disponibilidade](high-availability.md)
- [SLA do Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
