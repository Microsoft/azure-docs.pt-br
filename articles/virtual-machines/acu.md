---
title: Visão geral da unidade de computação do Azure
description: Visão geral do conceito das unidades de computação do Azure. A ACU é uma maneira de comparar o desempenho da CPU entre SKUs do Azure.
author: mimckitt
ms.service: virtual-machines
ms.subservice: azure-compute-unit
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.reviewer: davberg
ms.openlocfilehash: ae37771cd9e48f450ae538174b9bed9cfa866f9d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565522"
---
# <a name="azure-compute-unit-acu"></a>ACU (unidade de computação do Azure)

O conceito da ACU (Unidade de Computação do Azure) fornece uma maneira de comparar o desempenho de computação (CPU) em SKUs do Azure. Isso ajudará você a identificar facilmente qual SKU é tem maior probabilidade de satisfazer suas necessidades de desempenho. O ACU está atualmente padronizado em uma VM pequena (Standard_A1) sendo 100 e todos os outros SKUs representam aproximadamente quanto tempo a SKU pode executar um benchmark padrão

* ACUs usam a tecnologia Intel® Turbo para aumentar a frequência da CPU e fornecer um aumento de desempenho.  A quantidade do aumento de desempenho pode variar com base no tamanho da VM, na carga de trabalho e em outras cargas de trabalho em execução no mesmo host.

** ACUs usam tecnologia AMD® Boost para aumentar a frequência da CPU e fornecer um aumento de desempenho.  A quantidade do aumento de desempenho pode variar com base no tamanho da VM, na carga de trabalho e em outras cargas de trabalho em execução no mesmo host.

**Com Hyper-threading e capacidade de executar virtualização aninhada

> [!IMPORTANT]
> A ACU é apenas uma diretriz. Os resultados para sua carga de trabalho podem variar.
<br>

| Família de SKU | ACU \ vCPU | vCPU: Core |
| --- | --- |---|
| [A0](sizes-previous-gen.md) |50 | 1:1 |
| [A1 - A4](sizes-previous-gen.md) |100 | 1:1 |
| [A5 - A7](sizes-previous-gen.md) |100 | 1:1 |
| [A1_v2 - A8_v2](sizes-general.md) |100 | 1:1 |
| [A2m_v2 - A8m_v2](sizes-general.md) |100 | 1:1 |
| [A8 - A11](sizes-previous-gen.md) |225* | 1:1 |
| [B](sizes-b-series-burstable.md) |Varia | Varia |
| [D1 - D14](sizes-previous-gen.md) |160-250 | 1:1 |
| [D1_v2 - D15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [DS1 - DS14](sizes-previous-gen.md) |160-250 | 1:1 |
| [DS1_v2 - DS15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [D_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [Ds_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [Dav4](dav4-dasv4-series.md) |230-260 * * | 2:1 |
| [Dasv4](dav4-dasv4-series.md) |230-260 * * | 2:1 |
| [Dv4](dv4-dsv4-series.md) | 195 - 210 | 2:1\*\*\* |
| [Dsv4](dv4-dsv4-series.md) | 195 - 210 | 2:1\*\*\* |
| [Ddv4](ddv4-ddsv4-series.md) | 195-210 * | 2:1\*\*\* |
| [Ddsv4](ddv4-ddsv4-series.md) | 195-210 * | 2:1\*\*\* |
| [E_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\*|
| [Es_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\* |
| [Eav4](eav4-easv4-series.md) |230-260 * * | 2:1 |
| [Easv4](eav4-easv4-series.md) | 230-260 * * | 2:1 |
| [Ev4](ev4-esv4-series.md) | 195 - 210 | 2:1\*\*\* |
| [Esv4](ev4-esv4-series.md) | 195 - 210 | 2:1\*\*\* |
| [Edv4](edv4-edsv4-series.md) | 195-210 * | 2:1\*\*\* |
| [Edsv4](edv4-edsv4-series.md) | 195-210 * | 2:1\*\*\* |
| [F2s_v2 - F72s_v2](fsv2-series.md) |195-210 * | 2:1\*\*\* |
| [F1 - F16](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [F1s - F16s](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [G1 - G5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [GS1 - GS5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [H](h-series.md) |290 – 300* | 1:1 |
| [HB](hb-series.md) |199-216 * * | 1:1 |
| [HC](hc-series.md) |297-315 * | 1:1 |
| [L4s - L32s](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [L8s_v2 - L80s_v2](lsv2-series.md) |150 - 175** | 2:1 |
| [M](m-series.md) | 160-180 | 2:1\*\*\* |
| [NVv4](nvv4-series.md) |230-260 * * | 2:1 |

Nos links abaixo, você obtém mais informações sobre os diferentes tamanhos:

- [Uso geral](sizes-general.md)
- [Otimizado para memória](sizes-memory.md)
- [Computação otimizada](sizes-compute.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Otimizado para armazenamento](sizes-storage.md)
