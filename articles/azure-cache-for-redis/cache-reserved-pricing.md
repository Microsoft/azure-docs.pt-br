---
title: Pagar antecipadamente por computação com capacidade reservada-cache do Azure para Redis
description: Pagar antecipadamente pelo cache do Azure para recursos de computação Redis com capacidade reservada
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 2f3472aa495042749410bc0b9635f0924a02e1fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98598547"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Pagar antecipadamente pelo cache do Azure para recursos de computação Redis com capacidade reservada

O cache do Azure para Redis agora ajuda a economizar dinheiro prestando por recursos de computação em comparação com os preços pagos conforme o uso. Com o cache do Azure para capacidade reservada Redis, você faz um compromisso antecipado no cache por um período de um ou três anos para obter um desconto significativo nos custos de computação. Para comprar o cache do Azure para a capacidade reservada do Redis, você precisa especificar a região do Azure, a camada de serviço e o termo.

Você não precisa atribuir a reserva a um cache do Azure específico para instâncias Redis. Um cache do Azure já em execução para Redis ou aqueles que foram implantados recentemente obterá automaticamente o benefício do preço reservado, até o tamanho do cache reservado. Ao comprar uma reserva, você está pagando antecipadamente os custos de computação por um período de um ou três anos. Assim que você comprar uma reserva, o cache do Azure para encargos de computação Redis que correspondam aos atributos de reserva não serão cobrados com as tarifas pagas conforme o uso. Uma reserva não abrange encargos de rede ou armazenamento associados ao cache. No final do prazo de reserva, o benefício de cobrança expira e o cache do Azure para Redis é cobrado pelo preço pago conforme o uso. As reservas não são renovadas automaticamente. Para obter informações sobre preços, consulte a [oferta de capacidade reservada de cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache).

Você pode comprar o cache do Azure para a capacidade reservada do Redis no [portal do Azure](https://portal.azure.com/). Para comprar a capacidade reservada:

* Você deve estar na função de proprietário de pelo menos uma assinatura corporativa ou individual com tarifas pagas conforme o uso.
* Para as assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal EA](https://ea.azure.com/). Ou, se essa configuração estiver desabilitada, você deve ser um administrador de EA na assinatura.
* Para o programa CSP (provedor de soluções na nuvem), somente os agentes de administração ou os agentes de vendas podem comprar o cache do Azure para a capacidade reservada do Redis.

Para obter detalhes sobre como clientes corporativos e clientes pagos conforme o uso são cobrados por compras de reserva, consulte [entender o uso de reserva do Azure para seu registro corporativo](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) e [entender o uso de reserva do Azure para sua assinatura pré-paga](../cost-management-billing/reservations/understand-reserved-instance-usage.md).


## <a name="determine-the-right-cache-size-before-purchase"></a>Determinar o tamanho de cache correto antes da compra

O tamanho da reserva deve ser baseado na quantidade total de tamanho de memória usada pelo cache existente ou em breve para ser implantado em uma região específica e usando a mesma camada de serviço.

Por exemplo, vamos supor que você esteja executando dois caches-um a 13 GB e o outro em 26 GB. Você precisará de ambos por pelo menos um ano. Além disso, vamos supor que você planeje dimensionar os caches de 13 GB existentes para 26 GB para um mês a fim de atender à sua demanda sazonal e, em seguida, reduzir novamente. Nesse caso, você pode comprar um cache de 1 P2 e um cache de 1 P3 ou caches de 3 $ P2 em uma reserva de um ano para maximizar a economia. Você receberá um desconto na quantidade total de memória de cache reservada, independentemente de como essa quantidade é alocada em seus caches.


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Comprar o cache do Azure para capacidade reservada do Redis

Você pode comprar uma instância de VM reservada no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/). Pague pela reserva [antecipadamente ou com pagamentos mensais](../cost-management-billing/reservations/prepare-buy-reservation.md).

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione **Adicionar** e, em seguida, no painel reservas de compra, selecione **cache do Azure para Redis** para comprar uma nova reserva para seus caches.
4. Preencha os campos obrigatórios. Bancos de dados novos ou existentes que correspondem aos atributos que você selecionar qualificar para obter o desconto de capacidade reservada. O número real de seu cache do Azure para instâncias Redis que obtém o desconto depende do escopo e da quantidade selecionada.


![Visão geral de preços reservados](media/cache-reserved-pricing/cache-reserved-price.png)


A tabela a seguir descreve os campos obrigatórios.

| Campo | Descrição |
| :------------ | :------- |
| Subscription   | A assinatura usada para pagar pelo cache do Azure para reserva de capacidade reservada do Redis. O método de pagamento na assinatura é cobrado pelos custos antecipados para o cache do Azure para reserva de capacidade reservada Redis. O tipo de assinatura deve ser um Enterprise Agreement (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um contrato individual com preço pago conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma assinatura corporativa, os encargos são deduzidos do saldo do Pagamento antecipado do Azure do registro (chamado anteriormente de compromisso monetário) ou cobrados como excedente. Para uma assinatura individual com o preço pago conforme o uso, os encargos são cobrados no cartão de crédito ou no método de pagamento de fatura na assinatura.
| Escopo | O escopo de assinatura pode abranger uma ou várias assinaturas (escopo compartilhado). Se você selecionar: </br></br> **Compartilhado**, o desconto de reserva é aplicado ao cache do Azure para instâncias Redis em execução em qualquer assinatura em seu contexto de cobrança. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas no registro. Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.</br></br> **Assinatura única**, o desconto de reserva é aplicado ao cache do Azure para instâncias Redis nesta assinatura. </br></br> **Um grupo de recursos único**, o desconto de reserva é aplicado ao cache do Azure para instâncias Redis na assinatura selecionada e o grupo de recursos selecionado dentro dessa assinatura.
| Região | A região do Azure que é coberta pelo cache do Azure para reserva de capacidade reservada do Redis.
| Tipo de preço | A camada de serviço para o cache do Azure para servidores Redis.
| Termo | Um ano ou três anos
| Quantidade | A quantidade de recursos de computação que está sendo adquirida no cache do Azure para reserva de capacidade reservada Redis. A quantidade é um número de caches na região do Azure selecionada e na camada de serviço que estão sendo reservados e obterá o desconto de cobrança. Por exemplo, se você estiver executando ou planejando executar um cache do Azure para servidores Redis com a capacidade total de cache de 26 GB na região leste dos EUA, você deve especificar a quantidade que oferece o equivalente de 26 GB para maximizar o benefício de todos os caches. Isso pode ser de 1 a P3 ou 2 de caches P2.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="cache-size-flexibility"></a>Flexibilidade do tamanho do cache

A flexibilidade do tamanho do cache ajuda a escalar ou reduzir verticalmente dentro de uma camada de serviço e região, sem perder o benefício de capacidade reservada.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

O desconto de reserva é aplicado automaticamente ao cache do Azure para instâncias Redis que correspondem ao escopo e aos atributos de reserva. Você pode atualizar o escopo da reserva por meio do portal do Azure, do PowerShell, da CLI do Azure ou da API.

*  Para saber como os descontos de capacidade reservados são aplicados ao cache do Azure para Redis, consulte [entender o desconto de reserva do Azure](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md)

* Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

    * [O que são Reservas do Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
    * [Gerenciar Reservas do Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
    * [Compreender o desconto de Reservas do Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
    * [Entender o uso de reserva para a sua assinatura paga conforme o uso](../cost-management-billing/reservations/understand-reservation-charges-mysql.md)
    * [Entender o uso de reserva para seu registro de empresa](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    * [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](/partner-center/azure-reservations)