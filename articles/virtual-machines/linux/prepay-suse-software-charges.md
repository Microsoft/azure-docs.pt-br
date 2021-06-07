---
title: Pagar antecipadamente por planos de software-reservas do Azure
description: Saiba como você pode pagar por planos de software para economizar dinheiro em seus custos pagos conforme o uso.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines
ms.subservice: reserved-instances
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: banders
ms.openlocfilehash: 136207afef3fef9266fadcea1e67bb0aa6a38a32
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549746"
---
# <a name="prepay-for-azure-software-plans"></a>Pagar antecipadamente planos de software do Azure

Ao pagar por seu uso de software SUSE e RedHat no Azure, você pode economizar dinheiro nos custos pagos conforme o uso. Os descontos se aplicam apenas a medidores SUSE e RedHat e não ao uso da máquina virtual. Você pode comprar reservas para máquinas virtuais separadamente para economizar mais.

Você pode comprar planos de software SUSE e RedHat na portal do Azure. Comprar um plano:

- Você deve ter a função de proprietário para pelo menos uma assinatura corporativa ou individual com preço pago conforme o uso.
- Para assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal do EA](https://ea.azure.com/). Se a configuração estiver desabilitada, você deverá ser um administrador do EA para a assinatura.
- Para o programa CSP (provedor de soluções na nuvem), os agentes de administração ou os agentes de vendas podem comprar os planos de software.

## <a name="buy-a-software-plan"></a>Comprar um plano de software

1. Entre no portal do Azure e acesse as [reservas](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Clique em **Adicionar** e selecione o plano de software que você deseja comprar.
Preencha os campos obrigatórios. Qualquer VM SUSE Linux ou RedHat VM que corresponda aos atributos do que você compra Obtém o desconto. O número real de implantações que obtêm o desconto depende do escopo e da quantidade selecionada.
3. Selecione uma assinatura. Ele é usado para pagar pelo plano.
O método de pagamento de assinatura é cobrado pelos custos antecipados da reserva. O tipo de assinatura deve ser um Contrato Enterprise (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou contrato individual com o preço pago conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P).
    - Para uma assinatura corporativa, os encargos são deduzidos do saldo do Pagamento antecipado do Azure do registro (chamado anteriormente de compromisso monetário) ou cobrados como excedente.
    - Para uma assinatura individual com o preço pago conforme o uso, os encargos são cobrados no cartão de crédito da assinatura ou no método de pagamento da fatura.
4. Selecione um escopo. O escopo pode cobrir uma assinatura ou várias assinaturas (escopo compartilhado).
    - Assinatura única-o desconto do plano é aplicado ao uso correspondente na assinatura.
    - Compartilhado-o desconto do plano é aplicado às instâncias correspondentes em qualquer assinatura em seu contexto de cobrança. Para clientes corporativos, o contexto de cobrança é o registro e inclui todas as assinaturas no registro. Para o plano individual com clientes de preços pagos conforme o uso, o contexto de cobrança é todos os planos individuais com assinaturas de preços pagas conforme o uso criadas pelo administrador da conta.
    - Grupo de recursos único – aplica o desconto de reserva aos recursos correspondentes somente no grupo de recursos selecionado.
5. Selecione um produto para escolher o tamanho da VM e o tipo de imagem. O desconto se aplica somente ao tamanho de VM selecionado.
6. Selecione um termo de um ou três anos.
7. Escolha uma quantidade, que é o número de instâncias de VM pré-paga que podem obter o desconto de cobrança.
8. Adicione o produto ao carrinho, examine e compre.

O desconto de reserva é aplicado automaticamente ao medidor de software para o qual você paga. Os encargos de computação da VM não são cobertos pelo plano. Você pode comprar as reservas de VM separadamente.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>O desconto se aplica a diferentes tamanhos de VM SUSE

Assim como as instâncias de VM reservadas, os planos do SUSE Linux oferecem flexibilidade de tamanho de instância. Seu desconto se aplica mesmo quando você implanta uma VM que tem um tamanho diferente do plano SUSE que você comprou. Para obter mais informações, consulte [entender como o desconto do plano de software é aplicado](../../cost-management-billing/reservations/understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>Desconto do plano de RedHat

Os planos estão disponíveis apenas para máquinas virtuais Red Hat Enterprise Linux. O desconto não se aplica às VMs do RedHat Enterprise Linux SAP HANA VMs ou ao RedHat Enterprise Linux SAP Business apps.

Os descontos do plano do RedHat se aplicam somente ao tamanho da VM que você selecionar no momento da compra. Os planos de RHEL não podem ser reembolsados ou trocados após a compra.


## <a name="cancellation-and-exchanges-not-allowed"></a>Cancelamento e trocas não permitidas

Não é possível cancelar ou trocar um plano SUSE ou RedHat que você comprou. Verifique seu uso para garantir que você compre o plano certo. Para obter ajuda para identificar o que comprar, consulte [entender como o desconto do plano de software é aplicado](../../cost-management-billing/reservations/understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

Para saber como gerenciar uma reserva, consulte [gerenciar reservas do Azure](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Confira os seguintes artigos para saber mais:

- [O que são Reservas do Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Gerenciar Reservas no Azure](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Entender como o desconto de reserva do SUSE é aplicado](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
