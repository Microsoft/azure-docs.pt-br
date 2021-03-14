---
title: Visão geral da VM da série HBv2 – máquinas virtuais do Azure | Microsoft Docs
description: Saiba mais sobre o tamanho da VM da série HBv2 no Azure.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 6648f77c5eacf40f848bc9b24aa6e257d8adf626
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674641"
---
# <a name="hbv2-series-virtual-machine-overview"></a>Visão geral da máquina virtual da série HBv2 

 
A maximização do desempenho do aplicativo HPC (computação de alto desempenho) no AMD EPYC requer uma abordagem cuidadosa de localidade de memória e colocação de processo. Abaixo, descrevemos a arquitetura AMD EPYC e nossa implementação de ti no Azure para aplicativos HPC. Usaremos o termo **pNUMA** para fazer referência a um domínio numa físico e **vNUMA** para fazer referência a um domínio numa virtualizado. 

Fisicamente, um servidor da [série HBv2](../../hbv2-series.md) é 2 * 64-core EPYC 7742 CPUs para um total de 128 núcleos físicos. Esses 128 núcleos são divididos em domínios 32 pNUMA (16 por soquete), cada um deles com 4 núcleos e com o termo AMD como um **núcleo complexo** (ou **CCX**). Cada CCX tem seu próprio cache L3, que é como um sistema operacional verá um limite de pNUMA/vNUMA. Quatro CCXs adjacentes têm acesso a 2 canais de DRAM física. 

Para fornecer espaço para o hipervisor do Azure operar sem interferir na VM, reservamos domínios pNUMA físicos 0 e 16 (ou seja, o primeiro CCX de cada soquete de CPU). Todos os 30 domínios restantes do pNUMA são atribuídos à VM no ponto em que se tornam vNUMA. Assim, a VM verá:

`(30 vNUMA domains) * (4 cores/vNUMA) = 120` núcleos por VM 

A própria VM não tem reconhecimento de que pNUMA 0 e 16 são reservados. Ele enumera o vNUMA que vê como 0-29, com 15 vNUMA por soquete simetricamente, vNUMA 0-14 no vSocket 0 e vNUMA 15-29 no vSocket 1. Na próxima seção, há instruções sobre a melhor execução de aplicativos MPI neste layout NUMA assimétrica. 

A fixação do processo funcionará em VMs da série HBv2 porque expõemos o silício subjacente como está para a VM convidada. É altamente recomendável fixar o processo para desempenho e consistência ideais. 


## <a name="hardware-specifications"></a>Especificações de hardware 

| Especificações de hardware          | VM da série HBv2                   | 
|----------------------------------|----------------------------------|
| Núcleos                            | 120 (SMT desabilitado)               | 
| CPU                              | AMD EPYC 7742                    | 
| Frequência de CPU (não AVX)          | ~ 3,1 GHz (único + todos os núcleos)    | 
| Memória                           | 4 GB/núcleo (total de 480 GB)         | 
| Disco local                       | 960 GB NVMe (bloco), SSD de 480 GB (arquivo de paginação) | 
| Infiniband                       | 200 GB/s EDR Mellanox ConnectX-6 | 
| Rede                          | 50 GB/s de Ethernet (40 GB/s utilizáveis) Azure Second Gen SmartNIC | 


## <a name="software-specifications"></a>Especificações de software 

| Especificações de software     | VM da série HBv2                                            | 
|-----------------------------|-----------------------------------------------------------|
| Tamanho máximo do trabalho MPI            | 36000 núcleos (300 VMs em um único conjunto de dimensionamento de máquinas virtuais com singlePlacementGroup = true) |
| Suporte a MPI                 | HPC-X, Intel MPI, OpenMPi, MVAPICH2, MPICH, plataforma MPI  |
| Estruturas adicionais       | Comunicação unificada X, libfabric, PGAS                  |
| Suporte do armazenamento do Azure       | Discos Standard e Premium (máximo de 8 discos)              |
| Suporte do so para SRIOV RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +           |
| Suporte do Orchestrator        | CycleCloud, lote                                         | 


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre arquitetura do [AMD EPYC](https://bit.ly/2Epv3kC) e [arquiteturas de vários chips](https://bit.ly/2GpQIMb). Para obter informações mais detalhadas, consulte o [Guia de ajuste do HPC para processadores AMD EPYC](https://bit.ly/2T3AWZ9).
- Leia sobre os comunicados mais recentes e alguns exemplos de HPC nos [Blogs da comunidade de computação técnica do Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para obter uma visão de nível superior da arquitetura de execução de cargas de trabalho de HPC, confira [HPC (computação de alto desempenho) no Azure](/azure/architecture/topics/high-performance-computing/).