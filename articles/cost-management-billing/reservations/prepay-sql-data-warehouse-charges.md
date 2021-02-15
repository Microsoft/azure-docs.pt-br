---
title: Economize em encargos do Azure Synapse Analytics com a capacidade reservada do Azure
description: Saiba como você economiza custos de encargos do Azure Synapse Analytics com capacidade reservada para poupar dinheiro.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: bd43b668c318b825c5c5b6f36fc1da1055863bed
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599093"
---
# <a name="save-costs-for-azure-synapse-analytics-charges-with-reserved-capacity"></a>Economize custos para encargos do Azure Synapse Analytics com capacidade reservada

Você pode poupar dinheiro com o Azure Synapse Analytics comprometendo-se com uma reserva para o uso de cDWU previamente durante um ou três anos. Para comprar a capacidade reservada do Azure Synapse Analytics, você precisa escolher a região do Azure e o termo. Em seguida, adicione o SKU do Azure Synapse Analytics ao seu carrinho e escolha a quantidade de unidades de cDWU que deseja comprar.

Quando você compra uma reserva, o uso do Azure Synapse Analytics que corresponde aos atributos da reserva deixa de ser cobrado segundo as taxas de pagamento conforme o uso.

Uma reserva não abrange os encargos de armazenamento ou de rede associados ao uso do Azure Synapse Analytics.

Quando a capacidade reservada expira, as instâncias do Azure Synapse Analytics continuam a ser executadas, mas são cobradas pela taxa de pagamento conforme o uso. As reservas não são renovadas automaticamente.

Para obter informações sobre preços, confira a [Oferta de capacidade reservada do Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

Você pode comprar a capacidade reservada do Azure Synapse Analytics no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Pague pela reserva [antecipadamente ou com pagamentos mensais](./prepare-buy-reservation.md). Para comprar uma capacidade reservada:

- Você precisa ter a função de proprietário de pelo menos uma assinatura Enterprise ou paga conforme o uso.
- Para assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal do EA](https://ea.azure.com/). Se essa configuração estiver desabilitada, você precisará ser um administrador de EA.
- Para o programa Provedor de Soluções de Nuvem (CSP), apenas os agentes admin ou agentes de vendas podem adquirir a capacidade reservada do Azure Synapse Analytics.

Para saber mais sobre como clientes empresariais e clientes do método de Pagamento Conforme o Uso são cobrados para compras de reserva, confira [Entenda o uso de reserva do Azure para seu Registro Enterprise](understand-reserved-instance-usage-ea.md) e [Entenda o uso de reserva do Azure para sua assinatura paga conforme o uso](understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Escolher o tamanho correto antes da compra

O tamanho da reserva do Azure Synapse Analytics deve ser baseado no total de cDWU (unidades de data warehouse de computação) que você consome. As compras são feitas em incrementos de 100 cDWU.

Por exemplo, suponha que o consumo total do Azure Synapse Analytics seja DW3000c. Você deseja comprar a capacidade reservada referente a todo esse consumo. Sendo assim, você precisa comprar 30 unidades de capacidade reservada de cDWU.

## <a name="buy-azure-synapse-analytics-reserved-capacity"></a>Comprar capacidade reservada do Azure Synapse Analytics

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione uma assinatura. Use a lista Assinatura para escolher a assinatura usada para pagar a capacidade reservada. A forma de pagamento da assinatura é cobrada nos custos da capacidade reservada. O tipo de assinatura deve ser um contrato empresarial (números da oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Pagamento Conforme o Uso (números da oferta: MS-AZR-0003P ou MS-AZR-0023P).
   - Para uma assinatura corporativa, os encargos são deduzidos do saldo do Pagamento antecipado do Azure do registro (chamado anteriormente de compromisso monetário) ou cobrados como excedente.
   - Para a assinatura de Pagamento Conforme o Uso, as cobranças são feitas ao cartão de crédito ou à forma de pagamento de faturas na assinatura.
4. Selecione um escopo. Use a lista Escopo para escolher um escopo da assinatura.
   - **Escopo de grupo de recursos único** — aplica o desconto de reserva apenas aos recursos correspondentes no grupo de recursos selecionado.
   - **Escopo de assinatura única** — aplica o desconto de reserva apenas aos recursos correspondentes na assinatura selecionada.
   - **Escopo compartilhado** — aplica o desconto de reserva aos recursos correspondentes em assinaturas qualificadas que estão no contexto de cobrança. Para clientes do Contrato Enterprise, o contexto de cobrança é o registro. Para assinaturas individuais com tarifas pagas conforme o uso, o escopo do orçamento são todas as assinaturas qualificadas criadas pelo administrador da conta.
   - Para clientes empresariais, o contexto de cobrança é o registro do EA.
   - Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.
5. Selecione uma região a fim de escolher a região do Azure abrangida pela capacidade reservada.
6. Escolha uma quantidade. Insira a quantidade de unidades de 100 cDWUs (unidades de data warehouse) que você deseja comprar.    
   Por exemplo, uma quantidade de 30 forneceria 3.000 cDWU de capacidade reservada por hora.
7. Examine o custo de reserva de capacidade reservada do Azure Synapse Analytics na seção **Custos**.
8. Selecione **Comprar**.
9. Selecione **Exibir essa Reserva** para ver o status de sua compra.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](exchange-and-refund-azure-reservations.md).

Um desconto de reserva é aplicado automaticamente ao número de instâncias do Azure Synapse Analytics que correspondem à região e ao escopo da capacidade reservada do Azure Synapse Analytics. Você pode atualizar o escopo da capacidade reservada do Azure Synapse Analytics por meio do [Portal do Azure](https://portal.azure.com/), PowerShell, CLI ou por meio da API.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como os descontos de reserva se aplicam ao Azure Synapse Analytics, confira [Como os descontos de reserva se aplicam ao Azure Synapse Analytics](prepay-sql-data-warehouse-charges.md).

- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
  - [O que são Reservas do Azure?](save-compute-costs-reservations.md)
  - [Gerenciar Reservas do Azure](manage-reserved-vm-instance.md)
  - [Compreender o desconto de Reservas do Azure](understand-reservation-charges.md)
  - [Entender o uso de reserva para a sua assinatura paga conforme o uso](understand-reserved-instance-usage.md)
  - [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)