---
title: Alterar seu cartão de crédito para o Azure
description: Descreve como alterar o cartão de crédito usado para pagar uma assinatura do Azure.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: 7719ae83525883a6d3f014dbb99877b7319f2ccd
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383668"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Adicionar, atualizar ou remover um cartão de crédito do Azure

No portal do Azure, você pode adicionar um novo cartão de crédito, atualizar um cartão de crédito existente ou excluir um cartão de crédito que você não usa. Você deve ser um [administrador da conta](billing-subscription-transfer.md#whoisaa) para fazer essas alterações.

Se você tiver um [contrato](#check-access-to-a-microsoft-customer-agreement)com o cliente da Microsoft, seus métodos de pagamento serão associados a perfis de cobrança. Saiba como [alterar o método de pagamento padrão para um perfil de cobrança](#change-payment-method-for-a-billing-profile). Somente o usuário que se inscreveu no Azure pode atualizar o método de pagamento.

**Deseja mudar para pagar por fatura (transferência de cheque/transmissão)?** Consulte [Pagar assinaturas do Azure por fatura](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>Adicionar um novo cartão de crédito a uma assinatura do Azure

1. Entre no [Portal do Azure](https://portal.azure.com) como Administrador da Conta.
1. Procure **Gerenciamento de custos + cobrança**.

    ![Captura de tela que mostra a pesquisa](./media/billing-how-to-change-credit-card/search.png)

1. Selecione a assinatura à qual você deseja adicionar o cartão de crédito.
1. Selecione **Formas de pagamento**.

    ![Captura de tela que mostra a opção Gerenciar formas de pagamento selecionada.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. No canto superior esquerdo, selecione "+" para adicionar um cartão. Um formulário de cartão de crédito será exibido à direita.
1. Insira os detalhes do cartão de crédito.

    ![Captura de tela que mostra a adição de um novo cartão.](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Para tornar este cartão o seu método de pagamento ativo, marque a caixa ao lado de **tornar esse meu método de pagamento ativo** acima do formulário. Esse cartão se tornará o meio de pagamento ativo para todas as assinaturas usando o mesmo cartão, como a assinatura selecionada.

1. Selecione **Avançar**.

Se você receber um erro depois de adicionar o cartão de crédito, consulte [Cartão de crédito recusado na inscrição do Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-card"></a>Atualizar cartão de crédito existente

Se o seu cartão de crédito for renovado e o número permanecer o mesmo, atualize os detalhes do cartão de crédito existente, como a data de validade. Se o número de seu cartão de crédito for alterado devido a perda, roubo ou vencimento do cartão, siga as etapas da seção [Adicionar um cartão de crédito como uma forma de pagamento](#addcard). Você não precisa atualizar o CVV.

1. Entre no [Portal do Azure](https://portal.azure.com) como Administrador da Conta.
1. Procure **Gerenciamento de custos + cobrança**.

    ![Captura de tela que mostra a pesquisa](./media/billing-how-to-change-credit-card/search.png)

1. Selecione **Formas de pagamento**.

    ![Captura de tela que mostra a opção Gerenciar formas de pagamento selecionada.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Clique no cartão de crédito que você deseja editar. Um formulário de cartão de crédito será exibido à direita.

    ![Captura de tela que mostra o cartão de crédito selecionado.](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Atualize os detalhes do cartão de crédito.
1. Clique em **Salvar**.

## <a name="use-a-different-credit-card"></a>Usar um cartão de crédito diferente

Se mais de uma de suas assinaturas tiverem o mesmo método de pagamento ativo, a alteração do método de pagamento ativo em qualquer uma dessas assinaturas também atualizará o método de pagamento ativo nos outros.

1. Entre no [Portal do Azure](https://portal.azure.com) como Administrador da Conta.
1. Procure **Gerenciamento de custos + cobrança**.

    ![Captura de tela que mostra a pesquisa](./media/billing-how-to-change-credit-card/search.png)

1. Selecione a assinatura à qual você deseja adicionar o cartão de crédito.
1. Selecione **Formas de pagamento**.

    ![Captura de tela que mostra a opção Gerenciar formas de pagamento selecionada.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Selecione a caixa ao lado do cartão que você gostaria de tornar o método de pagamento ativo.
1. Clique em **definir ativo**.
    ![Captura de tela que mostra o cartão de crédito selecionado e definido como ativo.](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>Remover um cartão de crédito da conta

1. Entre no [Portal do Azure](https://portal.azure.com) como Administrador da Conta.
1. Selecione **Gerenciamento de custos + cobrança** no lado esquerdo da página.

    ![Captura de tela que mostra a pesquisa](./media/billing-how-to-change-credit-card/search.png)

1. Em **cobrança**, selecione **métodos de pagamento**.

    ![Captura de tela que mostra a opção Gerenciar formas de pagamento selecionada.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Selecione a caixa ao lado do cartão que você deseja remover.
1. Clique em **Excluir**.

Se seu cartão de crédito for o método de pagamento ativo para qualquer uma das suas assinaturas da Microsoft, você não poderá removê-lo da sua conta do Azure. Altere o método de pagamento ativo para todas as assinaturas vinculadas a este cartão de crédito e tente novamente
<!-- # Add, update, or remove a credit card for Azure

In the Account Center, you can add a new credit card, update an existing credit card, or delete a credit card that you don't use. You must be an [Account Administrator](billing-subscription-transfer.md#whoisaa) to make these changes.

**Want to switch to pay by invoice?** See [Pay for Azure subscriptions by invoice](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## Add a new credit card

1. Sign in to the [Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select a subscription.
1. On the right side of the page, select **Manage payment methods**.

    ![Screenshot that shows Manage payment methods option selected.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Select “+” to add a card.

    ![Screenshot that shows the edit option next to the payment method.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Enter credit card details.
1. Select **Save**.

If you get an error after you add the credit card, see [Credit card declined at Azure sign-up](billing-credit-card-fails-during-azure-sign-up.md).

## Update existing credit card

If your credit card gets renewed and the number remains the same, update the existing credit card details like the expiration date. If your credit card number changes because the card is lost, stolen, or expired, follow the steps in the [Add a credit card as a payment method](#addcard) section. You don't need to update the CVV.

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. Select **Manage payment methods**.
1. Select **Edit** next to the card you want to update.
1. Update the credit card details.
1. Select **Save**.

## Use a different credit card for the Azure subscription

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. On the right side of the page, select **Manage payment methods**.
1. Click **Use Instead** next to the card that you want to use. This also updates any other subscriptions currently associated with this card.

## Remove a credit card from the account

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
3. On the right side of the page, select **Manage payment methods**.
4. Click **Delete** for the credit card that you want to delete.

If your credit card is associated with other active Microsoft subscriptions, you can't remove it from your Azure account. Remove the credit card from all active subscriptions that you have with Microsoft and try again. -->

## <a name="change-payment-method-for-a-billing-profile"></a>Alterar o método de pagamento de um perfil de cobrança

Para alterar o método de pagamento de um perfil de cobrança, você deve ser a pessoa que se inscreveu no Azure.

Se você quiser mudar o método de pagamento padrão para verificação/transmissão, saiba como [mudar um perfil de cobrança para verificação/transmissão](billing-how-to-pay-by-invoice.md).

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.
1. No menu à esquerda, clique em perfis de **cobrança**.

    ![captura de tela que mostra o perfil de cobrança no menu](./media/billing-how-to-change-credit-card/billing-profile.png)

1. Selecione um perfil de cobrança.
1. No menu à esquerda, selecione métodos de **pagamento**.

   ![Captura de tela que mostra os métodos de pagamento no menu](./media/billing-how-to-change-credit-card/billing-profile-payment-methods.png)

1. Acima do método de pagamento padrão, clique em **alterar**.

    ![Captura de tela que mostra o botão Alterar](./media/billing-how-to-change-credit-card/customer-led-switch-credit-card.png)

1. Selecione um cartão existente ou adicione um novo.

## <a name="frequently-asked-questions"></a>Perguntas frequentes
As seções a seguir respondem a perguntas frequentes sobre como alterar suas informações de cartão de crédito.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Minha assinatura está desabilitada. Por que não é possível remover o cartão de crédito agora?

Depois que sua assinatura estiver desabilitada ou cancelada, poderemos esperar 90 dias antes de excluir permanentemente a sua assinatura. Mantemos sua forma de pagamento no arquivo durante o período de retenção caso você deseje reativar a assinatura. Depois disso, a assinatura é excluída permanentemente.

Se você precisar remover seu cartão de crédito antes de o período de retenção de 90 dias terminar, [reative sua assinatura](billing-subscription-become-disable.md). Se não conseguir reativar, [entre em contato com o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Por que continuo recebendo "Sua sessão de logon expirou. Clique aqui para fazer logon novamente"?

Se você continuar recebendo essa mensagem de erro mesmo que já tenha feito o logout e voltado, tente novamente com uma sessão de navegação privada.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Como usar um cartão diferente para cada assinatura que tenho?

Infelizmente, se suas assinaturas já estiverem usando o mesmo cartão, não será possível usar cartões diferentes. No entanto, ao se inscrever para uma nova assinatura, você pode escolher usar uma nova forma de pagamento para essa assinatura.

### <a name="how-do-i-make-payments"></a>Como faço pagamentos?

Se você configurar um cartão de crédito como seu método de pagamento, cobraremos automaticamente seu cartão após cada período de cobrança. Você não precisa fazer nada.

Se estiver [pagando por fatura](billing-how-to-pay-by-invoice.md), envie seu pagamento para o local listado na parte inferior da fatura.

### <a name="how-do-i-change-the-tax-id"></a>Como alterar a ID de imposto?

Para adicionar ou atualizar a ID do imposto, atualize seu perfil na [centro de contas do Azure](https://account.azure.com/Profile)e, em seguida, selecione **registro de impostos**. Essa ID de imposto é usada para cálculos de isenção de impostos e aparece em sua fatura.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato do cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre as [reservas do Azure](billing-save-compute-costs-reservations.md) para ver se elas podem economizar dinheiro.
