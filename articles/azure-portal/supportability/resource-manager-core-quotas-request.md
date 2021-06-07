---
title: Solicitações de aumento de cota de vCPU do Azure Resource Manager
description: Solicitações de aumento de cota de vCPU do Azure Resource Manager
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 2580da2a4ac7b943dee3e5e6ff8bdbd49664505b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745240"
---
# <a name="quota-increase-requests"></a>Solicitações de um aumento de cota

As cotas de vCPU do Resource Manager para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais são impostas em duas camadas para cada assinatura, em cada região.

A primeira camada é o limite de vCPUs regional total em todas as séries de VM. A segunda camada é o limite de vCPUs por série de VMs, como a vCPUs da série D. Sempre que uma nova máquina virtual for implantada, a soma do uso de vCPUs novo e existente para essa série de VMs não deverá exceder a cota de vCPU aprovada para essa série de VMs específica. Além disso, a contagem de vCPU total e nova e existente implantada em toda a série de VMs não deve exceder a cota de vCPUs regional total aprovada para a assinatura. Se uma dessas cotas for excedida, a implantação da VM não será permitida.
Você pode solicitar um aumento do limite de cota de vCPUs para a série de VMs de portal do Azure. Um aumento na cota da série de VMs aumenta automaticamente o limite de vCPUs regional total pelo mesmo valor.

Quando uma nova assinatura é criada, o vCPUs regional total padrão pode não ser igual à soma de cotas de vCPU padrão para todas as séries de VMs individuais. Esse fato pode resultar em uma assinatura com cota suficiente para cada série de VM individual que você deseja implantar. Ele pode não ter cota suficiente para o total de vCPUs regionais para todas as implantações. Nesse caso, você precisará enviar uma solicitação para aumentar o limite de vCPUs regional total explicitamente. O limite de vCPUs regional total não pode exceder a soma da cota aprovada em todas as séries de VM para a região.

> [!NOTE]
> Se você quiser aumentar o limite ou a cota acima do limite padrão, [abra uma solicitação de atendimento ao cliente online sem encargos](../../azure-resource-manager/templates/error-resource-quota.md#solution).

Para obter mais informações sobre cotas, consulte [cotas de vCPU de máquina virtual](../../virtual-machines/windows/quotas.md) e [assinatura do Azure e limites de serviço, cotas e restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md).