---
title: Solucionar problemas de erros ao mover recursos do Azure para o novo grupo de recursos ou assinatura
description: Use o Azure Resource Manager para mover recursos para um novo grupo de recursos ou uma nova assinatura.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: e23d7c571a010e5bfb42e5f15368e0194272ed53
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723461"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Solucionar problemas de movimentação de recursos do Azure para um novo grupo de recursos ou assinatura

Este artigo fornece sugestões para ajudar a resolver problemas ao mover recursos.

## <a name="upgrade-a-subscription"></a>Atualizar uma assinatura

Se você realmente quiser atualizar sua assinatura do Azure (por exemplo, mudando de gratuita para pré-paga), você precisará converter sua assinatura.

* Para atualizar uma avaliação gratuita, consulte [Faça o upgrade da sua avaliação gratuita ou da assinatura do Microsoft Imagine Azure para o Pague conforme o uso](../billing/billing-upgrade-azure-subscription.md).
* Para alterar uma conta de pagamento conforme o uso, consulte [Alterar sua assinatura do Azure Pay-As-You-Go para uma oferta diferente](../billing/billing-how-to-switch-azure-offer.md).

Se você não conseguir converter a assinatura, [crie uma solicitação de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md). Selecione **Subscription Management** para o tipo de problema.

## <a name="service-limitations"></a>Limitações do serviço

Alguns serviços exigem considerações adicionais ao mover recursos. Se você estiver movendo os seguintes serviços, certifique-se de que verificar as diretrizes e limitações.

* [Serviços de Aplicativos](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Modelo de implantação clássica](./move-limitations/classic-model-move-limitations.md)
* [Serviços de Recuperação](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Máquinas virtuais](./move-limitations/virtual-machines-move-limitations.md)
* [Redes Virtuais](./move-limitations/virtual-network-move-limitations.md)

## <a name="large-requests"></a>Solicitações grandes

Quando possível, quebre grandes movimentações em operações de movimentação separadas. O Resource Manager imediatamente retornará um erro quando houver mais de 800 recursos em uma única operação. No entanto, mover menos de 800 recursos também pode falhar por tempo limite.

## <a name="next-steps"></a>Próximas etapas

Para ver comandos para mover recursos, confira [Move resources to new resource group or subscription](resource-group-move-resources.md) (Mover recursos para o novo grupo de recursos ou assinatura).
