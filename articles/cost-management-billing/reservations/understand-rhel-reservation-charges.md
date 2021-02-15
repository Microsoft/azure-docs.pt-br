---
title: Descontos do plano de reserva do Red Hat – Azure
description: Saiba como os descontos de plano do Red Hat são aplicados ao software Red Hat em máquinas virtuais.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: banders
ms.openlocfilehash: 8e735d623cc991d2e3720e0ec418eee77aa8c46c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350850"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Entender como o desconto do plano de reserva de software do Red Hat Linux Enterprise é aplicado no Azure

Após a compra de um plano do Red Hat Linux, o desconto é aplicado automaticamente às VMs (máquinas virtuais) do Red Hat implantadas que correspondem à reserva. Um plano do Red Hat Linux cobre o custo de execução do software Red Hat em uma VM do Azure.

Para comprar o plano ideal do Red Hat Linux, você precisa entender quais VMs do Red Hat são executadas e o número de vCPUs nessas VMs. Use as seções a seguir para ajudar a identificar, a partir do arquivo CSV de uso, qual plano comprar.

## <a name="discount-applies-to-different-vm-sizes"></a>O desconto se aplica a diferentes tamanhos de VM

Como as Instâncias de VM Reservadas, as compras do plano do Red Hat oferecem flexibilidade de tamanho da instância. Isso significa que seu desconto se aplica mesmo quando você implanta uma VM com uma contagem diferente de vCPU. O desconto se aplica a diferentes tamanhos de VM dentro do plano de software.

O desconto se aplica a diferentes tamanhos de VM dentro do plano de software. A proporção compara a área de cobertura relativa de cada tamanho de VM nesse grupo. A proporção depende das vCPUs da VM. Use o valor da proporção para calcular quantas instâncias de VM obtêm o desconto do plano do Red Hat Linux.

Por exemplo, se você comprar um plano para o Red Hat Linux Enterprise Server para uma VM com 1 ou 4 vCPUs, a proporção dessa reserva será 1. O desconto cobre o custo do software Red Hat para:

- 1 VM implantada com 1 ou 4 vCPUs
- ou 0,46 ou, ainda, cerca de 46% dos custos de Red Hat Enterprise Linux para uma VM com 5 ou mais vCPUs.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Nomes do marketplace no portal do Azure:
- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 8.2

[Verificar os medidores do Red Hat Enterprise Linux aos quais o plano se aplica](https://isfratio.blob.core.windows.net/isfratio/RHELRatios.csv)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as reservas, consulte os seguintes artigos:

- [Quais são as reservas para o Azure](save-compute-costs-reservations.md)
- [Pagar antecipadamente por planos de software Red Hat com as reservas do Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Gerenciar reservas do Azure](manage-reserved-vm-instance.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).