---
title: Alterar o desempenho dos Azure Managed disks usando o portal do Azure
description: Saiba como alterar as camadas de desempenho para discos gerenciados novos e existentes usando o portal do Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/05/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 625fb1e3dd0b433da6b60f995aa6b380c23ec9ce
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901004"
---
# <a name="change-your-performance-tier-using-the-azure-portal"></a>Alterar o nível de desempenho usando o portal do Azure

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="getting-started"></a>Introdução

### <a name="new-disks"></a>Novos discos

As etapas a seguir mostram como alterar a camada de desempenho do disco quando você cria o disco pela primeira vez:

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Navegue até a VM para a qual você gostaria de criar um novo disco.
1. Ao selecionar o novo disco, primeiro escolha o tamanho do disco que você precisa.
1. Depois de selecionar um tamanho, selecione um nível de desempenho diferente para alterar seu desempenho.
1. Selecione **OK** para criar o disco.

:::image type="content" source="media/disks-performance-tiers-portal/new-disk-change-performance-tier.png" alt-text="Captura de tela da folha de criação de disco, um disco é realçado e a lista suspensa camada de desempenho é realçada." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::


## <a name="existing-disks"></a>Discos existentes

As etapas a seguir descrevem como alterar o nível de desempenho de um disco existente:

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Navegue até a VM que contém o disco que você gostaria de alterar.
1. Desaloque a VM ou desanexe o disco.
1. Selecione seu disco
1. Selecione **tamanho + desempenho**.
1. Na lista suspensa **nível de desempenho** , selecione uma camada diferente da camada de desempenho atual do disco.
1. Selecione **Redimensionar**.

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="Captura de tela da folha tamanho + desempenho, nível de desempenho realçado." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

## <a name="next-steps"></a>Próximas etapas

Se você precisar redimensionar um disco para tirar proveito dos níveis de desempenho mais alto, consulte estes artigos:

- [Expandir discos rígidos virtuais em uma VM do Linux com a CLI do Azure](linux/expand-disks.md)
- [Expandir um disco gerenciado conectado a uma máquina virtual do Windows](windows/expand-os-disk.md)
