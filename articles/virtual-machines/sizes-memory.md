---
title: Tamanhos de VM do Azure – memória | Microsoft Docs
description: Lista os diferentes tamanhos otimizados para memória disponíveis para máquinas virtuais no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e a largura de banda de rede para cada tamanho nessa série.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: Isolamento de VM, VM isolada, isolamento, isolada
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 1e27ee2c045404d13b10945ac65484281b0cd967
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019708"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Tamanhos de máquinas virtuais com GPU otimizadas para memória

Os tamanhos de VM com otimização de memória oferecem uma alta taxa de memória para CPU que é excelente para servidores de banco de dados relacionais, caches médios a grandes e análise na memória. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho neste agrupamento.

- As [séries Dv2 e DSv2](dv2-dsv2-series-memory.md), uma continuação da série D original, traz uma CPU com mais potência. A série Dv2 é aproximadamente 35% mais rápida do que a série D. Ela é executado nos processadores Intel&reg; Xeon&reg; 8171M 2,1 GHz (Skylake) ou Intel&reg; Xeon&reg; E5-2673 v4 2,3 GHz (Broadwell) ou Intel&reg; Xeon&reg; E5-2673 v3 2,4 GHz (Haswell) e com a Tecnologia Intel Turbo Boost 2.0. A série Dv2 tem as mesmas configurações de memória e disco que a série D.

    As séries Dv2 e DSv2 são ideais para aplicativos que exigem vCPUs mais rápidas, melhor desempenho de armazenamento temporário ou que tenham maior demanda de memória. Elas oferecem uma combinação poderosa para vários aplicativos de nível empresarial.

- As [séries Eav4 e Easv4](eav4-easv4-series.md) utilizam o processador EPYC<sup>TM</sup> 7452 2,35 GHz da AMD em uma configuração com multi-thread e um cache L3 de até 256 MB, aumentando as opções para executar a maioria das cargas de trabalho otimizadas para memória. As séries Eav4 e Easv4 têm as mesmas configurações de memória e disco que as séries Ev3 e Esv3.

- O processador Intel&reg; Xeon&reg; 8171M 2,1 GHz (Skylake) ou Intel&reg; Xeon&reg; E5-2673 v4 2,3 GHz (Broadwell) das [séries Ev3 e Esv3](ev3-esv3-series.md) em uma configuração hyper-threaded, fornecendo uma melhor proposta de valor para a maioria das cargas de trabalho de uso geral e levando a Ev3 para o alinhamento com as VMs de uso geral da maioria das outras nuvens. A memória foi expandida (de 7 GiB/vCPU a 8 GiB/vCPU), enquanto os limites de rede e disco por núcleo para alinhamento com a migração para o hyper-threading. A série Ev3 é o acompanhamento até os tamanhos de VM de memória alta das famílias D/Dv2.

- As [Ev4 e a série Esv4](ev4-esv4-series.md) são executadas nos processadores da 2ª geração Intel &reg; Xeon &reg; Platinum 8272CL (cascadey Lake) em uma configuração de hiperthread, são ideais para vários aplicativos empresariais com uso intensivo de memória e recursos até 504 GiB de RAM. Ele apresenta a tecnologia [Intel &reg; Turbo Boost 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [a &reg; tecnologia Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) e [ &reg; as extensões de vetor avançadas Intel 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). As Ev4 e Esv4-Series não incluem um disco temporário local. Para obter mais informações, consulte  [tamanhos de VM do Azure sem disco temporário local](azure-vms-no-temp-disk.md).

- A [Edv4 e a série Edsv4](edv4-edsv4-series.md) são executadas em processadores de 2ª geração Intel &reg; Xeon &reg; Platinum 8272CL (Cascade, Lake), ideais para bancos de dados muito grandes ou outros aplicativos que se beneficiam de contagens de vCPU altas e grandes quantidades de memória. Além disso, esses tamanhos de VM incluem um armazenamento de SSD local rápido e maior para aplicativos que se beneficiam de armazenamento local de alta velocidade e de baixa latência. Ele apresenta uma velocidade de clock de Turbo principal de 3,4 GHz [, &reg; tecnologia Intel Turbo Boost 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [ &reg; tecnologia Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) e [extensões de &reg; vetor avançadas Intel 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html).

- A [série M](m-series.md) oferece uma alta contagem de vCPU (até 128 vCPUs) e uma grande quantidade de memória (até 3,8 TiB). Ela também é ideal para bancos de dados extremamente grandes ou outros aplicativos que se beneficiam de altas contagens de vCPU e de grandes quantidades de memória.

- A [série Mv2](mv2-series.md) oferece a mais alta contagem de vCPU (até 416 vCPUs) e a maior memória (até 11,4 TiB) de qualquer VM na nuvem. Ela é ideal para bancos de dados extremamente grandes ou outros aplicativos que se beneficiam de altas contagens de vCPU e de grandes quantidades de memória.

A Computação do Azure oferece tamanhos de máquina virtual Isolada, para um tipo de hardware específico e dedicada a um único cliente. Esses tamanhos de máquina virtual são mais adequados para cargas de trabalho que exigem um alto grau de isolamento de outros clientes, para cargas de trabalho que envolvem elementos como requisitos normativos e de conformidade. Os clientes também podem optar por subdividir ainda mais os recursos dessas máquinas virtuais Isoladas usando [o Suporte do Azure para máquinas virtuais aninhadas](https://azure.microsoft.com/blog/nested-virtualization-in-azure/). Confira as páginas das famílias de máquina virtual abaixo para obter as opções de VM isoladas.

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Computação otimizada](sizes-compute.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.

Para obter mais informações sobre como o Azure nomeia suas VMs, consulte [convenções de nomenclatura de tamanhos de máquina virtual do Azure](./vm-naming-conventions.md).
