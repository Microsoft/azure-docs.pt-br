---
title: Tamanhos de máquina virtual do Azure para FPGA (matrizes de porta programável por campo)
description: Lista os diferentes tamanhos otimizados para FPGA disponíveis para máquinas virtuais no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho nessa série.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-fpga
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: vikancha
ms.openlocfilehash: d9eb0d5bc93cbe9c2a7cbae56c336115bb227b04
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557668"
---
# <a name="fpga-optimized-virtual-machine-sizes"></a>Tamanhos de máquina virtual FPGA otimizados

Os tamanhos de VM otimizados para FPGA são máquinas virtuais especializadas disponíveis com um ou vários FPGAs. Esses tamanhos são projetados para cargas de trabalho de computação intensiva. Este artigo fornece informações sobre o número e tipo de FPGAs, vCPUs, discos de dados e NICs. A taxa de transferência de armazenamento e a largura de banda de rede também são incluídos para cada tamanho neste agrupamento.

- Os tamanhos da [série NP](np-series.md) são otimizados para cargas de trabalho, incluindo inferência de aprendizado de máquina, transcodificação de vídeo e pesquisa de banco de dados & análise. A série NP é alimentada por Xilinx U250 Accelerators.


## <a name="deployment-considerations"></a>Considerações de implantação

- Para ver a disponibilidade das VMs da Série N, veja [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

- As VMs da Série N só podem ser implantadas no modelo de implantação do Resource Manager.

- Se você quiser implantar mais do que algumas VMs da Série N, considere uma assinatura pré-paga ou outras opções de compra. Se estiver usando uma [conta gratuita do Azure](https://azure.microsoft.com/free/), você poderá usar apenas um número limitado de núcleos de computação do Azure.

- Talvez seja necessário aumentar a cota de núcleos (por região) em sua assinatura do Azure e aumentar a cota separada para os núcleos de NP. Para solicitar um aumento de cota, [abra uma solicitação de atendimento ao cliente online](../azure-portal/supportability/how-to-create-azure-support-request.md) gratuitamente. Os limites padrão podem variar dependendo de sua categoria de assinatura.

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Computação otimizada](sizes-compute.md)
- [Computação acelerada por GPU](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
