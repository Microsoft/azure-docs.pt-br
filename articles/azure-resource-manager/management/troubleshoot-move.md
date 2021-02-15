---
title: Solucionar problemas de erros de movimentação
description: Solucionar problemas de movimentação de recursos para um novo grupo de recursos ou assinatura.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 41b1e2435caf9874f3582a3394664c7b7f5a8d29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90054155"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Solução de problemas de mudança de recursos para um novo grupo de recursos ou assinatura

Este artigo fornece sugestões para ajudar a resolver problemas ao mover recursos.

## <a name="upgrade-a-subscription"></a>Atualizar uma assinatura

Se você realmente quiser atualizar sua assinatura do Azure (por exemplo, mudando de gratuita para pré-paga), você precisará converter sua assinatura.

* Para atualizar uma avaliação gratuita, consulte [atualizar sua avaliação gratuita ou Microsoft imagine assinatura do Azure para](../../cost-management-billing/manage/upgrade-azure-subscription.md)pré-pago.
* Para alterar uma conta paga conforme o uso, consulte [alterar sua assinatura paga conforme o uso do Azure para uma oferta diferente](../../cost-management-billing/manage/switch-azure-offer.md).

Se você não puder converter a assinatura, [crie uma solicitação de suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Selecione **Subscription Management** para o tipo de problema.

## <a name="service-limitations"></a>Limitações de serviço

Alguns serviços exigem considerações adicionais ao mover recursos. Se você estiver movendo os seguintes serviços, certifique-se de verificar as diretrizes e as limitações.

* [Serviços de Aplicativos](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Modelo de implantação clássica](./move-limitations/classic-model-move-limitations.md)
* [Rede](./move-limitations/networking-move-limitations.md)
* [Serviços de Recuperação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Máquinas virtuais](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Solicitações grandes

Quando possível, quebre grandes movimentações em operações de movimentação separadas. O Resource Manager imediatamente retornará um erro quando houver mais de 800 recursos em uma única operação. No entanto, mover menos de 800 recursos também pode falhar por tempo limite.

## <a name="resource-not-in-succeeded-state"></a>Recurso que não está no estado com êxito

Quando você recebe uma mensagem de erro que indica que um recurso não pode ser movido porque não está em um estado bem-sucedido, ele pode, na verdade, ser um recurso dependente que está bloqueando a movimentação. Normalmente, o código de erro é **MoveCannotProceedWithResourcesNotInSucceededState**.

Se o grupo de recursos de origem ou de destino contiver uma rede virtual, os Estados de todos os recursos dependentes da rede virtual serão verificados durante a movimentação. A verificação inclui esses recursos diretamente e indiretamente dependente da rede virtual. Se qualquer um desses recursos estiver em um estado de falha, a movimentação será bloqueada. Por exemplo, se uma máquina virtual que usa a rede virtual falhou, a movimentação será bloqueada. A movimentação é bloqueada mesmo quando a máquina virtual não é um dos recursos que estão sendo movidos e não está em um dos grupos de recursos para a movimentação.

Ao receber esse erro, você tem duas opções. Mova seus recursos para um grupo de recursos que não tem uma rede virtual ou [contate o suporte](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Próximas etapas

Para ver comandos para mover recursos, confira [Move resources to new resource group or subscription](move-resource-group-and-subscription.md) (Mover recursos para o novo grupo de recursos ou assinatura).
