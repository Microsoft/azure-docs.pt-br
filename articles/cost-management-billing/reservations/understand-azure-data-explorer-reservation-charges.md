---
title: Entender como o desconto de reserva é aplicado ao Azure Data Explorer
description: Entenda como o desconto de reserva é aplicado ao medidor de marcação do Azure Data Explorer.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: b222e4ff519fbe2fbb4fa58b39555ac9fead0917
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352908"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-data-explorer"></a>Entender como o desconto de reserva é aplicado ao Azure Data Explorer

Após adquirir uma capacidade reservada do Azure Data Explorer, o desconto de reserva é aplicado automaticamente aos recursos do Azure Data Explorer que correspondem aos atributos e à quantidade da reserva. Uma reserva inclui os encargos de marcação do Azure Data Explorer. Ela não inclui recursos de computação, rede, armazenamento ou qualquer outro recurso do Azure usado para operar o cluster do Azure Data Explorer. As reservas para esses recursos devem ser compradas separadamente.

## <a name="how-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

Um desconto de reserva é do tipo "*usar ou perder*". Portanto, se você não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível postergar horas reservadas não utilizadas.

Quando você desliga um recurso, o desconto de reserva se aplica automaticamente a outro recurso correspondente no escopo especificado. Se nenhum recurso correspondente for encontrado no escopo especificado, as horas reservadas serão *perdidas*.

## <a name="reservation-discount-applied-to-azure-data-explorer-clusters"></a>Desconto de reserva aplicado aos clusters do Azure Data Explorer

Um desconto de reserva é aplicado ao consumo de marcação do Azure Data Explorer de hora em hora. Para recursos do Azure Data Explorer que não são executados durante toda a hora, o desconto de reserva é aplicado automaticamente a outros recursos do Data Explorer que correspondem aos atributos de reserva. O desconto pode se aplicar aos recursos do Azure Data Explorer que estão sendo executados simultaneamente. Se você não tiver recursos do Azure Data Explorer que sejam executados durante a hora inteira e que correspondam aos atributos da reserva, você não terá o benefício total do desconto da reserva para essa hora.

> [!NOTE]
> * É **altamente recomendado** comprar [capacidade de reserva](../../virtual-machines/prepay-reserved-vm-instances.md) para as máquinas virtuais usadas para o cluster do Azure Data Explorer a fim de maximizar a economia de capacidade reservada.
> * A compra da reserva aplicará descontos a todas as regiões.

## <a name="examples"></a>Exemplos

Os exemplos a seguir mostram como é a aplicação do desconto de capacidade reservada do Azure Data Explorer, dependendo do número de unidades de marcação que você comprou e de quando estão em execução.
Por exemplo, para o tamanho do cluster do mecanismo: **2 VMs D11_v2**, seu total de encargos sob demanda é para quatro unidades de medidor de marcação do Azure Data Explorer por hora.

**Cenário 1**

Você compra uma capacidade reservada do Azure Data Explorer para oito unidades de marcação do Azure Data Explorer. Você executa um cluster de mecanismos de duas VMs D13_v2 com um total de 16 núcleos, que cobra por 16 unidades de marcação do Azure Data Explorer por hora, e corresponde ao restante dos atributos da reserva. Você é cobrado pelo preço pago conforme o uso por oito núcleos de uso de computação do Azure Data Explorer e obtém o desconto de reserva por uma hora dos oito núcleos de uso de unidade de marcação do Azure Data Explorer.

Para o restante desses exemplos, suponha que a capacidade reservada do Azure Data Explorer comprada por você seja para um cluster do Azure Data Explorer de 16 núcleos, e o restante dos atributos de reserva corresponde ao cluster do Azure Data Explorer em execução.

**Cenário 2**

Você executa dois clusters do mecanismo Azure Data Explorer com oito núcleos cada por uma hora em duas regiões diferentes. O desconto de reserva de 16 núcleos é aplicado para o cluster e para as 16 unidades de marcação do Azure Data Explorer que eles consomem.

**Cenário 3**

Você executa um cluster de mecanismo do Azure Data Explorer de 16 núcleos das 13h às 13h30. Você executa outro cluster de mecanismo do Azure Data Explorer de 16 núcleos das 13h30 às 14h. Ambos são cobertas pelo desconto de reserva.

**Cenário 4**

Você executa um cluster de mecanismo do Azure Data Explorer de 16 núcleos das 13h às 13h45. Você executa outro cluster de mecanismo do Azure Data Explorer de 16 núcleos das 13h30 às 14h. Você é cobrado o preço pago conforme o uso para a sobreposição de 15 minutos. O desconto de reserva se aplica ao uso de marcação do Azure Data Explorer para o restante do tempo.

Para entender e visualizar a aplicação de suas reservas do Azure nos relatórios de uso de faturamento, confira [Entender o uso de reserva do Azure](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as reservas do Azure, consulte os seguintes artigos:

* [Pagar antecipadamente pelos recursos de computação do Azure Data Explorer com a capacidade reservada do Azure Data Explorer](/azure/data-explorer/pricing-reserved-capacity)  
* [Quais são as reservas para o Azure](save-compute-costs-reservations.md)  
* [Gerenciar reservas do Azure](manage-reserved-vm-instance.md)  
* [Entender o uso de reserva para a sua assinatura paga conforme o uso](understand-reserved-instance-usage.md)
* [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)
* [Entender o uso de reserva para assinaturas de CSP](/partner-center/azure-reservations)