---
title: Mover a computação do mainframe para máquinas virtuais do Azure
description: Os recursos de computação do Azure se comparam com capacidade de mainframe para que você possa migrar e modernizar os aplicativos IBM Z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.openlocfilehash: 32f259f20e0e24b4d5346c598e23fdc2df48dee6
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556427"
---
# <a name="move-mainframe-compute-to-azure"></a>Mover a computação do mainframe para o Azure

Os mainframes têm uma reputação de alta confiabilidade e disponibilidade e continuam a ser o backbone confiável de muitas empresas. Muitas vezes, eles são considerados uma escalabilidade e capacidade de computação quase ilimitadas. No entanto, algumas empresas aumentaram a capacidade dos maiores mainframes disponíveis. Se isso parece você, o Azure oferece agilidade, alcance e economia de infraestrutura.

Para executar cargas de trabalho de mainframe no Microsoft Azure, você precisa saber como os recursos de computação do mainframe se comparam ao Azure. Com base em um mainframe IBM Z14 (o modelo mais atual a partir deste documento), este artigo mostra como obter resultados comparáveis no Azure.

Para começar, considere os ambientes lado a lado. A figura a seguir compara um ambiente de mainframe para executar aplicativos em um ambiente de hospedagem do Azure.

![Os serviços do Azure e os ambientes de emulação oferecem suporte comparável e simplificam a migração](media/mainframe-compute-azure.png)

O poder dos mainframes é geralmente usado para sistemas OLTP (processamento de transações online) que lidam com milhões de atualizações para milhares de usuários. Esses aplicativos geralmente usam software para processamento de transações, manipulação de tela e entrada de formulário. Eles podem usar um CICS (sistema de controle de informações do cliente), IMS (sistema de gerenciamento de informações) ou TIP (pacote de interface de transação).

Como mostra a figura, um emulador TPM no Azure pode lidar com cargas de trabalho CICS e IMS. Um emulador de sistema do lote no Azure executa a função de JCL (linguagem de controle de trabalho). Os dados de mainframe são migrados para bancos de dado do Azure, como o banco de dados SQL do Azure. Os serviços do Azure ou outros softwares hospedados em máquinas virtuais do Azure podem ser usados para gerenciamento do sistema.

## <a name="mainframe-compute-at-a-glance"></a>Computação em mainframe em um relance

No mainframe Z14, os processadores são organizados em até quatro *empates*. Uma *gaveta* é simplesmente um cluster de processadores e chipsets. Cada gaveta pode ter seis chips de CP (processador central) ativos e cada CP tem 10 chips do SC (controlador do sistema). Na terminologia do Intel x86, há seis soquetes por gaveta, 10 núcleos por soquete e quatro empates. Essa arquitetura fornece o equivalente aproximado de 24 soquetes e 240 núcleos, máximo, para um Z14.

O Fast Z14 CP tem uma velocidade de clock de 5,2 GHz. Normalmente, um Z14 é entregue com todo o CPs na caixa. Eles são ativados conforme necessário. Um cliente geralmente é cobrado por pelo menos quatro horas de tempo de computação por mês, apesar do uso real.

Um processador de mainframe pode ser configurado como um dos seguintes tipos:

- Processador Uso Geral (GP)
- Sistema z Integrated Information Processor (zIIP)
- Recurso integrado para o processador Linux (IFL)
- Processador de assistência do sistema (SAP)
- Processador de instalação integrada de acoplamento (ICF)

## <a name="scaling-mainframe-compute-up-and-out"></a>Dimensionamento da computação do mainframe para cima e para fora

Os mainframes IBM oferecem a capacidade de escalar verticalmente até 240 núcleos (o tamanho atual do Z14 para um único sistema). Além disso, os mainframes IBM podem escalar horizontalmente por meio de um recurso chamado CF (Associação de acoplamento). O CF permite que vários sistemas de mainframe acessem os mesmos dados simultaneamente. Usando o CF, a tecnologia de sysplex paralelo de mainframe agrupa processadores de mainframe em clusters. Quando este guia foi escrito, o recurso sysplex paralelo suportava 32 agrupamentos de processadores 64 cada um. Até 2.048 processadores podem ser agrupados dessa maneira para escalar horizontalmente a capacidade de computação.

Um CF permite que os clusters de computação compartilhem dados com acesso direto. Ele é usado para informações de bloqueio, informações de cache e a lista de recursos de dados compartilhados. Um sysplex paralelo usando um ou mais CFs pode ser considerado como um cluster de cálculo de expansão de tudo "compartilhado tudo". Para obter mais informações sobre esses recursos, consulte [sysplex paralelo no IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) no site da IBM.

Os aplicativos podem usar esses recursos para fornecer alta disponibilidade e desempenho de expansão. Para obter informações sobre como o CICS pode usar o sysplex paralelo com o CF, baixe o [IBM CICS e o recurso de acoplamento: além das noções básicas de](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) Redbook.

## <a name="azure-compute-at-a-glance"></a>Computação do Azure em um relance

Algumas pessoas consideram erroneamente que os servidores baseados em Intel não são tão poderosos quanto os mainframes. No entanto, os novos sistemas com base em núcleo e processadores Intel têm tanto capacidade de computação quanto mainframes. Esta seção descreve as opções de IaaS (infraestrutura como serviço) do Azure para computação e armazenamento. O Azure fornece também opções de plataforma como serviço (PaaS), mas este artigo se concentra nas opções de IaaS que fornecem capacidade de mainframe comparável.

As máquinas virtuais do Azure fornecem poder computacional em uma variedade de tamanhos e tipos. No Azure, uma vCPU (CPU virtual) quase equivale a um núcleo em um mainframe.

Atualmente, o intervalo de tamanhos de máquina virtual do Azure fornece de 1 a 128 vCPUs. Os tipos de VM (máquina virtual) são otimizados para cargas de trabalho específicas. Por exemplo, a lista a seguir mostra os tipos de VM (atuais no momento da elaboração deste artigo) e seus usos recomendados:

| Tamanho     | Tipo e descrição                                                                 |
|----------|--------------------------------------------------------------------------------------|
| Série D | Uso geral com 64 vCPU e velocidade de clock de até 3,5 GHz                           |
| Série E | Memória otimizada com até 64 vCPUs                                                 |
| Série F | Computação otimizada com até 64 vCPUs e 3.. 7 GHz, velocidade de clock                       |
| Série H | Otimizado para aplicativos HPC (computação de alto desempenho)                          |
| Série L | Armazenamento otimizado para aplicativos de alta taxa de transferência apoiados por bancos de dados como NoSQL |
| Série M | Maiores VMs de computação e de memória otimizadas com até 128 vCPUs                        |

Para obter detalhes sobre as VMs disponíveis, consulte [série de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

Um mainframe Z14 pode ter até 240 núcleos. No entanto, os mainframes Z14 quase nunca usam todos os núcleos para um único aplicativo ou carga de trabalho. Em vez disso, um mainframe separa as cargas de trabalho em partições lógicas (LPARs) e os LPARs têm classificações – MIPS (milhões de instruções por segundo) ou MSU (milhão de unidades de serviço). Ao determinar o tamanho de VM comparável necessário para executar uma carga de trabalho de mainframe no Azure, fator na classificação MIPS (ou MSU).

Veja a seguir as estimativas gerais:

-   150 MIPS por vCPU

-   1.000 MIPS por processador

Para determinar o tamanho correto da VM para uma determinada carga de trabalho em um LPAR, primeiro Otimize a VM para a carga de trabalho. Em seguida, determine o número de vCPUs necessário. Uma estimativa conservadora é 150 MIPS por vCPU. Com base nessa estimativa, por exemplo, uma VM da série F com 16 vCPUs poderia facilmente dar suporte a uma carga de trabalho do IBM DB2 proveniente de um LPAR com 2.400 MIPS.

## <a name="azure-compute-scale-up"></a>Expansão de computação do Azure

As VMs da série M podem ser escaladas verticalmente para 128 vCPUs (no momento em que este artigo foi escrito). Usando a estimativa conservadora de 150 MIPS por vCPU, a VM da série M equivale a cerca de 19.000 MIPS. A regra geral para estimar MIPS para um mainframe é 1.000 MIPS por processador. Um mainframe Z14 pode ter até 24 processadores e fornecer cerca de 24.000 MIPS para um único sistema de mainframe.

O maior mainframe Z14 único tem aproximadamente 5.000 MIPS mais do que a maior VM disponível no Azure. Ainda é importante comparar como as cargas de trabalho são implantadas. Se um sistema de mainframe tiver um aplicativo e um banco de dados relacional, eles normalmente serão implantados no mesmo mainframe físico, cada um em seu próprio LPAR. A mesma solução no Azure é geralmente implantada usando uma VM para o aplicativo e uma VM de tamanho adequado e separada para o banco de dados.

Por exemplo, se um sistema m64 vCPU dá suporte ao aplicativo e um M96 vCPU é usado para o banco de dados, aproximadamente 150 vCPUs são necessários — ou cerca de 24.000 MIPS, como mostra a figura a seguir.

![Comparando implantações de carga de trabalho de 24.000 MIPS](media/mainframe-compute-mips.png)

A abordagem é migrar LPARs para VMs individuais. Em seguida, o Azure é facilmente dimensionado para o tamanho necessário para a maioria dos aplicativos implantados em um único sistema de mainframe.

## <a name="azure-compute-scale-out"></a>Expansão de computação do Azure

Uma das vantagens de uma solução baseada no Azure é a capacidade de escalar horizontalmente. O dimensionamento torna a capacidade de computação quase ilimitada disponível para um aplicativo. O Azure dá suporte a vários métodos para escalar horizontalmente a potência de computação:

- **Balanceamento de carga em um cluster.** Nesse cenário, um aplicativo pode usar um [balanceador de carga](../../../../load-balancer/load-balancer-overview.md) ou Gerenciador de recursos para distribuir a carga de trabalho entre várias VMs em um cluster. Se mais capacidade de computação for necessária, as VMs adicionais serão adicionadas ao cluster.

- **Conjuntos de dimensionamento de máquinas virtuais.** Nesse cenário de intermitência, um aplicativo pode ser dimensionado para [recursos de computação](../../../../virtual-machine-scale-sets/overview.md) adicionais com base no uso da VM. Quando a demanda cai, o número de VMs em um conjunto de dimensionamento também pode ficar inativo, garantindo o uso eficiente da capacidade de computação.

- **Dimensionamento de PaaS.** As ofertas de PaaS do Azure dimensionam os recursos de computação. Por exemplo, o [Azure Service Fabric](../../../../service-fabric/service-fabric-overview.md) aloca recursos de computação para atender a aumentos no volume de solicitações.

- **Clusters kubernetes.** Os aplicativos no Azure podem usar [clusters kubernetes](../../../../aks/concepts-clusters-workloads.md) para serviços de computação para recursos especificados. O AKS (serviço kubernetes do Azure) é um serviço gerenciado que orquestra nós, pools e clusters do kubernetes no Azure.

Para escolher o método certo para expandir os recursos de computação, é importante entender como o Azure e os mainframes diferem. A chave é como (ou se) os dados são compartilhados por recursos de computação. No Azure, os dados (por padrão) normalmente não são compartilhados por várias VMs. Se o compartilhamento de dados for exigido por várias VMs em um cluster de cálculo de expansão, os dados compartilhados deverão residir em um recurso que dá suporte a essa funcionalidade. No Azure, o compartilhamento de dados envolve o armazenamento conforme a seção a seguir aborda.

## <a name="azure-compute-optimization"></a>Otimização de computação do Azure

Você pode otimizar cada camada de processamento em uma arquitetura do Azure. Use o tipo mais adequado de VMs e recursos para cada ambiente. A figura a seguir mostra um padrão potencial para implantar VMs no Azure para dar suporte a um aplicativo CICS que usa DB2. No site primário, as VMs de produção, pré-produção e teste são implantadas com alta disponibilidade. O site secundário é feito para backup e recuperação de desastres.

Cada camada também pode fornecer serviços de recuperação de desastre apropriados. Por exemplo, VMs do banco de dados e produção podem requerer uma recuperação ativa ou passiva, enquanto as VMs de desenvolvimento e teste dão suporte a uma recuperação fria.

![Implantação altamente disponível que dá suporte à recuperação de desastre](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Próximas etapas

- [Migração de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Hospedagem de mainframe em máquinas virtuais do Azure](../overview.md)
- [Mover o armazenamento de mainframe para o Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>Recursos da IBM

- [Sysplex paralelo no IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS e o recurso de acoplamento: além dos conceitos básicos](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Criando usuários necessários para uma instalação de recurso do DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt-comando de criação de instância](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Solução de banco de dados clusterizado DB2 pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Governamental nuvem para aplicativos de mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft e FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Mais recursos de migração

- [Guia de deslocamento e comparação de precisão de Data Center Virtual do Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
