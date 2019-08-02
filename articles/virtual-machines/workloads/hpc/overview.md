---
title: Computação de alto desempenho em VMs da série H – Máquinas Virtuais do Azure | Microsoft Docs
description: Saiba mais sobre os recursos e as funcionalidades de VMs da série H otimizadas para HPC.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.author: amverma
ms.openlocfilehash: d6e857a87e4c7df8ffb2be1eefb7a0290da5b10a
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799001"
---
# <a name="high-performance-computing-on-h-series-vms"></a>Computação de alto desempenho em VMs da série H

HPC (computação de alto desempenho) em VMs das séries HB e HC possibilitam o desempenho de HPC mais otimizado de qualquer VM no Azure. VMs otimizadas para HPC são usadas para resolver alguns dos problemas matemáticos mais difíceis, como: dinâmica de fluidos, simulações de petróleo e gás e modelagem do clima.

Este artigo aborda alguns dos principais recursos de VMs das séries HB e HC, por que essas VMs executam bem em cenários de HPC e como começar a usá-las.

## <a name="features-and-capabilities"></a>Recursos e funcionalidades

VMs das séries HB e HC são projetadas para fornecer o melhor desempenho de HPC, escalabilidade de MPI (interface de transmissão de mensagens) e economia para cargas de trabalho HPC.

### <a name="message-passing-interface"></a>Interface de envio de mensagem

HB-series e HC-series são compatíveis com quase todos os tipos e versões de MPI. Alguns dos tipos de MPI mais comuns compatíveis são: OpenMPI, MVAPICH2, Platform MPI, Intel MPI e todos os verbos de RDMA (acesso remoto direto à memória). Para obter mais informações, confira [Configurar a Interface de Envio de Mensagens para HPC](setup-mpi.md).

### <a name="rdma-and-infiniband"></a>RDMA e InfiniBand

A interface RDMA é o padrão nas VMs das séries HB e HC. Instâncias compatíveis com RDMA comunicam-se em uma rede InfiniBand, operando a EDRs (taxas de dados aprimoradas) para máquinas virtuais das séries HB e HC. Instâncias compatíveis com RDMA podem melhorar a escalabilidade e o desempenho de alguns aplicativos de MPI.

A configuração de InfiniBand compatível com VMs das séries HB e HC são fat trees (árvores gordas) sem bloqueio com um design de diâmetro baixo para desempenho de RDMA consistente.

Confira [Habilitar InfiniBand](enable-infiniband.md) para saber mais sobre como configurar InfiniBand em suas VMs da série HB ou HC.

## <a name="get-started"></a>Introdução

Primeiro, decida qual VM da série H você pretende usar. Para obter detalhes sobre VMs otimizadas para HPC, confira [Visão geral da série HB](hb-series-overview.md) e [Visão geral da série HC](hc-series-overview.md). Para obter especificações, confira [Tamanhos de VM de computação de alto desempenho](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc).

Depois de ter selecionado e criado uma VM para seu aplicativo, você precisará configurá-lo habilitando InfiniBand. Para saber como habilitar InfiniBand em VMs do Linux e do Windows, confira [Habilitar InfiniBand](enable-infiniband.md).

Um componente crítico de cargas de trabalho HPC é MPI. HB-series e HC-series são compatíveis com quase todos os tipos e versões de MPI. Para obter mais informações, confira [Configurar a Interface de Envio de Mensagens para HPC](setup-mpi.md).

Depois de escolher sua série de VM, configurar Infiniband e MPI, você está pronto para começar a criar suas cargas de trabalho HPC.

## <a name="next-steps"></a>Próximas etapas

- Examine a [Visão geral da série HB](hb-series-overview.md) e a [Visão geral da série HC](hc-series-overview.md) para saber mais sobre as principais diferenças e especificações.

- Para um nível mais alto, a exibição da arquitetura de cargas de trabalho HPC em execução, confira [HPC (computação de alto desempenho) no Azure](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/).
