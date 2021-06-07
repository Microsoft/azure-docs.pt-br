---
title: Entender o desconto de reservas para fluxos de dados de Azure Data Factory | Microsoft Docs
description: Saiba como um desconto de reserva é aplicado à execução de fluxos de dados do ADF. O desconto é aplicado a esses fluxos de dados a cada hora.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: makromer
ms.openlocfilehash: 12b640fd97f48e293320593b33ab2fdc54980c0f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716288"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-data-factory-data-flows"></a>Como um desconto de reserva é aplicado a Azure Data Factory fluxos de dados

Depois de comprar a capacidade reservada do fluxo de dados do ADF, o desconto de reserva é aplicado automaticamente aos fluxos de dados usando um tempo de execução de integração do Azure que corresponde ao tipo de computação e à contagem de núcleos da reserva.

## <a name="how-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

Um desconto de reserva é do tipo "*usar ou perder*". Portanto, se você não tiver os recursos de integração do Azure correspondentes usados para qualquer hora, você perderá uma quantidade de reserva para essa hora. Não é possível postergar horas reservadas não utilizadas.

Quando você pára de usar o tempo de execução de integração para fluxos de dados, o desconto de reserva se aplica automaticamente a outro recurso correspondente no escopo especificado. Se nenhum recurso correspondente for encontrado no escopo especificado, as horas reservadas serão *perdidas*.

## <a name="discount-applied-to-adf-data-flows"></a>Desconto aplicado a fluxos de dados do ADF

O desconto de capacidade reservada do fluxo de dados do ADF é aplicado à execução de tempos de execução de integração por hora. A reserva que você compra é correspondida ao tipo de computação que está sendo usado pelos tempos de execução de integração para seus fluxos de dados. Para fluxos de dados que não executam a hora completa, a reserva é aplicada automaticamente a outros fluxos de dados que correspondem aos atributos de reserva. O desconto também pode ser aplicado a fluxos de dados que são executados simultaneamente. Se você não tiver fluxos de dados que são executados por uma hora completa que correspondam aos atributos de reserva, você não obterá o benefício completo do desconto de reserva para essa hora.

Os exemplos a seguir mostram como o desconto de capacidade reservada do fluxo de dados do ADF se aplica dependendo do número de núcleos comprados e quando eles estão em execução.

- Cenário 1: você compra uma reserva de fluxo de dados do ADF por 1 hora de 80 núcleos de computação com otimização de memória inserindo 80 como a quantidade para o tipo de computação com otimização de memória. Você executa um fluxo de dados com um tempo de execução de integração do Azure definido como 144 núcleos de memória otimizada por uma hora. Você é cobrado pelo preço pré-pago de 64 núcleos de uso de fluxo de dados por uma hora. Você Obtém o desconto de reserva por uma hora de 80 núcleos de uso com otimização de memória.
- Cenário 2: você compra uma reserva de fluxo de dados do ADF por 1 hora de 32 núcleos de computação de uso geral inserindo 32 como a quantidade para o tipo de computação de uso geral. Você depura seus fluxos de dados por uma hora usando 32 núcleos de computação geral do tempo de execução de integração do Azure. Você Obtém o desconto de reserva para a hora de uso inteira.

Para entender e visualizar o aplicativo de suas reservas do Azure nos relatórios de uso de faturamento, consulte [Entender o uso de reserva do Azure](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as reservas do Azure, consulte o seguinte artigo:

- [O que são Reservas do Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)