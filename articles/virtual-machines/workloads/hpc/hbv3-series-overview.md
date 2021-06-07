---
title: Visão geral da VM da série HBv3, arquitetura, topologia – máquinas virtuais do Azure | Microsoft Docs
description: Saiba mais sobre o tamanho da VM da série HBv3 no Azure.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f78420a65cd9c2402266eb9ba973eabe758d7ee5
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608214"
---
# <a name="hbv3-series-virtual-machine-overview"></a>Visão geral da máquina virtual da série HBv3 

Um servidor [HBv3-Series](../../hbv3-series.md) apresenta 2 * 64-core EPYC 7V13 CPUs para um total de 128 núcleos "Zen3" físicos. O SMT (autothreading simultâneo) está desabilitado em HBv3. Esses 128 núcleos são divididos em 16 seções (8 por soquete), cada seção que contém 8 núcleos de processador com acesso uniforme a um cache L3 de 32 MB. Os servidores HBv3 do Azure também executam as seguintes configurações de BIOS AMD:

```bash
Nodes per Socket (NPS) = 2
L3 as NUMA = Disabled
NUMA domains within VM OS = 4
C-states = Enabled
```

Como resultado, o servidor é inicializado com 4 domínios NUMA (2 por soquete) cada 32-núcleos de tamanho. Cada NUMA tem acesso direto a 4 canais de operação DRAM física a 3200 MT/s.

Para fornecer espaço para o hipervisor do Azure operar sem interferir na VM, reservamos 8 núcleos físicos por servidor.

## <a name="vm-topology"></a>Topologia de VM

O diagrama a seguir mostra a topologia do servidor. Reservamos esses 8 núcleos de host de hipervisor (amarelo) simetricamente em ambos os soquetes de CPU, levando em frente os dois primeiros núcleos de CCDs (esverdeidades complexas de núcleos) em cada domínio NUMA, com os núcleos restantes para a VM da série HBv3 (Green).

![Topologia do servidor da série HBv3](./media/architecture/hbv3/hbv3-topology-server.png)

Observe que o limite de CCD não é equivalente a um limite NUMA. No HBv3, um grupo de quatro CCDs consecutivas (4) é configurado como um domínio NUMA, tanto no nível de servidor de host quanto em uma VM convidada. Assim, todos os tamanhos de VM HBv3 expõem quatro domínios NUMA que aparecerão para um sistema operacional e aplicativo, conforme mostrado abaixo, 4 domínios NUMA uniformes, cada um com um número diferente de núcleos, dependendo do [tamanho da VM HBv3](../../hbv3-series.md)específica.

![Topologia da VM da série HBv3](./media/architecture/hbv3/hbv3-topology-vm.png)

Cada tamanho de VM HBv3 é semelhante em layout físico, recursos e desempenho de uma CPU diferente da série AMD EPYC 7003, da seguinte maneira:

| Tamanho da VM da série HBv3             | Domínios NUMA | Núcleos por domínio NUMA  | Similaridade com AMD EPYC         |
|---------------------------------|--------------|------------------------|----------------------------------|
Standard_HB120rs_v3               | 4            | 30                     | EPYC de dois soquetes 7713            |
Standard_HB120r-96s_v3            | 4            | 24                     | EPYC de dois soquetes 7643            |
Standard_HB120r-64s_v3            | 4            | 16                     | EPYC de dois soquetes 7543            |
Standard_HB120r-32s_v3            | 4            | 8                      | EPYC de dois soquetes 7313            |
Standard_HB120r-16s_v3            | 4            | 4                      | 72F3 EPYC de dois soquetes            |

> [!NOTE]
> Os tamanhos de VM de núcleos restritos reduzem apenas o número de núcleos físicos expostos à VM. Todos os ativos compartilhados globais (RAM, largura de banda de memória, cache L3, conectividade GMI e xGMI, InfiniBand, rede Ethernet do Azure, SSD local) permanecem constantes. Isso permite que um cliente escolha um tamanho de VM mais adequado para um determinado conjunto de cargas de trabalho ou necessidades de licenciamento de software.

O mapeamento NUMA virtual de cada tamanho de VM HBv3 é mapeado para a topologia NUMA física subjacente. Não há uma abstração potencialmente enganosa da topologia de hardware. 

A topologia exata para o [tamanho de VM](../../hbv3-series.md) de vários HBv3 é exibida da seguinte maneira usando a saída de [lstopo](https://linux.die.net/man/1/lstopo):
```bash
lstopo-no-graphics --no-io --no-legend --of txt
```
<br>
<details>
<summary>Clique para exibir a saída do lstopo para Standard_HB120rs_v3</summary>

![saída de lstopo para VM HBv3-120](./media/architecture/hbv3/hbv3-120-lstopo.png)
</details>

<details>
<summary>Clique para exibir a saída do lstopo para Standard_HB120rs 96_v3</summary>

![saída de lstopo para a VM HBv3-96](./media/architecture/hbv3/hbv3-96-lstopo.png)
</details>

<details>
<summary>Clique para exibir a saída do lstopo para Standard_HB120rs 64_v3</summary>

![saída de lstopo para VM HBv3-64](./media/architecture/hbv3/hbv3-64-lstopo.png)
</details>

<details>
<summary>Clique para exibir a saída do lstopo para Standard_HB120rs 32_v3</summary>

![saída de lstopo para VM HBv3-32](./media/architecture/hbv3/hbv3-32-lstopo.png)
</details>

<details>
<summary>Clique para exibir a saída do lstopo para Standard_HB120rs 16_v3</summary>

![saída de lstopo para VM HBv3-16](./media/architecture/hbv3/hbv3-16-lstopo.png)
</details>

## <a name="infiniband-networking"></a>Rede InfiniBand
As VMs HBv3 também apresentam adaptadores de rede do NVIDIA Mellanox HDR (ConnectX-6) operando em até 200 gigabits/s. A NIC é passada para a VM via SRIOV, permitindo que o tráfego de rede ignore o hipervisor. Como resultado, os clientes carregam drivers Mellanox OFED padrão em VMs HBv3 como seriam um ambiente bare-metal.

As VMs HBv3 dão suporte ao roteamento adaptável, ao transporte conectado dinâmico (DCT, em adicional aos transportes Standard RC e UD), e ao descarregamento baseado em hardware de MPI coletiva para o processador integrado do adaptador ConnectX-6. Esses recursos aprimoram o desempenho, a escalabilidade e a consistência do aplicativo e o uso deles é altamente recomendável.

## <a name="temporary-storage"></a>Armazenamento temporário
HBv3 VMs recurso 3 dispositivos SSD fisicamente locais. Um dispositivo é pré-formatado para servir como um arquivo de paginação e aparecerá dentro de sua VM como um dispositivo "SSD" genérico.

Dois outros, SSDs maiores, são fornecidos como dispositivos de bloco NVMe não formatado via NVMeDirect. Como o dispositivo de bloqueio de NVMe ignora o hipervisor, ele terá maior largura de banda, IOPS maior e menor latência por IOP.

Quando emparelhado em uma matriz distribuída, o SSD de NVMe fornece leituras de até 7 GB/s e gravações de 3 GB/s e até 186.000 IOPS (leituras) e 201.000 IOPS (gravações) para obter profundidades de fila profundas.

## <a name="hardware-specifications"></a>Especificações de hardware 

| Especificações de hardware          | VMs da série HBv3              |
|----------------------------------|----------------------------------|
| Núcleos                            | 120, 96, 64, 32 ou 16 (SMT desabilitado)               | 
| CPU                              | AMD EPYC 7V13                   | 
| Frequência de CPU (não AVX)          | 3,1 GHz (todos os núcleos), 3,675 GHz (até 10 núcleos)    | 
| Memória                           | 448 GB (RAM por núcleo depende do tamanho da VM)         | 
| Disco local                       | 2 * 960 GB NVMe (bloco), SSD de 480 GB (arquivo de paginação) | 
| Infiniband                       | 200 GB/s Mellanox ConnectX-6 HDR InfiniBand | 
| Rede                          | 50 GB/s de Ethernet (40 GB/s utilizáveis) Azure Second Gen SmartNIC | 

## <a name="software-specifications"></a>Especificações de software 

| Especificações de software        | VMs da série HBv3                                            | 
|--------------------------------|-----------------------------------------------------------|
| Tamanho máximo do trabalho MPI               | 36.000 núcleos (300 VMs em um único conjunto de dimensionamento de máquinas virtuais com singlePlacementGroup = true) |
| Suporte a MPI                    | HPC-X, Intel MPI, OpenMPi, MVAPICH2, MPICH  |
| Estruturas adicionais          | UCX, libfabric, PGAS                  |
| Suporte do armazenamento do Azure          | Discos Standard e Premium (máximo de 32 discos)              |
| Suporte do so para SRIOV RDMA      | CentOS/RHEL 7.6 +, Ubuntu 18.04 +, SLES 12 SP4 +, WinServer 2016 +           |
| Sistema operacional recomendado para desempenho | CentOS 8,1, Windows Server 2019 +
| Suporte do Orchestrator           | Azure CycleCloud, lote do Azure, AKS; [Opções de configuração de cluster](../../sizes-hpc.md#cluster-configuration-options)                      | 

> [!NOTE] 
> O Windows Server 2012 R2 não tem suporte no HBv3 e em outras VMs com mais de 64 núcleos (virtuais ou físicos). Veja [aqui](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) para obter mais detalhes.

## <a name="next-steps"></a>Próximas etapas

- Leia sobre os comunicados mais recentes, exemplos de carga de trabalho do HPC e resultados de desempenho nos [Blogs da comunidade técnica de computação do Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para obter uma visão de nível superior da arquitetura de execução de cargas de trabalho de HPC, confira [HPC (computação de alto desempenho) no Azure](/azure/architecture/topics/high-performance-computing/).
