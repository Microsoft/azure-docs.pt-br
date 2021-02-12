---
title: Intermitência de disco gerenciado
description: Saiba mais sobre a intermitência de disco para discos do Azure e máquinas virtuais do Azure.
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: dcdbf94e547581cb9ff885ac5896467abdf316ae
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576186"
---
# <a name="managed-disk-bursting"></a>Intermitência de disco gerenciado
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Intermitência no nível da máquina virtual
A intermitência de nível de VM está habilitada na série de VMs a seguir em todas as regiões com suporte no:
- [Lsv2-series](lsv2-series.md)
- [Dsv3-series](dv3-dsv3-series.md)
- [Série Esv3](ev3-esv3-series.md)

A intermitência é habilitada por padrão para máquinas virtuais que dão suporte a ela.

## <a name="disk-level-bursting"></a>Intermitência no nível de disco
A intermitência também está disponível em nossos [SSDs permium](disks-types.md#premium-ssd) para tamanhos de disco P20 e menores em todas as regiões na nuvem pública do Azure, na nuvem do Azure Governamental e na nuvem do Azure China. A intermitência de disco é habilitada por padrão em todas as implantações novas e existentes dos tamanhos de disco que dão suporte a ela. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]
