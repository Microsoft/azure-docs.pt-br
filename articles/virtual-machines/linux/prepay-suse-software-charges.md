---
title: Pagar antecipadamente por planos de software - reservas do Azure | Microsoft Docs
description: Saiba como você pode pagar antecipadamente para os planos economizar dinheiro ao longo de seus custos de pagamento conforme o uso de software.
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2019
ms.author: banders
ms.openlocfilehash: e15dcdbbcaed32d836bb751ef93ce17e90bd6905
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771236"
---
# <a name="prepay-for-azure-software-plans"></a>Pagar antecipadamente planos de software do Azure

Quando você paga antecipadamente para o uso de software do SUSE e RedHat no Azure, você pode economizar dinheiro ao longo de seus custos de pagamento conforme o uso. Os descontos se aplicam somente aos medidores SUSE e o RedHat e não sobre o uso de máquina virtual. Você pode comprar reservas para máquinas virtuais separadamente para economizar ainda mais.

Você pode comprar planos de software do SUSE e RedHat no portal do Azure. Comprar um plano:

- Você deve ter a função de proprietário para pelo menos uma Enterprise ou assinatura paga conforme o uso.
- Para assinaturas do Enterprise, o **adicionar instâncias reservadas** opção deve ser habilitada no [portal EA](https://ea.azure.com/). Se a configuração estiver desabilitada, você deve ser um administrador de EA para a assinatura.
- Para o programa de provedor de solução de nuvem (CSP), os agentes administradores ou agentes de vendas podem comprar os planos de software.

## <a name="buy-a-software-plan"></a>Compre um plano de software

1. Entrar no portal do Azure e acesse [reservas](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Clique em **adicionar** e, em seguida, selecione o plano de software que você deseja comprar.
Preencha os campos obrigatórios. Qualquer VM do Linux SUSE ou RedHat VM que corresponde aos atributos daquilo que você compra obtém o desconto. O número real de implantações que obtêm o desconto depende do escopo e da quantidade selecionada.
3. Selecione uma assinatura. Ele é usado para pagar para o plano.
O método de pagamento da assinatura é cobrado os custos iniciais para a reserva. O tipo de assinatura deve ser um Enterprise Agreement (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Pagamento Conforme o Uso (números da oferta: MS-AZR-0003P ou MS-AZR-0023P).
    - Para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como média.
    - Para uma assinatura pré-paga, os encargos são cobrados ao método de pagamento de cartão de crédito ou fatura da assinatura.
4. Selecione um escopo. O escopo pode cobrir uma assinatura ou várias assinaturas (escopo compartilhado).
    - Assinatura única - o desconto de plano é aplicada a correspondência de uso na assinatura.
    - Compartilhado - o desconto de plano é aplicado a correspondência de instâncias em qualquer assinatura em seu contexto de cobrança. Para clientes empresariais, o contexto de cobrança é o registro e inclui todas as assinaturas no registro. Para clientes de pré-pago, o contexto de cobrança é todas as assinaturas de pago conforme o uso criadas pelo administrador da conta.
5. Selecione um produto para escolher o tamanho da VM e o tipo de imagem. O desconto aplica-se ao tamanho da VM selecionado apenas.
6. Selecione uma condição de um ano ou três anos.
7. Escolha uma quantidade, o que é o número de instâncias de VM pré-pagas que pode obter o desconto de cobrança.
8. Adicione o produto ao carrinho, revisão e compra.

O desconto de reserva é aplicado automaticamente para o medidor de software que você pague previamente. Encargos de computação VM não são cobertos pelo plano. As reservas de VM pode ser adquirido separadamente.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Desconto se aplica às diferentes tamanhos de VM SUSE

Como as instâncias de VM reservadas, planos de SUSE Linux oferecem flexibilidade de tamanho de instância. O desconto aplica-se mesmo quando você implanta uma VM que tenha um tamanho diferente do plano de SUSE que você comprou. Para obter mais informações, consulte [entender como o desconto de plano de software é aplicado](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>Desconto de plano RedHat

Planos estão disponíveis apenas para máquinas de virtuais do Red Hat Enterprise Linux. O desconto não se aplica a VMs RedHat Enterprise Linux SAP HANA ou VMs de aplicativos de negócios RedHat Enterprise Linux SAP.

Os descontos de plano do RedHat se aplicam apenas ao tamanho da VM selecionada no momento da compra. RHEL planos não é possível ser reembolsados ou trocados após a compra.


## <a name="cancellation-and-exchanges-not-allowed"></a>Cancelamento e trocas não permitidas

Você não pode cancelar ou um plano do SUSE ou RedHat que você comprou do exchange. Verifique seu uso para garantir que você compre o plano certo. Para obter ajuda identificar o que comprar, consulte [entender como o desconto de plano de software é aplicado](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

Para aprender a gerenciar uma reserva, confira [Gerenciar reservas do Azure](../../billing/billing-manage-reserved-vm-instance.md).

Para saber mais, consulte os seguintes artigos:

- [O que são Reservas do Azure?](../../billing/billing-save-compute-costs-reservations.md)
- [Gerenciar reservas no Azure](../../billing/billing-manage-reserved-vm-instance.md)
- [Entender como o desconto de reserva do SUSE é aplicado](../../billing/billing-understand-suse-reservation-charges.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](../../billing/billing-understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](../../billing/billing-understand-reserved-instance-usage-ea.md)
