---
title: Tamanhos de VM
description: Lista os tamanhos diferentes disponíveis de máquinas virtuais no Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 07/21/2020
ms.author: jushiman
ms.openlocfilehash: d9377ba22f1461762e53b1004dfe5f06c2d7b972
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89420210"
---
# <a name="sizes-for-virtual-machines-in-azure"></a>Tamanhos das máquinas virtuais no Azure

Este artigo descreve os tamanhos e as opções disponíveis para as máquinas virtuais do Azure que você pode usar para executar seus aplicativos e cargas de trabalho. Ele também fornece considerações de implantação a serem observadas ao planejar o uso desses recursos. 

| Tipo | Tamanhos | Descrição |
|------|-------|-------------|
| [Propósito geral](sizes-general.md)   | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2, DV4, Dsv4, Ddv4, Ddsv4  | Relação equilibrada de CPU/memória. Ideal para teste e desenvolvimento, bancos de dados pequenos a médios e servidores Web de tráfego baixo a médio. |
| [Computação otimizada](sizes-compute.md) | F, FS, Fsv2 | Alta relação de CPU/memória. Boa para servidores web de tráfego médio, dispositivos de rede, processos de lote e servidores de aplicativo. |
| [Memória otimizada](sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Ev4, Esv4, Edv4, Edsv4, Mv2, M, DSv2, Dv2 | Alta relação de memória/CPU. Ótima para servidores de banco de dados relacionais, caches médios a grandes e análises na memória.                 |
| [Armazenamento otimizado](sizes-storage.md) | Lsv2 | Taxa de transferência de disco alta e de E/S são ideais para bancos de dados Big Data, SQL, NoSQL, armazenamento de dados e grandes dados transacionais.  |
| [GPU](sizes-gpu.md) | NC, NCv2, NCv3, NCasT4_v3 (visualização), ND, NDv2 (versão prévia), NV, NVv3, NVv4 | Máquinas virtuais especializadas direcionadas para edição de vídeo e renderização gráfica pesada, assim como inferência e treinamento do modelo (ND) com aprendizado profundo. Disponível com uma ou várias GPUs. |
| [Computação de alto desempenho](sizes-hpc.md) | HB, HBv2, HC, H | Nossas máquinas virtuais de CPU mais rápidas e potentes com adaptadores de rede de alta taxa de transferência (RDMA) opcionais. |

- Para obter informações sobre os preços de vários tamanhos, consulte as páginas de preços para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux) ou [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/Windows/#Windows).
- Para ver a disponibilidade de tamanhos de VM nas regiões do Azure, confira [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/).
- Para ver os limites gerais em VMs do Azure, consulte [Limites de assinatura e serviços do Azure, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Para obter mais informações sobre como o Azure nomeia suas VMs, consulte [convenções de nomenclatura de tamanhos de máquina virtual do Azure](./vm-naming-conventions.md).

## <a name="rest-api"></a>API REST

Para obter informações sobre como usar a API REST para consulta de tamanhos de VM, confira o seguinte:

- [Listar os tamanhos de máquina virtual disponíveis para redimensionamento](/rest/api/compute/virtualmachines/listavailablesizes)
- [Listar os tamanhos de máquina virtual disponíveis para uma assinatura](/rest/api/compute/resourceskus/list)
- [Listar os tamanhos de máquina virtual disponíveis em um conjunto de disponibilidade](/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.

## <a name="benchmark-scores"></a>Pontuações de parâmetro de comparação

Saiba mais sobre desempenho de computação para VMs do Linux usando as [pontuações de parâmetro de comparação CoreMark](./linux/compute-benchmark-scores.md).

Saiba mais sobre o desempenho de computação para VMs do Windows usando as pontuações de parâmetro de [comparação SPECInt](./windows/compute-benchmark-scores.md).

## <a name="manage-costs"></a>Gerenciar os custos

[!INCLUDE [cost-management-horizontal](../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os diferentes tamanhos de VM que estão disponíveis:

- [Propósito geral](sizes-general.md)
- [Computação otimizada](sizes-compute.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- Verifique a página [Geração anterior](sizes-previous-gen.md) para Standard A, Dv1 (D1-4 e D11-14 v1) e série A8-A11
