---
title: Tamanhos de VM do Azure – otimizado para computação | Microsoft Docs
description: Lista os diferentes tamanhos otimizados para computação disponíveis para máquinas virtuais no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e a largura de banda de rede para cada tamanho nessa série.
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-compute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 01eba7bff571a8db25591b8bfa5c5e712511588f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557685"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>Compute tamanhos de máquinas virtuais otimizadas

Os tamanhos de VM otimizados para computação têm uma alta taxa de CPU para memória. Esses tamanhos são bons para servidores Web de tráfego médio, dispositivos de rede, processos em lote e servidores de aplicativos. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs. Ele também inclui informações sobre a taxa de transferência de armazenamento e a largura de banda de rede para cada tamanho neste agrupamento.

A [série Fsv2](fsv2-series.md) é executada na 2ª geração de processadores Intel® Xeon® platina 8272CL (cascadey Lake) e processadores Intel® Xeon® Platinum 8168 (Skylake). Ele apresenta uma velocidade de clock de Turbo principal de 3,4 GHz e uma frequência máxima de Turbo de núcleo único de 3,7 GHz. As instruções Intel® AVX-512 são novas em processadores escalonáveis da Intel. Essas instruções fornecem um aumento de desempenho 2X para cargas de trabalho de processamento de vetores em operações de ponto flutuante de precisão única e dupla. Em outras palavras, eles são realmente rápidos para qualquer carga de trabalho computacional.

A um preço de lista por hora inferior, a série Fsv2 é o melhor valor de preço/desempenho no portfólio do Azure com base na ACU (Unidade de Computação do Azure) por vCPU.

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.

Para obter mais informações sobre como o Azure nomeia suas VMs, consulte [convenções de nomenclatura de tamanhos de máquina virtual do Azure](./vm-naming-conventions.md).