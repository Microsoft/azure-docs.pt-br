---
title: Cancelar sua assinatura do Azure
description: Descreve como cancelar sua assinatura do Azure, como a assinatura de Avaliação Gratuita
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: banders
ms.openlocfilehash: 0d0dd5342cfc57da776e38ddef3df3b9c2199ac2
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735768"
---
# <a name="cancel-your-azure-subscription"></a>Cancelar sua assinatura do Azure

Você poderá cancelar sua assinatura do Azure no portal do Azure se não precisar mais dela.

Embora não seja necessário, a Microsoft *recomenda* que você execute as seguintes ações para cancelar a assinatura:

* Faça backup dos dados. Por exemplo, se você estiver colocando dados no armazenamento do Azure ou SQL, baixe uma cópia. Se você tiver uma máquina virtual, salve uma imagem dela localmente.
* Finalize seus serviços. Vá para a [página de recursos no portal de gerenciamento](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources), e **Pare** quaisquer máquinas virtuais, aplicativos ou outros serviços em execução.
* Considere migrar seus dados. Consulte [Mover recursos para um novo grupo de recursos ou uma nova assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Exclua todos os recursos e todos os grupos de recursos.
* Se você tiver funções personalizadas que fazem referência a essa assinatura no `AssignableScopes`, deverá atualizá-las para remover a assinatura. Se tentar atualizar uma função personalizada após cancelar uma assinatura, você poderá receber um erro. Para obter mais informações, confira [Solucionar problemas com funções personalizadas](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) e [Funções personalizadas do Azure](../../role-based-access-control/custom-roles.md).

Se você cancelar um plano de Suporte do Azure pago, o restante do período de assinatura será cobrado de você. Para obter mais informações, consulte [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).

## <a name="who-can-cancel-a-subscription"></a>Quem pode cancelar uma assinatura?

A tabela abaixo descreve a permissão necessária para cancelar uma assinatura.

|Tipo de assinatura     |Quem pode cancelar  |
|---------|---------|
|Assinaturas criadas quando você se inscreve no Azure por meio do site do Azure. Por exemplo, quando você se inscreve em uma [conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), uma [conta com taxas pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) ou como [assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Administrador da conta e proprietários da assinatura  |
|[Contrato Enterprise da Microsoft](https://azure.microsoft.com/pricing/enterprise-agreement/) e [Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Proprietário da conta e proprietários da assinatura       |
|[Plano do Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) e [Plano do Azure para DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Proprietários da assinatura      |


## <a name="cancel-subscription-in-the-azure-portal"></a>Cancelar assinatura no portal do Azure

1. Selecione sua assinatura na [página Assinaturas no Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a assinatura que você quer cancelar.
1. Selecione **Visão Geral** e, em seguida, selecione **Cancelar assinatura**.
    ![Captura de tela que mostra o botão Cancelar](./media/cancel-azure-subscription/cancel_ibiza.png)
1. Siga os prompts e conclua o cancelamento.

> [!NOTE]
> Os parceiros poderão suspender ou cancelar uma assinatura se for solicitado por um cliente ou em casos de não pagamento ou fraude. Para obter mais informações, confira [Suspender ou cancelar uma assinatura](/partner-center/create-a-new-subscription#suspend-or-cancel-a-subscription).

## <a name="cancel-a-support-plan"></a>Cancelar um plano de suporte

Se você comprar seu plano de suporte por meio do site do Azure, do portal do Azure ou se tiver um nos termos de um Contrato de Cliente da Microsoft, poderá cancelá-lo. Caso tenha comprado seu plano de suporte por meio de um representante ou um parceiro da Microsoft, entre em contato com eles para obter assistência. 

1. No portal do Azure, procure **Gerenciamento de Custos e Cobrança**.
1. Em **Cobrança**, selecione **Encargos recorrentes**.
1. No lado direito do item de linha do plano de suporte, selecione as reticências ( **...** ) e a opção **Desativar a renovação automática**.

## <a name="what-happens-after-subscription-cancellation"></a>O que acontece após o cancelamento da assinatura?

Após o cancelamento, a cobrança será interrompida imediatamente. No entanto, pode levar até 10 minutos para que o cancelamento seja mostrado no portal. Se você cancelar no meio de um período de cobrança, enviaremos a fatura final na data da fatura normal após o término do período.

Após o cancelamento, seus serviços serão desabilitados. Isso significa que as máquinas virtuais são desalocadas, os endereços IP temporários são liberados e o armazenamento é somente leitura.

Após o cancelamento de sua assinatura, a Microsoft aguarda de 30 a 90 dias antes de excluir permanentemente seus dados, caso você precise acessá-los ou mude de ideia. Nós não cobramos para manter os dados. Para saber mais, consulte a [Central de Confiabilidade da Microsoft – Como gerenciamos seus dados](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="delete-free-trial-subscription"></a>Excluir uma assinatura de avaliação gratuita

Se tiver uma assinatura de avaliação gratuita, você não precisará aguardar 30 dias para que a assinatura seja excluída automaticamente. Você pode excluir sua assinatura *três dias* após cancelá-la. A opção **Excluir assinatura** fica indisponível até três dias após o cancelamento da assinatura.

1. Aguarde três dias após a data em que você cancelou a assinatura.
1. Selecione sua assinatura na página [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure.
1. Selecione a assinatura que deseja excluir.
1. Selecione **Visão Geral** e escolha **Excluir assinatura**.

## <a name="reactivate-a-subscription"></a>Reativar uma assinatura

Se você cancelar acidentalmente sua assinatura com taxas pagas conforme o uso, poderá [reativá-la no portal do Azure](subscription-disabled.md).

Caso sua assinatura não tenha taxas Pagas Conforme o Uso, entre em contato com o suporte em até 90 dias após o cancelamento para reativar a assinatura.

## <a name="why-dont-i-see-the-cancel-subscription-option-on-the-azure-portal"></a>Por que eu não vejo a opção Cancelar Assinatura no portal do Azure? 

Talvez você não tenha as permissões necessárias para cancelar uma assinatura. Confira [Quem pode cancelar uma assinatura?](#who-can-cancel-a-subscription) para obter uma descrição de quem pode cancelar vários tipos de assinaturas.

## <a name="how-do-i-delete-my-azure-account"></a>Como faço para excluir a minha conta do Azure?

*Preciso remover minha conta, incluindo todas as minhas informações pessoais. Eu já cancelei minhas assinaturas ativas (da avaliação gratuita). Não tenho nenhuma assinatura ativa e gostaria de excluir minha conta por completo*.

* Se você tiver uma conta do Azure Active Directory por intermédio de uma organização, o administrador do Azure AD poderá excluir a conta. Depois disso, os serviços são desabilitados. Isso significa que as máquinas virtuais são desalocadas, os endereços IP temporários são liberados e o armazenamento é somente leitura. Em resumo, após o cancelamento, a cobrança será interrompida imediatamente.

* Se não tiver uma conta do Azure AD por intermédio de uma organização, você poderá cancelar e excluir as assinaturas do Azure e, depois, remover os dados do cartão de crédito da conta. Embora essa ação não exclua a conta, fará com que a conta fique inoperável. Você poderá ir um pouco além e também excluir a conta Microsoft associada se ela não estiver sendo usada para nenhuma outra finalidade.

## <a name="how-do-i-cancel-a-visual-studio-professional-account"></a>Como faço para cancelar uma conta do Visual Studio Professional?

Confira o artigo [Renovação e Cancelamento](/visualstudio/subscriptions/faq/admin/renewal-cancellation). Se você tiver alguma assinatura do Azure do Visual Studio, ela precisará ser cancelada e também excluída.

## <a name="next-steps"></a>Próximas etapas

- Se necessário, você pode reativar uma assinatura paga conforme o uso no [portal do Azure](subscription-disabled.md).