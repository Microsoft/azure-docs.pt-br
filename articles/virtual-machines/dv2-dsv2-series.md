---
title: Dv2 e série DSv2-máquinas virtuais do Azure
description: Especificações para as VMs da série Dv2 e Dsv2.
author: joelpelley
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: a3dee22a1ee793641e48be6edb1b33a00219217e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560303"
---
# <a name="dv2-and-dsv2-series"></a>Séries Dv2 e DSv2

A Dv2 e a série DSv2, um acompanhamento da série D original, apresentam uma CPU mais potente e configuração ideal de CPU para memória, tornando-as adequadas para a maioria das cargas de trabalho de produção. A série Dv2 é aproximadamente 35% mais rápida do que a série D. Dv2-série de execução no Intel® Xeon® Platinum 8272CL (Cascadey Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou os processadores Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) com a tecnologia Intel Turbo Boost 2,0. A série Dv2 tem as mesmas configurações de memória e disco que a série D.

## <a name="dv2-series"></a>Série Dv2

Os tamanhos da série Dv2 são executados no Intel® Xeon® Platinum 8272CL (Cascadey Lake), no Intel® Xeon® 8171M 2.1 GHz (Skylake) ou nos processadores Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) com a tecnologia Intel Turbo Boost 2,0.

[ACU](acu.md): 210-250<br>
[Armazenamento Premium](premium-storage-performance.md): sem suporte<br>
[Armazenamento em cache Premium](premium-storage-performance.md): sem suporte<br>
[Migração ao vivo](maintenance-and-updates.md): com suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): com suporte<br>
[Suporte à geração de VM](generation-2.md): geração 1<br>
[Rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): com suporte (*requer um mínimo de 4 vCPU*)<br>
[Discos do sistema operacional efêmero](ephemeral-os-disks.md): sem suporte <br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Taxa de transferência máxima de armazenamento temporário: IOPS/MBps de leitura/MBps de gravação | Discos de dados máximos | Taxa de transferência: IOPS | Máximo de NICs | Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_D1_v2 | 1  | 3,5 | 50  | 3000/46/23    | 4  | 4x500  | 2|750   |
| Standard_D2_v2 | 2  | 7   | 100 | 6000/93/46    | 8  | 8 x 500  | 2|1500  |
| Standard_D3_v2 | 4  | 14  | 200 | 12000/187/93  | 16 | 16 x 500 | 4|3000  |
| Standard_D4_v2 | 8  | 28  | 400 | 24000/375/187 | 32 | 32 x 500 | 8|6000  |
| Standard_D5_v2 | 16 | 56  | 800 | 48000/750/375 | 64 | 64x500 | 8|12000 |

## <a name="dsv2-series"></a>Série DSv2

Os tamanhos da série DSv2 são executados no Intel® Xeon® Platinum 8272CL (Cascade, Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake) ou os processadores Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) com a tecnologia Intel Turbo Boost 2,0 e usam o armazenamento Premium.

[ACU](acu.md): 210-250<br>
[Armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Cache de armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Migração ao vivo](maintenance-and-updates.md): com suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): com suporte<br>
[Suporte à geração de VM](generation-2.md): geração 1 e 2<br>
[Rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): com suporte (*requer um mínimo de 4 vCPU*)<br>
[Discos do sistema operacional efêmero](ephemeral-os-disks.md): com suporte <br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento em cache e temporária: IOPS/MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs|Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_DS1_v2 | 1  | 3,5 | 7   | 4  | 4000/32 (43)    | 3200/48   | 2|750   |
| Standard_DS2_v2 | 2  | 7   | 14  | 8  | 8000/64 (86)    | 6400/96   | 2|1500  |
| Standard_DS3_v2 | 4  | 14  | 28  | 16 | 16000/128 (172) | 12800/192 | 4|3000  |
| Standard_DS4_v2 | 8  | 28  | 56  | 32 | 32000/256 (344) | 25600/384 | 8|6000  |
| Standard_DS5_v2 | 16 | 56  | 112 | 64 | 64000/512 (688) | 51200/768 | 8|12000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

Calculadora de preços: [calculadora de preços](https://azure.microsoft.com/pricing/calculator/)

Mais informações sobre tipos de discos: [tipos de disco](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
