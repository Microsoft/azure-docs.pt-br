---
title: Séries Dav4 e Dasv4
description: Especificações para as VMs das séries Dav4 e Dasv4.
author: migerdes
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: d68e5b7406b2cfa32b06f4731180684bae0c4334
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554098"
---
# <a name="dav4-and-dasv4-series"></a>Séries Dav4 e Dasv4

As séries Dav4 e Dasv4 são novos tamanhos que utilizam o processador 2,35 GHz EPYC<sup>TM</sup> 7452 da AMD em uma configuração multithread com até 256 MB de cache L3, que dedica 8 MB desse cache L3 a cada 8 núcleos, aumentando as opções do cliente para executar as cargas de trabalho de uso geral. As séries Dav4 e Dasv4 têm as mesmas configurações de memória e disco que as séries Dsv3 e D.

## <a name="dav4-series"></a>Dav4-series

[ACU](acu.md): 230-260<br>
[Armazenamento Premium](premium-storage-performance.md): sem suporte<br>
[Cache de Armazenamento Premium](premium-storage-performance.md): sem suporte<br>
[Migração ao Vivo](maintenance-and-updates.md): com suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): com suporte<br>
[Suporte à geração de VM](generation-2.md): geração 1<br>
[Rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): com suporte (*requer um mínimo de 4 vCPUs*)<br>
[Discos de SO efêmero](ephemeral-os-disks.md): com suporte <br>
<br>

Os tamanhos da série Dav4 são baseados no processador AMD EPYC<sup>TM</sup> 7452 de 2,35 GHz, que pode alcançar uma frequência máxima aumentada de 3,35 GHz. Os tamanhos da série Dav4 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção. O armazenamento do disco de dados é faturado separadamente das máquinas virtuais. Para usar o SSD Premium, use os tamanhos da Dasv4. Os medidores de cobrança e preço para os tamanhos da Dasv4 são os mesmos que os da série Dav4.

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps de leitura / MBps de gravação | Máximo de NICs | Largura de banda de rede esperada (Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000 / 46 / 23   | 2 | 800 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000 / 93 / 46   | 2 | 1600 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000 / 187 / 93 | 4 | 3200 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000 / 375 / 187 |8 | 6400 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000 / 750 / 375 |8 | 12.800 |
| Standard_D48a_v4| 48 | 192| 1200 | 32 | 96.000 / 1.000 / 500 | 8 | 19200 |
| Standard_D64a_v4| 64 | 256 | 1600 | 32 | 96.000 / 1.000 / 500 | 8 | 25.600 |
| Standard_D96a_v4| 96 | 384 | 2400 | 32 | 96.000 / 1.000 / 500 | 8 | 32000 |

## <a name="dasv4-series"></a>Série Dasv4

[ACU](acu.md): 230-260<br>
[Armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Cache de Armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Migração ao Vivo](maintenance-and-updates.md): com suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): com suporte<br>
[Suporte à geração de VM](generation-2.md): geração 1 e 2<br>
[Rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): com suporte (*requer um mínimo de 4 vCPUs*)<br>
[Discos de SO efêmero](ephemeral-os-disks.md): com suporte <br>
<br>

Os tamanhos da série Dasv4 são baseados no processador AMD EPYC<sup>TM</sup> 7452 de 2,35 GHz, que pode alcançar uma frequência máxima aumentada de 3,35 GHz e usa SSD Premium. Os tamanhos da série Dasv4 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção.

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo de NICs | Largura de banda de rede esperada (Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4\.000 / 32 (50)|3\.200 / 48|2 | 800 |
| Standard_D4as_v4|4|16|32|8|8\.000 / 64 (100)|6\.400 / 96|2 | 1600 |
| Standard_D8as_v4|8|32|64|16|16.000 / 128 (200)|12.800 / 192|4 | 3200 |
| Standard_D16as_v4|16|64|128|32|32.000 / 255 (400)|25.600 / 384|8 | 6400 |
| Standard_D32as_v4|32|128|256|32|64.000 / 510 (800)|51.200 / 768|8 | 12.800 |
| Standard_D48as_v4|48|192|384|32|96.000 / 1.020 (1200)|76.800 / 1.148|8 | 19200 |
| Standard_D64as_v4|64|256|512|32|128.000 / 1.020 (1600)|80.000 / 1200|8 | 25.600 | 
| Standard_D96as_v4|96|384|768|32|192.000 / 1.020 (2400)|80.000 / 1200|8 | 32000 |

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
