---
title: "Alterar seu cartão de crédito do Azure | Microsoft Docs"
description: "Descreve como alterar o cartão de crédito usado para pagar uma assinatura do Azure"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: genli
ms.openlocfilehash: 03764377b3ea0e17d4a192a7e05bb495ec56f331
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Adicionar, atualizar ou remover um cartão de crédito ou débito do Azure

No Centro de Contas, é possível adicionar um novo cartão de crédito, atualizar um cartão de crédito existente ou excluir um cartão de crédito que você não usa. Você deve fazer logon como um [Administrador da Conta](billing-subscription-transfer.md#whoisaa) para fazer essas alterações.

**Deseja mudar para pagamento por fatura?** Consulte [Pagar assinaturas do Azure por fatura](billing-how-to-pay-by-invoice.md).
 
<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card"></a>Adicionar um novo cartão de crédito ou débito

1. Entre no [Centro de Contas](https://account.windowsazure.com/Subscriptions) como o Administrador da Conta.
1. Selecione uma assinatura.
1. No lado direito da página, selecione **Gerenciar métodos de pagamento**.

    ![Captura de tela que mostra a opção Gerenciar formas de pagamento selecionada.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Selecione “+” para adicionar um cartão.

    ![Captura de tela que mostra a opção Editar ao lado da forma de pagamento.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Insira os detalhes do cartão de crédito ou débito.
1. Selecione **Salvar**. 

Se você receber um erro depois de adicionar o cartão de crédito, consulte [Cartão de crédito recusado na inscrição do Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-or-debit-card"></a>Atualizar cartão de crédito ou débito existente

Caso seu cartão de crédito seja renovado e o número permaneça o mesmo, atualize os detalhes do cartão de crédito existente, como a data de vencimento. Se o número de seu cartão de crédito for alterado devido a perda, roubo ou vencimento do cartão, siga as etapas da seção [Adicionar um cartão de crédito como uma forma de pagamento](#addcard). Você não precisa atualizar o CVV.

1. Entre no [Centro de Contas do Azure](https://account.windowsazure.com/Subscriptions) como o Administrador da Conta.
1. Selecione a assinatura que está vinculada ao cartão.
1. Selecione **Gerenciar formas de pagamento**.
1. Selecione **Editar** ao lado do cartão que você deseja atualizar.
1. Atualizar os detalhes do cartão de crédito ou débito.
1. Selecione **Salvar**.

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Usar um cartão de crédito diferente para a assinatura do Azure

1. Entre no [Centro de Contas do Azure](https://account.windowsazure.com/Subscriptions) como o Administrador da Conta.
1. Selecione a assinatura que está vinculada ao cartão.
1. No lado direito da página, selecione **Gerenciar métodos de pagamento**.
1. Clique em **Usar** ao lado do cartão que você deseja usar. Isso também atualiza quaisquer outras assinaturas associadas atualmente a esse cartão. 

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>Remover um cartão de crédito ou débito da conta

1. Entre no [Centro de Contas do Azure](https://account.windowsazure.com/Subscriptions) como o administrador da conta.
1. Selecione a assinatura que está vinculada ao cartão.
3. No lado direito da página, selecione **Gerenciar métodos de pagamento**.
4. Clique em **Excluir** no cartão de crédito que você deseja excluir.

Caso seu cartão de crédito esteja associado a outras assinaturas ativas da Microsoft, você não poderá removê-lo de sua conta do Azure. Remova o cartão de crédito de todas as assinaturas ativas que você tem com a Microsoft e tente novamente.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Minha assinatura está desabilitada. Por que não é possível remover o cartão de crédito agora?

Depois que sua assinatura estiver desabilitada ou cancelada, poderemos esperar 90 dias antes de excluir permanentemente a sua assinatura. Mantemos sua forma de pagamento no arquivo durante o período de retenção caso você deseje reativar a assinatura. Depois disso, a assinatura será completamente excluída.

Se você precisar remover seu cartão de crédito ou débito antes do período de retenção de 90 dias terminar, [entre em contato com o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Por que continuo recebendo "Sua sessão de logon expirou. Clique aqui para fazer logon novamente"?

Se você estiver recebendo essa mensagem de erro, mesmo se já tiver feito logoff e retornado, tente novamente com uma sessão de navegação privada.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Como usar um cartão diferente para cada assinatura que tenho?

Infelizmente, se suas assinaturas já estiverem usando o mesmo cartão, não será possível usar cartões diferentes. No entanto, ao se inscrever para uma nova assinatura, você pode escolher usar uma nova forma de pagamento para essa assinatura.

### <a name="how-do-i-make-payments"></a>Como faço pagamentos?

Se você tiver configurado um cartão de crédito ou débito como forma de pagamento, faremos a cobrança no cartão automaticamente após o período de cobrança. Você não precisa fazer nada.

Se estiver [pagando por fatura](billing-how-to-pay-by-invoice.md), envie seu pagamento para o local listado na parte inferior da fatura.

### <a name="how-do-i-make-a-one-time-payment"></a>Como fazer um pagamento único?

Infelizmente, o Azure não dá suporte a pagamentos únicos no momento para cartões de crédito ou débito. 

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
