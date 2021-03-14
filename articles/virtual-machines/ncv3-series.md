---
title: Série NCv3-máquinas virtuais do Azure
description: Especificações para as VMs da série NCv3.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: c28ed042156865c52607ce031507c696f28b4e7c
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102617294"
---
# <a name="ncv3-series"></a>Série NCv3

VMs da série NCv3 têm a tecnologia de GPUs NVIDIA Tesla V100. Essas GPUs podem fornecer desempenho computacional 1,5 vezes maior da série NCv2. Os clientes podem aproveitar essas GPUs atualizadas para cargas de trabalho HPC tradicionais como modelagem de reservatório, sequenciamento de DNA, análise de proteína, simulações de Monte Carlo, entre outros. A configuração NC24rs v3 fornece um adaptador de rede de alta taxa de transferência e baixa latência, otimizado para cargas de trabalho de computação paralela firmemente acopladas. Além das GPUs, as VMs da série NCv3 também são alimentadas por CPUs Intel Xeon E5-2690 V4 (Broadwell).

[Armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Cache de armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Migração ao vivo](maintenance-and-updates.md): sem suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): sem suporte<br>
[Suporte à geração de VM](generation-2.md): geração 1 e 2<br>
[Rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): sem suporte<br>
[Discos do sistema operacional efêmero](ephemeral-os-disks.md): com suporte <br>
Interconexão NVIDIA NVLink: sem suporte<br>

> [!IMPORTANT]
> Para essa série de VMs, a cota de vCPU (núcleo) em sua assinatura é inicialmente definida como 0 em cada região. [Solicite um aumento de cota de vCPU](../azure-portal/supportability/resource-manager-core-quotas-request.md) para esta série em uma [região disponível](https://azure.microsoft.com/regions/services/). Essas SKUs não estão disponíveis para avaliação ou assinaturas do Azure do assinante do Visual Studio. Seu nível de assinatura pode não dar suporte à seleção ou implantação dessas SKUs. 
>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v3    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v3* | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = um cartão V100.

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
