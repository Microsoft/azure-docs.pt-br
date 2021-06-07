---
title: Tamanhos de VM do Azure-GPU | Microsoft Docs
description: Lista os diferentes tamanhos otimizados para GPU disponíveis para máquinas virtuais no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho nessa série.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 09d62ea5f2db77c14e8faff44de7fb3ce759c6fe
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709728"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Tamanhos de máquinas virtuais com GPU otimizadas

Os tamanhos de VM otimizadas para GPU são máquinas virtuais especializadas disponíveis com GPUs únicas, múltiplas ou fracionárias. Esses tamanhos são projetados para cargas de trabalho de visualização e com muita computação e muitos gráficos. Este artigo fornece informações sobre o número e o tipo de GPUs, vCPUs, discos de dados e NICs. A taxa de transferência de armazenamento e a largura de banda de rede também são incluídos para cada tamanho neste agrupamento.

- Os tamanhos da série [NCv3](ncv3-series.md) e do [NC T4_v3](nct4-v3-series.md) são otimizados para aplicativos com aceleração de GPU com uso intensivo de computação. Alguns exemplos são aplicativos baseados em CUDA e OpenCL e simulações, ia e aprendizado profundo. O NC T4 v3-Series se concentra em cargas de trabalho de inferência com a GPU Tesla T4 da NVIDIA e o processador AMD EPYC2 Roma. A série NCv3 está concentrada em cargas de trabalho de computação e de ia de alto desempenho com a GPU Tesla V100 da NVIDIA.

- O tamanho da [série NDv2](ndv2-series.md) é voltado para aplicativos de treinamento de aprendizado profundo de expansão e expansão. A série NDv2 usa o NVIDIA voltar V100 e o processador Intel Xeon Platinum 8168 (Skylake).

- Os tamanhos da série [NV](nv-series.md) e da [série NVv3](nvv3-series.md) são otimizados e desenvolvidos para cenários de visualização remota, streaming, jogos, codificação e VDI usando estruturas como OpenGL e DirectX. Essas VMs são apoiadas pela GPU Tesla M60 da NVIDIA.

- [Série NVv4](nvv4-series.md) Tamanhos de VM otimizados e projetados para VDI e visualização remota. Com GPUs particionadas, o NVv4 oferece o tamanho certo para cargas de trabalho que exigem recursos menores de GPU. Essas VMs são apoiadas pela GPU AMD Radeon instinto MI25. Atualmente, as VMs NVv4 dão suporte apenas ao sistema operacional convidado do Windows.

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operacionais e drivers com suporte

Para aproveitar os recursos de GPU das VMs da série N do Azure, os drivers NVIDIA ou AMD GPU devem ser instalados.

- Para VMs apoiadas por GPUs NVIDIA, a [extensão de Driver Nvidia GPU](./extensions/hpccompute-gpu-windows.md) instala drivers NVIDIA CUDA ou Grid apropriados. Instale ou gerencie a extensão usando o portal do Azure ou ferramentas, como Azure PowerShell ou modelos do Azure Resource Manager. Confira a [documentação da Extensão de Driver de GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) para saber quais são os sistemas operacionais compatíveis e as etapas de implantação. Para obter informações gerais sobre extensões de VM, confira [Recursos e extensões de máquina virtual do Azure](./extensions/overview.md).

   Como alternativa, você pode instalar os drivers NVIDIA GPU manualmente. Confira [instalar drivers NVIDIA GPU em VMs da série n executando o Windows](./windows/n-series-driver-setup.md) ou [instalar drivers NVIDIA GPU em VMs da série n executando o Linux](./linux/n-series-driver-setup.md) para sistemas operacionais, Drivers, instalação e etapas de verificação com suporte.

- Para VMs apoiadas por GPUs AMD, a [extensão de driver AMD GPU](./extensions/hpccompute-amd-gpu-windows.md) instala os drivers AMD apropriados. Instale ou gerencie a extensão usando o portal do Azure ou ferramentas, como Azure PowerShell ou modelos do Azure Resource Manager. Para obter informações gerais sobre extensões de VM, confira [Recursos e extensões de máquina virtual do Azure](./extensions/overview.md).

   Como alternativa, você pode instalar os drivers do AMD GPU manualmente. Confira [instalar drivers AMD GPU em VMs da série N executando o Windows](./windows/n-series-amd-driver-setup.md) para sistemas operacionais, Drivers, instalação e etapas de verificação com suporte.

## <a name="deployment-considerations"></a>Considerações de implantação

- Para ver a disponibilidade das VMs da Série N, veja [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

- As VMs da Série N só podem ser implantadas no modelo de implantação do Resource Manager.

- As VMs da série N diferem no tipo de Armazenamento do Microsoft Azure que dão suporte aos discos. As VMs NC e NV dão suporte somente a discos de VM que executam backup em HDD (Armazenamento em Disco Standard). Todas as outras VMs de GPU dão suporte a discos de VM que são apoiados por Standard Armazenamento em Disco e Armazenamento em Disco Premium (SSD).

- Se você quiser implantar mais do que algumas VMs da Série N, considere uma assinatura pré-paga ou outras opções de compra. Se estiver usando uma [conta gratuita do Azure](https://azure.microsoft.com/free/), você poderá usar apenas um número limitado de núcleos de computação do Azure.

- Talvez seja necessário aumentar a cota de núcleos (por região) na sua assinatura do Azure, e aumentar a cota separada para núcleos NC, NCv2, NCv3, ND, NDv2, NV, ou NVv2. Para solicitar um aumento de cota, [abra uma solicitação de atendimento ao cliente online](../azure-portal/supportability/how-to-create-azure-support-request.md) gratuitamente. Os limites padrão podem variar dependendo de sua categoria de assinatura.

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Computação otimizada](sizes-compute.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
