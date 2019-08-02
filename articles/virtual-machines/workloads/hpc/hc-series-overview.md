---
title: Visão geral de VM série HC - máquinas virtuais do Azure | Microsoft Docs
description: Saiba mais sobre o suporte de visualização para o tamanho VM da série HC no Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 6cdb539846104f70dabf684925685fb062fea8af
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797538"
---
# <a name="hc-series-virtual-machine-overview"></a>Visão geral da máquina virtual de HC-series

Maximizar o desempenho de aplicativos de HPC em processadores escalonáveis da Intel Xeon requer uma abordagem consciente para posicionamento de processo nessa nova arquitetura. Aqui, vamos descrever nossa implementação em VMs da série HC do Azure para aplicativos HPC. Usaremos o termo "pNUMA" para se referir a um domínio NUMA físico e "vnuma;" para se referir a um domínio NUMA virtualizado. Da mesma forma, usaremos o termo "pCore" para referir-se para os núcleos de CPU físicos e virtualizados de "vCore" para referir-se de núcleos de CPU.

Fisicamente, um servidor HC é 2 * 24-core Intel Xeon Platinum 8168 CPUs para um total de 48 núcleos físicos. Cada CPU é um domínio único pNUMA e tem acesso a seis canais de DRAM de Unificação. Recurso de CPUs Intel Xeon de Platinum um 4 x cache L2, maior do que em gerações anteriores (256 KB/core-> 1 MB/núcleo), enquanto também reduz o cache L3 em comparação comparado CPUs Intel de anterior (2,5 MB/core-> 1.375 MB/core).

Topologia acima é transportado para a configuração do hipervisor series HC também. Para fornecer espaço para o hipervisor do Azure operar sem interferir na VM, nós nos reservamos pCores 0-1 e 24 e 25 (ou seja, os 2 primeiros pCores em cada soquete). Em seguida, atribuímos pNUMA domínios todos os núcleos restantes para a máquina virtual. Portanto, a VM será exibida:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` núcleos por VM

A VM não tem conhecimento que pCores 0-1 e 24 e 25 não foram fornecidos a ele. Portanto, ele expõe cada vnuma; como se tivesse nativamente 22 núcleos.

Intel Xeon Platinum, Gold e Silver CPUs também apresentam uma rede de malha 2D no chip para a comunicação dentro e externo para o soquete da CPU. É altamente recomendável o processo de fixação para consistência e desempenho ideal. A fixação de processo funcionará em VMs da série HC porque silício subjacente é exposto como-é a VM convidada. Para obter mais informações, consulte [arquitetura Intel Xeon SP](https://bit.ly/2RCYkiE).

O diagrama a seguir mostra a segregação de núcleos reservados para hipervisor do Azure e a VM da série HC.

![Segregação de núcleos reservados para o hipervisor do Azure e VM da série HC](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Especificações de hardware

| Especificações de hardware          | VM da série HC                     |
|----------------------------------|----------------------------------|
| Núcleos                            | 40 (HT desabilitado)                 |
| CPU                              | Intel Xeon Platinum 8168*        |
| Frequência da CPU (não AVX)          | 3,7 GHz (núcleo único), 3.4 de 2,7 GHz (todos os núcleos) |
| Memória                           | 8 GB/core (total de 352)            |
| Disco local                       | NVMe de 700 GB                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 * * |
| Rede                          | 50 Gb Ethernet (40 Gb utilizável) do Azure segundo Gen SmartNIC * * * |

## <a name="software-specifications"></a>Especificações do software

| Especificações do software     | VM da série HC          |
|-----------------------------|-----------------------|
| Tamanho do trabalho de MPI máx            | 4400 núcleos (conjuntos de dimensionamento de máquina virtual 100,) 8800 núcleos (200 conjuntos de dimensionamento de máquina virtual) |
| Suporte do MPI                 | MVAPICH2, OpenMPI, MPICH, plataforma MPI, Intel MPI  |
| Estruturas adicionais       | X de comunicação unificada, libfabric, PGAS |
| Suporte ao armazenamento do Azure       | STD + Premium (máximo 4 discos) |
| Suporte do sistema operacional para RDMA SRIOV   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+ |
| Suporte do Azure CycleCloud    | Sim                         |
| Suporte do lote do Azure         | Sim                         |

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os tamanhos de VM do HPC para [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) no Azure.

* Saiba mais sobre [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
