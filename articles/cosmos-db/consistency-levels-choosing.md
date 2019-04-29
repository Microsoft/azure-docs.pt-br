---
title: Escolhendo o nível de consistência correto para seu aplicativo que usa o Azure Cosmos DB
description: Como escolher o nível certo de coerência para o seu aplicativo no Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2018
ms.reviewer: sngun
ms.openlocfilehash: f32434e5ac0cd35cf620c1589aeb441476622442
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60892192"
---
# <a name="choose-the-right-consistency-level"></a>Escolher nível certo de consistência 

Bancos de dados distribuídos que dependem de replicação para alta disponibilidade, baixa latência ou ambos realizam o equilíbrio fundamental entre a coerência de leitura versus a disponibilidade, a latência e a taxa de transferência. A maioria dos bancos de dados distribuídos comercialmente disponíveis perguntar aos desenvolvedores escolher entre os dois modelos de consistência extreme: *forte* consistência e *eventual* consistência. O Azure Cosmos DB permite aos desenvolvedores escolher entre cinco modelos de consistência bem definidos: *forte*, *desatualização*, *sessão*, *consistente prefixo* e *eventual*. Cada um desses modelos de coerência é bem definido, intuitivo e pode ser usado para cenários específicos do mundo real. Cada um dos modelos de cinco consistência fornecer precisos [compensações de desempenho e disponibilidade](consistency-levels-tradeoffs.md) e têm o suporte de SLAs abrangentes. As considerações simples a seguir lhe ajudarão a fazer a escolha certa em muitos cenários comuns.

## <a name="sql-api-and-table-api"></a>SQL API e a API de Tabela

Se seu aplicativo é criado usando a API do SQL ou a API de tabela, considere os seguintes pontos:

- Para muitos cenários do mundo real, a coerência de sessão é ideal e é a opção recomendada. Para obter mais informações, consulte [Como gerenciar o token de sessão do seu aplicativo](how-to-manage-consistency.md#utilize-session-tokens).

- Se o seu aplicativo exigir consistência forte, é recomendável que você use um nível de consistência de stality limitado.

- Se você precisar de garantias de consistência mais rigorosas do que as fornecidas pela consistência da sessão e pela latência de um dígito de milissegundo para gravações, é recomendável usar o nível de consistência do stality limitado.  

- Se o seu aplicativo exigir consistência eventual, é recomendável usar um nível consistente de consistência de prefixo.

- Se você precisar de garantias de consistência menos rígidas do que as fornecidas pela consistência da sessão, é recomendável usar um nível consistente de consistência de prefixo.

- Se você precisa de mais alta disponibilidade e a latência mais baixa, use o nível de consistência eventual.

- Se você precisar de mais durabilidade de dados ainda sem perder desempenho, poderá criar um nível de consistência personalizado na camada de aplicativo. Para obter mais informações, confira [Como implementar a sincronização personalizada em seus aplicativos](how-to-custom-synchronization.md).

## <a name="cassandra-mongodb-and-gremlin-apis"></a>Cassandra, MongoDB e APIs Gremlin

- Para obter detalhes sobre o mapeamento entre o “Read Consistency Level” oferecido nos níveis de consistência do Apache Cassandra e do Cosmos DB, consulte [Níveis de consistência e as APIs do Cosmos DB](consistency-levels-across-apis.md#cassandra-mapping).

- Para obter detalhes sobre o mapeamento entre o "Problema de leitura" do MongoDB e os níveis de coerência do Azure Cosmos DB, confira [Níveis de coerência e APIs do Cosmos DB](consistency-levels-across-apis.md#mongo-mapping).

## <a name="consistency-guarantees-in-practice"></a>Garantias de consistência na prática

Na prática, você poderá obter muitas vezes mais fortes garantias de consistência. Garantias de consistência para uma operação de leitura correspondem à atualização e ordenação do estado do banco de dados que você solicita. Consistência de leitura é vinculada ao pedido e a propagação das operações de gravação/atualização.  

* Quando o nível de coerência é definido como **desatualização limitada**, o Cosmos DB garante que os clientes sempre leiam o valor de uma gravação anterior, com um limitado pela janela de desatualização.

* Quando o nível de consistência é definido como **strong**, a janela de staleness é equivalente a zero e os clientes têm a garantia de ler o último valor confirmado da operação de gravação.

* Para os três níveis de coerência restantes, a janela de desatualização limitada depende muito da carga de trabalho. Por exemplo, se não houver operações de gravação no banco de dados, é provável que uma operação de leitura com **eventuais**, **sessão** ou **consistência consistente prefira** resultados como uma operação de leitura com um nível de consistência forte.

Se sua conta do Cosmos do Azure é configurada com um nível de consistência que não seja a coerência forte, você pode descobrir a probabilidade de que os clientes podem obter forte e leituras consistentes para suas cargas de trabalho, observando o *Probabilistically Desatualização* métrica (PBS). Essa métrica é exposta no portal do Azure, para obter mais informações, consulte [métrica Monitor Probabilistic Bounded Staleness (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

A desatualização limitada probabilística mostra o quão eventual é a sua coerência eventual. Essa métrica fornece uma visão geral de quantas vezes você pode obter uma consistência mais forte que o nível de consistência que você configurou atualmente em sua conta do Cosmos do Azure. Em outras palavras, você pode ver a probabilidade (medida em milissegundos) de obter leituras fortemente consistentes para uma combinação de regiões de gravação e leitura.

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre os níveis de coerência nos seguintes artigos:

* [Mapeamento dos níveis de coerência entre as APIs do Cosmos DB](consistency-levels-across-apis.md)
* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)
* [Como gerenciar o token de sessão para o seu aplicativo](how-to-manage-consistency.md#utilize-session-tokens)
* [Monitorar métrica PBS (Desatualização Limitada Probabilística)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
