---
title: Visão geral de VM série HB - máquinas virtuais do Azure | Microsoft Docs
description: Saiba mais sobre o suporte de visualização para o tamanho VM da série HB no Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: 62e4d3dbd7357f8c98df3307c1c8fe52cbed1c5e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707770"
---
# <a name="hb-series-virtual-machines-overview"></a>Visão geral de máquinas virtuais de série HB

Maximizar o desempenho dos aplicativos de alto desempenho (HPC) de computação no AMD EPYC requer uma localidade e o processo de posicionamento de memória do abordagem consciente. A seguir, descrevemos a arquitetura do AMD EPYC e nossa implementação no Azure para aplicativos HPC. Usaremos o termo "pNUMA" para se referir a um domínio NUMA físico e "vnuma;" para se referir a um domínio NUMA virtualizado.

Fisicamente, uma série de HB é 2 * 32 núcleos EPYC 7551 CPUs para um total de 64 núcleos físicos. Esses 64 núcleos são divididos em 16 domínios pNUMA (8 por soquete), cada um deles é de quatro núcleos e conhecidos como "Complexo" CPU"(ou"CCX"). Cada CCX tem seu próprio cache L3, que é como um sistema operacional verá um limite de pNUMA/vnuma;. Um par de CCXs adjacentes compartilha o acesso aos dois canais de DRAM físico (32 GB de DRAM em servidores HB série).

Para fornecer espaço para o hipervisor do Azure operar sem interferir na VM, nós nos reservamos pNUMA físico domínio 0 (o primeiro CCX). Em seguida, atribuímos pNUMA domínios 1 a 15 (as unidades restantes CCX) para a VM. A VM será exibida:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` núcleos por VM

A VM, em si, não sabe que pNUMA 0 não foi atribuído a ele. A VM entende pNUMA 1 a 15 como vnuma; de 0 a 14, com 7 vnuma; na vnuma; de vSocket 0 e 8 em vSocket 1. Embora isso seja assimétrica, seu sistema operacional deve inicializar e operar normalmente. Neste documento, eu o instruir a melhor maneira para executar aplicativos MPI nesse layout NUMA assimétrica.

A fixação de processo funcionará em VMs da série HB porque podemos expor silício subjacente como-é a VM convidada. É altamente recomendável o processo de fixação para consistência e desempenho ideal.

Veja mais sobre [arquitetura AMD EPYC](https://bit.ly/2Epv3kC) e [arquiteturas de chip várias](https://bit.ly/2GpQIMb) no LinkedIn. Para obter mais informações, consulte o [guia de ajuste do HPC para processadores AMD de EPYC](https://bit.ly/2T3AWZ9).

O diagrama a seguir mostra a segregação de núcleos reservados para hipervisor do Azure e a VM da série HB.

![Segregação de núcleos reservados para o hipervisor do Azure e VM da série HB](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Especificações de hardware

| Especificações de hardware                | VM da série HB                     |
|----------------------------------|----------------------------------|
| Núcleos                            | 60 (SMT desabilitado)                |
| CPU                              | AMD EPYC 7551*                   |
| Frequência da CPU (não AVX)          | ~2.55 GHz (único + todos os núcleos)   |
| Memória                           | 4 GB/core (total de 240)            |
| Disco local                       | NVMe de 700 GB                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 * * |
| Rede                          | 50 Gb Ethernet (40 Gb utilizável) do Azure segundo Gen SmartNIC * * * |

## <a name="software-specifications"></a>Especificações do software

| Especificações de software           |VM da série HB           |
|-----------------------------|-----------------------|
| Tamanho do trabalho de MPI máx            | núcleos de 12000 6000 núcleos (100 conjuntos de dimensionamento de máquina virtual) (200 conjuntos de dimensionamento de máquina virtual)  |
| Suporte do MPI                 | MVAPICH2, OpenMPI, MPICH, plataforma MPI, Intel MPI  |
| Estruturas adicionais       | X de comunicação unificada, libfabric, PGAS |
| Suporte ao armazenamento do Azure       | STD + Premium (máximo 4 discos) |
| Suporte do sistema operacional para RDMA SRIOV   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+  |
| Suporte do Azure CycleCloud    | Sim                         |
| Suporte do lote do Azure         | Sim                         |

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os tamanhos de VM do HPC para [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) no Azure.

* Saiba mais sobre [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
