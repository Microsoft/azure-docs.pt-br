---
title: Economize custos de computação com capacidade reservada
description: Saiba como comprar Azure Data Factory capacidade reservada de fluxo de dados para economizar em seus custos de computação.
ms.topic: conceptual
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.date: 02/05/2021
ms.openlocfilehash: c4d6ebc8d57857deeb2a5cc71867484bd3519ea6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699667"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-data-factory-data-flows"></a>Economize custos para recursos com capacidade reservada-Azure Data Factory fluxos de dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Economize dinheiro com Azure Data Factory custos de fluxo de dados ao confirmar uma reserva de recursos de computação em comparação com os preços pagos conforme o uso. Com a capacidade reservada, você faz um compromisso com o uso do fluxo de dados do ADF por um período de um ou três anos para obter um desconto significativo nos custos de computação. Para comprar a capacidade reservada, você precisa especificar a região do Azure, o tipo de computação, a quantidade de contagem de núcleos e o termo.

Você não precisa atribuir a reserva a uma fábrica ou tempo de execução de integração específico. Fábricas existentes ou fábricas recentemente implantadas obtêm o benefício automaticamente. Ao comprar uma reserva, você compromete o uso dos custos de computação do fluxo de dados por um período de um ou três anos. Assim que você comprar uma reserva, os encargos de computação que corresponderem aos atributos de reserva não serão mais cobrados com as tarifas pagas conforme o uso. 

Você pode comprar [capacidade reservada](https://portal.azure.com) escolhendo reservas [para frente ou para pagamentos mensais](../cost-management-billing/reservations/prepare-buy-reservation.md). Para comprar uma capacidade reservada:

- Você deve estar na função de proprietário de pelo menos uma assinatura corporativa ou individual com tarifas pagas conforme o uso.
- Para as assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal EA](https://ea.azure.com). Ou, se essa configuração estiver desabilitada, você deve ser um administrador de EA na assinatura. Capacidade reservada.

Para obter mais informações sobre como clientes corporativos e clientes pagos conforme o uso são cobrados por compras de reserva, consulte [entender o uso de reserva do Azure para seu registro corporativo](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) e [entender o uso de reserva do Azure para sua assinatura pré-paga](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

> [!NOTE]
> A compra da capacidade reservada não aloca ou reserva recursos específicos de infraestrutura (clusters ou máquinas virtuais) para seu uso.

## <a name="determine-proper-azure-ir-sizes-needed-before-purchase"></a>Determinar os tamanhos de Azure IR adequados necessários antes da compra

O tamanho da reserva deve ser baseado na quantidade total de computação usada pelos fluxos de dados existentes ou em breve para serem implantados usando a mesma camada de computação.

Por exemplo, vamos supor que você esteja executando um pipeline por hora usando memória otimizada com 32 núcleos. Além disso, digamos que você planeja implantar no mês seguinte um pipeline adicional que usa núcleos 64 de uso geral. Além disso, vamos supor que você saiba que precisará desses recursos por pelo menos 1 ano. Nesse caso, insira o número de núcleos necessários para cada tipo de computação por 1 hora. No portal do Azure, procure reservas. Escolha Data Factory fluxos de dados > e, em seguida, insira 32 para memória otimizada e 64 para fins gerais.

## <a name="buy-reserved-capacity"></a>Comprar uma capacidade reservada

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione **Adicionar** e, em seguida, no painel **reservas de compra** , selecione fluxos de **dados do ADF** para comprar uma nova reserva para fluxos de dados do ADF.
4. Preencha os campos e atributos necessários que você selecionou qualificar para obter o desconto de capacidade reservada. O número real de fluxos de dados que obtém o desconto depende do escopo e da quantidade selecionada.
5. Examine o custo da reserva de capacidade na seção de **custos** .
6. Selecione **Comprar**.
7. Selecione **Exibir essa Reserva** para ver o status de sua compra.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [Compreender o desconto de Reservas do Azure](data-flow-understand-reservation-charges.md)