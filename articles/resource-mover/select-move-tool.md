---
title: Escolha uma ferramenta para mover recursos do Azure entre regiões
description: Examine as opções e ferramentas para mover recursos do Azure entre regiões
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 1b233028b52175842c73660ff116ac592d8296e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90603349"
---
# <a name="choose-a-tool-for-moving-azure-resources"></a>Escolha uma ferramenta para mover recursos do Azure

Você pode mover recursos no Azure da seguinte maneira:

- **Mover recursos entre regiões**: mova o recurso de dentro do hub do Resource Mover ou de um grupo de recursos. 
- **Mover recursos entre os grupos de recursos/as assinaturas**: mova de dentro de um grupo de recursos. 
- **Mover recursos entre as nuvens do Azure**: use o serviço Azure Site Recovery para mover recursos entre nuvens públicas e governamentais.
- **Mover recursos entre zonas de disponibilidade na mesma região**: use o serviço Azure Site Recovery para mover recursos entre zonas de disponibilidade na mesma região do Azure.


## <a name="compare-move-tools"></a>Comparar ferramentas de movimentação

**Ferramenta** | **Quando usar** | **Saiba mais** 
--- | --- | ---
**Mover dentro do grupo de recursos** | Mover recursos para um grupo de recursos/uma assinatura diferente ou entre regiões.<br/><br/> Se você fizer a movimentação entre regiões, no grupo de recursos, selecione os recursos que deseja mover e, em seguida, mova-os para o hub do Resource Mover a fim de verificar as dependências e mova os recursos para a região de destino. | [Mover recursos para outro grupo de recursos/outra assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md).<br/><br/> [Mover recursos para outra região de um grupo de recursos](move-region-within-resource-group.md).
**Mover recursos do hub do Resource Mover** | Mova recursos entre regiões. <br/><br/> Você pode fazer a movimentação para uma região de destino, para uma zona de disponibilidade específica ou para um conjunto de disponibilidade dentro da região de destino. | [Mover recursos entre regiões no hub do Resource Mover]().
**Mover as VMs com o Site Recovery** | Use essa opção para mover VMs do Azure entre nuvens governamentais e públicas.<br/><br/> Use essa opção se desejar mover VMs entre zonas de disponibilidade na mesma região. |[Mover recursos entre nuvens governamentais/públicas](../site-recovery/region-move-cross-geos.md), [Mover recursos para zonas de disponibilidade na mesma região](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

## <a name="next-steps"></a>Próximas etapas

[Saiba mais](about-move-process.md) sobre os componentes do Resource Mover e sobre o processo de movimentação.
