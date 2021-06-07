---
title: Preço de computação reservado-banco de dados do Azure para PostgreSQL-servidor único
description: Pagar antecipadamente pelo banco de dados do Azure para PostgreSQL recursos de computação com capacidade reservada
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 9b8dafa4a69358b3f6f09551ac426b908750e2f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735465"
---
# <a name="prepay-for-azure-database-for-postgresql---single-server-compute-resources-with-reserved-capacity"></a>Pagar antecipadamente pelo banco de dados do Azure para PostgreSQL-recursos de computação de servidor único com capacidade reservada

O banco de dados do Azure para PostgreSQL agora ajuda você a economizar dinheiro prestando por recursos de computação em comparação com os preços pagos conforme o uso. Com a capacidade reservada do banco de dados do Azure para PostgreSQL, você faz um compromisso antecipado no servidor PostgreSQL por um período de um ou três anos para obter um desconto significativo nos custos de computação. Para comprar a capacidade reservada do banco de dados do Azure para PostgreSQL, você precisa especificar a região do Azure, o tipo de implantação, o nível de desempenho e o termo. </br>

Você não precisa atribuir a reserva a servidores do banco de dados do Azure específicos para PostgreSQL. Um banco de dados do Azure já em execução para PostgreSQL (ou aqueles que foram implantados recentemente) obterá automaticamente o benefício do preço reservado. Ao comprar uma reserva, você está pagando pelos custos de computação por um período de um ou três anos. Assim que você comprar uma reserva, os encargos de computação do banco de dados do Azure para PostgreSQL que correspondam aos atributos de reserva não serão cobrados com as tarifas pagas conforme o uso. Uma reserva não abrange os encargos de software, rede ou armazenamento associados aos servidores de banco de dados PostgreSQL. No final do prazo de reserva, o benefício de cobrança expira e o banco de dados do Azure para PostgreSQL são cobrados pelo preço pago conforme o uso. As reservas não são renovadas automaticamente. Para obter informações sobre preços, consulte a [oferta de capacidade reservada do banco de dados do Azure para PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/). </br>

> [!IMPORTANT]
> O preço da capacidade reservada está disponível para o banco de dados do Azure para PostgreSQL em opções de implantação de Citus de [servidor único](./overview.md#azure-database-for-postgresql---single-server) e de [hiperescala](./overview.md#azure-database-for-postgresql--hyperscale-citus) . Para obter informações sobre os preços de RI em hiperescala (Citus), consulte [esta página](concepts-hyperscale-reserved-pricing.md).

Você pode comprar a capacidade reservada do banco de dados do Azure para PostgreSQL no [portal do Azure](https://portal.azure.com/). Pague pela reserva [antecipadamente ou com pagamentos mensais](../cost-management-billing/reservations/prepare-buy-reservation.md). Para comprar a capacidade reservada:

* Você deve estar na função de proprietário de pelo menos uma assinatura corporativa ou individual com tarifas pagas conforme o uso.
* Para as assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal EA](https://ea.azure.com/). Ou, se essa configuração estiver desabilitada, você deve ser um administrador de EA na assinatura.
* Para o programa CSP (provedor de soluções na nuvem), somente os agentes de administração ou os agentes de vendas podem comprar a capacidade reservada do banco de dados do Azure para PostgreSQL. </br>

Os detalhes sobre como clientes corporativos e clientes pagos conforme o uso são cobrados por compras de reserva, consulte [entender o uso de reserva do Azure para seu registro corporativo](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) e [entender o uso de reserva do Azure para sua assinatura pré-paga](../cost-management-billing/reservations/understand-reserved-instance-usage.md).


## <a name="determine-the-right-server-size-before-purchase"></a>Determinar o tamanho do servidor correto antes da compra

O tamanho da reserva deve ser baseado na quantidade total de computação usada pelos servidores existentes ou em breve para serem implantados em uma região específica e usando o mesmo nível de desempenho e geração de hardware.</br>

Por exemplo, suponhamos que você esteja executando um banco de dados PostgreSQL de uso geral Gen5 – 32 vCore de Gen5 e dois bancos de dados PostgreSQL com otimização de memória – 16. Além disso, vamos pretender que você planeja implantar no próximo mês um servidor de banco de dados vCore Gen5 – 8 de uso geral e um servidor de banco de dados vCore Gen5 com otimização de memória – 32. Vamos supor que você saiba que precisará desses recursos por pelo menos um ano. Nesse caso, você deve comprar um 40 (32 + 8) vCores, reserva de um ano para o único banco de dados de uso geral-Gen5 e um 64 (2x16 + 32) de reserva de um ano para a memória de banco de dados individual otimizada-Gen5


## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Comprar a capacidade reservada do banco de dados do Azure para PostgreSQL

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione **Adicionar** e, em seguida, no painel reservas de compra, selecione **banco de dados do Azure para PostgreSQL** para comprar uma nova reserva para seus bancos de dados PostgreSQL.
4. Preencha os campos obrigatórios. Bancos de dados novos ou existentes que correspondem aos atributos que você selecionar qualificar para obter o desconto de capacidade reservada. O número real de seus servidores do banco de dados do Azure para PostgreSQL que obtém o desconto depende do escopo e da quantidade selecionada.


:::image type="content" source="media/concepts-reserved-pricing/postgresql-reserved-price.png" alt-text="Visão geral de preços reservados":::


A tabela a seguir descreve os campos obrigatórios.

| Campo | Descrição |
| :------------ | :------- |
| Subscription   | A assinatura usada para pagar pela reserva de capacidade reservada do banco de dados do Azure para PostgreSQL. O método de pagamento na assinatura é cobrado pelos custos antecipados para a reserva de capacidade reservada do banco de dados do Azure para PostgreSQL. O tipo de assinatura deve ser um Enterprise Agreement (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um contrato individual com preço pago conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma assinatura corporativa, os encargos são deduzidos do saldo do Pagamento antecipado do Azure do registro (chamado anteriormente de compromisso monetário) ou cobrados como excedente. Para uma assinatura individual com o preço pago conforme o uso, os encargos são cobrados no cartão de crédito ou no método de pagamento de fatura na assinatura.
| Escopo | O escopo da reserva vCore pode cobrir uma assinatura ou várias assinaturas (escopo compartilhado). Se você selecionar: </br></br> **Compartilhado**, o desconto de reserva vCore é aplicado ao banco de dados do Azure para servidores PostgreSQL em execução em qualquer assinatura em seu contexto de cobrança. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas no registro. Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.</br></br> **Assinatura única**, o desconto de reserva vCore é aplicado ao banco de dados do Azure para servidores PostgreSQL nesta assinatura. </br></br> **Um grupo de recursos único**, o desconto de reserva é aplicado ao banco de dados do Azure para servidores PostgreSQL na assinatura selecionada e ao grupo de recursos selecionado dentro dessa assinatura.
| Região | A região do Azure que é coberta pelo banco de dados do Azure para PostgreSQL reserva de capacidade reservada.
| Tipo de implantação | O tipo de recurso do banco de dados do Azure para PostgreSQL do qual você deseja comprar a reserva.
| Nível de desempenho | A camada de serviço do banco de dados do Azure para servidores PostgreSQL.
| Termo | Um ano
| Quantidade | A quantidade de recursos de computação que está sendo adquirida no banco de dados do Azure para PostgreSQL reserva de capacidade reservada. A quantidade é um número de vCores na região do Azure e no nível de desempenho selecionados que estão sendo reservados e receberão o desconto de cobrança. Por exemplo, se você estiver executando ou planejando executar um banco de dados do Azure para servidores PostgreSQL com a capacidade de computação total de Gen5 16 vCores na região leste dos EUA, você deverá especificar a quantidade como 16 para maximizar o benefício para todos os servidores.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Flexibilidade de tamanho do vCore

A flexibilidade de tamanho do vCore ajuda você a aumentar ou diminuir dentro de uma região e nível de desempenho sem perder o benefício de capacidade reservada. Se você dimensionar para vCores maiores do que a capacidade reservada, você será cobrado pelo excesso de vCores usando o preço pago conforme o uso.


## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

O desconto de reserva vCore é aplicado automaticamente ao número de servidores do banco de dados do Azure para PostgreSQL que correspondem ao banco de dados do Azure para os atributos e escopo de reserva de capacidade reservada do PostgreSQL. Você pode atualizar o escopo do banco de dados do Azure para PostgreSQL reserva de capacidade reservada por meio de portal do Azure, PowerShell, CLI ou por meio da API.

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

* [O que são as reservas do Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md)?
* [Gerenciar Reservas do Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Compreender o desconto de Reservas do Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Entender o uso de reserva para a sua assinatura paga conforme o uso](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [Entender o uso de reserva para seu registro de empresa](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](/partner-center/azure-reservations)