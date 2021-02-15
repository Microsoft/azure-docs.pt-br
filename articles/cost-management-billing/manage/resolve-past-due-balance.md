---
title: Email de saldo vencido do Azure
description: Descreve como fazer um pagamento se a sua assinatura do Azure tiver um saldo devido anterior
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 3c42db58017dc3a692c227f6eb454cf7b597e77b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91297577"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Resolver o saldo vencido de sua assinatura do Azure

Este artigo se aplica aos clientes que se inscreveram no Azure Online com um cartão de crédito e que têm uma conta de cobrança do Programa Microsoft Online Services. Saiba como [verificar o tipo de sua conta de cobrança](#check-the-type-of-your-account). Caso você tenha uma conta de cobrança do Contrato de Cliente da Microsoft, confira [Pagar sua fatura do Microsoft Azure](../understand/pay-bill.md).

Se o pagamento não for recebido ou se não pudermos processá-lo, você receberá um email e verá um alerta no portal do Azure informando que a sua assinatura está vencida. Se a forma de pagamento padrão for cartão de crédito, o [administrador da conta](../understand/subscription-transfer.md#whoisaa) poderá liquidar os preços pendentes no portal do Azure. Se você pagar por fatura (cheque/transferência eletrônica), envie o pagamento para a localização listada na parte inferior da fatura.

> [!IMPORTANT]
> * Se você tiver várias assinaturas que usam o mesmo cartão de crédito e todas estiverem vencidas, deverá pagar todo o saldo pendente de uma vez.
> * O cartão de crédito que você usar para liquidar os preços pendentes se tornará a nova forma de pagamento padrão para todas as assinaturas que estavam usando a forma de pagamento com falha.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Resolver saldo vencido no portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com) como Administrador de Contas.
1. Pesquise **Gerenciamento de Custos + Cobrança**.
1. Selecione a assinatura vencida na página **Visão geral**.
1. Na página **Visão geral da assinatura**, clique na faixa vermelha Vencida para liquidar o saldo.
    > [!NOTE]
    > Se você não for o administrador da conta, não poderá liquidar o saldo.
1. Na nova página **Liquidar saldo**, clique em **Selecionar forma de pagamento**.
    ![Captura de tela que mostra o link selecionar forma de pagamento](./media/resolve-past-due-balance/settle-balance-screen.png)

1. Na nova folha à direita, selecione um cartão de crédito na lista suspensa ou adicione um novo clicando no link azul **Adicionar nova forma de pagamento**. Esse cartão de crédito se tornará a forma de pagamento ativa para todas as assinaturas que atualmente usam a forma de pagamento com falha.
     > [!NOTE]
     > * O saldo pendente total reflete os encargos pendentes em todos os serviços da Microsoft que usam a forma de pagamento com falha.
     > * Se a forma de pagamento selecionada também tiver encargos pendentes para serviços da Microsoft, isso se refletirá no saldo total pendente. Também é necessário pagar esses encargos pendentes.
1. Clique em **Pagar**.

## <a name="troubleshoot-declined-credit-card"></a>Solucionar problemas de cartão de crédito recusado

Se a cobrança em seu cartão de crédito for recusada pela instituição financeira, entre em contato com a instituição financeira para resolver o problema. Verifique com o banco para se certificar de que:
- Transações internacionais estão habilitadas no cartão.
- O cartão tem limite de crédito ou fundos suficientes para liquidar o saldo.
- Pagamentos recorrentes estão habilitados no cartão.

## <a name="not-getting-billing-email-notifications"></a>Não está recebendo notificações de email de cobrança?

Se você for o Administrador da Conta, [verifique qual endereço de email é usado para notificações](change-azure-account-profile.md). Recomendamos que você use um endereço de email que é verificado regularmente. Se o email estiver correto, verifique a pasta de spam.

## <a name="if-i-forget-to-pay-what-happens"></a>Se eu me esquecer de pagar, o que acontecerá?

O serviço será cancelado e seus recursos não estarão mais disponíveis. Todos os seus dados do Azure serão excluídos 90 dias após o encerramento do serviço. Para saber mais, consulte a [Central de Confiabilidade da Microsoft – Como gerenciamos seus dados](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

Caso esteja ciente de que o pagamento foi processado, mas sua assinatura ainda estiver desabilitada, entre em contato com o [Suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="check-the-type-of-your-account"></a>Verifique o tipo de sua conta
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
