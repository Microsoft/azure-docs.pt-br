---
title: Pagar antecipadamente pela taxa de carimbo isolado do serviço de Azure App com capacidade reservada
description: Saiba como você pode pagar por Azure App taxa de carimbo isolado de serviço com capacidade reservada para economizar dinheiro.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: banders
ms.openlocfilehash: 40ccee7a993ce39a9b4c7a86309b0554daa56026
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298263"
---
# <a name="prepay-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>Pagar antecipadamente pela taxa de carimbo isolado do serviço de Azure App com capacidade reservada

Você pode economizar dinheiro em taxas de carimbo isolado de serviço Azure App por prepagar pelo uso de seu carimbo por uma duração de três anos. Para comprar a capacidade reservada da taxa de carimbo isolado, você precisa escolher a região do Azure onde o carimbo será implantado e o número de carimbos a serem comprados.

Quando você adquire uma reserva, o uso da taxa de selo isolado que corresponde aos atributos de reserva não é mais cobrado com as tarifas de pagamento conforme o uso. A reserva é aplicada automaticamente ao número de carimbos isolados que correspondem à região e ao escopo da capacidade reservada. Você não precisa atribuir uma reserva a um carimbo isolado. A reserva não se aplica a trabalhadores, portanto, quaisquer outros recursos associados ao carimbo serão cobrados separadamente.

Quando a capacidade reservada expira, carimbos isolados continuam a ser executados, mas são cobrados com a taxa paga conforme o uso. As reservas não são renovadas automaticamente.

## <a name="determine-the-right-reservation-to-purchase"></a>Determinar a reserva correta a ser comprada

Ao comprar uma reserva, você está pagando por usar quantidades reservadas o tempo todo ao longo dos próximos três anos. Verifique os dados de uso para determinar quantos Serviço do Aplicativo Isolado carimbos você está usando consistentemente e pode usar no futuro.

Além disso, certifique-se de entender como o carimbo isolado emite o medidor do Linux ou do Windows.

- Por padrão, um carimbo isolado vazio emite o medidor de selo do Windows. Por exemplo, sem trabalhos implantados. Ele continuará emitindo esse medidor se os trabalhos do Windows estiverem implantados no carimbo.
- O medidor muda para o medidor de selo do Linux se você implantar um trabalho do Linux.
- Nos casos em que os trabalhadores do Linux e do Windows são implantados, o carimbo emite o medidor do Windows.

Portanto, o medidor de selo pode mudar entre o Windows e o Linux durante a vida útil do selo.

Compre reservas de selo do Windows se você tiver um ou mais trabalhadores do Windows no carimbo. A única vez que você deve comprar uma reserva de carimbo do Linux é se planeja ter _apenas_ trabalhadores do Linux no selo.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Comprar capacidade reservada de carimbo isolado

Você pode comprar a capacidade reservada de carimbo isolado no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D). Para comprar a capacidade reservada, você deve ter a função de proprietário de pelo menos uma assinatura da empresa ou uma assinatura individual com tarifas pagas conforme o uso.

- Para assinaturas Enterprise, a opção **adicionar instâncias reservadas** deve ser habilitada no [portal de ea](https://ea.azure.com/). Ou, se a configuração estiver desabilitada, você deverá ser um administrador do EA.
- Para o programa CSP (provedor de soluções na nuvem), somente os agentes de administração ou os agentes de vendas podem comprar SQL Data Warehouse capacidade reservada.

**Para comprar:**

1. Vá para a [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Selecione uma assinatura. Use a lista **assinatura** para escolher a assinatura que é usada para pagar pela capacidade reservada. O método de pagamento da assinatura é cobrado pelos custos iniciais da capacidade reservada. O tipo de assinatura deve ser um contrato empresarial (números da oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Pagamento Conforme o Uso (números da oferta: MS-AZR-0003P ou MS-AZR-0023P) ou uma assinatura CSP.
    - Para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como média.
    - Para a assinatura de Pagamento Conforme o Uso, as cobranças são feitas ao cartão de crédito ou à forma de pagamento de faturas na assinatura.
1. Selecione um **escopo** para escolher um escopo de assinatura.
    - **Escopo do grupo de recursos único** — aplica o desconto de reserva aos recursos correspondentes somente no grupo de recursos selecionado.
    - **Escopo de assinatura única** — aplica o desconto de reserva aos recursos de correspondência na assinatura selecionada.
    - **Escopo compartilhado** — aplica o desconto de reserva aos recursos correspondentes em assinaturas qualificadas que estão no contexto de cobrança. Para clientes Enterprise Agreement, o contexto de cobrança é o registro. Para assinaturas individuais com tarifas pagas conforme o uso, o escopo de cobrança é todas as assinaturas qualificadas criadas pelo administrador da conta.
1. Selecione uma **região** para escolher uma região do Azure coberta pela capacidade reservada e adicione a reserva ao carrinho.
1. Selecione um tipo de plano isolado e clique em **selecionar**.  
    ![Exemplo](./media/billing-prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. Insira a quantidade de carimbos de Serviço do Aplicativo Isolado para reservar. Por exemplo, uma quantidade de três forneceria três carimbos reservados para uma região. Clique em **Avançar: Examine + comprar**.
1. Examine e clique em **comprar agora**.

Após a compra, acesse [reservas](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) para exibir o status de compra e monitorá-lo a qualquer momento.

## <a name="cancellations-and-exchanges"></a>Cancelamentos e trocas

Se você precisar cancelar a capacidade reservada de carimbo isolado, poderá haver uma taxa de rescisão de 12% no início. Os reembolsos baseiam-se no preço de compra ou no preço atual da reserva, o que for mais baixo. Os reembolsos são limitados a $50000 por ano. O reembolso que você recebe é o saldo rateado restante menos a taxa de rescisão inicial de 12%. Para cancelar, vá para a reserva no portal do Azure e selecione **reembolso**.

Se você precisar mover a capacidade reservada de carimbo isolado para outra região, poderá Exchange para outra reserva que seja de valor igual ou maior. A data de início do prazo da nova reserva não é a mesma da reserva trocada. Um termo de três anos começa quando você cria a nova reserva. Para o Exchange, acesse a portal do Azure, selecione a reserva que você deseja trocar e selecione **Exchange**.

Para obter mais informações sobre como trocar ou reembolsar reservas, consulte trocas [e reembolsos de reserva](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="discount-application-shown-in-usage-data"></a>Aplicativo de desconto mostrado nos dados de uso

Os dados de uso têm um preço efetivo de zero para o uso que obtém um desconto de reserva. Os dados de uso mostram o desconto de reserva para cada instância de carimbo em cada reserva.

Para obter mais informações sobre como o desconto de reserva é mostrado nos dados de uso, consulte [obter Enterprise Agreement custos de reserva e uso](billing-understand-reserved-instance-usage-ea.md) se você for um cliente de Enterprise Agreement (ea). Caso contrário, veja [o uso de reserva do Azure para sua assinatura individual com tarifas](billing-understand-reserved-instance-usage.md)pré-pagas.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
  - [O que são Reservas do Azure?](billing-save-compute-costs-reservations.md)
  - [Entenda como um desconto de reserva de carimbo isolado do serviço de Azure App é aplicado](billing-reservation-discount-app-service-isolated-stamp.md)
  - [Entender o uso de reserva para seu registro de empresa](billing-understand-reserved-instance-usage-ea.md)
