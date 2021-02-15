---
title: Noções básicas sobre o desconto de Instâncias Reservadas de Host Dedicado do Azure
description: Saiba como o desconto em Instâncias de VM Reservadas do Azure é aplicado aos Hosts Dedicados do Azure.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 8d273aae3588a006f7b0a55d2798b7a43c040c9b
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148383"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-azure-dedicated-hosts"></a>Como o desconto de reserva do Azure é aplicado aos Hosts Dedicados do Azure

Após comprar uma Instância Reservada de Host Dedicado do Azure, o desconto de reserva é aplicado automaticamente aos hosts dedicados correspondentes aos atributos e à quantidade da reserva. Uma reserva abrange os custos de computação dos hosts dedicados.

## <a name="how-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

Um desconto de reserva é do tipo "*usar ou perder*". Portanto, se você não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível postergar horas reservadas não utilizadas.

Quando você exclui um host dedicado, o desconto de reserva se aplica automaticamente a outro recurso correspondente no escopo especificado. Se nenhum recurso correspondente for encontrado no escopo especificado, as horas reservadas serão *perdidas*.

## <a name="reservation-discount-for-dedicated-hosts"></a>Desconto de reserva para Hosts Dedicados

A instância reservada de Hosts Dedicados do Azure fornece um desconto no custo da infraestrutura de computação usada com seus hosts dedicados. O desconto se aplica aos seus hosts dedicados, independentemente de estarem sendo utilizados por máquinas virtuais ou não. A reserva não cobre custos adicionais, como licenciamento, uso de rede ou consumo de armazenamento por máquina virtual implantada no host dedicado.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Caso tenha dúvidas ou precise de ajuda,  [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são reservas para o Azure?](./save-compute-costs-reservations.md)

- [Usando Hosts Dedicados do Azure](../../virtual-machines/dedicated-hosts.md)

- [Preço de Hosts Dedicados](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Gerenciar reservas do Azure](./manage-reserved-vm-instance.md)

- [Entender o uso de reserva para a sua assinatura paga conforme o uso](./understand-reserved-instance-usage.md)

- [Entender o uso de reserva para seu registro de empresa](./understand-reserved-instance-usage-ea.md)

- [Entender o uso de reserva para assinaturas de CSP](/partner-center/azure-reservations)