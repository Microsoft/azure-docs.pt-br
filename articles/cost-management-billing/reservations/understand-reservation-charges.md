---
title: Noções básicas sobre o desconto de reservas para o Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como um desconto de reserva é aplicado aos bancos de dados SQL do Azure em execução. O desconto é aplicado a esses bancos de dados por hora.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: banders
ms.openlocfilehash: 99f2c9c16f2ca2147a6e6da333ff64116e42aecc
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338072"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-database"></a>Como um desconto de reserva é aplicado ao Banco de Dados SQL do Azure

Depois que você compra uma capacidade reservada do Banco de Dados SQL do Azure, o desconto de reserva é aplicado automaticamente aos bancos de dados SQL que correspondem aos atributos e à quantidade da reserva. Uma reserva se aplica aos custos de computação do Banco de Dados SQL, incluindo a réplica primária e todas as réplicas secundárias faturáveis. Você é cobrado por software, armazenamento e rede às taxas normais. Você pode cobrir os custos de licenciamento para o Banco de Dados SQL com o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Observe que os descontos de reserva não se aplicam ao Banco de Dados SQL do Azure sem servidor.

Para instâncias de máquina Virtual reservadas, consulte [entender instâncias reservadas do Azure VM desconto](../manage/understand-vm-reservation-charges.md).

## <a name="how-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

Um desconto de reserva é do tipo "*usar ou perder*". Portanto, se você não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível postergar horas reservadas não utilizadas.

Quando você desliga um recurso, o desconto de reserva se aplica automaticamente a outro recurso correspondente no escopo especificado. Se nenhum recurso correspondente for encontrado no escopo especificado, as horas reservadas serão *perdidas*.

## <a name="discount-applied-to-running-sql-databases"></a>Desconto aplicado aos bancos de dados SQL em execução

O desconto de capacidade reservada do Banco de Dados SQL é aplicado aos bancos de dados SQL em execução por hora. A reserva que você compra é correspondida ao uso de computação emitido pelos bancos de dados SQL em execução. Para os bancos de dados SQL que não são executados durante uma hora inteira, a reserva é aplicada automaticamente a outros bancos de dados SQL que correspondem aos atributos de reserva. O desconto pode ser aplicado aos bancos de dados SQL que estão sendo executados simultaneamente. Se você não tiver bancos de dados SQL que sejam executados durante uma hora inteira que correspondam aos atributos de reserva, não obterá o benefício total do desconto de reserva para essa hora.

Os exemplos a seguir mostram como o desconto de capacidade reservada do Banco de Dados SQL se aplica, dependendo do número de núcleos que você comprou e quando está sendo executado.

- Cenário 1: Você compra um Banco de Dados SQL com capacidade reservada para um Banco de Dados SQL de 8 núcleos. Você executa um Banco de Dados SQL de 16 núcleos que corresponde ao restante dos atributos da reserva. Você é cobrado pelo preço pré-pago de 8 núcleos de uso de computação do Banco de Dados SQL. Você recebe o desconto de reserva para uma hora de uso de computação do Banco de Dados SQL principal de 8.

Para o restante destes exemplos, suponha que a capacidade reservada do Banco de Dados do SQL que você comprar seja destinada a um Banco de Dados SQL de 16 núcleos e o restante dos atributos de reserva correspondam aos bancos de dados SQL em execução.

- Cenário 2: Você executa dois bancos de dados SQL com 8 núcleos cada por uma hora. O desconto de reserva de 16 núcleos é aplicado ao uso de computação para os dois bancos de dados SQL de 8 núcleos.
- Cenário 3: você executa um Banco de Dados SQL de 16 núcleos das 13h às 13h30. Você pode executar outro núcleo 16 banco de dados SQL de 1:30 às 2 horas. Ambos são cobertas pelo desconto de reserva.
- Cenário 4: você executa um Banco de Dados SQL de 16 núcleos das 13h às 13h45. Você pode executar outro núcleo 16 banco de dados SQL de 1:30 às 2 horas. Você será cobrado o preço pago conforme o uso para a sobreposição de 15 minutos. O desconto de reserva se aplica ao uso de computação para o restante do tempo.
- Cenário 5: Você executa o banco de dados de Hiperescala do SQL de quatro núcleos com três réplicas secundárias, cada uma com quatro núcleos. A reserva aplica-se ao uso de computação para as réplicas primárias e para todas as réplicas secundárias.

Para entender e visualizar o aplicativo de suas reservas do Azure nos relatórios de uso de faturamento, consulte [Entender o uso de reserva do Azure](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são Reservas do Azure?](save-compute-costs-reservations.md)
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Pagar antecipadamente por recursos de computação de banco de dados SQL com capacidade reservada do Banco de Dados SQL do Azure](../../azure-sql/database/reserved-capacity-overview.md)
- [Gerenciar Reservas do Azure](manage-reserved-vm-instance.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)
- [Entender o uso de reserva para assinaturas de CSP](/partner-center/azure-reservations)