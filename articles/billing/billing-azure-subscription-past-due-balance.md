---
title: Email de saldo vencido do Azure | Microsoft Docs
description: Descreve como fazer um pagamento se a sua assinatura do Azure tiver um saldo devido anterior
services: ''
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.assetid: d0b88c92-fb9d-4d12-931b-c26104ad63e9
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: banders
ms.openlocfilehash: f58969b167b6c0f0a66d46731ad76c1f6e9acc41
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491464"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Resolver o saldo vencido de sua assinatura do Azure

Se seu pagamento não seja recebido ou não foi possível processar o pagamento, você pode receber um email ou ver um alerta no portal do Azure ou do Centro de contas.
Se você for o [administrador da conta](billing-subscription-transfer.md#whoisaa), você pode efetuar os encargos pendentes [portal do Azure](https://portal.azure.com). Se você estiver em um modo de pagamento de fatura, envie o pagamento para o local indicado na parte inferior da fatura.

> [!IMPORTANT]
> * Se você tiver várias assinaturas usando o mesmo cartão de crédito e eles são todos vencido, você deve pagar o saldo pendente inteiro ao mesmo tempo.
> * O instrumento de pagamento que você usa para efetuar os encargos pendentes se tornará o novo método de pagamento ativo para todas as assinaturas que estava usando o método de pagamento com falha.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Resolver vencido saldo no portal do Azure

1. Entrar para o [portal do Azure](https://portal.azure.com) como o administrador da conta.
1. Pesquise **custo de gerenciamento + cobrança**.
1. Na página de visão geral, você verá uma lista de suas assinaturas. Se o status da sua assinatura está atrasado, clique o **liquidar Saldo** link.
    ![Captura de tela que mostra liquidar o link de saldo](./media/billing-azure-subscription-past-due-balance/settle-balance-entry-point.png)
1. O saldo pendente total reflete os encargos pendentes em todos os serviços da Microsoft usando o método de pagamento com falha.
1. Selecione um método de pagamento para pagar o saldo. Este método de pagamento se tornará o método de pagamento ativo para todas as assinaturas no momento usando o método de pagamento com falha.
    ![Captura de tela que mostra o link do método de pagamento de select](./media/billing-azure-subscription-past-due-balance/settle-balance-screen.png)
1. Se o método de pagamento selecionado também tem encargos pendentes para serviços da Microsoft, isso será refletido no saldo total pendente. Você deve pagar os encargos pendentes, muito.
1. Clique em **pagar**.

## <a name="not-getting-billing-email-notifications"></a>Não está recebendo notificações de email de cobrança?

Se você for o Administrador da Conta, [verifique qual endereço de email é usado para notificações](billing-how-to-change-azure-account-profile.md). Recomendamos que você use um endereço de email que é verificado regularmente. Se o email estiver correto, verifique a pasta de spam.

## <a name="if-i-forget-to-pay-what-happens"></a>Se eu me esquecer de pagar, o que acontecerá?

O serviço será cancelado e seus recursos não estarão mais disponíveis. Todos os seus dados do Azure serão excluídos 90 dias após o encerramento do serviço. Para saber mais, consulte a [Central de Confiabilidade da Microsoft – Como gerenciamos seus dados](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

Caso esteja ciente de que o pagamento foi processado, mas sua assinatura ainda estiver desabilitada, entre em contato com o [Suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
