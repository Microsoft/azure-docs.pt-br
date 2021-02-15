---
title: Desempenho de máquina virtual e disco
description: Saiba mais sobre como as máquinas virtuais e seus discos anexados funcionam em conjunto para o desempenho.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 5e9f6ecc733eccf317e3013752ee2f5b0586ea78
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540487"
---
# <a name="virtual-machine-and-disk-performance"></a>Desempenho de máquina virtual e disco
[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Limites em cache de máquina virtual desarmazenado versus
As máquinas virtuais habilitadas para armazenamento Premium e cache de armazenamento Premium têm dois limites de largura de banda de armazenamento diferentes. Vamos examinar a máquina virtual Standard_D8s_v3 como um exemplo. Aqui está a documentação sobre a [série Dsv3](dv3-dsv3-series.md) e o Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-2.md)]

Vamos executar um teste de benchmark nessa máquina virtual e na combinação de disco que cria a atividade de e/s. Para saber como avaliar o parâmetro de e/s de armazenamento no Azure, confira [avaliar seu aplicativo no armazenamento em disco do Azure](disks-benchmarks.md). Na ferramenta de benchmarking, você pode ver que a combinação de VM e disco pode atingir 22.800 IOPS:

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-3.md)]
