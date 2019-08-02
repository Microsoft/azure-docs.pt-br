---
title: Pagar antecipadamente por SQL Data Warehouse encargos com a capacidade reservada do Azure
description: Saiba como você pode pagar por SQL Data Warehouse encargos com capacidade reservada para economizar dinheiro.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: banders
ms.openlocfilehash: 08c9f958f5103da9961f4c2d29be97f455cecc4b
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359258"
---
# <a name="prepay-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Pagar antecipadamente por SQL Data Warehouse encargos com capacidade reservada

Você pode economizar dinheiro com o Azure SQL Data Warehouse prestando pelo uso de cDWU por uma duração de um ou três anos. Para comprar SQL Data Warehouse capacidade reservada, você precisa escolher a região do Azure e o termo. Em seguida, adicione o SQL Data Warehouse SKU ao seu carrinho e escolha a quantidade de unidades cDWU que você deseja comprar.

Quando você adquire uma reserva, o uso de SQL Data Warehouse que corresponde aos atributos de reserva não é mais cobrado com as tarifas de pagamento conforme o uso.

Uma reserva não abrange os encargos de armazenamento ou de rede associados ao uso de SQL Data Warehouse.

Quando a capacidade reservada expira, SQL Data Warehouse instâncias continuam a ser executadas, mas são cobradas com a taxa paga conforme o uso. As reservas não são renovadas automaticamente.

Para obter informações sobre preços, consulte a [SQL data warehouse oferta de capacidade reservada](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

Você pode comprar o Azure SQL Data Warehouse capacidade reservada no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Para comprar uma capacidade reservada:

- Você deve ter a função de proprietário para pelo menos uma assinatura corporativa ou paga conforme o uso.
- Para assinaturas Enterprise, a opção **adicionar instâncias reservadas** deve ser habilitada no [portal de ea](https://ea.azure.com/). Se a configuração estiver desabilitada, você deverá ser um administrador do EA.
- Para o programa CSP (provedor de soluções na nuvem), somente os agentes de administração ou os agentes de vendas podem comprar SQL Data Warehouse capacidade reservada.

Para obter mais informações sobre como clientes corporativos e clientes pagos conforme o uso são cobrados por compras de reserva, consulte [entender o uso de reserva do Azure para seu registro corporativo](billing-understand-reserved-instance-usage-ea.md) e [entender o uso de reserva do Azure para seu Assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Escolha o tamanho certo antes da compra

O tamanho da reserva de SQL Data Warehouse deve ser baseado no total de cDWU (unidades de data warehouse de computação) que você consumir. As compras são feitas em incrementos de 100 cDWU.

Por exemplo, suponha que o consumo total de SQL Data Warehouse seja DW3000c. Você deseja comprar a capacidade reservada para todas elas. Portanto, você deve comprar 30 unidades de capacidade reservada cDWU.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Comprar SQL Data Warehouse capacidade reservada

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecionar uma assinatura. Use a lista assinatura para escolher a assinatura que é usada para pagar pela capacidade reservada. O método de pagamento da assinatura é cobrado pelos custos iniciais da capacidade reservada. O tipo de assinatura deve ser um contrato empresarial (números da oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Pagamento Conforme o Uso (números da oferta: MS-AZR-0003P ou MS-AZR-0023P).
   - Para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como média.
   - Para a assinatura de Pagamento Conforme o Uso, as cobranças são feitas ao cartão de crédito ou à forma de pagamento de faturas na assinatura.
4. Selecione um escopo. Use a lista escopo para escolher um escopo de assinatura.
   - **Escopo do grupo de recursos único** — aplica o desconto de reserva aos recursos correspondentes somente no grupo de recursos selecionado.
   - **Escopo de assinatura única** — aplica o desconto de reserva aos recursos de correspondência na assinatura selecionada.
   - **Escopo compartilhado** — aplica o desconto de reserva aos recursos correspondentes em assinaturas qualificadas que estão no contexto de cobrança. Para clientes Enterprise Agreement, o contexto de cobrança é o registro. Para assinaturas individuais com tarifas pagas conforme o uso, o escopo de cobrança é todas as assinaturas qualificadas criadas pelo administrador da conta.
   - Para clientes corporativos, o contexto de cobrança é o registro de EA.
   - Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.
5. Selecione uma região para escolher uma região do Azure coberta pela capacidade reservada.
6. Escolha uma quantidade. Insira a quantidade de 100 cDWU (unidades de data warehouse) que você deseja comprar.    
   Por exemplo, uma quantidade de 30 forneceria 3.000 cDWU de capacidade reservada a cada hora.
7. Examine o custo de reserva de capacidade reservada SQL Data Warehouse na seção de **custos** .
8. Selecione **Comprar**.
9. Selecione **exibir esta reserva** para ver seu status de compra.

## <a name="cancellations-and-exchanges"></a>Cancelamentos e trocas

Se você precisar cancelar sua SQL Data Warehouse capacidade reservada, pode haver uma taxa de rescisão de 12% no início. Os reembolsos baseiam-se no preço de compra ou no preço atual da reserva, o que for mais baixo. Os reembolsos são limitados a $50000 por ano. O reembolso que você recebe é o saldo rateado restante menos a taxa de rescisão inicial de 12%. Para cancelar, vá para a reserva no portal do Azure e selecione **reembolso**.

Se você precisar alterar sua SQL Data Warehouse capacidade reservada para outra região ou termo, poderá Exchange para outra reserva de valor igual ou maior. A data de início do prazo da nova reserva não é a mesma da reserva trocada. O termo de um ou três anos começa quando você cria a nova reserva. Para o Exchange, abra a reserva no portal do Azure e selecione **Exchange**.

Para obter mais informações sobre como trocar ou reembolsar reservas, consulte trocas [e reembolsos de reserva](billing-azure-reservations-self-service-exchange-and-refund.md).

O desconto de reserva é aplicado automaticamente ao número de instâncias de SQL Data Warehouse que correspondem à região e ao escopo da capacidade reservada SQL Data Warehouse. Você pode atualizar o escopo do SQL Data Warehouse capacidade reservada com o [portal do Azure](https://portal.azure.com/), o PowerShell, a CLI ou por meio da API.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contate-nos

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como os descontos de reserva se aplicam ao SQL Data Warehouse do Azure, confira [como os descontos de reserva se aplicam ao azure SQL data warehouse](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md).

- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
  - [O que são Reservas do Azure?](billing-save-compute-costs-reservations.md)
  - [Gerenciar Reservas do Azure](billing-manage-reserved-vm-instance.md)
  - [Compreender o desconto de Reservas do Azure](billing-understand-reservation-charges.md)
  - [Entender o uso de reserva para a sua assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md)
  - [Entender o uso de reserva para seu registro de empresa](billing-understand-reserved-instance-usage-ea.md)
