---
title: Preço de computação reservado-banco de dados do Azure para PostgreSQL-Citus (hiperescala)
description: Pagar antecipadamente pelo banco de dados do Azure para PostgreSQL – recursos de computação de Citus (hiperescala) com capacidade reservada.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 35d5d196eccb222bf17e0a129fe4e4041b1f6053
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98600732"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>Pagar antecipadamente pelo banco de dados do Azure para PostgreSQL – recursos de computação de Citus (hiperescala) com capacidade reservada

O banco de dados do Azure para PostgreSQL – Citus (hiperescala) agora ajuda você a economizar dinheiro prestando por recursos de computação em comparação com os preços pagos conforme o uso. Com a capacidade reservada de Citus (hiperescala), você faz um compromisso antecipado no grupo de servidores de hiperescala (Citus) por um período de um ou três anos para obter um desconto significativo nos custos de computação. Para adquirir a capacidade reservada do Citus (hiperescala), você precisa especificar a região do Azure, o termo de reserva e a frequência de cobrança.

> [!IMPORTANT]
> Este artigo é sobre a capacidade reservada para o banco de dados do Azure para PostgreSQL – Citus (hiperescala). Para obter informações sobre a capacidade reservada para o banco de dados do Azure para PostgreSQL – servidor único, consulte [pagar pelo banco de dados do Azure para PostgreSQL – recursos de computação de servidor único com capacidade reservada](./concept-reserved-pricing.md).

Você não precisa atribuir a reserva a grupos de servidores Citus (hiperscale) específicos. Um grupo de servidores que já está executando o Citus (hiperscale) ou aqueles que foram implantados recentemente têm o benefício de preços reservados. Ao comprar uma reserva, você está prepagando os custos de computação por um ano ou três anos. Assim que você comprar uma reserva, os encargos de computação de hiperescala (Citus) que correspondam aos atributos de reserva não serão mais cobrados com as tarifas pré-pagas. 

Uma reserva não abrange os encargos de software, rede ou armazenamento associados aos grupos de servidores de hiperescala (Citus). No final do prazo de reserva, o benefício de cobrança expira e os grupos de servidores de hiperescala (Citus) são cobrados pelo preço pago conforme o uso. As reservas não são renovadas. Para obter informações sobre preços, consulte a [oferta de capacidade reservada do banco de dados do Azure para PostgreSQL – Citus (hiperescala)](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/).

Você pode comprar a capacidade reservada de Citus (hiperescala) no [portal do Azure](https://portal.azure.com/). Pague pela reserva [antecipadamente ou com pagamentos mensais](../cost-management-billing/reservations/prepare-buy-reservation.md). Para comprar a capacidade reservada:

* Você deve estar na função de proprietário de pelo menos uma Contrato Enterprise (EA) ou assinatura individual com tarifas de pagamento conforme o uso.
* Para assinaturas Contrato Enterprise, **adicionar instâncias reservadas** deve estar habilitada no [portal de ea](https://ea.azure.com/). Ou, se essa configuração estiver desabilitada, você deverá ser um administrador Contrato Enterprise na assinatura.
* Para o programa CSP (provedor de soluções na nuvem), somente os agentes de administração ou os agentes de vendas podem adquirir capacidade reservada de Citus (hiperescala).

Para obter informações sobre como Contrato Enterprise clientes e clientes pagos conforme o uso são cobrados por compras de reserva, consulte:
- [Entender o uso de reserva do Azure para seu registro de Contrato Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Entender o uso de reserva do Azure para sua assinatura pré-paga](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-the-right-server-group-size-before-purchase"></a>Determinar o tamanho do grupo de servidores correto antes da compra

O tamanho da reserva é baseado na quantidade total de computação usada pelo coordenador e nós de trabalho existentes ou em breve para serem implantados em grupos de servidores Citus (hiperescala) em uma região específica.

Por exemplo, vamos supor que você esteja executando um grupo de servidores de hiperescala (Citus) com 16 nós de trabalho vCore e 3 8. Além disso, vamos supor que você planeja implantar no mês seguinte um grupo de servidores adicional de hiperescala (Citus) com um coordenador vCore de 32 e nós de trabalho de 2 4 vCore. Vamos supor também que você precisa desses recursos por pelo menos um ano.

Nesse caso, compre uma reserva de um ano para:

* Total de 16 vCores + 32 vCores = 48 vCores para nós de coordenador
* Total de 3 nós x 8 vCores + 2 nós x 4 vCores = 24 + 8 = 32 vCores para nós de trabalho

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Comprar a capacidade reservada do banco de dados do Azure para PostgreSQL

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Selecione **Todos os serviços** > **Reservas**.
1. Selecione **Adicionar**. No painel **reservas de compra** , selecione **banco de dados do Azure para PostgreSQL** para comprar uma nova reserva para seus bancos de dados PostgreSQL.
1. Selecione o tipo de **computação de hiperescala (Citus)** para comprar e clique em **selecionar**.
1. Examine a quantidade do tipo de computação selecionado na guia **produtos** .
1. Continue na guia **comprar + revisão** para concluir a compra.

A tabela a seguir descreve os campos obrigatórios.

| Campo        | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subscription | A assinatura usada para pagar pela reserva de capacidade reservada do banco de dados do Azure para PostgreSQL. O método de pagamento na assinatura é cobrado pelos custos antecipados para a reserva de capacidade reservada do banco de dados do Azure para PostgreSQL. O tipo de assinatura deve ser um Contrato Enterprise (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um contrato individual com preço pago conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma assinatura Contrato Enterprise, os encargos são deduzidos do saldo antecipado do Azure do registro (anteriormente chamado de compromisso monetário) ou cobrados como excedentes. Para uma assinatura individual com o preço pago conforme o uso, os encargos são cobrados no cartão de crédito ou no método de pagamento de fatura na assinatura.                                                                                  |
| Escopo        | O escopo da reserva de vCore pode cobrir uma assinatura ou várias assinaturas (escopo compartilhado). Se você selecionar **compartilhado**, o desconto de reserva de vCore será aplicado aos grupos de servidores de hiperescala (Citus) em execução em qualquer assinatura no contexto de cobrança. Para clientes Contrato Enterprise, o escopo compartilhado é o registro e inclui todas as assinaturas dentro do registro. Para clientes pagos conforme o uso, o escopo compartilhado é todas as assinaturas pagas conforme o uso criadas pelo administrador da conta. Se você selecionar **assinatura única**, o desconto de reserva vCore será aplicado aos grupos de servidores de hiperescala (Citus) nesta assinatura. Se você selecionar **um único grupo de recursos**, o desconto de reserva será aplicado aos grupos de servidores de hiperescala (Citus) na assinatura selecionada e ao grupo de recursos selecionado nessa assinatura. |
| Região       | A região do Azure coberta pela reserva de capacidade reservada do banco de dados do Azure para PostgreSQL – Citus (hiperescala).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Termo         | Um ano ou três anos.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Quantidade     | A quantidade de recursos de computação que está sendo adquirida na reserva de capacidade reservada (Citus) de hiperescala. Em particular, o número de vCores do nó de operador ou coordenador na região do Azure selecionada que está sendo reservada e que obterá o desconto de cobrança. Por exemplo, se você estiver executando (ou planeja executar) grupos de servidores de hiperescala (Citus) com a capacidade de computação total do nó de coordenador 64 vCores e 32 nó de trabalho vCores na região leste dos EUA, especifique a quantidade como 64 e 32 para nós de coordenador e de trabalho, respectivamente, para maximizar o benefício para todos os servidores.                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, consulte [trocas e reembolsos de autoatendimento para reservas do Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Flexibilidade de tamanho do vCore

a flexibilidade do tamanho vCore ajuda você a escalar ou reduzir verticalmente os nós de coordenador e de trabalho em uma região, sem perder o benefício de capacidade reservada.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

O desconto de reserva vCore é aplicado automaticamente ao número de grupos de servidores de hiperescala (Citus) que correspondem ao banco de dados do Azure para atributos e escopo de reserva de capacidade reservada do PostgreSQL. Você pode atualizar o escopo da reserva de capacidade reservada do banco de dados do Azure para PostgreSQL – Citus (hiperescala) por meio do portal do Azure, do PowerShell, do CLI do Azure ou da API.

Para saber mais sobre reservas do Azure, confira os seguintes artigos:

* [O que são reservas do Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Gerenciar reservas do Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Entender o desconto de reserva do Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Entenda o uso de reserva para sua assinatura pré-paga](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [Entender o uso de reserva para seu registro de Contrato Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Reservas do Azure no programa do provedor de soluções de nuvem do Partner Center](/partner-center/azure-reservations)