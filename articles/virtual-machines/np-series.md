---
title: Série NP-máquinas virtuais do Azure
description: Especificações para as VMs da série NP.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: 2606881cec5527084c3c2788d40f8bdfd8abf5a3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416938"
---
# <a name="np-series-preview"></a>Série NP (versão prévia)

As máquinas virtuais da série NP são alimentadas por [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) FPGAs para acelerar cargas de trabalho, incluindo inferência de aprendizado de máquina, transcodificação de vídeo e pesquisa de & de banco de dados. As VMs da série NP também são alimentadas por CPUs do Intel Xeon 8171M (Skylake) com toda a velocidade do clock de Turbo principal de 3,2 GHz.


[Armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Cache de armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Migração ao vivo](maintenance-and-updates.md): sem suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): sem suporte<br>
Suporte à geração de VM: geração 1<br>
[Rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): com suporte<br>
[Discos do sistema operacional efêmero](ephemeral-os-disks.md): sem suporte <br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | FPGA | Memória FPGA: GiB | Discos de dados máximos | Máximo de NICs/largura de banda de rede esperada (MBps) | 
|---|---|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operacionais e drivers com suporte
Visite [notas de versão do Xilinx Runtime (XRT)](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf) para obter a lista completa de sistemas operacionais com suporte.

Durante o programa de visualização Microsoft Azure as equipes de engenharia compartilharão instruções específicas para a instalação do driver.

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
