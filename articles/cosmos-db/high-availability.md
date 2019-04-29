---
title: Alta disponibilidade no Azure Cosmos DB
description: Este artigo descreve como o Azure Cosmos DB fornece alta disponibilidade
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/24/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 1e866560ceab342f08a98ba3db05980a2b0947d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888493"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Alta disponibilidade com o Azure Cosmos DB

O Azure Cosmos DB replica de forma transparente seus dados em todas as regiões do Azure associadas à sua conta do Cosmos. O Cosmos DB utiliza várias camadas de redundância para seus dados, como mostra a imagem a seguir:

![Particionamento físico](./media/high-availability/cosmosdb-data-redundancy.png)

- Os dados dentro de contêineres do Cosmos [particionado horizontalmente](partitioning-overview.md).

- Dentro de cada região, toda partição é protegida por um conjunto de réplicas com todas as gravações replicadas e confirmadas permanentemente pela maioria das réplicas. As réplicas são distribuídas em até 10 a 20 domínios de falha.

- Em todas as regiões, cada partição é replicada. Cada região contém todas as partições de dados de um contêiner do Cosmos e pode aceitar gravações e fornecer leituras.  

Se sua conta do Cosmos é distribuída entre *N* regiões do Azure, haverá pelo menos *N* x 4 cópias de todos os seus dados. Além de fornecer acesso de dados de baixa latência e dimensionamento de taxa de transferência de leitura/gravação entre regiões associadas à sua conta do Cosmos, tendo mais regiões (mais alto *N*) melhora a disponibilidade.  

## <a name="slas-for-availability"></a>SLAs de disponibilidade

Como um banco de dados distribuído globalmente, o Cosmos DB fornece SLAs abrangentes que englobam a taxa de transferência, a latência no 99º percentil, a coerência e a alta disponibilidade. A tabela abaixo mostra as garantias de alta disponibilidade fornecidas pelo Cosmos DB para contas de uma e várias regiões. Para alta disponibilidade, sempre configure suas contas do Cosmos para ter várias regiões de gravação.

|Tipo de operação  | Região única |Várias regiões (gravações de região única)|Várias regiões (gravações de várias regiões) |
|---------|---------|---------|-------|
|Gravações    | 99,99    |99,99   |99,999|
|Leituras     | 99,99    |99,999  |99,999|

> [!NOTE]
> Na prática, a disponibilidade de gravação real para a desatualização limitada, sessão, prefixo consistente e modelos de consistência eventual é significativamente maior do que os SLAs publicados. A disponibilidade de leitura real para todos os níveis de coerência é significativamente mais alta do que os SLAs publicados.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Alta disponibilidade com o Cosmos DB em caso de interrupções regionais

Interrupções regionais não são incomuns e Azure Cosmos DB garante que seu banco de dados sempre está altamente disponível. Os detalhes a seguir capturam o comportamento do Cosmos DB durante uma interrupção, dependendo da configuração de conta do Cosmos:

- Com o Cosmos DB, antes que uma operação de gravação seja confirmada para o cliente, os dados serão confirmados por um quorum de réplicas dentro da região que aceitará as operações de gravação.

- Contas de várias regiões configuradas com várias regiões de gravação estarão altamente disponíveis para leituras e gravações. Failovers regionais são instantâneos e não exigem nenhuma alteração do aplicativo.

- **Contas de várias regiões com uma região de gravação única (interrupção de região de gravação):** Durante uma interrupção da região de gravação, essas contas permanecerão altamente disponíveis para leituras. No entanto, para gravações devem **"habilitar o failover automático"** no seu Cosmos conta para failover de região para outra região afetada. O failover ocorrerá na ordem de prioridade de região especificada por você. Quando a região afetada fique online novamente, os dados não replicados presentes na região de gravação afetados durante a interrupção são disponibilizados por meio de [conflitos feed](how-to-manage-conflicts.md#read-from-conflict-feed). Aplicativos podem ler os conflitos de feed, resolva os conflitos com base na lógica específica do aplicativo e gravar os dados atualizados de volta para o contêiner do Cosmos conforme apropriado. Depois que a região de gravação anteriormente afetada se recupera, ela fica automaticamente disponível como uma região de leitura. É possível invocar um failover manual e configurar a região afetada como a região de gravação. Novamente, você pode fazer um failover manual por meio [CLI do Azure ou portal do Azure](how-to-manage-database-account.md#manual-failover). Não há **nenhuma perda de dados ou disponibilidade** antes, durante ou após o failover manual. O aplicativo continua sendo altamente disponível. 

- **Contas de várias regiões com uma região de gravação única (interrupção de região de leitura):** Durante uma interrupção de região de leitura, essas contas permanecerão altamente disponíveis para leituras e gravações. A região afetada será desconectada automaticamente da região de gravação e será marcada como offline. O [SDKs do Cosmos DB](sql-api-sdk-dotnet.md) irá redirecionar chamadas de leitura para a próxima região disponível na lista de regiões preferenciais. Se nenhuma das regiões na lista de regiões preferenciais estiver disponível, as chamadas retornarão automaticamente à região de gravação atual. Não é necessária nenhuma alteração no código do aplicativo para lidar com a interrupção da região de leitura. Por fim, quando a região afetada ficar online novamente, a região de leitura afetada anteriormente será automaticamente sincronizada com a região de gravação atual e estará disponível novamente para fornecer solicitações de leitura. Leituras subsequentes são redirecionadas para a região recuperada sem exigir nenhuma alteração ao código do aplicativo. Durante o failover e incluir novamente de uma região com falha, consistência de leitura em garantias continuam sendo cumpridas pelo Cosmos DB.

- Contas de uma única região poderão perder disponibilidade após uma indisponibilidade regional. É recomendável sempre definir **pelo menos duas regiões** (de preferência, pelo menos dois gravar regiões) com sua conta do Cosmos para garantir a alta disponibilidade em todos os momentos.

- Mesmo em um evento extremamente raro e infeliz quando a região do Azure está permanentemente irrecuperável, não há nenhuma perda de dados se sua conta do Cosmos de várias regiões é configurada com o nível de consistência padrão de *forte*. No caso de uma região de gravação irrecuperáveis permanentemente, para as contas do Cosmos de várias regiões configuradas com a consistência de desatualização limitada, a janela potencial de perda de dados é restrita para a janela de desatualização (*K* ou *T*); por sessão, níveis de consistência de prefixo consistente e eventual, a janela potencial de perda de dados é restrita a um máximo de cinco segundos.

## <a name="building-highly-available-applications"></a>Criando aplicativos altamente disponíveis

- Para garantir a alta disponibilidade de leitura e gravação, configure sua conta do Cosmos para abranger pelo menos duas regiões com várias regiões de gravação. Essa configuração fornece a mais alta disponibilidade, latência mais baixa, e melhor escalabilidade para as leituras e gravações com suporte por SLAs. Para saber mais, confira como [configurar sua conta do Cosmos com várias regiões de gravação](tutorial-global-distribution-sql-api.md).

- Para contas do Cosmos de várias regiões configuradas com uma única região de gravação, [habilite o failover automático usando a CLI do Azure ou o portal do Azure](how-to-manage-database-account.md#automatic-failover). Depois de habilitar o failover automático, sempre que houver um desastre regional, o Cosmos DB fará o failover da sua conta automaticamente.  

- Mesmo se a conta do Cosmos estiver altamente disponível, seu aplicativo poderá não ser corretamente projetado para permanecer altamente disponível. Para testar a alta disponibilidade de ponta a ponta de seu aplicativo, chamar periodicamente o [failover manual por meio da CLI do Azure ou portal do Azure](how-to-manage-database-account.md#manual-failover), como parte do seu teste de aplicativos ou a recuperação de desastres (DR) simulações.

- Em um ambiente de banco de dados distribuído globalmente, há uma relação direta entre a durabilidade dos dados e o nível de consistência no caso de uma interrupção em toda a região. À medida que você vai desenvolvendo o plano de continuidade dos negócios, precisará saber qual é o tempo máximo aceitável antes que o aplicativo se recupere completamente após um evento de interrupção. O tempo necessário para o aplicativo se recuperar totalmente é conhecido como RTO (objetivo de tempo de recuperação). Também é necessário saber o período máximo de atualizações de dados recentes que o aplicativo pode perder sem maiores problemas durante a recuperação após um evento de interrupção. O período de tempo de atualizações que você pode perder é conhecido como RPO (objetivo de ponto de recuperação). Para ver o RTO e o RPO do Azure Cosmos DB, confira [Níveis de consistência e durabilidade dos dados](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Próximas etapas

Em seguida, você poderá ler os artigos a seguir:

* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)
* [Taxa de transferência provisionada para dimensionamento global](scaling-throughput.md)
* [Distribuição global – nos bastidores](global-dist-under-the-hood.md)
* [Níveis de coerência no Azure Cosmos DB](consistency-levels.md)
* [Como configurar sua conta do Cosmos com várias regiões de gravação](how-to-multi-master.md)
