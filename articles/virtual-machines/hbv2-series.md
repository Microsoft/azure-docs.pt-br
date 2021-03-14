---
title: Série HBv2-máquinas virtuais do Azure
description: Especificações para as VMs da série HBv2.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 1abc05cf1486651b87094f40777f3679d234a34b
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009330"
---
# <a name="hbv2-series"></a>Série HBv2

As VMs da série HBv2 são otimizadas para aplicativos orientados por largura de banda de memória, como dinâmica de fluidos, análise de elemento finito e simulação de reservatório. HBv2 VMs Feature 120 AMD EPYC 7742 núcleos de processador, 4 GB de RAM por núcleo de CPU e nenhum multithread simultâneo. Cada VM HBv2 fornece até 340 GB/s de largura de banda de memória e até 4 teraFLOPS de computação FP64.

As VMs HBv2-Series apresentam 200 GB/s Mellanox HDR InfiniBand. Essas VMs são conectadas em uma árvore Fat sem bloqueio para desempenho de RDMA otimizado e consistente. Essas VMs dão suporte ao roteamento adaptável e ao transporte conectado dinâmico (DCT, além dos transportes Standard RC e UD). Esses recursos aprimoram o desempenho, a escalabilidade e a consistência do aplicativo e seu uso é recomendado.

[Armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Cache de armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Migração ao vivo](maintenance-and-updates.md): sem suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): sem suporte<br>
[Suporte à geração de VM](generation-2.md): geração 1 e 2<br>
[Rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): com suporte ([saiba mais](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965) sobre o desempenho e problemas potenciais) <br>
[Discos do sistema operacional efêmero](ephemeral-os-disks.md): sem suporte <br>
<br>

| Tamanho | vCPU | Processador | Memória (GiB) | Largura de banda de memória GB/s | Frequência de CPU base (GHz) | Frequência de todos os núcleos (GHz, pico) | Frequência de núcleo único (GHz, pico) | Desempenho de RDMA (GB/s) | Suporte a MPI | Armazenamento temporário (GiB) | Discos de dados máximos | VNICs Ethernet máx. |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 456 | 350 | 2.45 | 3.1 | 3.3 | 200 | Todos | 480 + 960 | 8 | 8 |

Saiba mais sobre:
- [Arquitetura subjacente e topologia de VM](./workloads/hpc/hbv2-series-overview.md)
- [Pilha de software com suporte](./workloads/hpc/hbv2-series-overview.md#software-specifications) , incluindo sistema operacional com suporte
- [Desempenho](./workloads/hpc/hbv2-performance.md) esperado da VM da série HBv2.

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

- Leia sobre os comunicados mais recentes, exemplos de carga de trabalho do HPC e resultados de desempenho nos [Blogs da comunidade técnica de computação do Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para obter uma visão de nível superior da arquitetura de execução de cargas de trabalho de HPC, confira [HPC (computação de alto desempenho) no Azure](/azure/architecture/topics/high-performance-computing/).
- Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
