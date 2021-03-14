---
title: Série Av2
description: Especificações para as VMs da série Av2.
author: migerdes
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 680ffe7964104f157debc64e44ac2f004ddac86d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565505"
---
# <a name="av2-series"></a>Série Av2

As VMs da série Av2 podem ser implantadas em uma variedade de tipos de hardware e processadores. As VMs da série Av2 têm configurações de desempenho e memória de CPU mais adequadas para cargas de trabalho de nível de entrada, como desenvolvimento e teste. O tamanho é limitado para oferecer desempenho de processador consistente para a instância em execução, independentemente do hardware no qual ele está implantado. Para determinar o hardware físico no qual esse tamanho é implantado, consulte o hardware virtual de dentro da Máquina Virtual. Alguns casos de uso de exemplo incluem servidores de desenvolvimento e teste, servidores Web de tráfego baixo, bancos de dados pequenos a médios, prova de conceitos e repositórios de código.

[ACU](acu.md): 100<br>
[Armazenamento Premium](premium-storage-performance.md): sem suporte <br>
[Armazenamento em cache Premium](premium-storage-performance.md): sem suporte <br>
[Migração ao vivo](maintenance-and-updates.md): com suporte <br>
[Atualizações de preservação de memória](maintenance-and-updates.md): com suporte <br>
[Suporte à geração de VM](generation-2.md): geração 1 <br>
[Rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): sem suporte<br>
[Discos do sistema operacional efêmero](ephemeral-os-disks.md): sem suporte <br>
<br>

| Tamanho | vCore | Memória: GiB | Armazenamento temporário (SSD) GiB | Taxa de transferência máxima de armazenamento temporário: IOPS/MBps de leitura/MBps de gravação | Máximo de discos de dados/taxa de transferência: IOPS | Máximo de NICs | Largura de banda de rede esperada (Mbps)
|---|---|---|---|---|---|---|---|
| Standard_A1_v2  | 1 | 2  | 10 | 1000/20/10  | 2/2x500   | 2 | 250  |
| Standard_A2_v2  | 2 | 4  | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4_v2  | 4 | 8  | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8_v2  | 8 | 16 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |
| Standard_A2m_v2 | 2 | 16 | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4m_v2 | 4 | 32 | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8m_v2 | 8 | 64 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |

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
