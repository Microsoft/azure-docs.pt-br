---
title: Série ND-máquinas virtuais do Azure
description: Especificações para as VMs da série ND.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 5516e17a74fffd28f432df305dfa7f592644d4a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102611565"
---
# <a name="nd-series"></a>Série ND

As máquinas virtuais da série ND são uma nova adição à família de GPU projetada para cargas de trabalho AI e Deep Learning. Elas oferecem um desempenho excelente para treinamento e Inferência. As instâncias do ND são alimentadas por [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPUs e CPUs Intel Xeon E5-2690 V4 (Broadwell). Essas instâncias oferecem um desempenho excelente para operações de ponto flutuante de precisão simples, para cargas de trabalho de AI que utilizam o Cognitive Toolkit, o TensorFlow, o Caffe e outras estruturas. A série ND também oferece um tamanho de memória de GPU muito maior (24 GB), permitindo usar modelos de rede neural muito maiores. Como a série NC, a série ND oferece uma configuração com uma baixa latência secundária, uma rede com alta taxa de transferência por meio de RDMA e a conectividade InfiniBand, permitindo executar trabalhos de grande escala que abrangem várias GPUs.

[Armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Cache de armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Migração ao vivo](maintenance-and-updates.md): sem suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): sem suporte<br>
[Suporte à geração de VM](generation-2.md): geração 1 e 2<br>
[Rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): sem suporte<br>
[Discos do sistema operacional efêmero](ephemeral-os-disks.md): com suporte <br>
Interconexão NVIDIA NVLink: sem suporte<br>

> [!IMPORTANT]
> Para essa série de VMs, a cota de vCPU (núcleo) por região em sua assinatura é definida inicialmente como 0. [Solicite um aumento de cota de vCPU](../azure-portal/supportability/resource-manager-core-quotas-request.md) para esta série em uma [região disponível](https://azure.microsoft.com/regions/services/).
>
| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 24 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = um cartão de P40.

*Compatível com RDMA

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operacionais e drivers com suporte

Para aproveitar os recursos de GPU das VMs da série N do Azure, os drivers NVIDIA GPU devem ser instalados.

A [Extensão de Driver de GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) instala drivers CUDA ou GRID NVIDIA apropriados em VMs da série N. Instale ou gerencie a extensão usando o portal do Azure ou ferramentas, como Azure PowerShell ou modelos do Azure Resource Manager. Confira a [documentação da Extensão de Driver de GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) para saber quais são os sistemas operacionais compatíveis e as etapas de implantação. Para obter informações gerais sobre extensões de VM, confira [Recursos e extensões de máquina virtual do Azure](./extensions/overview.md).

Se você optar por instalar manualmente os drivers NVIDIA GPU, consulte [configuração do driver GPU da série n para Windows](./windows/n-series-driver-setup.md) ou [instalação do driver de GPU da série n para Linux](./linux/n-series-driver-setup.md) para sistemas operacionais, Drivers, instalação e etapas de verificação com suporte.

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
