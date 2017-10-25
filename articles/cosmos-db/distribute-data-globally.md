---
title: Distribuir dados globalmente com o Azure Cosmos DB | Microsoft Docs
description: "Saiba mais sobre replicação geográfica em escala mundial, failover e recuperação de dados usando bancos de dados globais no Azure Cosmos DB, um serviço de multimodelo de banco de dados distribuído globalmente."
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: ba5ad0cc-aa1f-4f40-aee9-3364af070725
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2017
ms.author: arramac
ms.openlocfilehash: a293ab42591fad2b913971465bc85743bcf05dad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-distribute-data-globally-with-azure-cosmos-db"></a>Como distribuir os dados globalmente com o Azure Cosmos DB
O Azure é onipresente: ele tem uma superfície global que abrange mais de 30 regiões geográficas e aumenta continuamente. Com sua presença em todo o mundo, um dos recursos diferenciados que o Azure oferece aos desenvolvedores é a capacidade de criar, implantar e gerenciar aplicativos distribuídos globalmente com facilidade. 

O [Azure Cosmos DB](../cosmos-db/introduction.md) é o serviço multimodelo de banco de dados da Microsoft, distribuído globalmente, para aplicativos críticos. O Azure Cosmos DB fornece distribuição global turnkey, [dimensionamento elástico da produtividade e do armazenamento](../cosmos-db/partition-data.md) no mundo todo, latências de milissegundos de um dígito no 99º percentil, [cinco níveis de consistência bem-definidos](consistency-levels.md) e garantia de alta disponibilidade, tudo isso com suporte de [SLAs líderes do setor](https://azure.microsoft.com/support/legal/sla/cosmos-db/). O Azure Cosmos DB [indexa dados automaticamente](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sem a necessidade de lidar com o gerenciamento do esquema e do índice. Ele tem vários modelos e dá suporte a modelos de dados de colunas, gráficos, valores-chave e documentos. Como um serviço de nuvem, o Azure Cosmos DB foi cuidadosamente desenvolvido com distribuição global e multilocatário desde o início.

**Uma única coleção do Azure Cosmos DB particionada e distribuída em várias regiões do Azure**

![Coleção do Azure Cosmos DB particionada e distribuída em três regiões](./media/distribute-data-globally/global-apps.png)

Como aprendemos durante a criação do Azure Cosmos DB, a adição da distribuição global não pode ser uma consideração posterior: ela não pode ser “forçada” em um sistema de banco de dados de “site único”. Os recursos oferecidos por um banco de dados distribuído globalmente se estendem além daqueles da recuperação de desastres geográficos tradicional (Geo-DR) oferecidos por bancos de dados de "site único". Bancos de dados de site único que oferecem a capacidade de Geo-DR são um subconjunto restrito de bancos de dados distribuídos globalmente. 

Com a distribuição de global turnkey do Azure Cosmos DB, os desenvolvedores não precisam criar seu próprio scaffolding de replicação utilizando o padrão Lambda (por exemplo, [replicação AWS DynamoDB](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md)) no log de banco de dados ou fazendo “gravações duplas” em várias regiões. Não recomendamos essas abordagens, pois é impossível garantir sua exatidão e fornecer SLAs robustos. 

Neste artigo, apresentamos uma visão geral das funcionalidades de distribuição global do Azure Cosmos DB. Também descrevemos a abordagem exclusiva do Azure Cosmos DB para o fornecimento de SLAs abrangentes. 

## <a id="EnableGlobalDistribution"></a>Habilitando a distribuição global turnkey
O Azure Cosmos DB fornece as funcionalidades a seguir para que você possa escrever facilmente aplicativos de escala mundial. Essas funcionalidades estão disponíveis por meio de [APIs REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/) baseadas em provedores de recursos do Azure Cosmos DB, bem como no portal do Azure.

### <a id="RegionalPresence"></a>Presença regional em todos os lugares 
O Azure está constantemente expandindo sua presença geográfica colocando [novas regiões](https://azure.microsoft.com/regions/) online. O Azure Cosmos DB está disponível em todas as novas regiões do Azure por padrão. Isso permite que você associe uma região geográfica à sua conta de banco de dados do Azure Cosmos DB assim que o Azure disponibilizar a nova região.

**O Azure Cosmos DB está disponível em todas as regiões do Azure por padrão**

![Azure Cosmos DB disponível em todas as regiões do Azure](./media/distribute-data-globally/azure-regions.png)

### <a id="UnlimitedRegionsPerAccount"></a>Associando um número ilimitado de regiões à sua conta de banco de dados do Azure Cosmos DB
O Azure Cosmos DB permite associar qualquer número de regiões do Azure à sua conta de banco de dados do Azure Cosmos DB. Exceto as restrições de isolamento geográfico (por exemplo, China e Alemanha), não há limitação quanto ao número de regiões que podem ser associadas à sua conta de banco de dados do Azure Cosmos DB. A figura a seguir mostra uma conta de banco de dados configurada para abranger 25 regiões do Azure.  

**Conta de banco de dados do Azure Cosmos DB de um locatário abrangendo 25 regiões do Azure**

![Conta de banco de dados do Azure Cosmos DB abrangendo 25 regiões do Azure](./media/distribute-data-globally/spanning-regions.png)

### <a id="PolicyBasedGeoFencing"></a>Isolamento geográfico baseado em políticas
O Azure Cosmos DB foi projetado para ter funcionalidades de isolamento geográfico baseado em política. O isolamento geográfico é um componente importante para garantir restrições de conformidade e governança de dados e pode impedir a associação de uma região específica a sua conta. Exemplos de isolamento geográfico incluem (sem restrição) o escopo de distribuição global para as regiões em uma nuvem soberana (por exemplo, na China e na Alemanha) ou em um limite de tributação do governo (por exemplo, Austrália). As políticas são controladas usando os metadados de sua assinatura do Azure.

### <a id="DynamicallyAddRegions"></a>Adicionar e remover regiões dinamicamente
O Azure Cosmos DB permite que você adicione (associe) ou remova (desassocie) regiões de sua conta de banco de dados a qualquer momento (consulte a [figura anterior](#UnlimitedRegionsPerAccount)). Em virtude da replicação de dados entre partições em paralelo, o Azure Cosmos DB garante que quando uma nova região ficar online, o Azure Cosmos DB estará disponível em até 30 minutos em qualquer lugar do mundo para até 100 TB. 

### <a id="FailoverPriorities"></a>Prioridades de failover
Para controlar a sequência exata de failovers regionais quando há uma interrupção de várias regiões, o Azure Cosmos DB permite associar a prioridade a várias regiões associadas à conta de banco de dados (consulte a figura a seguir). O Azure Cosmos DB garante que a sequência de failover automático ocorre na ordem de prioridade especificada. Para obter mais informações sobre failovers regionais, consulte [Failovers regionais automáticos para continuidade dos negócios no Azure Cosmos DB](regional-failover.md).

**Um locatário do Azure Cosmos DB pode configurar a ordem de prioridade do failover (painel direito) de regiões associadas a uma conta de banco de dados**

![Configurando prioridades de failover com o Azure Cosmos DB](./media/distribute-data-globally/failover-priorities.png)

### <a id="ConsistencyLevels"></a>Vários modelos de consistência bem definidos para bancos de dados replicados globalmente
O Azure Cosmos DB expõe [vários níveis de consistência bem definidos](consistency-levels.md) com suporte de SLAs. Você pode escolher um modelo específico de consistência (da lista de opções disponíveis) dependendo da carga de trabalho/dos cenários. 

### <a id="TunableConsistency"></a>Consistência ajustável para bancos de dados replicados globalmente
O Azure Cosmos DB permite que você substitua de forma programática e reduza a opção de consistência padrão por solicitação, em tempo de execução. 

### <a id="DynamicallyConfigurableReadWriteRegions"></a>Regiões de leitura e gravação configuráveis dinamicamente
O Azure Cosmos DB permite configurar as regiões (associadas ao banco de dados) para regiões de “leitura”, “gravação” ou “leitura/gravação”. 

### <a id="ElasticallyScaleThroughput"></a>Dimensionando a taxa de transferência com elasticidade entre regiões do Azure
Você pode dimensionar de modo elástico uma coleção do Azure Cosmos DB provisionando a produtividade de forma programática. A taxa de transferência é aplicada a todas as regiões em que a coleção é distribuída.

### <a id="GeoLocalReadsAndWrites"></a>Leituras e gravações geograficamente locais
O principal benefício de um banco de dados distribuído globalmente é oferecer acesso de baixa latência para os dados em qualquer lugar no mundo. O Azure Cosmos DB oferece garantias de baixa latência em P99 para várias operações de banco de dados. Isso garante que todas as leituras sejam roteadas para a região de leitura local mais próxima. Para atender a uma solicitação de leitura, o local de quórum para a região em que a leitura é emitida é usado; o mesmo se aplica às gravações. Uma gravação é confirmada somente depois que a maioria das réplicas confirma de forma permanente a gravação localmente, mas sem ser ligada a réplicas remotas para confirmar as gravações. Em outras palavras, o protocolo de replicação do Azure Cosmos DB opera com a premissa de que os quoruns de leitura e gravação sempre são locais para as regiões de leitura e gravação, respectivamente, no qual a solicitação é emitida.

### <a id="ManualFailover"></a>Iniciar manualmente o failover regional
O Azure Cosmos DB permite disparar o failover da conta de banco de dados para validar as propriedades de disponibilidade *de ponta a ponta* de todo o aplicativo (além do banco de dados). Como as propriedades de segurança e execução da detecção de falha e eleição de líder são garantidas, o Azure Cosmos DB garante *zero perda de dados* para uma operação de failover manual iniciada pelo locatário.

### <a id="AutomaticFailover"></a>Failover automático
O Azure Cosmos DB dá suporte ao failover automático no caso de uma ou mais interrupções regionais. Durante um failover regional, o Azure Cosmos DB mantém seus SLAs de latência de leitura, disponibilidade de tempo de atividade, consistência e produtividade. O Azure Cosmos DB fornece um limite superior na duração de uma operação de failover automático para conclusão. Essa é a janela de perda de dados potencial durante a interrupção regional.

### <a id="GranularFailover"></a>Projetado para diferentes granularidades de failover
Atualmente, os recursos de failover automático e manual são expostos na granularidade da conta do banco de dados. Observe que, internamente, o Azure Cosmos DB foi projetado para oferecer failover *automático* com uma granularidade maior de um banco de dados, uma coleção ou até mesmo uma partição (de uma coleção que tem um intervalo de chaves). 

### <a id="MultiHomingAPIs"></a>APIs de hospedagem múltipla no Azure Cosmos DB
O Azure Cosmos DB permite que você interaja com o banco de dados usando pontos de extremidade lógicos (independentes de região) ou físicos (específicos à região). Usar pontos de extremidade lógicos garante que o aplicativo possa ser multihomed de forma transparente no caso de failover. Os pontos de extremidade físicos fornecem controle refinado para que o aplicativo redirecione leituras e gravações para regiões específicas.

Encontre informações sobre como configurar preferências de leitura para a [API do DocumentDB](../cosmos-db/tutorial-global-distribution-documentdb.md), [API do Graph](../cosmos-db/tutorial-global-distribution-graph.md), [API da Tabela](../cosmos-db/tutorial-global-distribution-table.md) e [API do MongoDB](../cosmos-db/tutorial-global-distribution-mongodb.md) em seus respectivos artigos vinculados.

### <a id="TransparentSchemaMigration"></a>Migração de esquema e de índice de banco de dados transparente e consistente 
O Azure Cosmos DB é totalmente [independente de esquema](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). O design exclusivo de seu mecanismo de banco de dados permite que ele indexe de forma automática e síncrona todos os dados que consome, sem a necessidade de esquema ou índices secundários de sua parte. Isso permite que você itere o aplicativo distribuído globalmente com rapidez, sem se preocupar com a migração de esquema e de índice de banco de dados ou a coordenação da implantação de aplicativos de várias fases de alterações de esquema. O Azure Cosmos DB garante que todas as alterações feitas explicitamente por você nas políticas de indexação não resultam na degradação de desempenho ou disponibilidade.  

### <a id="ComprehensiveSLAs"></a>SLAs abrangentes (além de apenas alta disponibilidade)
Como um serviço de banco de dados distribuído globalmente, o Azure Cosmos DB oferece um SLA bem definido para **perda de dados**, **disponibilidade**, **latência em P99**, **produtividade** e **consistência** para o banco de dados como um todo, independentemente do número de regiões associadas ao banco de dados.  

## <a id="LatencyGuarantees"></a>Garantias de latência
O principal benefício de um serviço de banco de dados globalmente distribuído como o Azure Cosmos DB é oferecer acesso de baixa latência aos dados em qualquer lugar do mundo. O Azure Cosmos DB oferece baixa latência garantida a P99 para várias operações de banco de dados. O protocolo de replicação utilizado pelo Azure Cosmos DB garante que as operações de banco de dados (de preferência, leituras e gravações) são sempre executadas na região local do cliente. O SLA de latência do Azure Cosmos DB inclui P99 para leituras, gravações indexadas (de forma síncrona) e consultas para diversos tamanhos de solicitação e resposta. As garantias de latência para gravações incluem confirmações de quórum de maioria durável no datacenter local.

### <a id="LatencyAndConsistency"></a>Relação da latência com a consistência 
Para que um serviço distribuído globalmente ofereça forte consistência em uma instalação distribuída globalmente, ele precisa replicar sincronicamente as gravações síncronas ou executar leituras entre regiões. A velocidade da luz e a confiabilidade da rede de longa distância ditam que a forte consistência resulta em altas latências e baixa disponibilidade de operações de banco de dados. Portanto, a fim de oferecer latências menores garantidas com P99 e 99,99 de disponibilidade, o serviço deve empregar a replicação assíncrona. Por sua vez, isso requer que o serviço também ofereça [opções de consistência bem definidas e reduzidas](consistency-levels.md), mais fracas do que o ideal (para oferecer garantias de baixa latência de disponibilidade ) e idealmente mais seguras do que a consistência "eventual" (para oferecer um modelo de programação intuitivo).

O Azure Cosmos DB garante que uma operação de leitura não precisa acessar réplicas em várias regiões para fornecer a garantia de nível de consistência específica. Da mesma forma, ele garante que uma operação de gravação não seja bloqueada enquanto os dados são replicados em todas as regiões (ou seja, as gravações são replicadas de maneira assíncrona entre regiões). Para contas de banco de dados de várias regiões, estão disponíveis vários níveis de consistência reduzida. 

### <a id="LatencyAndAvailability"></a>Relação da latência com a disponibilidade 
Disponibilidade e latência são os dois lados da mesma moeda. Falamos sobre a latência da operação no estado estável e a disponibilidade, no caso de falhas. Do ponto de vista do aplicativo, uma operação de banco de dados com execução lenta é indistinguível de um banco de dados que não está disponível. 

Para distinguir a alta latência da indisponibilidade, o Azure Cosmos DB fornece um limite superior absoluto na latência para várias operações de banco de dados. Se a operação de banco de dados leva mais tempo do que o limite superior para ser concluída, o Azure Cosmos DB retorna um erro de tempo limite. O SLA de disponibilidade do Azure Cosmos DB garante que os tempos limite são contados em relação ao SLA de disponibilidade. 

### <a id="LatencyAndThroughput"></a>Relação da latência com a taxa de transferência
O Azure Cosmos DB não o obriga a escolher entre latência e produtividade. Ele respeita o SLA para a latência em P99 e fornece a taxa de transferência que você provisionou. 

## <a id="ConsistencyGuarantees"></a>Garantias de consistência
Embora o [modelo de consistência forte](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) seja o padrão ouro de capacidade de programação, ele acarreta alta latência (no estado estável) e perda de disponibilidade (no caso de falhas). 

O Azure Cosmos DB oferece um modelo de programação bem definido de forma que você entenda a consistência dos dados replicados. Para permitir a criação de aplicativos de hospedagem múltipla, os modelos de consistência expostos pelo Azure Cosmos DB foram projetados para serem independentes de região e não dependem da região na qual as leituras e gravações são feitas. 

O SLA de consistência do Azure Cosmos DB garante que 100% das solicitações de leitura atenderão à garantia de consistência para o nível de consistência solicitado por você (o nível de consistência padrão da conta de banco de dados ou o valor substituído na solicitação). Uma solicitação de leitura é considerada como tendo cumprido o SLA de consistência se todas as garantias de consistência associadas ao nível de consistência são atendidas. A tabela a seguir captura as garantias de consistência que correspondem aos níveis de consistência específicos oferecidos pelo Azure Cosmos DB.

**Garantias de consistência associadas a um nível de consistência específico do Azure Cosmos DB**

<table>
    <tr>
        <td><strong>Nível de Consistência</strong></td>
        <td><strong>Características de Consistência</strong></td>
        <td><strong>SLA</strong></td>
    </tr>
    <tr>
        <td rowspan="3">Session</td>
        <td>Ler sua própria gravação</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Leitura monotônica</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Prefixo consistente</td>
        <td>100%</td>
    </tr>
    <tr>
        <td rowspan="3">Bounded staleness</td>
        <td>Leitura monotônica (em uma região)</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Prefixo consistente</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Desatualização limitada &lt; K,T</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Prefixo consistente</td>
        <td>Prefixo consistente</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Strong</td>
        <td>Linearizável</td>
        <td>100%</td>
    </tr>
</table>

### <a id="ConsistencyAndAvailability"></a>Relação de consistência com a disponibilidade
O [resultado de impossibilidade](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) do [teorema CAP](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) prova que é impossível, de fato, que o sistema permaneça disponível e ofereça consistência linearizável em caso de falhas. O serviço de banco de dados deve escolher CP ou AP. Sistemas CP abrem mão da disponibilidade em favor da consistência linearizável, enquanto sistemas AP abandonam a [consistência linearizável](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) em favor da disponibilidade. O Azure Cosmos DB nunca viola o nível de consistência solicitado, o que, formalmente, o torna um sistema CP. No entanto, na prática, a consistência não é uma questão de tudo ou nada. Há vários modelos de consistência bem definidos no espectro de consistência entre consistência eventual e linearizável. No Azure Cosmos DB, tentamos identificar vários dos modelos de consistência reduzidos com a aplicabilidade do mundo real e um modelo de programação intuitivo. O Azure Cosmos DB lida com as compensações de consistência e disponibilidade oferecendo um SLA de 99,99 de disponibilidade junto com [vários níveis de consistência reduzidos, mas bem definidos](consistency-levels.md). 

### <a id="ConsistencyAndAvailability"></a>Relação da consistência com a latência
Uma variação mais abrangente do CAP foi proposta pelo Prof. Daniel Abadi e é chamado de [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf), abrangendo compensações de latência e consistência no estado estacionário. Ele indica que, no estado estável, o sistema de banco de dados deve escolher entre consistência e latência. Com vários modelos de consistência reduzidos (com suporte da replicação assíncrona e leitura local e quoruns de gravação), o Azure Cosmos DB garante que todas as leituras e gravações são locais para as regiões de leitura e gravação, respectivamente.  Isso permite que o Azure Cosmos DB ofereça garantias de baixa latência na região para os níveis de consistência.  

### <a id="ConsistencyAndThroughput"></a>Relação da consistência com a taxa de transferência
Como a implementação de um modelo específico de consistência depende da escolha de um [tipo de quórum](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), a taxa de transferência também varia de acordo com a opção de consistência. Por exemplo, no Azure Cosmos DB, a produtividade com leituras altamente consistentes é de aproximadamente metade daquela para leituras eventualmente consistentes. 
 
**Relação de capacidade de leitura para um nível de consistência específico no Azure Cosmos DB**

![Relação entre a consistência e a taxa de transferência](./media/distribute-data-globally/consistency-and-throughput.png)

## <a id="ThroughputGuarantees"></a>Garantias de taxa de transferência 
O Azure Cosmos DB permite que você dimensione a produtividade (bem como o armazenamento) de modo elástico em regiões diferentes, dependendo da demanda. 

**Uma única coleção do Azure Cosmos DB particionada (em três fragmentos) e, depois, distribuída em três regiões do Azure**

![Coleções distribuídas e particionadas do Azure Cosmos DB](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

Uma coleção do Azure Cosmos DB é distribuída usando duas dimensões – em uma região e, depois, entre regiões. Faça assim: 

* Em uma única região, uma coleção do Azure Cosmos DB é escalada horizontalmente em termos de partições de recursos. Cada partição de recursos gerencia um conjunto de chaves e é altamente consistente e disponível em virtude da replicação da máquina de estado entre um conjunto de réplicas. O Azure Cosmos DB é um sistema com governança total de recursos em que uma partição de recursos é responsável por fornecer sua cota de produtividade para o orçamento de recursos do sistema alocado a ela. A escala de uma coleção do Azure Cosmos DB é completamente transparente: o Azure Cosmos DB gerencia as partições de recursos e as divide e mescla conforme necessário. 
* Em seguida, cada uma das partições de recursos é distribuída em várias regiões. As partições de recursos que têm o mesmo conjunto de chaves em várias regiões formam um conjunto de partições (confira a [figura anterior](#ThroughputGuarantees)).  As partições de recursos em um conjunto de partições são coordenadas com o uso da replicação da máquina de estado em várias regiões. Dependendo do nível de consistência configurado, as partições de recursos em um conjunto de partições são configuradas dinamicamente usando topologias diferentes (por exemplo, estrela, cascata, árvore etc.). 

Em virtude de um gerenciamento de partições altamente dinâmico, do balanceamento de carga e de uma governança de recursos rígida, o Azure Cosmos DB permite escalar de modo elástico a produtividade em várias regiões do Azure, em uma coleção do Azure Cosmos DB. A alteração da produtividade em uma coleção é uma operação em tempo de execução do Azure Cosmos DB. Assim como em outras operações de banco de dados, o Azure Cosmos DB garante o limite superior absoluto na latência para sua solicitação a fim de alterar a produtividade. Por exemplo, a figura a seguir mostra a coleção de um cliente com taxa de transferência provisionada com elasticidade (variando de 1 M a 10 M de solicitações por segundo em duas regiões) com base na demanda.
 
**Coleção de um cliente com taxa de transferência provisionada com elasticidade (1 M - 10 M solicitações/s)**

![Produtividade provisionada de modo elástico do Azure Cosmos DB](./media/distribute-data-globally/elastic-throughput.png)

### <a id="ThroughputAndConsistency"></a>Relação da taxa de transferência com a consistência 
O mesmo que [Relação entre a consistência e a taxa de transferência](#ConsistencyAndThroughput).

### <a id="ThroughputAndAvailability"></a>Relação entre a taxa de transferência e a disponibilidade
O Azure Cosmos DB continua mantendo sua disponibilidade quando alterações são feitas na produtividade. O Azure Cosmos DB gerencia partições (por exemplo, operações de divisão, mesclagem e clonagem) de forma transparente e garante que as operações não prejudicam o desempenho nem a disponibilidade, enquanto o aplicativo aumenta ou diminui a produtividade de modo elástico. 

## <a id="AvailabilityGuarantees"></a>Garantia de disponibilidade
O Azure Cosmos DB oferece um SLA de 99,99% de disponibilidade de tempo de atividade para cada uma das operações de plano de controle e de dados. Conforme descrito anteriormente, as garantias de disponibilidade do Azure Cosmos DB incluem um limite superior absoluto na latência para todas as operações de plano de controle e de dados. As garantias de disponibilidade são sólidas e não se alteram com o número de regiões ou a distância geográfica entre as regiões. As garantias de disponibilidade se aplicam ao failover manual e automático. O Azure Cosmos DB oferece APIs transparentes de hospedagem múltipla que garantem que o aplicativo pode operar em pontos de extremidade lógicos e encaminhar de forma transparente as solicitações para a nova região no caso de failover. Em outras palavras, o aplicativo não precisa ser reimplantado durante o failover regional, e os SLAs de disponibilidade são mantidos.

### <a id="AvailabilityAndConsistency"></a>Relação entre a disponibilidade e a consistência, a latência e a taxa de transferência
A relação entre a disponibilidade e a consistência, a latência e a taxa de transferência é descrita em [Relação entre a consistência e a disponibilidade](#ConsistencyAndAvailability), [Relação entre a latência e a disponibilidade](#LatencyAndAvailability) e [Relação entre a taxa de transferência e a disponibilidade](#ThroughputAndAvailability). 

## <a id="GuaranteesAgainstDataLoss"></a>Garantias e comportamento do sistema para "perda de dados"
No Azure Cosmos DB, cada partição (de uma coleção) se torna altamente disponível por várias réplicas, que são distribuídas entre, pelo menos, 10 a 20 domínios de falha. Todas as gravações são confirmadas de forma síncrona e permanente pelo quórum de maioria das réplicas antes de serem reconhecidas para o cliente. A replicação assíncrona é aplicada com coordenação entre partições espalhadas por várias regiões. O Azure Cosmos DB garante que não há perda de dados para um failover manual iniciado pelo locatário. Durante o failover automático, o Azure Cosmos DB garante um limite superior do intervalo de desatualização limitada configurado na janela de perda de dados como parte de seu SLA.

## <a id="CustomerFacingSLAMetrics"></a>Métricas de SLA voltadas para o cliente
O Azure Cosmos DB expõe de forma transparente as métricas de produtividade, latência, consistência e disponibilidade. Essas métricas são acessíveis programaticamente e por meio do portal do Azure (confira a figura a seguir). Você também pode configurar alertas em vários limites usando o Azure Application Insights.
 
**Métricas de consistência, latência, taxa de transferência e disponibilidade estão disponíveis de forma transparente para cada locatário**

![Métricas do SLA visíveis para o cliente do Azure Cosmos DB](./media/distribute-data-globally/customer-slas.png)

## <a id="Next Steps"></a>Próximas etapas
* Para implementar a replicação global em sua conta do Azure Cosmos DB usando o portal do Azure, consulte [Como executar a replicação de banco de dados global do Azure Cosmos DB usando o portal do Azure](tutorial-global-distribution-documentdb.md).
* Para saber mais sobre como implementar arquiteturas de vários mestres com o Azure Cosmos DB, consulte [Arquiteturas de banco de dados de vários mestres com o Azure Cosmos DB](multi-region-writers.md).
* Para saber mais sobre como failovers automáticos e manuais funcionam no Azure Cosmos DB, consulte [Failovers regionais no Azure Cosmos DB](regional-failover.md).

## <a id="References"></a>Referências
1. Eric Brewer. [Para garantir sistemas distribuídos robustos](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)
2. Eric Brewer. [CAP Twelve Years Later – How the rules have changed](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf) (CAP 12 anos mais tarde – como as regras foram alteradas)
3. Gilbert, Lynch. - [Brewer&#39;s Conjecture and Feasibility of Consistent, Available, Partition Tolerant Web Services](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) (Conjectura e viabilidade de serviços Web tolerantes a partição consistentes e disponíveis)
4. Daniel Abadi. [Consistency Tradeoffs in Modern Distributed Database Systems Design](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf) (Compensações de consistência no projeto de sistemas de bancos de dados modernos distribuídos)
5. Martin Kleppmann. [Please stop calling databases CP or AP](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html) (Pare de chamar bancos de dados de CP ou AP)
6. Peter Bailis et al. [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) (PBS (Probabilistic Bounded Staleness) para quóruns parciais práticos)
7. Naor e Wool. [Load, Capacity and Availability in Quorum Systems](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) (Carga, capacidade e disponibilidade em sistemas de quorum)
8. Herlihy e Wing. [Lineralizability: A correctness condition for concurrent objects](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) (Linearidade: uma condição de correção para objetos simultâneos)
9. [SLA do Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/)
