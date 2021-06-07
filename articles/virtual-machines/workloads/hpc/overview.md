---
title: Computação de alto desempenho em VMs das séries H e N habilitadas para InfiniBand – Máquinas Virtuais do Azure
description: Conheça os recursos e as funcionalidades das VMs das séries H e N habilitadas para InfiniBand e otimizadas para HPC.
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: overview
ms.date: 03/18/2021
ms.reviewer: cynthn
ms.openlocfilehash: 65b37a8c07e083f5e9809812e2d4446cc48717d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720586"
---
# <a name="high-performance-computing-on-infiniband-enabled-h-series-and-n-series-vms"></a>Computação de alto desempenho em VMs das séries H e N habilitadas para InfiniBand

As VMs das séries H e N habilitadas para InfiniBand do Azure foram projetadas para fornecer desempenho no nível de liderança, escalabilidade da MPI (Interface de Passagem de Mensagem) e redução de custos para uma variedade de cargas de trabalho de HPC e IA do mundo real. Essas VMs otimizadas para HPC (computação de alto desempenho) são usadas para resolver alguns dos problemas que exigem uma computação mais intensiva em ciência e engenharia como: dinâmica de fluidos, modelagem da Terra, simulações climáticas etc.

Estes artigos descrevem como começar a usar as VMs das séries N e H habilitadas para InfiniBand no Azure, bem como a configuração ideal das cargas de trabalho de HPC e IA nas VMs para escalabilidade.

## <a name="features-and-capabilities"></a>Recursos e funcionalidades

As VMs das séries H e N habilitadas para InfiniBand foram projetadas para fornecer o melhor em desempenho de HPC e escalabilidade da MPI (Interface de Passagem de Mensagem), bem como redução de custos para cargas de trabalho de HPC. Confira as VMs da [série H](../../sizes-hpc.md) e da [série N](../../sizes-gpu.md) para saber mais sobre os recursos e as funcionalidades delas.

### <a name="rdma-and-infiniband"></a>RDMA e InfiniBand

As VMs [compatíveis com RDMA](../../sizes-hpc.md#rdma-capable-instances) da [série H](../../sizes-hpc.md) e da [série N](../../sizes-gpu.md) se comunicam pela rede InfiniBand de baixa latência e alta largura de banda. A funcionalidade RDMA em uma interconexão como essa é crítica para aumentar a escalabilidade e o desempenho de cargas de trabalho de HPC e IA de nó distribuído. As VMs das séries H e N habilitadas para InfiniBand estão conectadas em uma fat tree sem bloqueio com um design de diâmetro baixo para desempenho otimizado e consistente de RDMA.
Confira [Habilitar o InfiniBand](enable-infiniband.md) para saber mais sobre como configurá-lo nas VMs habilitadas para InfiniBand.

### <a name="message-passing-interface"></a>Interface de envio de mensagem

As séries H e N habilitadas para SR-IOV dão suporte a quase todas as versões e bibliotecas MPI. Algumas das bibliotecas MPI mais usadas são: Intel MPI, OpenMPI, HPC-X, MVAPICH2, MPICH e Platform MPI. Todos os verbos de RDMA (acesso remoto direto à memória) são compatíveis.
Confira [Configurar a MPI](setup-mpi.md) para saber mais sobre como instalar várias bibliotecas MPI compatíveis e a configuração ideal delas.

## <a name="get-started"></a>Introdução

A primeira etapa é selecionar o tipo de VM ideal da [série H](../../sizes-hpc.md) e da [série N](../../sizes-gpu.md) para a carga de trabalho com base nas especificações da VM e na [funcionalidade RDMA](../../sizes-hpc.md#rdma-capable-instances).
Em segundo lugar, configure a VM habilitando o InfiniBand. Há vários métodos para fazer isso, incluindo o uso de imagens de VM otimizadas com drivers inclusos; confira [Otimização para Linux](configure.md) e [Habilitar o InfiniBand](enable-infiniband.md) para obter detalhes.
Em terceiro lugar, é fundamental escolher e configurar a MPI de modo apropriado para executar cargas de trabalho de nó distribuído. Confira [Configurar a MPI](setup-mpi.md) para obter detalhes.
Em quarto lugar, configure cargas de trabalho de modo ideal para obter desempenho e escalabilidade seguindo as diretrizes específicas da família de VMs, conforme mostrado na [Visão geral da série HBv3](hbv3-series-overview.md) e na [Visão geral da série HC](hc-series-overview.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como [configurar e otimizar](configure.md) as VMs da [série H](../../sizes-hpc.md) e da [série N](../../sizes-gpu.md) habilitadas para InfiniBand.
- Examine a [Visão geral da série HBv3](hb-series-overview.md) e a [Visão geral da série HC](hc-series-overview.md) para saber mais sobre como configurar cargas de trabalho de modo ideal para obter desempenho e escalabilidade.
- Leia informações sobre comunicados mais recentes, exemplos de cargas de trabalho de HPC e resultados de desempenho nos [Blogs do programa Tech Groups da Computação do Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para obter uma visão de nível superior da arquitetura de execução de cargas de trabalho de HPC, confira [HPC (computação de alto desempenho) no Azure](/azure/architecture/topics/high-performance-computing/).
