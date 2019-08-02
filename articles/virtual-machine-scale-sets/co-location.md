---
title: Co-localizar conjuntos de dimensionamento de máquinas virtuais do Azure | Microsoft Docs
description: Saiba como a colocalização de recursos do conjunto de dimensionamento de máquinas virtuais do Azure pode melhorar o desempenho.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/14/2019
ms.author: cynthn
ms.openlocfilehash: 555c4da18169ae9fd1e917fd0b8e3c4e98e87178
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850529"
---
# <a name="co-location"></a>Colocalização

Um dos maiores colaboradores da latência entre as VMs é simplesmente a distância.

## <a name="preview-proximity-placement-groups"></a>Visualização: Grupos de posicionamento de proximidade 

[!INCLUDE [virtual-machines-common-ppg-overview](../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Próximas etapas

Crie um [grupo de posicionamento de proximidade](proximity-placement-groups.md) para seu conjunto de dimensionamento.

