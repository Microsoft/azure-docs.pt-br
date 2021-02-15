---
title: Visão geral da VM da série HB – máquinas virtuais do Azure | Microsoft Docs
description: Saiba mais sobre o suporte de visualização para o tamanho da VM da série HB no Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 1e37f13bc6e819079c63c583a1eeda12af5e213c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965452"
---
# <a name="hb-series-virtual-machines-overview"></a>Visão geral das máquinas virtuais da série HB

A maximização do desempenho do aplicativo HPC (computação de alto desempenho) no AMD EPYC requer uma abordagem cuidadosa de localidade de memória e colocação de processo. Abaixo, descrevemos a arquitetura AMD EPYC e nossa implementação de ti no Azure para aplicativos HPC. Usaremos o termo "pNUMA" para fazer referência a um domínio NUMA físico e "vNUMA" para fazer referência a um domínio NUMA virtualizado.

Fisicamente, um servidor da [série HB](../../hb-series.md) é 2 * 32-core EPYC 7551 CPUs para um total de 64 núcleos físicos. Esses 64 núcleos são divididos em 16 domínios do pNUMA (8 por soquete), cada um dos quais são quatro núcleos e conhecidos como "CPU complexa" (ou "CCX"). Cada CCX tem seu próprio cache L3, que é como um sistema operacional verá um limite de pNUMA/vNUMA. Um par de CCXs adjacentes compartilha o acesso a dois canais de DRAM física (32 GB de DRAM em servidores da série HB).

Para fornecer espaço para o hipervisor do Azure operar sem interferir na VM, reservamos o domínio pNUMA físico 0 (o primeiro CCX). Em seguida, atribuímos domínios pNUMA 1-15 (as unidades de CCX restantes) para a VM. A VM verá:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` núcleos por VM

A VM, em si, não sabe que pNUMA 0 não foi fornecido a ela. A VM compreende pNUMA 1-15 como vNUMA 0-14, com 7 vNUMA em vSocket 0 e 8 vNUMA no vSocket 1. Embora isso seja assimétrico, seu sistema operacional deve inicializar e operar normalmente. Posteriormente neste guia, instruímos a melhor forma de executar aplicativos MPI neste layout NUMA assimétrica.

A fixação do processo funcionará em VMs da série HB porque expõemos o silício subjacente como está para a VM convidada. É altamente recomendável fixar o processo para desempenho e consistência ideais.

O diagrama a seguir mostra a diferenciação de núcleos reservada para o hipervisor do Azure e a VM da série HB.

![Segregação de núcleos reservada para a VM do Azure hypervisor e da série HB](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Especificações de hardware

| Especificações de hardware                | VM da série HB                     |
|----------------------------------|----------------------------------|
| Núcleos                            | 60 (SMT desabilitado)                |
| CPU                              | AMD EPYC 7551                    |
| Frequência de CPU (não AVX)          | ~ 2,55 GHz (único + todos os núcleos)   |
| Memória                           | 4 GB/núcleo (total de 240 GB)         |
| Disco local                       | SSD DE 700 GB                       |
| Infiniband                       | 100 GB EDR Mellanox ConnectX-5 |
| Rede                          | 50 GB de Ethernet (40 GB utilizáveis) Azure Second Gen SmartNIC |

## <a name="software-specifications"></a>Especificações de software

| Especificações de software           |VM da série HB           |
|-----------------------------|-----------------------|
| Tamanho máximo do trabalho MPI            | 18000 núcleos (300 VMs em um único conjunto de dimensionamento de máquinas virtuais com singlePlacementGroup = true)  |
| Suporte a MPI                 | HPC-X, Intel MPI, OpenMPi, MVAPICH2, MPICH, plataforma MPI  |
| Estruturas adicionais       | Comunicação unificada X, libfabric, PGAS |
| Suporte do armazenamento do Azure       | Discos Standard e Premium (máximo de 4 discos) |
| Suporte do so para SRIOV RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +  |
| Suporte do Orchestrator        | CycleCloud, lote  |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre arquitetura do [AMD EPYC](https://bit.ly/2Epv3kC) e [arquiteturas de vários chips](https://bit.ly/2GpQIMb). Para obter informações mais detalhadas, consulte o [Guia de ajuste do HPC para processadores AMD EPYC](https://bit.ly/2T3AWZ9).
- Leia os comunicados mais recentes e alguns exemplos e resultados da HPC nos [Blogs da Tech Community da Computação do Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para obter uma visão de nível superior da arquitetura de execução de cargas de trabalho de HPC, confira [HPC (computação de alto desempenho) no Azure](/azure/architecture/topics/high-performance-computing/).
