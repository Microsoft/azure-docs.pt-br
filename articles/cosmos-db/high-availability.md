---
title: Alta disponibilidade no Azure Cosmos DB
description: Este artigo descreve como o Azure Cosmos DB fornece alta disponibilidade
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: fd704d45aa7dc10835a205f12ce26fc01a7ea44f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584492"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Como o Azure Cosmos DB fornece alta disponibilidade
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Cosmos DB fornece alta disponibilidade de duas maneiras principais. Primeiro, Azure Cosmos DB replica dados entre regiões configuradas em uma conta do cosmos. Em segundo lugar, Azure Cosmos DB mantém 4 réplicas de dados em uma região.

O Azure Cosmos DB é um serviço de banco de dados distribuído globalmente e é um serviço básico disponível em [todas as regiões em que o Azure está disponível](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all). Você pode associar qualquer número de regiões do Azure à sua conta do Azure Cosmos e seus dados serão replicados de maneira automática e transparente. Você pode adicionar ou remover uma região da sua conta do Azure Cosmos a qualquer momento. O Cosmos DB está disponível em todos os cinco ambientes distintos de nuvem do Azure disponíveis para os clientes:

* **Nuvem pública do Azure**, que está disponível globalmente.

* O **Azure China 21vianet** está disponível por meio de uma parceria exclusiva entre a Microsoft e a 21vianet, um dos maiores provedores de Internet do país na China.

* O **Azure Alemanha** fornece serviços em um modelo de data Trustee, que garante que os dados do cliente permaneçam na Alemanha sob o controle de T-Systems International GmbH, uma subsidiária de alemão Telekom, agindo como o data Trustee do alemão.

* **Azure Governamental** está disponível em quatro regiões nos Estados Unidos para agências do governo dos EUA e seus parceiros.

* O **Azure governamental para o departamento de defesa (DoD)** está disponível em duas regiões do Estados Unidos para o departamento de defesa dos EUA.

Dentro de uma região, Azure Cosmos DB mantém quatro cópias de seus dados como réplicas em partições físicas, conforme mostrado na imagem a seguir:

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="Particionamento físico" border="false":::

* Os dados nos contêineres de Cosmos do Azure são [particionados horizontalmente](partitioning-overview.md).

* Um conjunto de partições é uma coleção de vários conjuntos de réplicas. Dentro de cada região, toda partição é protegida por um conjunto de réplicas com todas as gravações replicadas e confirmadas permanentemente pela maioria das réplicas. As réplicas são distribuídas em até 10 a 20 domínios de falha.

* Em todas as regiões, cada partição é replicada. Cada região contém todas as partições de dados de um contêiner Cosmos do Azure e pode fornecer leituras, bem como servir gravações quando as gravações de várias regiões estiverem habilitadas.  

Se sua conta do Azure Cosmos for distribuída entre *n* regiões do Azure, haverá pelo menos *n* x 4 cópias de todos os seus dados. Ter uma conta do Azure Cosmos em mais de 2 regiões melhora a disponibilidade do seu aplicativo e fornece baixa latência entre as regiões associadas.

## <a name="slas-for-availability"></a>SLAs de disponibilidade

O Azure Cosmos DB fornece SLAs abrangentes que abrangem a taxa de transferência, a latência no 99 º percentil, a consistência e a alta disponibilidade. A tabela a seguir mostra as garantias de alta disponibilidade fornecidas por Azure Cosmos DB para contas de uma e várias regiões. Para maior disponibilidade de gravação, configure sua conta do Azure Cosmos para ter várias regiões de gravação.

|Tipo de operação  | Região única |Várias regiões (gravações de região única)|Várias regiões (gravações de várias regiões) |
|---------|---------|---------|-------|
|Gravações    | 99,99    |99,99   |99,999|
|Leituras     | 99,99    |99,999  |99,999|

> [!NOTE]
> Na prática, a disponibilidade de gravação real para desatualização limitada, sessão, prefixo consistente e modelos de consistência eventual é significativamente maior do que os SLAs publicados. A disponibilidade de leitura real para todos os níveis de coerência é significativamente mais alta do que os SLAs publicados.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>Alta disponibilidade com Azure Cosmos DB em caso de interrupções regionais

Para casos raros de interrupção regional, o Azure Cosmos DB garante que seu banco de dados esteja sempre altamente disponível. Os detalhes a seguir capturam Azure Cosmos DB comportamento durante uma interrupção, dependendo da configuração da sua conta do Azure Cosmos:

* Com Azure Cosmos DB, antes que uma operação de gravação seja confirmada para o cliente, os dados são permanentemente confirmados por um quorum de réplicas dentro da região que aceita as operações de gravação. Para obter mais detalhes, consulte [níveis de consistência e taxa de transferência](./consistency-levels.md#consistency-levels-and-throughput)

* Contas de várias regiões configuradas com várias regiões de gravação estarão altamente disponíveis para leituras e gravações. Os failovers regionais são detectados e manipulados no cliente do Azure Cosmos DB. Eles também são instantâneos e não exigem nenhuma alteração do aplicativo.

* Contas de uma única região poderão perder disponibilidade após uma indisponibilidade regional. É sempre recomendável configurar **pelo menos duas regiões** (preferencialmente, pelo menos duas regiões de gravação) com sua conta do Azure Cosmos para garantir a alta disponibilidade em todos os momentos.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Contas de várias regiões com uma região de gravação única (interrupção da região de gravação)

* Durante uma interrupção da região de gravação, a conta do Azure Cosmos promoverá automaticamente uma região secundária para ser a nova região de gravação primária quando **habilitar o failover automático** for configurado na conta do Azure Cosmos. Quando habilitado, o failover ocorrerá em outra região na ordem de prioridade de região que você especificou.

* Observe que o failover manual não deve ser disparado e não terá sucesso na presença de uma interrupção da região de origem ou de destino. Isso ocorre devido a uma verificação de consistência exigida pelo procedimento de failover que requer conectividade entre as regiões.

* Quando a região afetada anteriormente estiver novamente online, todos os dados de gravação que não foram replicados quando a região falharem serão disponibilizados por meio do [feed de conflitos](how-to-manage-conflicts.md#read-from-conflict-feed). Os aplicativos podem ler o feed de conflitos, resolver os conflitos com base na lógica específica do aplicativo e gravar os dados atualizados de volta no contêiner Cosmos do Azure, conforme apropriado.

* Depois que a região de gravação anteriormente afetada se recupera, ela fica automaticamente disponível como uma região de leitura. Você pode alternar de volta para a região recuperada como a região de gravação. Você pode alternar as regiões usando o [PowerShell, CLI do Azure ou portal do Azure](how-to-manage-database-account.md#manual-failover). Não há **perda de dados ou disponibilidade** antes, durante ou depois que você alterna a região de gravação e seu aplicativo continua a ser altamente disponível.

> [!IMPORTANT]
> É altamente recomendável que você configure as contas do Azure Cosmos usadas para cargas de trabalho de produção para **habilitar o failover automático**. Isso permite que Cosmos DB fazer failover dos bancos de dados da conta para regiões disponível automaticamente. Na ausência dessa configuração, a conta passará por perda de disponibilidade de gravação para toda a duração da interrupção da região de gravação, pois o failover manual não terá sucesso devido à falta de conectividade de região.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Contas de várias regiões com uma região de gravação única (interrupção da região de leitura)

* Durante uma interrupção da região de leitura, as contas do Azure Cosmos usando qualquer nível de consistência ou consistência forte com três ou mais regiões de leitura permanecerão altamente disponíveis para leituras e gravações.

* As contas do Azure Cosmos usando uma consistência forte com três regiões (uma gravação, duas lidas) manterão a disponibilidade de gravação durante uma interrupção da região de leitura. Para contas com duas regiões e failover automático habilitado, a conta deixará de aceitar gravações até que a região seja marcada como falha e failover automático ocorra.

* A região afetada é desconectada automaticamente e será marcada como offline. Os [SDKs de Azure Cosmos DB](sql-api-sdk-dotnet.md) redirecionarão as chamadas de leitura para a próxima região disponível na lista de regiões preferenciais.

* Se nenhuma das regiões na lista de regiões preferenciais estiver disponível, as chamadas retornarão automaticamente à região de gravação atual.

* Não é necessária nenhuma alteração no código do aplicativo para lidar com a interrupção da região de leitura. Quando a região de leitura afetada voltar a ficar online, ela será sincronizada automaticamente com a região de gravação atual e estará disponível novamente para atender às solicitações de leitura.

* As próximas leituras são redirecionadas para a região recuperada sem exigir nenhuma alteração do código do aplicativo. Durante o failover e a rejunção de uma região com falha anteriormente, as garantias de consistência de leitura continuam a ser respeitadas pelo Azure Cosmos DB.

* Mesmo em um evento raro e infeliz quando a região do Azure for permanentemente irrecuperável, não haverá perda de dados se sua conta do Azure Cosmos de várias regiões estiver configurada com consistência *forte* . No caso de uma região de gravação permanentemente irrecuperável, uma conta do Azure Cosmos de várias regiões configurada com consistência de desatualização limitada, a possível janela de perda de dados é restrita à janela de desatualização (*k* ou *T*) em que K = 100.000 atualizações ou T = 5 minutos, o que nunca acontece primeiro. Para os níveis de sessão, de prefixo consistente e de consistência eventual, a janela potencial de perda de dados é restrita a um máximo de 15 minutos. Para obter mais informações sobre os destinos RTO e RPO para Azure Cosmos DB, consulte [níveis de consistência e durabilidade de dados](./consistency-levels.md#rto)

## <a name="availability-zone-support"></a>Suporte à zona de disponibilidade

Além da resiliência entre regiões, Azure Cosmos DB também dá suporte à **redundância de zona** em regiões com suporte ao selecionar uma região a ser associada à sua conta do Azure Cosmos.

Com o suporte a zona de disponibilidade (AZ), o Azure Cosmos DB garantirá que as réplicas sejam colocadas em várias zonas em uma determinada região para fornecer alta disponibilidade e resiliência a falhas zonais. Zonas de Disponibilidade fornecer um SLA de disponibilidade de 99,995% sem alterações na latência. No caso de uma falha de zona única, a redundância de zona fornece durabilidade total de dados com RPO = 0 e disponibilidade com RTO = 0. A redundância de zona é um recurso suplementar para replicação regional. A redundância de zona sozinha não pode ser confiável para atingir a resiliência regional.

A redundância de zona só pode ser configurada ao adicionar uma nova região a uma conta do Azure Cosmos. Para regiões existentes, a redundância de zona pode ser habilitada removendo a região e, em seguida, adicionando-a novamente com a redundância de zona habilitada. Para uma única conta de região, isso requer a adição de uma região adicional para realizar o failover temporário e, em seguida, a remoção e a adição da região desejada com redundância de zona habilitada.

Ao configurar gravações de várias regiões para sua conta do Azure Cosmos, você pode aceitar a redundância de zona sem custo adicional. Caso contrário, consulte a tabela abaixo em relação aos preços para o suporte à redundância de zona. Para obter uma lista de regiões em que as zonas de disponibilidade estão disponíveis, consulte as [zonas de disponibilidade](../availability-zones/az-region.md).

A tabela a seguir resume a capacidade de alta disponibilidade de várias configurações de conta:

|KPI|Região única sem AZs|Região única com AZs|Várias regiões, gravações de região única com AZs|Várias regiões, gravações de várias regiões com AZs|
|---------|---------|---------|---------|---------|
|SLA de disponibilidade de gravação | 99,99% | 99,995% | 99,995% | 99,999% |
|SLA de leitura de disponibilidade  | 99,99% | 99,995% | 99,995% | 99,999% |
|Falhas de zona – perda de dados | Perda de dados | Sem perda de dados | Sem perda de dados | Sem perda de dados |
|Falhas de zona – disponibilidade | Perda de disponibilidade | Sem perda de disponibilidade | Sem perda de disponibilidade | Sem perda de disponibilidade |
|Interrupção regional – perda de dados | Perda de dados |  Perda de dados | Dependente do nível de consistência. Consulte [compensações de consistência, disponibilidade e desempenho](./consistency-levels.md) para obter mais informações. | Dependente do nível de consistência. Consulte [compensações de consistência, disponibilidade e desempenho](./consistency-levels.md) para obter mais informações.
|Interrupção regional – disponibilidade | Perda de disponibilidade | Perda de disponibilidade | Nenhuma perda de disponibilidade para a falha de região de leitura, temporária para falha de região de gravação | Sem perda de disponibilidade |
|Preço (***1** _) | N/D | Taxa de RU/s 1,25 x provisionada | Taxa de RU/s x 1,25 provisionada (_ *_2_* *) | Taxa de gravação de várias regiões |

***1*** para as unidades de solicitação de contas sem servidor (ru) são multiplicadas por um fator de 1,25.

a taxa de ***2*** 1,25 aplica-se somente a essas regiões nas quais AZ está habilitado.

Zonas de Disponibilidade pode ser habilitado por meio de:

* [Azure portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

* [PowerShell do Azure](manage-with-powershell.md#create-account)

* [CLI do Azure](manage-with-cli.md#add-or-remove-regions)

* [Modelos do Gerenciador de Recursos do Azure](./manage-with-templates.md)

## <a name="building-highly-available-applications"></a>Criando aplicativos altamente disponíveis

* Examine o [comportamento esperado dos SDKs do Azure Cosmos](troubleshoot-sdk-availability.md) durante esses eventos e quais são as configurações que o afetam.

* Para garantir alta disponibilidade de gravação e leitura, configure sua conta do Azure Cosmos para abranger pelo menos duas regiões com regiões de várias gravações. Essa configuração fornecerá a mais alta disponibilidade, a menor latência e a melhor escalabilidade para leituras e gravações apoiadas por SLAs. Para saber mais, Confira como [configurar sua conta do Azure cosmos com várias regiões de gravação](tutorial-global-distribution-sql-api.md).

* Para contas do Azure Cosmos de várias regiões configuradas com uma região de gravação única, [habilite o failover automático usando CLI do Azure ou portal do Azure](how-to-manage-database-account.md#automatic-failover). Depois de habilitar o failover automático, sempre que houver um desastre regional, o Cosmos DB fará o failover da sua conta automaticamente.  

* Mesmo que sua conta do Azure Cosmos esteja altamente disponível, seu aplicativo pode não ter sido projetado corretamente para permanecer altamente disponível. Para testar a alta disponibilidade de ponta a ponta de seu aplicativo, como parte de seus testes de aplicativo ou de recuperação de desastre (DR), desabilitar temporariamente o failover automático para a conta, invocar o [failover manual usando o PowerShell, CLI do Azure ou portal do Azure](how-to-manage-database-account.md#manual-failover), em seguida, monitorar o failover do aplicativo. Uma vez concluído, você pode fazer failover para a região primária e restaurar o failover automático para a conta.

> [!IMPORTANT]
> Não invoque o failover manual durante uma interrupção de Cosmos DB nas regiões de origem ou de destino, pois requer conectividade de regiões para manter a consistência dos dados e ele não terá sucesso.

* Em um ambiente de banco de dados distribuído globalmente, há uma relação direta entre o nível de consistência e a durabilidade dos dados na presença de uma interrupção em toda a região. À medida que você vai desenvolvendo o plano de continuidade dos negócios, precisará saber qual é o tempo máximo aceitável antes que o aplicativo se recupere completamente após um evento de interrupção. O tempo necessário para o aplicativo se recuperar totalmente é conhecido como RTO (objetivo de tempo de recuperação). Também é necessário saber o período máximo de atualizações de dados recentes que o aplicativo pode perder sem maiores problemas durante a recuperação após um evento de interrupção. O período de tempo de atualizações que você pode perder é conhecido como RPO (objetivo de ponto de recuperação). Para ver o RTO e o RPO do Azure Cosmos DB, confira [Níveis de consistência e durabilidade dos dados](./consistency-levels.md#rto)

## <a name="what-to-expect-during-a-region-outage"></a>O que esperar durante uma interrupção da região

Para contas de região única, os clientes sofrerão perda de disponibilidade de leitura e gravação.

As contas de várias regiões passarão por comportamentos diferentes dependendo da tabela a seguir.

| Regiões de gravação | failover automático | O que esperar | O que fazer |
| -- | -- | -- | -- |
| Região de gravação única | não ativado | No caso de interrupção em uma região de leitura, todos os clientes serão redirecionados para outras regiões. Nenhuma perda de disponibilidade de leitura ou gravação. Sem perda de dados. <p/> No caso de uma interrupção na região de gravação, os clientes sofrerão perda de disponibilidade de gravação. A perda de dados será dependente do nível de constistency selecionado. <p/> Cosmos DB irá restaurar a disponibilidade de gravação automaticamente quando a interrupção terminar. | Durante a interrupção, verifique se há capacidade suficiente provisionada nas regiões restantes para dar suporte ao tráfego de leitura. <p/> Não *dispare um* failover manual durante a interrupção, pois ele não terá sucesso. <p/> Quando a interrupção terminar, ajuste novamente a capacidade provisionada conforme apropriado. |
| Região de gravação única | habilitado | No caso de interrupção em uma região de leitura, todos os clientes serão redirecionados para outras regiões. Nenhuma perda de disponibilidade de leitura ou gravação. Sem perda de dados. <p/> No caso de uma interrupção na região de gravação, os clientes sofrerão perda de disponibilidade de gravação até que Cosmos DB elege automaticamente uma nova região como a nova região de gravação de acordo com suas preferências. A perda de dados será dependente do nível de constistency selecionado. | Durante a interrupção, verifique se há capacidade suficiente provisionada nas regiões restantes para dar suporte ao tráfego de leitura. <p/> Não *dispare um* failover manual durante a interrupção, pois ele não terá sucesso. <p/> Quando a interrupção terminar, você poderá recuperar os dados não replicados na região com falha de seu feed de [conflitos](how-to-manage-conflicts.md#read-from-conflict-feed), mover a região de gravação de volta para a região original e ajustar novamente a capacidade provisionada conforme apropriado. |
| Várias regiões de gravação | Não aplicável | Nenhuma perda de disponibilidade de leitura ou gravação. <p/> Perda de dados de acordo com o nível de consistência selecionado. | Durante a interrupção, verifique se há capacidade suficiente provisionada nas regiões restantes para dar suporte a tráfego adicional. <p/> Quando a interrupção terminar, você poderá recuperar os dados não replicados na região com falha de seu feed de [conflitos](how-to-manage-conflicts.md#read-from-conflict-feed) e ajustar novamente a capacidade provisionada conforme apropriado. |

## <a name="next-steps"></a>Próximas etapas

Em seguida, você poderá ler os artigos a seguir:

* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](./consistency-levels.md)

* [Taxa de transferência provisionada para dimensionamento global](./request-units.md)

* [Distribuição global - nos bastidores](global-dist-under-the-hood.md)

* [Níveis de consistência no Azure Cosmos DB](consistency-levels.md)

* [Como configurar sua conta do cosmos com várias regiões de gravação](how-to-multi-master.md)

* [Comportamento do SDK em ambientes de várias regiões](troubleshoot-sdk-availability.md)