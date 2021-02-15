---
title: arquivo de inclusão
description: arquivo de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/06/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0b185d545e129c941d5df2e8ce86ee684174b666
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81008311"
---
Por enquanto, somente os ultra discos e o SSDs Premium podem habilitar discos compartilhados. Tamanhos de disco diferentes podem ter um `maxShares` limite diferente, que você não pode exceder ao definir o `maxShares` valor. Para o SSDs Premium, os tamanhos de disco que dão suporte ao compartilhamento de discos são P15 e superior.

Para cada disco, você pode definir um `maxShares` valor que representa o número máximo de nós que podem compartilhar o disco simultaneamente. Por exemplo, se você planeja configurar um cluster de failover de 2 nós, defina `maxShares=2` . O valor máximo é um limite superior. Os nós podem ingressar ou sair do cluster (montar ou desmontar o disco), desde que o número de nós seja menor do que o `maxShares` valor especificado.

> [!NOTE]
> O `maxShares` valor só pode ser definido ou editado quando o disco é desanexado de todos os nós.

### <a name="premium-ssd-ranges"></a>Intervalos de SSD Premium

A tabela a seguir ilustra os valores máximos permitidos para os `maxShares` tamanhos de disco Premium:

|Tamanhos do disco  |limite de maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Os limites de IOPS e largura de banda de um disco não são afetados pelo `maxShares` valor. Por exemplo, o IOPS máximo de um disco P15 é 1100 se maxShares = 1 ou maxShares > 1.

### <a name="ultra-disk-ranges"></a>Intervalos de ultra Disk

O `maxShares` valor mínimo é 1, enquanto o `maxShares` valor máximo é 5. Não há restrições de tamanho em ultra discos, qualquer tamanho de disco pode usar qualquer valor para `maxShares` , até e incluindo o valor máximo.