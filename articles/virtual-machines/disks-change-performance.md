---
title: Níveis de desempenho para Azure Managed disks
description: Saiba mais sobre as camadas de desempenho para discos gerenciados.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/19/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 28980756ac9e41c9477d687ea9df608b512759e3
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94986769"
---
# <a name="performance-tiers-for-managed-disks"></a>Níveis de desempenho para discos gerenciados

O desempenho do seu disco gerenciado do Azure é definido quando você cria o disco, na forma de seu nível de desempenho. O nível de desempenho determina a IOPS e a taxa de transferência que o disco gerenciado tem. Quando você define o tamanho provisionado do disco, um nível de desempenho é selecionado automaticamente. O nível de desempenho pode ser alterado na implantação ou posteriormente, sem alterar o tamanho do disco.

Alterar o nível de desempenho permite que você se prepare e atenda a uma demanda maior sem usar o recurso de intermitência do disco. Pode ser mais econômico alterar o nível de desempenho em vez de se basear na intermitência, dependendo de quanto tempo o desempenho adicional é necessário. Isso é ideal para eventos que exigem temporariamente um nível de desempenho consistentemente mais alto, como compras de feriados, testes de desempenho ou execução de um ambiente de treinamento. Para lidar com esses eventos, você pode usar um nível de desempenho mais alto pelo tempo necessário. Em seguida, você pode retornar à camada original quando não precisar mais do desempenho adicional.

## <a name="how-it-works"></a>Como ele funciona

Quando você implanta ou provisiona um disco pela primeira vez, o nível de desempenho de linha de base desse disco é definido com base no tamanho do disco provisionado. Você pode usar um nível de desempenho maior do que a linha de base original para atender à demanda mais alta. Quando você não precisar mais desse nível de desempenho, poderá retornar para o nível de desempenho de linha de base inicial.

Sua cobrança muda à medida que sua camada de desempenho muda. Por exemplo, se você provisionar um disco P10 (128 GiB), o nível de desempenho de linha de base será definido como P10 (500 IOPS e 100 MBps). Você será cobrado na taxa de P10. Você pode atualizar a camada para corresponder ao desempenho de p50 (7.500 IOPS e 250 MBps) sem aumentar o tamanho do disco. Durante o tempo da atualização, você será cobrado na taxa de P50. Quando você não precisar mais do melhor desempenho, poderá retornar para a camada P10. O disco será novamente cobrado na taxa de P10.

| Tamanho do disco | Camada de desempenho de linha de base | Pode ser atualizado para |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32 GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64 GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128 GiB | P10 | P15, P20, P30, P40, P50 |
| 256 GiB | P15 | P20, P30, P40, P50 |
| 512 GiB | P20 | P30, P40, P50 |
| 1 TiB | P30 | P40, P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | Nenhum |
| 8 TiB | P60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | Nenhum |

Para obter informações de cobrança, consulte [preços de disco gerenciado](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="next-steps"></a>Próximas etapas

Para saber como alterar o nível de desempenho, consulte os artigos sobre o [portal](disks-performance-tiers-portal.md) ou [PowerShell/CLI](disks-performance-tiers.md) .

