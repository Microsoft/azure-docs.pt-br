---
title: Série Eav4 e série Easv4
description: Especificações para as VMs da série Eav4 e Easv4.
author: migerdes
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: ec320d7eeebbe5eab14061cd5c194f15acf5638a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557923"
---
# <a name="eav4-and-easv4-series"></a>Séries Eav4 e Easv4

A série Eav4 e a Easv4 utilizam o processador de 2.35 GHz EPYC<sup>TM</sup> 7452 em uma configuração multi-threaded com até 256 MB de cache L3, aumentando as opções para executar a maioria das cargas de trabalho com otimização de memória. As séries Eav4 e Easv4 têm as mesmas configurações de memória e disco que as séries Ev3 e Esv3.

## <a name="eav4-series"></a>Série Eav4

[ACU](acu.md): 230-260<br>
[Armazenamento Premium](premium-storage-performance.md): sem suporte<br>
[Armazenamento em cache Premium](premium-storage-performance.md): sem suporte<br>
[Migração ao vivo](maintenance-and-updates.md): com suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): com suporte<br>
[Suporte à geração de VM](generation-2.md): gerações 1 e 2<br>
[Rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): com suporte (*requer um mínimo de 4 vCPU*)<br>
[Discos do sistema operacional efêmero](ephemeral-os-disks.md): com suporte <br>
<br>

Os tamanhos da série Eav4 são baseados no processador AMD EPYC<sup>TM</sup> 7452 de 2.35 GHz que pode alcançar uma frequência máxima aumentada de 3.35 GHz. Os tamanhos da série Eav4 são ideais para aplicativos empresariais com uso intensivo de memória. O armazenamento do disco de dados é faturado separadamente das máquinas virtuais. Para usar o SSD Premium, use os tamanhos da série Easv4. Os medidores de cobrança e preço para os tamanhos de Easv4 são os mesmos que os da série Eav3.

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps de leitura / MBps de gravação | Máximo de NICs | Largura de banda de rede esperada (Mbps) |
| -----|-----|-----|-----|-----|-----|-----|-----|
| \_E2a \_ v4 padrão|2|16|50|4|3000 / 46 / 23|2 | 800 |
| \_E4a \_ v4 padrão|4|32|100|8|6000 / 93 / 46|2 | 1600 |
| \_E8a \_ v4 padrão|8|64|200|16|12000 / 187 / 93|4 | 3200 |
| \_E16a \_ v4 padrão|16|128|400|32|24000 / 375 / 187|8 | 6400 |
| \_E20a \_ v4 padrão|20|160|500|32|30000/468/234|8 | 8000 |
| \_E32a \_ v4 padrão|32|256|800|32|48000 / 750 / 375|8 | 12800 |
| \_E48a \_ v4 padrão|48|384|1200|32|96000/1000 (500)|8 | 19200 |
| \_E64a \_ v4 padrão|64|512|1600|32|96000/1000 (500)|8 | 25600 |
| \_E96a \_ v4 padrão|96|672|2400|32|96000/1000 (500)|8 | 32000 |

## <a name="easv4-series"></a>Série Easv4

[ACU](acu.md): 230-260<br>
[Armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Cache de armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Migração ao vivo](maintenance-and-updates.md): com suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): com suporte<br>
[Suporte à geração de VM](generation-2.md): gerações 1 e 2<br>
[Rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): com suporte (*requer um mínimo de 4 vCPU*)<br>
[Discos do sistema operacional efêmero](ephemeral-os-disks.md): com suporte <br>
<br>

Os tamanhos da série Easv4 são baseados no processador AMD EPYC<sup>TM</sup> 7452 de 2.35 GHz que pode alcançar uma frequência máxima aumentada de 3.35 GHz e usar SSD Premium. Os tamanhos da série Easv4 são ideais para aplicativos empresariais com uso intensivo de memória.

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo de NICs | Largura de banda de rede esperada (Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000/32 (50)|3200/48|2 | 800 |
| Standard_E4as_v4|4|32|64|8|8000/64 (100)|6400/96|2 | 1600 |
| Standard_E8as_v4|8|64|128|16|16000/128 (200)|12800/192|4 | 3200 |
| Standard_E16as_v4|16|128|256|32|32000/255 (400)|25600/384|8 | 6400 |
| Standard_E20as_v4|20|160|320|32|40000/320 (500)|32000/480|8 | 8000 |
| Standard_E32as_v4|32|256|512|32|64000/510 (800)|51200/768|8 | 12800 |
| Standard_E48as_v4|48|384|768|32|96000/1020 (1200)|76800/1148|8 | 19200 |
| Standard_E64as_v4|64|512|1024|32|128000/1020 (1600)|80000/1200|8 | 25600 |
| Standard_E96as_v4 <sup>1</sup>|96|672|1344|32|192000/1020 (2400)|80000/1200|8 | 32000 |

<sup>1</sup> [tamanhos de núcleos restritos disponíveis](./constrained-vcpu.md).

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
