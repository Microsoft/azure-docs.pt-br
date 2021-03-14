---
title: Pagar antecipadamente pelos hosts dedicados do Azure para economizar dinheiro
description: Saiba como comprar instâncias reservadas de hosts do Azure dedicado para economizar em seus custos de computação.
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 1450f03ae72e79daca5a5ae2c04506e6bee5180b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672276"
---
# <a name="save-costs-with-azure-dedicated-host-reservations"></a>Economize custos com reservas de host dedicadas do Azure

Ao se comprometer com uma instância reservada de hosts dedicados do Azure, você pode economizar dinheiro. O desconto de reserva é aplicado automaticamente ao número de hosts dedicados em execução que correspondem ao escopo e aos atributos de reserva. Você não precisa atribuir uma reserva a um host dedicado para obter os descontos. Uma compra de instância reservada abrange apenas a parte de computação do seu uso e inclui os custos de licenciamento de software. Consulte a [visão geral dos hosts dedicados do Azure para máquinas virtuais](./dedicated-hosts.md).

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>Determine o SKU do host dedicado correto antes de comprar


Antes de comprar uma reserva, você deve determinar qual host dedicado é necessário. Uma SKU é definida para um host dedicado que representa a série e o tipo da VM. 

Comece passando os tamanhos com suporte para a [máquina virtual do Windows](./sizes.md) ou para o [Linux](./sizes.md) para identificar a série de VMs.

Em seguida, verifique se há suporte para os hosts dedicados do Azure. A página de [preços dos hosts dedicados do Azure](https://aka.ms/ADHPricing) tem a lista completa de SKUs de hosts dedicados, suas informações de CPU e várias opções de preços (incluindo instâncias reservadas).

Você pode encontrar várias SKUs que dão suporte a uma série de VMs (com tipos diferentes). Identifique a melhor SKU comparando a capacidade do host (número de vCPUs). Observe que você poderá aplicar sua reserva a várias SKUs de hosts dedicados que dão suporte à mesma série de VMs (por exemplo DSv3_Type1 e DSv3_Type2), mas não em séries de VM diferentes (como DSv3 e ESv3).



## <a name="purchase-restriction-considerations"></a>Considerações sobre a restrição de compra

As instâncias reservadas estão disponíveis para os tamanhos de host mais dedicados, com algumas exceções.

Os descontos de reserva não se aplicam ao seguinte:

- **Nuvens**: as reservas não estão disponíveis para compra nas regiões da Alemanha ou da China.

- **Cota insuficiente** – uma reserva com escopo para uma única assinatura deve ter a cota vCPU disponível na assinatura para a nova instância reservada. Por exemplo, se a assinatura de destino tiver um limite de cota de 10 vCPUs para a série DSv3, você não poderá comprar uma reserva de hosts dedicados que dão suporte a essa série. A verificação de cota para reservas inclui as VMs e os hosts dedicados já implantados na assinatura. Você pode [criar uma solicitação de aumento de cota](../azure-portal/supportability/resource-manager-core-quotas-request.md) para resolver esse problema.

- **Restrições de capacidade** -em raras circunstâncias, o Azure limita a compra de novas reservas para o subconjunto de SKUs de host dedicado, devido à baixa capacidade em uma região.

## <a name="buy-a-reservation"></a>Comprar uma reserva

Você pode comprar uma instância reservada de uma instância de host dedicada do Azure no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Pague pela reserva [antecipadamente ou com pagamentos mensais](../cost-management-billing/reservations/prepare-buy-reservation.md). Esses requisitos se aplicam à compra de uma instância de host dedicada reservada:

- Você precisa ter a função Proprietário em, no mínimo, uma assinatura do EA ou uma assinatura com uma taxa paga conforme o uso.

- Para as assinaturas do EA, a opção **Adicionar Instâncias Reservadas** precisa estar habilitada no [Portal do EA](https://ea.azure.com/). Ou, se essa configuração estiver desabilitada, você precisará ser um Administrador de EA da assinatura.

- Para o programa do CSP (Provedor de Solução na Nuvem) somente os agentes administradores ou agentes de vendas podem comprar reservas.

Para comprar uma instância:

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Selecione **Todos os serviços** \> **Reservas**.

3. Selecione **Adicionar** para comprar uma nova reserva e clique em **hosts dedicados**.

4. Preencha os campos obrigatórios. Executar instâncias de hosts dedicadas que correspondem aos atributos que você selecionar qualificar para obter o desconto de reserva. O número real de suas instâncias de host dedicadas que obtêm o desconto depende do escopo e da quantidade selecionada.

Se você tiver um contrato EA, poderá usar a opção **Adicionar mais** para adicionar mais instâncias rapidamente. A opção não está disponível para outros tipos de assinaturas.

| **Campo**           | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subscription        | A assinatura usada para pagar pela reserva. Os custos da reserva são cobrados segundo a forma de pagamento da assinatura. O tipo de assinatura deve ser um contrato empresarial (números da oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Contrato de Cliente da Microsoft ou ainda uma assinatura individual com tarifas pagas conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Os preços são deduzidos do saldo do Pagamento antecipado do Azure (anteriormente conhecido como compromisso monetário), se disponível, ou cobrados como excedente. Para uma assinatura com taxas pagas conforme o uso, as cobranças são feitas na forma de pagamento de cartão de crédito ou de fatura na assinatura. |
| Escopo               | O escopo de assinatura pode abranger uma ou várias assinaturas (escopo compartilhado). Se você selecionar:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Região              | A região do Azure que é coberta pela reserva.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Tamanho de host dedicado | O tamanho das instâncias de host dedicadas.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Termo                | Um ano ou três anos.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Quantidade            | O número de instâncias sendo compradas na reserva. A quantidade é o número de instâncias de host dedicadas em execução que podem obter o desconto de cobrança.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **Escopo de grupo de recursos único** — aplica o desconto de reserva apenas aos recursos correspondentes no grupo de recursos selecionado.

- **Escopo de assinatura única** — aplica o desconto de reserva apenas aos recursos correspondentes na assinatura selecionada.

- **Escopo compartilhado** — aplica o desconto de reserva aos recursos correspondentes em assinaturas qualificadas que estão no contexto de cobrança. Para clientes do EA, o contexto de cobrança é o registro. Para assinaturas individuais com tarifas pagas conforme o uso, o escopo do orçamento são todas as assinaturas qualificadas criadas pelo administrador da conta.

## <a name="usage-data-and-reservation-utilization"></a>Dados de uso e utilização de reserva

Seus dados de uso têm um preço efetivo de zero para o uso, que obtém um desconto de reserva. Você pode ver qual instância de VM recebeu o desconto de reserva para cada reserva.

Para obter mais informações sobre como os descontos de reserva aparecem nos dados de uso, consulte [entender o uso de reserva do Azure para o registro de sua empresa](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) se você for um cliente do ea. Se você tiver uma assinatura individual, consulte [entender o uso de reserva do Azure para sua assinatura paga conforme o uso](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Alterar uma reserva após a compra

É possível realizar os seguintes tipos de alterações em uma reserva após a compra:

- Atualizar o escopo de reserva

- Flexibilidade de tamanho de instância (se aplicável)

- Propriedade

Você também pode dividir uma reserva em partes menores e mesclar reservas já divididas. Nenhuma das alterações causa uma nova transação comercial ou alterar a data de término da reserva.

Você não pode fazer os seguintes tipos de alterações após a compra, diretamente:

- Uma região de reserva existente

- SKU

- Quantidade

- Duration

No entanto, você pode *trocar* uma reserva se desejar fazer alterações.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

Para aprender a gerenciar uma reserva, confira [Gerenciar Reservas do Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são Reservas do Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)

- [Usando Hosts Dedicados do Azure](./dedicated-hosts.md)

- [Preço de Hosts Dedicados](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Gerenciar Reservas no Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)

- [Entender como o desconto de reserva é aplicado](../cost-management-billing/manage/understand-vm-reservation-charges.md)

- [Noções básicas sobre o uso de reserva para uma assinatura com taxas pagas conforme o uso](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

- [Entender o uso de reserva para seu registro de empresa](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)

- [Custos de software do Windows não estão incluídos nas reservas](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)

- [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](/partner-center/azure-reservations)