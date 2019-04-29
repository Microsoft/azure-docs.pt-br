---
title: Mover computação de mainframe para máquinas virtuais do Azure
description: Comparar recursos favorável a capacidade de mainframe de computação do Azure para que você possa migrar e modernizar os aplicativos z14 IBM.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 8aea4a74ba84855f011dada70ea75ec0d5fb64fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485428"
---
# <a name="move-mainframe-compute-to-azure"></a>Mover computação de mainframe para o Azure

Mainframes tem uma reputação de alta confiabilidade e disponibilidade e continuam a ser o backbone confiável de muitas empresas. Eles são geralmente considerados para ter escalabilidade quase infinita e capacidade de computação, bem. No entanto, algumas empresas superaram a capacidade dos maior mainframes disponíveis. Se isso parece como você, o Azure oferece economia de infra-estrutura, alcance e agilidade.

Para executar cargas de trabalho de mainframe no Microsoft Azure, você precisa saber como o mainframe computação recursos em comparação com o Azure. Com base em um mainframe de z14 da IBM (o modelo mais recente a partir da redação deste artigo), este artigo informa como obter resultados comparáveis no Azure.

Para começar, considere os ambientes de lado a lado. A figura a seguir compara um ambiente de mainframe para aplicativos em execução para um ambiente de hospedagem do Azure.

![Oferta de ambientes de emulação comparáveis e serviços do Azure dão suporte e simplifica a migração](media/mainframe-compute-azure.png)

O poder dos mainframes geralmente é usado para transações online sistemas OLTP (processamento) que lidar com milhões de atualizações para milhares de usuários. Esses aplicativos geralmente usam o software para o processamento de transações, a manipulação de tela e a entrada de formulário. Eles podem usar um sistema de controle de informações do cliente (CICS), o sistema de gerenciamento de informações (IMS) ou o pacote de Interface de transação (TIP).

Como mostra a figura, um emulador do TPM no Azure pode lidar com cargas de trabalho CICS e IMS. Um emulador de sistema de lote no Azure executa a função de linguagem de controle de trabalho (JCL). Dados de mainframe são migrados para bancos de dados do Azure, como banco de dados SQL. Serviços do Azure ou outros softwares hospedados em máquinas virtuais do Azure podem ser usado para gerenciamento do sistema.

## <a name="mainframe-compute-at-a-glance"></a>Computação de mainframe em um relance

O mainframe z14, processadores são distribuídos em até quatro *gavetas*. Um *gaveta* é simplesmente um cluster de processadores e chipsets. Cada gaveta pode ter seis chips de processador central Active Directory (CP), e cada CP tem 10 chips de controlador (SC) do sistema. Na terminologia do Intel x86, há seis soquetes por gaveta, 10 núcleos por soquete e gavetas de quatro. Essa arquitetura fornece o equivalente aproximado de 24 soquetes e 240 núcleos, máximos, para um z14.

O fast CP z14 tem uma velocidade de clock 5.2 GHz. Normalmente, um z14 é fornecido com o CPs na caixa. Eles são ativados conforme necessário. Um cliente normalmente é cobrado pelo menos quatro horas de tempo de computação por mês, apesar do uso real.

Um processador de mainframe pode ser configurado como um dos seguintes tipos:

- Processador de finalidade (GP) geral
- Processador integrada de informações do sistema z (zIIP)
- Recurso integrado para o processador do Linux (IFL)
- Processador de assistência do sistema (SAP)
- Processador de acoplamento instalações do Windows integrada

## <a name="scaling-mainframe-compute-up-and-out"></a>Computação de mainframe colocação em escala verticalmente e horizontalmente

Mainframes IBM oferecem a capacidade de dimensionar até 240 núcleos (o tamanho de z14 atual para um único sistema). Além disso, mainframes IBM podem escalar horizontalmente por meio de um recurso chamado o acoplamento Facility (CF). O CF permite que vários sistemas de mainframe acessar os mesmos dados simultaneamente. Usando o FC, os processadores de mainframe de grupos do mainframe Sysplex paralelo tecnologia em clusters. Quando este guia foi escrito, o recurso de Sysplex paralelo suporte 32 agrupamentos de 64 processadores. Até 2.048 processadores pode ser agrupado dessa maneira para expandir a capacidade de computação.

Um CF permite que os clusters de computação compartilhar dados com acesso direto. Ele é usado para a lista de recursos de dados compartilhada, informações de cache e informações de bloqueio. Um Sysplex paralelo usando um ou mais CFs pode ser pensado como um "compartilhadas tudo" cluster de computação de expansão. Para obter mais informações sobre esses recursos, consulte [Sysplex paralelo no IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) no site da IBM.

Aplicativos podem usar esses recursos para fornecer alta disponibilidade e desempenho de escalabilidade horizontal. Para obter informações sobre como o CICS podem usar Sysplex paralelo com CF, baixe o [IBM CICS e a facilidade de acoplamento: Além do básico](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) redbook.

## <a name="azure-compute-at-a-glance"></a>Computação do Azure em um relance

Algumas pessoas, por engano, pensam que servidores baseados em Intel não são tão poderosos quanto mainframes. No entanto, os novos sistemas de núcleo densa, baseados em Intel tem como computação muito capacidade como mainframes. Esta seção descreve as opções de (IaaS) de infraestrutura como serviço do Azure para computação e armazenamento. O Azure fornece também opções do plataforma como serviço (PaaS), mas este artigo se concentra nas opções IaaS que fornecem a capacidade de mainframe comparável.

Máquinas virtuais do Azure fornecem poder de computação em um intervalo de tipos e tamanhos. No Azure, uma CPU virtual (vCPU) aproximadamente é igual a um núcleo em um mainframe.

Atualmente, os tamanhos de intervalo de máquina Virtual do Azure fornece de 1 a 128 vCPUs. Tipos de máquina virtual (VM) são otimizados para cargas de trabalho específicas. Por exemplo, a lista a seguir mostra os tipos de VM (atuais a partir da redação deste artigo) e seus usos recomendados:

| Tamanho     | Tipo e descrição                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D-série | Uso geral com 64 Vcpus e até a velocidade de clock de 3,5 GHz                           |
| Série E | Memória otimizada com até 64 vCPUs                                                 |
| Série F | Computação otimizada com até 64 vCPUs e velocidade de clock 3..7 GHz                       |
| Série H | Otimizado para aplicativos de computação de alto desempenho (HPC)                          |
| L-Series | Armazenamento otimizado para aplicativos de alta taxa de transferência apoiados por bancos de dados como NoSQL |
| Série M | Memória e computação maior otimizado VMs com até 128 vCPUs                        |

Para obter detalhes sobre as VMs disponíveis, consulte [série da máquina Virtual](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

Um mainframe z14 pode ter até 240 núcleos. No entanto, mainframes z14 quase nunca usam todos os núcleos para um único aplicativo ou carga de trabalho. Em vez disso, um mainframe separa as cargas de trabalho em partições lógicas (LPARs) e os LPARs têm classificações — MIPS (milhões de instruções por segundo) ou MSU (unidade milhões de serviço). Ao determinar o tamanho da VM comparáveis necessário para executar uma carga de trabalho de mainframe no Azure, classificação de fator em MIPS (ou MSU).

A seguir é estimativas gerais:

-   150 MIPS por vCPU

-   1.000 MIPS por processador

Para determinar o tamanho VM correto para uma determinada carga de trabalho em um LPAR, primeiro Otimize a carga de trabalho da VM. Em seguida, determine o número de vCPUs necessários. Uma estimativa conservadora é 150 MIPS por vCPU. Com base nessa estimativa, por exemplo, uma VM da série F com 16 vCPUs poderia facilmente dar suporte a uma carga de trabalho do IBM Db2 proveniente de um LPAR com MIPS 2.400.

## <a name="azure-compute-scale-up"></a>Aumento de escala de computação do Azure

As VMs da série M podem dimensionar até 128 vCPUs (no momento que neste artigo foi escrito). A VM da série M usando a estimativa conservadora de MIPS 150 por vCPU, equivale a aproximadamente 19.000 MIPS. A regra geral para estimar MIPS para um mainframe é 1.000 MIPS por processador. Um mainframe z14 pode ter até 24 processadores e fornecem aproximadamente 24.000 MIPS para um sistema de mainframe único.

O mainframe z14 único maior tem aproximadamente 5.000 MIPS mais do que a maior VM disponível no Azure. Ainda é importante a ser comparado como cargas de trabalho são implantadas. Se um sistema de mainframe tiver um aplicativo e um banco de dados relacional, normalmente são implantados no mesmo mainframe físico — cada um em seu próprio LPAR. A mesma solução no Azure geralmente é implantada usando uma máquina virtual para o aplicativo e uma VM separada, dimensionada adequadamente para o banco de dados.

Por exemplo, se um sistema de vCPU M64 suporta o aplicativo e uma vCPU M96 é usada para o banco de dados, são necessários cerca de 150 vCPUs — ou aproximadamente 24.000 MIPS, como mostra a figura a seguir.

![Comparando as implantações de carga de trabalho de 24.000 MIPS](media/mainframe-compute-mips.png)

A abordagem é migrar LPARs para VMs individuais. Em seguida, Azure facilmente sobe para o tamanho necessário para a maioria dos aplicativos que são implantados em um sistema de mainframe único.

## <a name="azure-compute-scale-out"></a>Expansão de computação do Azure

Uma das vantagens de uma solução com base no Azure é a capacidade de escalar horizontalmente. Torna a colocação em escala quase infinita a capacidade disponível para um aplicativo de computação. Azure dá suporte a vários métodos para escalar horizontalmente a potência de computação:

- **Balanceamento de carga em um cluster.** Nesse cenário, um aplicativo pode usar um [balanceador de carga](/azure/load-balancer/load-balancer-overview) ou resource manager para distribuir a carga de trabalho entre várias VMs em um cluster. Se mais de computação for necessária capacidade, VMs adicionais são adicionadas ao cluster.

- **Conjuntos de dimensionamento de máquina virtual.** Nesse cenário de intermitente, um aplicativo ser dimensionado para adicional [recursos de computação](/azure/virtual-machine-scale-sets/overview) com base no uso VM. Quando a demanda falha, o número de VMs em um conjunto de dimensionamento também pode ir para baixo, garantindo o uso eficiente da capacidade de computação.

- **Dimensionamento de PaaS.** Recursos de computação de escala de ofertas de PaaS do Azure. Por exemplo, [do Azure Service Fabric](/azure/service-fabric/service-fabric-overview) aloca recursos de computação para atender ao aumento do volume de solicitações.

- **Clusters de Kubernetes.** Os aplicativos no Azure podem usar [clusters Kubernetes](/azure/aks/concepts-clusters-workloads) para serviços de computação para recursos especificados. Serviço de Kubernetes do Azure (AKS) é um serviço gerenciado que orquestra a clusters no Azure, pools e nós Kubernetes.

Para escolher o método certo para expandir os recursos de computação, é importante compreender a diferença entre Azure e mainframes. A chave é como — ou, se — dados são compartilhados pelos recursos de computação. No Azure, dados (por padrão) não é normalmente compartilhados por várias VMs. Se o compartilhamento de dados requer várias VMs em uma expansão de cluster de computação, os dados compartilhados devem residir em um recurso que dá suporte a essa funcionalidade. No Azure, compartilhamento de dados envolve o armazenamento discute a seção a seguir.

## <a name="azure-compute-optimization"></a>Otimização de computação do Azure

Você pode otimizar cada camada de processamento em uma arquitetura do Azure. Use o tipo mais adequado de máquinas virtuais e recursos para cada ambiente. A figura a seguir mostra um padrão de potencial para a implantação de VMs no Azure para dar suporte a um aplicativo do CICS que usa o Db2. No site primário, as VMs de produção, pré-produção e teste são implantadas com alta disponibilidade. O site secundário é feito para backup e recuperação de desastres.

Cada camada também pode fornecer desastres apropriada dos serviços de recuperação. Por exemplo, VMs do banco de dados e produção podem requerer uma recuperação ativa ou passiva, enquanto as VMs de desenvolvimento e teste dão suporte a uma recuperação fria.

![Implantação altamente disponível que dá suporte à recuperação de desastres](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Próximas etapas

- [Migração de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Mainframe rehosting em máquinas virtuais do Azure](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Mover o armazenamento de mainframe no Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>Recursos da IBM

- [Sysplex paralelo no IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS e a facilidade de acoplamento: Além do básico](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Criando usuários necessários para uma instalação do Recurso Db2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - criar instância de comando](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale solução de banco de dados clusterizado](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Nuvem de governo do Microsoft Azure para aplicativos de mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [A Microsoft e FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Mais recursos de migração

- [Aliança de Modernização de Plataforma: IBM Db2 no Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)
- [Guia de deslocamento e comparação de precisão de Data Center Virtual do Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
