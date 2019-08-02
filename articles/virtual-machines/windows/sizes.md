---
title: Tamanhos das VMs do Windows no Azure | Microsoft Docs
description: Lista os tamanhos diferentes disponíveis de máquinas virtuais do Windows no Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/02/2019
ms.author: jonbeck
ms.openlocfilehash: 78b0de43652aba20563e2712e249e18ccd18c407
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710076"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Tamanhos das máquinas virtuais do Windows no Azure

Este artigo descreve os tamanhos e as opções disponíveis de máquinas virtuais do Azure que você pode usar para executar seus aplicativos Windows e cargas de trabalho. Ele também fornece considerações de implantação a serem observadas ao planejar o uso desses recursos.  Este artigo também está disponível para [máquinas virtuais Linux](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


| Tipo                     | Tamanhos           |    DESCRIÇÃO       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Propósito geral](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, Av2, DC  | Relação equilibrada de CPU/memória. Ideal para teste e desenvolvimento, bancos de dados pequenos a médios e servidores Web de tráfego baixo a médio. |
| [Computação otimizada](sizes-compute.md)        | Fsv2           | Alta relação de CPU/memória. Boa para servidores web de tráfego médio, dispositivos de rede, processos de lote e servidores de aplicativo.        |
| [Memória otimizada](sizes-memory.md)         | Esv3, Ev3, Mv2, M, DSv2, Dv2  | Alta relação de memória/CPU. Ótima para servidores de banco de dados relacionais, caches médios a grandes e análises na memória.                 |
| [Armazenamento otimizado](sizes-storage.md)        | Lsv2                | Taxa de transferência de disco alta e de E/S são ideais para bancos de dados Big Data, SQL, NoSQL, armazenamento de dados e grandes dados transacionais.  |
| [GPU](sizes-gpu.md)            | NC, NCv2, NCv3, ND, NDv2 (Preview), NV, NVv3 (Preview) | Máquinas virtuais especializadas direcionadas para edição de vídeo e renderização gráfica pesada, assim como inferência e treinamento do modelo (ND) com aprendizado profundo. Disponível com uma ou várias GPUs.       |
| [Computação de alto desempenho](sizes-hpc.md) | HB, HC,  H | Nossas máquinas virtuais de CPU mais rápidas e potentes com adaptadores de rede de alta taxa de transferência (RDMA) opcionais. |


<br> 

- Para obter informações sobre os preços dos vários tamanhos, consulte [Preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). 
- Para ver os limites gerais em VMs do Azure, consulte [Limites de assinatura e serviços do Azure, cotas e restrições](../../azure-subscription-service-limits.md).
- Os custos de armazenamento são calculados separadamente com base nas páginas usadas na conta de armazenamento. Para obter detalhes, [Preço de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).
- Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.


## <a name="rest-api"></a>API REST

Para obter informações sobre como usar a API REST para consulta de tamanhos de VM, confira o seguinte:

- [Listar os tamanhos de máquina virtual disponíveis para redimensionamento](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Listar os tamanhos de máquina virtual disponíveis para uma assinatura](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Listar os tamanhos de máquina virtual disponíveis em um conjunto de disponibilidade](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.

## <a name="benchmark-scores"></a>Pontuações de parâmetro de comparação

Saiba mais sobre desempenho de computação para VMs do Windows usando as [pontuações de parâmetro de comparação CoreMark](compute-benchmark-scores.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os diferentes tamanhos de VM que estão disponíveis:
- [Propósito geral](sizes-general.md)
- [Computação otimizada](sizes-compute.md)
- [Memória otimizada](../virtual-machines-windows-sizes-memory.md)
- [Armazenamento otimizado](../virtual-machines-windows-sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- Verifique a página [Geração anterior](sizes-previous-gen.md) para Standard A, Dv1 (D1-4 e D11-14 v1) e série A8-A11




