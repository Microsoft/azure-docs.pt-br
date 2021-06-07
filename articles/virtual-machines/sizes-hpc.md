---
title: Tamanhos de VM do Azure – HPC | Microsoft Docs
description: Lista os diferentes tamanhos disponíveis para máquinas virtuais de computação de alto desempenho no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho nessa série.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/19/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: a41dce28427db40dfd19879e4ada95add64009c3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772426"
---
# <a name="high-performance-computing-vm-sizes"></a>Tamanhos de VM de computação de alto desempenho

As máquinas virtuais (VMs) da série H do Azure foram projetadas para fornecer desempenho, escalabilidade e eficiência de custo de nível de liderança para várias cargas de trabalho do HPC do mundo real.

[Série HBv3](hbv3-series.md) As VMs são otimizadas para aplicativos HPC, como dinâmica de fluidos, análise explícita e implícita de elementos finitos, modelagem meteorológico, processamento sísmico, simulação de reservatório e simulação DPE. O recurso de VMs HBv3 até 120 AMD EPYC™ 7003-Series (Milão) núcleos de CPU, 448 GB de RAM e nenhum hyperthreading. As VMs da série HBv3 também fornecem 350 GB/s de largura de banda de memória, até 32 MB de cache L3 por núcleo, até 7 GB/s de desempenho de SSD de dispositivo de bloco e frequências de relógio de até 3,675 GHz. 

Todas as VMs da série HBv3 apresentam um cabeçalho HDR de 200 GB/s de rede NVIDIA para habilitar cargas de trabalho MPI de escala de supercomputador. Essas VMs são conectadas em uma árvore Fat sem bloqueio para desempenho de RDMA otimizado e consistente. A malha InfiniBand HDR também dá suporte ao roteamento adaptável e ao transporte conectado dinâmico (DCT, além dos transportes Standard RC e UD). Esses recursos aprimoram o desempenho, a escalabilidade e a consistência do aplicativo, e seu uso é altamente recomendado.

[Série HBv2](hbv2-series.md) As VMs são otimizadas para aplicativos orientados pela largura de banda da memória, como dinâmica de fluidos, análise de elemento finito e simulação de reservatório. HBv2 VMs Feature 120 AMD EPYC 7742 núcleos de processador, 4 GB de RAM por núcleo de CPU e nenhum multithread simultâneo. Cada VM HBv2 fornece até 340 GB/s de largura de banda de memória e até 4 teraFLOPS de computação FP64.

O recurso de VMs HBv2 de 200 GB/s Mellanox HDR InfiniBand, enquanto as VMs da série HB e HC apresentam 100 GB/s Mellanox EDR InfiniBand. Cada um desses tipos de VM é conectado em uma árvore Fat sem bloqueio para desempenho de RDMA otimizado e consistente. As VMs HBv2 dão suporte ao roteamento adaptável e ao transporte conectado dinâmico (DCT, além dos transportes Standard RC e UD). Esses recursos aprimoram o desempenho, a escalabilidade e a consistência do aplicativo, e seu uso é altamente recomendado.

[Série HB](hb-series.md) As VMs são otimizadas para aplicativos orientados por largura de banda de memória, como dinâmica de fluidos, análise de elemento finito explícito e modelagem de clima. As VMs HB apresentam 60 AMD EPYC 7551 núcleos de processador, 4 GB de RAM por núcleo de CPU e nenhum hyperthreading. A plataforma AMD EPYC fornece mais de 260 GB/s de largura de banda de memória.

[Série HC](hc-series.md) As VMs são otimizadas para aplicativos orientados por computação densa, como análise implícita de elemento finito, Molecular Dynamics e química computacional. O HC VMs Feature 44 Intel Xeon Platinum 8168 núcleos de processador, 8 GB de RAM por núcleo de CPU e nenhum hyperthreading. A plataforma Intel Xeon Platinum dá suporte ao ecossistema avançado de ferramentas de software da Intel, como a biblioteca de kernels matemáticos da Intel.

[Série H](h-series.md) As VMs são otimizadas para aplicativos orientados por altas frequências de CPU ou grandes requisitos de memória por núcleo. As VMs da série H apresentam 8 ou 16 núcleos de processador Intel Xeon E5 2667 v3, 7 ou 14 GB de RAM por núcleo de CPU e nenhum hyperthreading. A série H apresenta 56 GB/s Mellanox FDR InfiniBand em uma configuração de árvore de Fat sem bloqueio para desempenho consistente de RDMA. As VMs da série H dão suporte ao Intel MPI 5. x e ao MS-MPI.

> [!NOTE]
> Todas as VMs da série HBv3, HBv2, HB e HC têm acesso exclusivo aos servidores físicos. Há apenas 1 VM por servidor físico e não há multilocação compartilhada com outras VMs para esses tamanhos de VM.

> [!NOTE]
> As [VMs A8 – a11](./sizes-previous-gen.md#a-series---compute-intensive-instances) são desativadas a partir de 3/2021. Nenhuma nova implantação de VM desses tamanhos agora é possível. Se você tiver VMs existentes, consulte notificações por email para as próximas etapas, incluindo a migração para outros tamanhos de VM no [Guia de migração do HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>Instâncias compatíveis com RDMA

A maioria dos tamanhos de VM do HPC tem um adaptador de rede para conectividade RDMA (acesso remoto direto à memória). Os tamanhos de [série N](./nc-series.md) selecionados designados com ' r ' também são compatíveis com RDMA. Essa interface é além da interface de rede Ethernet padrão do Azure disponível nos outros tamanhos de VM.

Essa interface secundária permite que as instâncias compatíveis com RDMA se comuniquem por meio de uma rede InfiniBand (IB), operando com tarifas HDR para HBv3, HBv2, EDRs para as taxas HB, HC, NDv2 e FDR para H16r, H16mr e outras máquinas virtuais da série N compatíveis com RDMA. Esses recursos RDMA podem impulsionar a escalabilidade e o desempenho de aplicativos baseados em MPI (interface de transmissão de mensagens).

> [!NOTE]
> **Suporte a Sr-IOV**: no Azure HPC, atualmente, há duas classes de VMs, dependendo se elas estão habilitadas para a INFINIBAND em Sr-iov. Atualmente, quase todas as VMs habilitadas para RDMA ou de geração mais recentes no Azure são SR-IOV habilitado, exceto para H16r, H16mr e NC24r.
> O RDMA só é habilitado pela rede InfiniBand (IB) e tem suporte para todas as VMs compatíveis com RDMA.
> Só há suporte para IP sobre IB em VMs habilitadas para SR-IOV.
> O RDMA não está habilitado pela rede Ethernet.

- **Sistema operacional** -distribuições do Linux, como CentOS, RHEL, Ubuntu, SuSE são comumente usadas. O Windows Server 2016 e versões mais recentes têm suporte em todas as VMs da série HPC. Também há suporte para o Windows Server 2012 R2 e o Windows Server 2012 nas VMs habilitadas para não-SR-IOV. Observe que [o Windows Server 2012 R2 não tem suporte em HBv2 em diante como tamanhos de VM com mais de 64 núcleos (virtuais ou físicos)](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows). Consulte [imagens de VM](./workloads/hpc/configure.md) para obter uma lista de imagens de VM com suporte no Marketplace e como elas podem ser configuradas adequadamente. As respectivas páginas de tamanho de VM também listam o suporte à pilha de software.

- **InfiniBand e drivers** -em VMs habilitadas para InfiniBand, os drivers apropriados são necessários para habilitar o RDMA. Consulte [imagens de VM](./workloads/hpc/configure.md) para obter uma lista de imagens de VM com suporte no Marketplace e como elas podem ser configuradas adequadamente. Consulte também [habilitar a InfiniBand](./workloads/hpc/enable-infiniband.md) para saber mais sobre extensões de VM ou instalação manual de drivers InfiniBand.

- **MPI** -os tamanhos de VM habilitados para Sr-IOV no Azure permitem que quase qualquer tipo de MPI seja usado com o Mellanox ofed. Em VMs não habilitadas para SR-IOV, as implementações MPI com suporte usam a interface do Microsoft Network Direct (ND) para se comunicar entre as VMs. Portanto, somente o Intel MPI 5. x e o Microsoft MPI (MS-MPI) 2012 R2 ou versões posteriores têm suporte. Versões posteriores da biblioteca de tempo de execução do Intel MPI podem ou não ser compatíveis com os drivers RDMA do Azure. Consulte [Setup MPI for HPC](./workloads/hpc/setup-mpi.md) para obter mais detalhes sobre como configurar MPI em VMs do HPC no Azure.

  > [!NOTE]
  > **Espaço de endereço de rede RDMA**: a rede RDMA no Azure reserva o espaço de endereço 172.16.0.0/16. Para executar aplicativos MPI em instâncias implantadas em uma rede virtual do Azure, verifique se o espaço do endereço de rede virtual não se sobrepõe à rede RDMA.

## <a name="cluster-configuration-options"></a>Opções de configuração de cluster

O Azure fornece várias opções para criar clusters de VMs HPC que podem se comunicar usando a rede RDMA, incluindo: 

- **Máquinas virtuais**  – implante as VMs HPC compatíveis com RDMA no mesmo conjunto de dimensionamento ou conjunto de disponibilidade (ao usar o modelo de implantação Azure Resource Manager). Se você usar o modelo de implantação clássico, implante as VMs no mesmo serviço de nuvem.

- **Conjuntos de dimensionamento de máquinas virtuais** – em um conjunto de dimensionamento de máquinas virtuais, certifique-se de limitar a implantação a um único grupo de posicionamento para comunicação InfiniBand dentro do conjunto de dimensionamento. Por exemplo, em um modelo do Resource Manager, defina a `singlePlacementGroup`propriedade como`true`. Observe que o tamanho máximo do conjunto de dimensionamento que pode ser girado `singlePlacementGroup=true` é limitado às VMs 100 por padrão. Se suas necessidades de escala de trabalho do HPC forem maiores que 100 VMs em um único locatário, você poderá solicitar um aumento, [abrir uma solicitação](../azure-portal/supportability/how-to-create-azure-support-request.md) de atendimento ao cliente online sem encargos. O limite do número de VMs em um único conjunto de dimensionamento pode ser aumentado para 300. Observe que, ao implantar VMs usando conjuntos de disponibilidade, o limite máximo é de 200 VMs por conjunto de disponibilidade.

  > [!NOTE]
  > **MPI entre máquinas virtuais**: se RDMA (por exemplo, usando a comunicação MPI) for necessário entre VMs (máquinas virtuais), verifique se as VMs estão no mesmo conjunto de dimensionamento de máquinas virtuais ou conjunto de disponibilidade.

- **Azure CycleCloud** -criar um cluster HPC usando o [CycleCloud do Azure](/azure/cyclecloud/) para executar trabalhos MPI.

- **Lote do Azure** -crie um pool [do lote do Azure](../batch/index.yml) para executar cargas de trabalho MPI. Para usar instâncias de computação intensiva para executar aplicativos MPI com o Lote do Azure, consulte [Usar tarefas de várias instâncias para executar aplicativos MPI (Interface de Transmissão de Mensagens) no Lote do Azure](../batch/batch-mpi.md).

- **Microsoft HPC Pack**  -  O [HPC Pack](/powershell/high-performance-computing/overview) inclui um ambiente de tempo de execução para MS-MPI que usa a rede RDMA do Azure quando implantado em VMs do Linux compatíveis com RDMA. Por exemplo, para implantações, consulte [configurar um cluster de RDMA do Linux com o HPC Pack para executar aplicativos MPI](/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Considerações de implantação

- **Assinatura do Azure** – Para implantar um número maior de instâncias de computação intensiva, considere uma assinatura pré-paga ou outras opções de compra. Se estiver usando uma [conta gratuita do Azure](https://azure.microsoft.com/free/), você poderá usar apenas um número limitado de núcleos de computação do Azure.

- **Preços e disponibilidade** -Verifique os preços e a [disponibilidade](https://azure.microsoft.com/global-infrastructure/services/) da [VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) pelas regiões do Azure.

- **Cota de núcleos** – Talvez seja preciso aumentar a cota de núcleos em sua assinatura do Azure, saindo do valor padrão. Sua assinatura também pode limitar o número de núcleos que você pode implantar em determinadas famílias de tamanho de VM, incluindo a série de H. Para solicitar um aumento de cota, [abra uma solicitação de atendimento ao cliente online](../azure-portal/supportability/how-to-create-azure-support-request.md) gratuitamente. (Os limites padrão podem variar dependendo de sua categoria de assinatura.)

  > [!NOTE]
  > Entre em contato com o Suporte do Azure se precisar de capacidade em larga escala. Cotas do Azure são limites de crédito, não garantias de capacidade. Independentemente de sua cota, você é cobrado apenas pelo núcleos utilizados.
  
- **Rede virtual** – Não é necessário ter uma [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) do Azure para usar instâncias de computação intensiva. No entanto, para muitas implantações, é necessária pelo menos uma rede virtual do Azure baseada em nuvem ou uma conexão site a site se você precisar acessar recursos locais. Quando necessário, você precisará criar uma nova rede virtual para implantar as instâncias. Não há suporte para a adição de VMs de computação intensiva a uma rede virtual em um grupo de afinidades.

- **Redimensionamento** – devido ao seu hardware especializado, você só pode redimensionar instâncias de computação intensiva dentro da mesma família de tamanho (série H ou série N). Por exemplo, somente é possível redimensionar uma VM da série H de um tamanho da série H para outro. Considerações adicionais sobre o suporte do driver InfiniBand e discos de NVMe podem precisar ser consideradas para determinadas VMs.


## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Computação otimizada](sizes-compute.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como [configurar suas VMs](./workloads/hpc/configure.md), [habilitar a INFINIBAND](./workloads/hpc/enable-infiniband.md), [Configurar MPI](./workloads/hpc/setup-mpi.md) e otimizar aplicativos HPC para o Azure em [cargas de trabalho do HPC](./workloads/hpc/overview.md).
- Examine as visão geral da série [HBv3](./workloads/hpc/hbv3-series-overview.md) e [HC-Series](./workloads/hpc/hc-series-overview.md).
- Leia sobre os comunicados mais recentes, exemplos de carga de trabalho do HPC e resultados de desempenho nos [Blogs da comunidade técnica de computação do Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para obter uma visão de nível superior da arquitetura de execução de cargas de trabalho de HPC, confira [HPC (computação de alto desempenho) no Azure](/azure/architecture/topics/high-performance-computing/).
