---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: d610193783d44b86d48cd5ceaa91377b32b7061b
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99580244"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Definições da tabela de tamanhos

- A capacidade de armazenamento é mostrada em unidades de GiB ou de 1024^3 bytes. Quando você compara os discos medidos em GB (1000 ^ 3 bytes) para os discos medidos em GiB (1024 ^ 3), lembre-se de que os números de capacidade fornecidos em GiB podem parecer menores. Por exemplo, 1023 GiB = 1098,4 GB.
- A taxa de transferência do disco é medida em IOPS (operações de entrada/saída por segundo) e em MBps, em que MBps = 10^6 bytes/s.
- Os discos de dados podem operar nos modos em cache ou não armazenado em cache. Para a operação do disco de dados armazenados em cache, o modo de cache do host é definido como **ReadOnly** ou **ReadWrite**.  Para as operação do disco de dados não armazenados em cache, o modo de cache do host é definido como **Nenhum**.
- Para saber como obter o melhor desempenho de armazenamento para suas VMs, consulte [desempenho de disco e máquina virtual](../articles/virtual-machines/disks-performance.md).
- **Largura de banda de rede esperada** é a largura de banda agregada máxima alocada por tipo de VM em todas as NICs para todos os destinos. Para obter mais informações, consulte [largura de banda de rede da máquina virtual](../articles/virtual-network/virtual-machine-network-throughput.md).

  Os limites superiores não são garantidos. Os limites oferecem orientação para selecionar o tipo de VM correto para o aplicativo pretendido. O desempenho real da rede dependerá de vários fatores, incluindo o congestionamento da rede, cargas de aplicativos e configurações de rede. Para obter informações sobre como otimizar a taxa de transferência de rede, consulte [otimizar a taxa de transferência de rede para máquinas virtuais do Azure](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Para obter o desempenho de rede esperado no Linux ou no Windows, talvez seja necessário selecionar uma versão específica ou otimizar sua VM. Para obter mais informações, consulte [NTTTCP (largura de banda/teste de taxa de transferência)](../articles/virtual-network/virtual-network-bandwidth-testing.md).



