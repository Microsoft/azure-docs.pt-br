---
title: Trocas e reembolsos realizados via autoatendimento nas Reservas do Azure
description: Saiba como você pode trocar ou reembolsar as Reservas do Azure. Você precisa ter acesso de proprietário ao Pedido de Reserva para trocar ou reembolsar reservas.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 12/15/2020
ms.author: banders
ms.openlocfilehash: 045ab35a35aa4caefb1e1bcbbf7bf78b726c09f7
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601465"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Trocas e reembolsos realizados via autoatendimento nas Reservas do Azure

As Reservas do Azure fornecem flexibilidade para ajudar a atender às suas necessidades em constante evolução. Você pode trocar uma reserva por outra do mesmo tipo. Por exemplo, você pode trocar uma reserva de máquina virtual para comprar outra reserva de qualquer outro tamanho ou região de VM. Da mesma forma, uma reserva do Banco de Dados de PaaS do SQL pode ser trocada para comprar outra reserva de qualquer tipo ou região do Banco de Dados de PaaS do SQL. Você também pode reembolsar reservas, mas a soma total de todos os compromissos de reserva cancelados em seu escopo do orçamento (como EA, Contrato de Cliente da Microsoft e Contrato de Parceiro da Microsoft) não pode ultrapassar US$ 50.000 em uma janela ininterrupta de 12 meses. A capacidade reservada do Azure Databricks, a reserva da solução VMware da CloudSimple no Azure, a reserva do Azure Red Hat Open Shift, planos do Red Hat e planos do SUSE Linux não são elegíveis para reembolsos.

Os recursos de cancelamento e troca de autoatendimento não estão disponíveis para clientes do US Government Enterprise Agreement. Há suporte para outros tipos de assinatura do governo dos EUA, incluindo Pagamento Conforme o Uso e CSP (Provedor de Solução de Nuvem).

> [!NOTE]
> - **Você precisa ter acesso de proprietário ao Pedido de Reserva para trocar ou reembolsar uma reserva existente**. Você pode [Adicionar ou alterar os usuários que podem gerenciar uma reserva](./manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).
> - Atualmente, a Microsoft não está cobrando valores de término antecipado para reembolsos de reserva. Poderemos cobrar valores para reembolsos feitos no futuro. No momento, não temos uma data para habilitar o valor.

## <a name="how-to-exchange-or-refund-an-existing-reservation"></a>Como trocar ou reembolsar uma reserva existente

Você pode trocar sua reserva no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Selecione as reservas que deseja reembolsar e selecione **Trocar**.  
    [![Exemplo de imagem mostrando reservas a serem devolvidas](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png#lightbox)
1. Selecione o produto de VM que você deseja comprar e digite uma quantidade. Verifique se o novo total de compra é maior que o total de devolução. [Determine o tamanho correto antes da compra](../../virtual-machines/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    [![Exemplo de imagem mostrando o produto da VM a ser comprado com uma troca](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png#lightbox)
1. Examine e conclua a transação.  
    [![Exemplo de imagem mostrando o produto da VM a ser comprado com uma troca, concluindo a devolução](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png#lightbox)

Para reembolsar uma reserva, acesse **Detalhes da Reserva** e selecione **Reembolsar**.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Trocar armazenamento não Premium por armazenamento Premium

Você pode trocar uma reserva comprada para um tamanho de VM que não é compatível com o armazenamento Premium por um tamanho de VM correspondente que é compatível. Por exemplo, uma _F1_ por uma _F1s_. Para fazer a troca, acesse os Detalhes da Reserva e selecione **Trocar**. A troca não redefine o prazo da instância reservada, nem cria uma nova transação. 

## <a name="how-transactions-are-processed"></a>Como as transações são processadas

Primeiro, a Microsoft cancela a reserva existente e reembolsa o valor proporcional àquela reserva. Caso haja uma troca, a nova compra será processada. A Microsoft processa os reembolsos por meio de um dos seguintes métodos, dependendo do tipo de conta e da forma de pagamento:

### <a name="enterprise-agreement-customers"></a>Clientes do Contrato Enterprise

Adiciona-se dinheiro ao Pagamento antecipado do Azure (anteriormente conhecido como compromisso monetário) para trocas e reembolsos caso a compra original tenha sido feita usando tal. Se o termo do Pagamento antecipado do Azure que usa a reserva foi comprado não estiver mais ativo, o crédito será adicionado ao termo do Pagamento antecipado do Azure do seu Contrato Enterprise atual. O crédito é válido por 90 dias a partir da data do reembolso. Créditos não utilizados expiram no final dos 90 dias.

Se a compra original foi feita como excedente, a fatura original na qual a reserva foi adquirida e todas as notas fiscais posteriores serão reabertas e reajustadas. A Microsoft emite um memorando de crédito para os reembolsos.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Pagamentos de fatura pagos conforme o uso e programa CSP

A fatura de compra da reserva original é cancelada e uma nova fatura é criada para o reembolso. Para trocas, a nova fatura mostra o reembolso e a nova compra. O valor do reembolso é ajustado em relação à compra. Se você reembolsou apenas uma reserva, o valor proporcional permanece com a Microsoft e é ajustado em uma compra futura de reserva. Se você comprou uma reserva por tarifas de pagamento conforme o uso e depois mudou para um CSP, a reserva pode ser devolvida e recomprada sem multas.

### <a name="pay-as-you-go-credit-card-customers"></a>Clientes de cartão de crédito pago conforme o uso

A fatura original é cancelada e uma nova fatura é gerada. O dinheiro é reembolsado no cartão de crédito que foi usado para realizar a compra original. Caso tenha mudado de cartão, [entre em contato com o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>Políticas de cancelamento, troca e reembolso

O Azure tem as políticas de cancelamento, troca e reembolso a seguir.

**Políticas de troca**

- Você pode devolver várias reservas existentes a fim de comprar uma nova reserva do mesmo tipo. Não é permitido trocar reservas de um tipo por outro. Por exemplo, você não pode devolver uma reserva de VM para comprar uma reserva de SQL. Você pode alterar a propriedade de uma reserva, como família, série, versão, SKU, região, quantidade e prazo com uma troca.
- Somente os proprietários de reserva podem processar uma troca. [Saiba como adicionar ou alterar os usuários que podem gerenciar uma reserva](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).
- Uma troca é processada como um reembolso seguido de uma nova compra – diferentes transações são criadas para o cancelamento e a nova compra de reserva. O valor rateado da reserva que você troca é reembolsado. O valor integral é cobrado pela nova compra. O valor proporcional da reserva é o valor residual diário proporcional da reserva que está sendo devolvida.
- Você pode trocar ou reembolsar reservas mesmo que o Contrato Enterprise usado para comprar a reserva esteja expirado e tenha sido renovado como um novo contrato.
- O compromisso de tempo de vida da nova reserva deve ser igual ou maior que o compromisso restante da reserva devolvida. Exemplo: para uma reserva de três anos de US$ 100 por mês trocada após o 18° pagamento, o compromisso de tempo de vida da nova reserva deve ser de US$ 1800 ou mais (com pagamento mensal ou antecipado).
- A nova reserva comprada como parte de uma troca tem um novo prazo, que começa a contar desde o momento da efetivação da troca.
- Não há penalidades ou limites anuais para trocas.

**Políticas de reembolso**

- No momento, não estamos cobrando um valor de término antecipado, mas no futuro poderá haver um valor de término antecipado de 12% para cancelamentos.
- O compromisso cancelado total não pode ultrapassar US$ 50.000 em uma janela ininterrupta de 12 meses para um perfil de cobrança ou registro único. Por exemplo, para uma reserva de três anos de US$ 100 por mês reembolsada no 18° mês, o compromisso cancelado é de US$ 1.800. Após o reembolso, seu novo limite disponível para reembolso será de US$ 48.200. Em 365 dias depois deste reembolso, o limite de US$ 48.200 será aumentado em US$ 1.800 e seu novo pool será de US$ 50.000. Qualquer outro cancelamento de reserva para o perfil de cobrança ou registro de EA esvaziará o mesmo pool, e a mesma lógica de reabastecimento será aplicada.
- O Azure não processará nenhum reembolso que ultrapasse o limite de US$ 50.000 em uma janela de 12 meses para um perfil de cobrança ou registro de EA.
- O cálculo dos reembolsos é baseado no valor mais baixo entre o preço de compra e o preço atual da reserva.
- Somente os proprietários de pedidos de reserva podem processar um reembolso. [Saiba como adicionar ou alterar os usuários que podem gerenciar uma reserva](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

- Para aprender a gerenciar uma reserva, confira [Gerenciar Reservas do Azure](manage-reserved-vm-instance.md).
- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
    - [O que são Reservas do Azure?](save-compute-costs-reservations.md)
    - [Gerenciar Reservas no Azure](manage-reserved-vm-instance.md)
    - [Entender como o desconto de reserva é aplicado](../manage/understand-vm-reservation-charges.md)
    - [Entender o uso de reserva para a sua assinatura paga conforme o uso](understand-reserved-instance-usage.md)
    - [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)
    - [Custos de software do Windows não estão incluídos nas reservas](reserved-instance-windows-software-costs.md)
    - [Reservas do Azure no programa CSP](/partner-center/azure-reservations)