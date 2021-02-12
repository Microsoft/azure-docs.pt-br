---
title: Economize custos de computação com capacidade reservada
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Saiba como comprar o banco de dados SQL do Azure e a capacidade reservada do SQL Instância Gerenciada para economizar em seus custos de computação.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 10/13/2020
ms.openlocfilehash: cacd43502a01352c24f8fcfd85b12aac781dccbd
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602515"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-sql-database--sql-managed-instance"></a>Economize custos para recursos com capacidade reservada-banco de dados SQL do Azure & SQL Instância Gerenciada
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

Economize dinheiro com o banco de dados SQL do Azure e o SQL Instância Gerenciada confirmando uma reserva de recursos de computação em comparação com os preços pagos conforme o uso. Com a capacidade reservada, você faz um compromisso para o banco de dados SQL e/ou o SQL Instância Gerenciada usar por um período de um ou três anos para obter um desconto significativo nos custos de computação. Para comprar a capacidade reservada, você precisa especificar a região do Azure, o tipo de implantação, o nível de desempenho e o termo.

Você não precisa atribuir a reserva a um banco de dados ou instância gerenciada específica. A correspondência de implantações existentes que já estão em execução ou aquelas que foram implantadas recentemente Obtém o benefício automaticamente. Ao comprar uma reserva, você compromete o uso dos custos de computação por um período de um ou três anos. Assim que você comprar uma reserva, os encargos de computação que corresponderem aos atributos de reserva não serão mais cobrados com as tarifas pagas conforme o uso. 

Uma reserva se aplica a réplicas de computação secundárias primárias e faturáveis, mas não abrange os encargos de software, rede ou armazenamento associados ao serviço. No final do prazo de reserva, o benefício de cobrança expira e o banco de dados ou a instância gerenciada é cobrada com o preço pago conforme o uso. As reservas não são renovadas automaticamente. Para obter informações sobre preços, consulte a [oferta de capacidade reservada](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Você pode comprar a capacidade reservada no [portal do Azure](https://portal.azure.com). Pague pela reserva [antecipadamente ou com pagamentos mensais](../../cost-management-billing/reservations/prepare-buy-reservation.md). Para comprar uma capacidade reservada:

- Você deve estar na função de proprietário de pelo menos uma assinatura corporativa ou individual com tarifas pagas conforme o uso.
- Para as assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal EA](https://ea.azure.com). Ou, se essa configuração estiver desabilitada, você deve ser um administrador de EA na assinatura. Capacidade reservada.

Para obter mais informações sobre como clientes corporativos e clientes pagos conforme o uso são cobrados por compras de reserva, consulte [entender o uso de reserva do Azure para seu registro corporativo](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) e [entender o uso de reserva do Azure para sua assinatura pré-paga](../../cost-management-billing/reservations/understand-reserved-instance-usage.md).

> [!NOTE]
> A compra da capacidade reservada não aloca previamente ou reserva recursos específicos de infraestrutura (máquinas virtuais ou nós) para seu uso.

## <a name="determine-correct-size-before-purchase"></a>Determinar o tamanho correto antes da compra

O tamanho da reserva deve ser baseado na quantidade total de computação usada pelo banco de dados existente ou em breve para ser implantado ou instância gerenciada em uma região específica e usando o mesmo nível de desempenho e geração de hardware.

Por exemplo, vamos supor que você esteja executando uma finalidade geral, Gen5 – 16 pool elástico do vCore e dois Gen5 críticos para os negócios – 4. Além disso, vamos pretender que você planeja implantar no mês que vem um Gen5 de uso geral adicional – 16 pool elásticos vCore e um pool elástico de Gen5 crítico – 32. Além disso, vamos supor que você saiba que precisará desses recursos por pelo menos 1 ano. Nesse caso, você deve comprar uma reserva de 32 (2x16) vCores de 1 ano para o banco de dados único/pool elástico de uso geral-Gen5 e uma 40 (2x4 + 32) de reserva de 1 ano para o banco de dados/pool elástico comercial crítico-Gen5.

## <a name="buy-reserved-capacity"></a>Comprar uma capacidade reservada

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione **Adicionar** e, em seguida, no painel **reservas de compra** , selecione **banco de dados SQL** para comprar uma nova reserva para o banco de dados SQL.
4. Preencha os campos obrigatórios. Bancos de dados existentes no banco de dados SQL e SQL Instância Gerenciada que correspondem aos atributos que você selecionar qualificar para obter o desconto de capacidade reservada. O número real de bancos de dados ou instâncias gerenciadas que obtém o desconto depende do escopo e da quantidade selecionada.

    ![Captura de tela antes de enviar a capacidade reservada Purchase](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

    A tabela a seguir descreve os campos obrigatórios.
    
    | Campo      | Descrição|
    |------------|--------------|
    |Assinatura|A assinatura usada para pagar pela reserva de capacidade. São cobrados os custos iniciais para a reserva à forma de pagamento na assinatura. O tipo de assinatura deve ser um Enterprise Agreement (número da oferta MS-AZR-0017P ou MS-AZR-0148P) ou um contrato individual com preço pago conforme o uso (número da oferta MS-AZR-0003P ou MS-AZR-0023P). Para uma assinatura empresarial, os encargos são deduzidos do saldo antecipado do Azure do registro (anteriormente chamado de compromisso monetário) ou cobrados como excedentes. Para uma assinatura individual com o preço pago conforme o uso, os encargos são cobrados no cartão de crédito ou no método de pagamento de fatura na assinatura.|
    |Escopo       |O escopo da reserva de vCore pode cobrir uma assinatura ou várias assinaturas (escopo compartilhado). Se você selecionar <br/><br/>**Compartilhado**, o desconto de reserva vCore é aplicado ao banco de dados ou instância gerenciada em execução em qualquer assinatura no contexto de cobrança. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas no registro. Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.<br/><br/>**Assinatura única**, o desconto de reserva vCore é aplicado aos bancos de dados ou instâncias gerenciadas nesta assinatura. <br/><br/>**Um grupo de recursos único**, o desconto de reserva é aplicado às instâncias de bancos de dados ou instâncias gerenciadas na assinatura selecionada e ao grupo de recursos selecionado dentro dessa assinatura.|
    |Região      |A região do Azure que é coberta pela reserva de capacidade.|
    |Tipo de implantação|O tipo de recurso de SQL para o qual você deseja comprar a reserva.|
    |Nível de desempenho|A camada de serviço para os bancos de dados ou instâncias gerenciadas. |
    |Termo        |Um ano ou três anos.|
    |Quantidade    |A quantidade de recursos de computação que está sendo adquirida dentro da reserva de capacidade. A quantidade é um número de vCores na região do Azure e no nível de desempenho selecionados que estão sendo reservados e receberão o desconto de cobrança. Por exemplo, se você executar ou planejar a execução de vários bancos de dados com a capacidade de computação total de Gen5 16 vCores na região leste dos EUA, você especificaria a quantidade como 16 para maximizar o benefício de todos os bancos de dados. |

1. Examine o custo da reserva de capacidade na seção de **custos** .
1. Selecione **Comprar**.
1. Selecione **Exibir essa Reserva** para ver o status de sua compra.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Flexibilidade de tamanho do vCore

A flexibilidade de tamanho do vCore ajuda você a aumentar ou diminuir dentro de uma região e nível de desempenho sem perder o benefício de capacidade reservada. A capacidade reservada também fornece a flexibilidade para mover temporariamente seus bancos de dados ativos para dentro e fora de pools elásticos (dentro da mesma região e nível de desempenho) como parte de suas operações normais sem perder o benefício de capacidade reservada. Ao manter um buffer não aplicado em sua reserva, você pode gerenciar efetivamente os picos de desempenho sem exceder seu orçamento.

## <a name="limitation"></a>Limitação

Não é possível reservar bancos de dados baseados em DTU (básico, Standard ou Premium) no banco de dados SQL.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

O desconto de reserva vCore é aplicado automaticamente ao número de bancos de dados ou instâncias gerenciadas que correspondem ao escopo e aos atributos de reserva de capacidade. Você pode atualizar o escopo da reserva de capacidade por meio do [portal do Azure](https://portal.azure.com), do PowerShell, do CLI do Azure ou da API.

Para saber como gerenciar a reserva de capacidade, consulte [gerenciar capacidade reservada](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são Reservas do Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Gerenciar Reservas do Azure](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Compreender o desconto de Reservas do Azure](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](/partner-center/azure-reservations)