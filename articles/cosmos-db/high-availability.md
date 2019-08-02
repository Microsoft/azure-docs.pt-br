---
title: Alta disponibilidade no Azure Cosmos DB
description: Este artigo descreve como o Azure Cosmos DB fornece alta disponibilidade
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 904994134db28a8244f15ff42e0104e8565c68dd
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839792"
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

Interrupções regionais não são incomuns e o Azure Cosmos DB garante que seu banco de dados esteja sempre altamente disponível. Os detalhes a seguir capturam o comportamento do Cosmos DB durante uma interrupção, dependendo da configuração de conta do Cosmos:

- Com o Cosmos DB, antes que uma operação de gravação seja confirmada para o cliente, os dados serão confirmados por um quorum de réplicas dentro da região que aceitará as operações de gravação.

- Contas de várias regiões configuradas com várias regiões de gravação estarão altamente disponíveis para leituras e gravações. Failovers regionais são instantâneos e não exigem nenhuma alteração do aplicativo.

- **Contas de várias regiões com uma região de gravação única (interrupção de região de gravação):** Durante uma interrupção da região de gravação, essas contas permanecerão altamente disponíveis para leituras. No entanto, para gravações devem **"habilitar o failover automático"** no seu Cosmos conta para failover de região para outra região afetada. O failover ocorrerá na ordem de prioridade de região especificada por você. Quando a região afetada fique online novamente, os dados não replicados presentes na região de gravação afetados durante a interrupção são disponibilizados por meio de [conflitos feed](how-to-manage-conflicts.md#read-from-conflict-feed). Aplicativos podem ler os conflitos de feed, resolva os conflitos com base na lógica específica do aplicativo e gravar os dados atualizados de volta para o contêiner do Cosmos conforme apropriado. Depois que a região de gravação anteriormente afetada se recupera, ela fica automaticamente disponível como uma região de leitura. É possível invocar um failover manual e configurar a região afetada como a região de gravação. Novamente, você pode fazer um failover manual por meio [CLI do Azure ou portal do Azure](how-to-manage-database-account.md#manual-failover). Não há **nenhuma perda de dados ou disponibilidade** antes, durante ou após o failover manual. O aplicativo continua sendo altamente disponível. 

- **Contas de várias regiões com uma região de gravação única (interrupção de região de leitura):** Durante uma interrupção de região de leitura, essas contas permanecerão altamente disponíveis para leituras e gravações. A região afetada será desconectada automaticamente da região de gravação e será marcada como offline. O [SDKs do Cosmos DB](sql-api-sdk-dotnet.md) irá redirecionar chamadas de leitura para a próxima região disponível na lista de regiões preferenciais. Se nenhuma das regiões na lista de regiões preferenciais estiver disponível, as chamadas retornarão automaticamente à região de gravação atual. Não é necessária nenhuma alteração no código do aplicativo para lidar com a interrupção da região de leitura. Por fim, quando a região afetada ficar online novamente, a região de leitura afetada anteriormente será automaticamente sincronizada com a região de gravação atual e estará disponível novamente para fornecer solicitações de leitura. Leituras subsequentes são redirecionadas para a região recuperada sem exigir nenhuma alteração ao código do aplicativo. Durante o failover e incluir novamente de uma região com falha, consistência de leitura em garantias continuam sendo cumpridas pelo Cosmos DB.

- Contas de uma única região poderão perder disponibilidade após uma indisponibilidade regional. É recomendável sempre definir **pelo menos duas regiões** (de preferência, pelo menos dois gravar regiões) com sua conta do Cosmos para garantir a alta disponibilidade em todos os momentos.

- Mesmo em um evento raro e infeliz quando a região do Azure está permanentemente irrecuperável, não há nenhuma perda de dados se sua conta do Cosmos de várias regiões é configurada com o nível de consistência padrão de *forte*. No caso de uma região de gravação irrecuperáveis permanentemente, para as contas do Cosmos de várias regiões configuradas com a consistência de desatualização limitada, a janela potencial de perda de dados é restrita para a janela de desatualização (*K* ou *T*); por sessão, níveis de consistência de prefixo consistente e eventual, a janela potencial de perda de dados é restrita a um máximo de cinco segundos. 

## <a name="availability-zone-support"></a>Suporte para zonas de disponibilidade

O Azure Cosmos DB é um serviço de banco de dados distribuído globalmente, vários mestres que fornece alta disponibilidade e resiliência durante interrupções regionais. Além para cruzar a resiliência de região, agora você pode habilitar **redundância de zona** ao selecionar uma região para associar ao seu banco de dados Cosmos do Azure. 

Com o suporte de zona de disponibilidade do Azure Cosmos DB garantirá que as réplicas são colocadas em várias zonas dentro de uma determinada região para fornecer alta disponibilidade e resiliência durante falhas de zona. Não há nenhuma alteração à latência e outros SLAs nessa configuração. No caso de uma falha de única zona, redundância de zona fornece a durabilidade de dados completo com RPO = 0 e a disponibilidade com RTO = 0. 

Redundância de zona é uma *recurso suplementar* para o [replicação multimestre](how-to-multi-master.md) recurso. Redundância de zona sozinha não pode ser usada para obter resiliência regional. Por exemplo, no caso de interrupções regionais ou acesso de baixa latência em todas as regiões, é recomendável para ter várias regiões de gravação, além de redundância de zona. 

Ao configurar as gravações de várias regiões para sua conta do Cosmos do Azure, você pode aceitar redundância de zona sem nenhum custo extra. Caso contrário, consulte a observação abaixo sobre os preços para o suporte de redundância de zona. Você pode habilitar a redundância de zona em uma região existente da sua conta do Azure Cosmos removendo a região e adicioná-lo novamente com a redundância de zona habilitada.

Esse recurso está disponível nas seguintes regiões do Azure:

* Sul do Reino Unido
* Sudeste Asiático 
* East US
* Leste dos EUA 2 
* Centro dos EUA

> [!NOTE] 
> Habilitando as zonas de disponibilidade para uma conta do Azure Cosmos de região única resultará em encargos que são equivalentes à adição de uma região adicional à sua conta. Para obter detalhes sobre preços, consulte o [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) e o [custos de várias regiões no Azure Cosmos DB](optimize-cost-regions.md) artigos. 

A tabela a seguir resume a capacidade de alta disponibilidade de várias configurações de conta: 

|KPI  |Região única sem zonas de disponibilidade (Non-AZ)  |Única região com zonas de disponibilidade (AZ)  |Grava de várias regiões com zonas de disponibilidade (2 regiões, AZ) – a configuração recomendada de maioria |
|---------|---------|---------|---------|
|SLA de disponibilidade de gravação     |   99,99%      |    99,99%     |  99,999%  |
|Leia o SLA de disponibilidade   |   99,99%      |   99,99%      |  99,999%       |
|Preço  |  Taxa de cobrança de região única |  Taxa de cobrança de zona de disponibilidade de região única |  Taxa de cobrança de várias regiões       |
|Falhas de zona – perda de dados   |  Perda de dados  |   Sem perda de dados |   Sem perda de dados  |
|Falhas de zona – disponibilidade |  Perda de disponibilidade  | Sem perda de disponibilidade  |  Sem perda de disponibilidade  |
|Latência de leitura    |  Entre a região    |   Entre a região   |    Baixo  |
|Latência de gravação    |   Entre a região   |  Entre a região    |   Baixo   |
|Interrupção regional – perda de dados    |   Perda de dados      |  Perda de dados       |   Perda de dados <br/><br/> Quando uso limitado consistência de desatualização limitada com o mestre de múltiplos e mais de uma região, perda de dados é limitada para a desatualização limitada configurada na sua conta. <br/><br/> Perda de dados durante a interrupção regional pode ser evitada Configurando uma coerência forte em várias regiões. Essa opção é fornecido com as compensações que afetam o desempenho e disponibilidade.      |
|Interrupção regional – disponibilidade  |  Perda de disponibilidade       |  Perda de disponibilidade       |  Sem perda de disponibilidade  |
|Taxa de transferência    |  Taxa de transferência provisionada de X RU/s      |  Taxa de transferência provisionada de X RU/s       |  2 x taxa de transferência provisionada de RU/s <br/><br/> Esse modo de configuração requer o dobro da taxa de transferência em comparação com uma única região com zonas de disponibilidade porque há duas regiões.   |

> [!NOTE] 
> Para habilitar o suporte de zona de disponibilidade para uma região de várias conta Cosmos do Azure, a conta deve ter as gravações de vários mestres habilitadas.


Ao adicionar uma região a contas do Cosmos do Azure novas ou existentes, você pode habilitar redundância de zona. Atualmente, só é possível habilitar a redundância de zona usando o Azure portal, PowerShell e o Azure Resource Manager de modelos. Para habilitar a redundância de zona em sua conta do Cosmos do Azure, você deve definir a `isZoneRedundant` sinalizador como `true` para um local específico. Você pode definir esse sinalizador dentro da propriedade locais. Por exemplo, o seguinte trecho do powershell permite a redundância de zona para a região de "Sudeste Asiático":

```powershell
$locations = @( 
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" }, 
    @{ "locationName"="East US"; "failoverPriority"=1 } 
) 
```

Você pode habilitar as zonas de disponibilidade usando o portal do Azure ao criar uma conta do Cosmos do Azure. Quando você cria uma conta, certifique-se de habilitar o **redundância geográfica**, **grava em várias regiões**e escolha uma região onde as zonas de disponibilidade têm suporte: 

![Habilitar as zonas de disponibilidade usando o portal do Azure](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Criando aplicativos altamente disponíveis

- Para garantir a alta disponibilidade de leitura e gravação, configure sua conta do Cosmos para abranger pelo menos duas regiões com várias regiões de gravação. Essa configuração fornece a mais alta disponibilidade, latência mais baixa, e melhor escalabilidade para as leituras e gravações com suporte por SLAs. Para saber mais, confira como [configurar sua conta do Cosmos com várias regiões de gravação](tutorial-global-distribution-sql-api.md).

- Para contas do Cosmos de várias regiões configuradas com uma única região de gravação, [habilite o failover automático usando a CLI do Azure ou o portal do Azure](how-to-manage-database-account.md#automatic-failover). Depois de habilitar o failover automático, sempre que houver um desastre regional, o Cosmos DB fará o failover da sua conta automaticamente.  

- Mesmo se a conta do Cosmos estiver altamente disponível, seu aplicativo poderá não ser corretamente projetado para permanecer altamente disponível. Para testar a alta disponibilidade de ponta a ponta de seu aplicativo, chamar periodicamente o [failover manual por meio da CLI do Azure ou portal do Azure](how-to-manage-database-account.md#manual-failover), como parte do seu teste de aplicativos ou a recuperação de desastres (DR) simulações.

- Em um ambiente de banco de dados globalmente distribuído, há uma relação direta entre a durabilidade de dados e o nível de consistência na presença de uma interrupção em toda a região. À medida que você vai desenvolvendo o plano de continuidade dos negócios, precisará saber qual é o tempo máximo aceitável antes que o aplicativo se recupere completamente após um evento de interrupção. O tempo necessário para o aplicativo se recuperar totalmente é conhecido como RTO (objetivo de tempo de recuperação). Também é necessário saber o período máximo de atualizações de dados recentes que o aplicativo pode perder sem maiores problemas durante a recuperação após um evento de interrupção. O período de tempo de atualizações que você pode perder é conhecido como RPO (objetivo de ponto de recuperação). Para ver o RTO e o RPO do Azure Cosmos DB, confira [Níveis de consistência e durabilidade dos dados](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Próximas etapas

Em seguida, você poderá ler os artigos a seguir:

* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)
* [Taxa de transferência provisionada para dimensionamento global](scaling-throughput.md)
* [Distribuição global – nos bastidores](global-dist-under-the-hood.md)
* [Níveis de coerência no Azure Cosmos DB](consistency-levels.md)
* [Como configurar sua conta do Cosmos com várias regiões de gravação](how-to-multi-master.md)
