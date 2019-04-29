---
title: As trocas de autoatendimento e os reembolsos para reservas do Azure | Microsoft Docs
description: Saiba como você pode trocar ou o reembolso de reservas do Azure.
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 54578746ea8029a760663edc456660f98358abc5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615981"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>As trocas de autoatendimento e os reembolsos para reservas do Azure

Reservas do Azure fornecem flexibilidade para atender às suas necessidades em constante evolução. Você pode trocar uma reserva por outra do mesmo tipo. Você também poderá pedir reembolso de uma reserva, de até USD 50.000 por ano, se não precisar mais dela.

Os recursos de cancelamento e troca de autoatendimento não estão disponíveis para clientes do US Government Enterprise Agreement. Há suporte para outros tipos de assinatura do governo dos EUA incluindo pagamento conforme o uso e o CSP.

Você deve ter acesso de proprietário no pedido de reserva para trocar ou reembolso de uma reserva existente.

## <a name="exchange-an-existing-reserved-instance"></a>Uma instância reservada existente do Exchange

Você pode trocar sua reserva com três etapas rápidas na [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Selecione as reservas de que você deseja reembolso e clique em **Exchange**.  
    ![Imagem de exemplo que mostra as reservas para retornar](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. Selecione o produto de VM que você deseja comprar e digite uma quantidade. Certifique-se de que o novo total de compra é maior que o total de retorno. [Determinar o tamanho correto antes de adquirir](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Imagem de exemplo que mostra o produto VM para aquisição com o exchange](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. Revise e conclua a transação.  
    ![Imagem de exemplo que mostra o produto VM para aquisição com uma troca, Concluindo o retorno](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

Para reembolso de uma reserva, vá para **detalhes de reserva** e clique em **reembolso**.

## <a name="how-transactions-are-processed"></a>Como as transações são processadas

Em primeiro lugar, a Microsoft cancela a reserva existente e reembolsos a quantidade proporcional para essa reserva. Se houver uma troca, a nova compra é processada. Microsoft processa reembolsos usando um dos métodos a seguir, dependendo do seu tipo de conta e método de pagamento:

### <a name="enterprise-agreement-customers"></a>Clientes do Enterprise agreement

Dinheiro é adicionado ao compromisso monetário por trocas e reembolsos se a compra original tiver sido feita usando um. Todas as faturas excedentes, pois as compras originais são reabertas e rerated para certificar-se de compromisso monetário é usado. Se o termo de compromisso monetário usando que a reserva foi comprada não está mais ativo, crédito é adicionado ao seu período atual de compromisso monetário de contrato enterprise.

Se a compra original tiver sido feita como um excedente, a Microsoft emite um memorando de crédito.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Pagamentos de nota fiscal pago conforme o uso e o programa CSP

A fatura original de compra de reserva é cancelada e, em seguida, uma nova fatura é criada para obter o reembolso. Trocas, a nova fatura mostra o reembolso e nova compra. A quantidade de reembolso é ajustada em relação a compra. Se você apenas reembolsada uma reserva, em seguida, a quantidade proporcional permanece com a Microsoft e ele será ajustado em relação a uma compra de reserva futuras.

### <a name="pay-as-you-go-credit-card-customers"></a>Clientes de cartão de crédito de pré-pago

A fatura original é cancelada e uma nova fatura é criada. O dinheiro será reembolsado ao cartão de crédito que foi usado para a compra original. Se você tiver alterado seu cartão [entre em contato com o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="exchange-policies"></a>Políticas do Exchange

- Você pode retornar várias reservas existentes para comprar uma nova reserva do mesmo tipo. Você não pode trocar as reservas de um tipo para outro. Por exemplo, você não pode retornar uma reserva de VM para comprar uma reserva de SQL.
- Somente os proprietários de reserva podem processar uma troca. [Saiba como adicionar ou alterar os usuários podem gerenciar uma reserva](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance#add-or-change-users-who-can-manage-a-reservation).
- Uma troca é processada como um reembolso e nova compra – transações diferentes são criadas para o cancelamento e nova compra. A quantidade proporcional de reserva será reembolsada para as reservas de que você trade. Você será cobrado totalmente para a compra de novo. A quantidade proporcional de reserva é o valor residual proporcional diário da reserva que está sendo retornado.
- Você pode trocar ou reservas de reembolso, mesmo se o contrato enterprise usados para comprar a reserva expirou e foi renovado como um novo contrato.
- Você pode alterar qualquer propriedade de reserva, como tamanho, região, quantidade e termo com uma troca.
- O novo total de compra deve ser igual ou ser maior que o valor retornado.
- A nova reserva adquirida como parte do exchange tem um novo termo a partir do momento do exchange.
- Não há nenhuma penalidade ou limites de anuais para trocas.

## <a name="refund-policies"></a>Políticas de reembolso

- O valor de reembolso total não pode exceder US $ 50.000 em uma janela de 12 meses sem interrupção.
- Somente os proprietários de reserva podem processar um reembolso. [Saiba como adicionar ou alterar os usuários podem gerenciar uma reserva](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- A Microsoft se reserva o direito de cobrar uma penalidade de 12% para qualquer retorna, embora a penalidade no momento, não é cobrada.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Armazenamento não premium para o armazenamento premium do Exchange

Você pode trocar uma reserva adquirida para um tamanho de VM que não oferece suporte a armazenamento premium para um tamanho VM correspondente que faz. Por exemplo, um _F1_ para um _F1s_. Para tornar o exchange, vá para detalhes de reserva e clique em **Exchange**. O exchange não redefinir o termo da instância reservada ou crie uma nova transação.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

- Para aprender a gerenciar uma reserva, confira [Gerenciar Reservas do Azure](billing-manage-reserved-vm-instance.md).
- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
    - [O que são Reservas do Azure?](billing-save-compute-costs-reservations.md)
    - [Gerenciar Reservas no Azure](billing-manage-reserved-vm-instance.md)
    - [Entender como o desconto de reserva é aplicado](billing-understand-vm-reservation-charges.md)
    - [Entender o uso de reserva para a sua assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md)
    - [Entender o uso de reserva para seu registro de empresa](billing-understand-reserved-instance-usage-ea.md)
    - [Custos de software do Windows não estão incluídos nas reservas](billing-reserved-instance-windows-software-costs.md)
    - [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](/partner-center/azure-reservations)
