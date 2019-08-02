---
title: Colocalizar VMs do Azure do Linux | Microsoft Docs
description: Saiba como a colocalização de recursos de VM do Azure pode melhorar a latência.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: cynthn
ms.openlocfilehash: d6d8986117bd2899ea0de0aa6490954aef6c3a56
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850334"
---
# <a name="co-locate-resources-for-improved-latency"></a>Colocalizar recursos para maior latência

Ao implantar seu aplicativo no Azure, a difusão de instâncias entre regiões ou zonas de disponibilidade cria a latência de rede, o que pode afetar o desempenho geral do seu aplicativo. 

## <a name="preview-proximity-placement-groups"></a>Visualização: Grupos de posicionamento de proximidade

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Próximas etapas

Implante uma VM em um [grupo de posicionamento de proximidade](proximity-placement-groups.md) usando o CLI do Azure.

