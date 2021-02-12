---
title: Série NDv2
description: Especificações para as VMs da série NDv2.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 97eb134a22bbf2304c39fe7c55457e394467f1d2
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99095267"
---
# <a name="updated-ndv2-series"></a>Série NDv2 atualizada

A máquina virtual da série NDv2 é uma nova adição à família de GPU projetada para as necessidades das cargas de trabalho mais exigentes do ia, do aprendizado de máquina, da simulação e do HPC com maior rapidez.

A NDv2 é alimentada por oito GPUs NVIDIA Tesla V100 NVLINK conectadas, cada uma com 32 GB de memória de GPU. Cada VM NDv2 também tem 40 núcleos não hiperthreads Intel Xeon Platinum 8168 (Skylake) e 672 GiB de memória do sistema.

As instâncias de NDv2 fornecem excelente desempenho para cargas de trabalho do HPC e do AI utilizando kernels de computação otimizados para GPU CUDA e as muitas ferramentas de ia, ML e análise que dão suporte à aceleração de GPU "pronta para uso", como TensorFlow, Pytorch, Caffe, RAPIDS e outras estruturas.

De forma crucial, o NDv2 é criado para expansão computacionalmente intensa (aproveitando 8 GPUs por VM) e escalar horizontalmente (aproveitando várias VMs trabalhando juntas) cargas de trabalho. A série NDv2 agora dá suporte à rede de back-end InfiniBand EDR de 100-Gigabit, semelhante àquela disponível na série HB da VM HPC, para permitir o clustering de alto desempenho para cenários paralelos, incluindo treinamento distribuído para ia e ML. Essa rede de back-end dá suporte a todos os principais protocolos InfiniBand, incluindo aqueles empregados pelas bibliotecas NCCL2 da NVIDIA, permitindo um clustering contínuo de GPUs.

> [!IMPORTANT]
> Ao [habilitar a InfiniBand](./workloads/hpc/enable-infiniband.md) na VM ND40rs_v2, use o driver 4.7-1.0.0.1 Mellanox ofed.
>
> Devido à maior memória da GPU, o novo ND40rs_v2 VM requer o uso de [VMs de geração 2](./generation-2.md) e imagens do Marketplace. 
>
> Observação: o ND40s_v2 incluindo 16 GB de memória por GPU não está mais disponível para visualização e foi substituído pelo ND40rs_v2 atualizado.

<br>

[Armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Cache de armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Migração ao vivo](maintenance-and-updates.md): sem suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): sem suporte<br>
[Suporte à geração de VM](generation-2.md): geração 2<br>
[Rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): com suporte<br>
[Discos do sistema operacional efêmero](ephemeral-os-disks.md): sem suporte <br>
InfiniBand: com suporte<br>
Interconexão NVIDIA NVLink: com suporte<br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD): GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Largura de banda de rede máxima | Máximo de NICs |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 32 | 32 | 80000/800 | 24000 Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operacionais e drivers com suporte

Para aproveitar os recursos de GPU das VMs da série N do Azure, os drivers NVIDIA GPU devem ser instalados.

A [Extensão de Driver de GPU NVIDIA](./extensions/hpccompute-gpu-linux.md) instala drivers CUDA ou GRID NVIDIA apropriados em VMs da série N. Instale ou gerencie a extensão usando o portal do Azure ou ferramentas, como Azure PowerShell ou modelos do Azure Resource Manager. Para obter informações gerais sobre extensões de VM, confira [Recursos e extensões de máquina virtual do Azure](./extensions/overview.md).

Se você optar por instalar os drivers NVIDIA GPU manualmente, consulte [instalação do driver de GPU da série N para Linux](./linux/n-series-driver-setup.md).

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
