---
title: Instâncias reservadas de VM de EA do Azure
description: Este artigo resume como as reservas do Azure para instâncias reservadas de VM podem ajudá-lo a economizar dinheiro com seu Registro Enterprise.
author: bandersmsft
ms.author: banders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 072377f98939d996547cb6ee9f9a535b4b32a3dc
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98597963"
---
# <a name="azure-ea-vm-reserved-instances"></a>Instâncias reservadas de VM de EA do Azure

Este artigo resume como as reservas do Azure para instâncias reservadas de VM podem ajudá-lo a economizar dinheiro com seu Registro Enterprise. Para obter mais informações sobre reservas, confira [O que são as Reservas do Azure?](../reservations/save-compute-costs-reservations.md).

## <a name="reservation-exchanges-and-refunds"></a>Trocas e reembolsos de reserva

Você pode trocar uma reserva por outra do mesmo tipo. Também é possível pedir reembolso de uma reserva, de até US$ 50.000 por ano, se não precisar mais dela. O portal do Azure pode ser usado para trocar ou reembolsar uma reserva. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](../reservations/exchange-and-refund-azure-reservations.md).

### <a name="partial-refunds"></a>Reembolsos parciais

Emitiremos um reembolso parcial quando os clientes EA devolverem reservas que foram adquiridas por meio do excedente e não Pagamento antecipado do Azure (anteriormente conhecido como compromisso monetário).

O reembolso será exibido no Portal do EA como um ajuste negativo no mês anterior e um ajuste positivo no mês atual. Ele será exibido de maneira semelhante a uma troca de reservas. A nota de crédito referenciará o número da fatura original; portanto, para reconciliar a compra inicial com a nota de crédito, veja o número da fatura original.

## <a name="reservation-costs-and-usage"></a>Custos e uso de reserva

Clientes do Contrato Enterprise podem exibir dados de custo e uso nas APIs Rest do portal do Azure. Com relação aos custos e ao uso de reservas, você pode:

- Obter dados de compra de reserva.
- Saber qual assinatura, grupo de recursos ou recurso usou uma reserva.
- Estornar pelo uso da reserva.
- Calcular a economia de reserva.
- Obter dados de subutilização de reserva.
- Amortizar custos de reserva.

Para obter mais informações sobre custos de reserva e uso, confira [Obter uso e custos de reserva do Contrato Enterprise](../reservations/understand-reserved-instance-usage-ea.md).

Para saber mais sobre os preços, confira [Preços de Máquinas Virtuais do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Preços das Máquinas Virtuais do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

## <a name="reserved-instances-api-support"></a>Suporte para a API de instâncias reservadas

Use as APIs do Azure para obter programaticamente informações para sua organização sobre reservas de software ou serviço do Azure. Por exemplo, use as APIs para:

- Localizar as reservas para comprar
- Comprar uma reserva
- Exibir as reservas adquiridas
- Exibir e gerenciar o acesso à reserva
- Dividir ou mesclar reservas
- Alterar o escopo de reservas

Para obter mais informações, confira [APIs para automação de reserva do Azure](../reservations/reservation-apis.md).

## <a name="azure-reserved-virtual-machine-instances"></a>Instâncias de máquinas virtuais reservadas do Azure

As instâncias reservadas podem reduzir os custos de sua máquina virtual em até 72% em relação aos preços pagos conforme o uso em todas as VMs. A economia pode chegar a 82% quando elas são combinadas com o Benefício Híbrido do Azure. As instâncias reservadas ajudam você a gerenciar as cargas de trabalho, o orçamento e a aprimorar suas previsões, com pagamento antecipado para um prazo de um ou de três anos. Você também pode trocar ou cancelar reservas conforme as necessidades do negócio mudam.

### <a name="how-to-buy-reserved-virtual-machine-instances"></a>Como comprar instância de máquina virtual reservada

Para comprar uma instância de máquina virtual reservada do Azure, um administrador de registro do Azure Enterprise deve habilitar a opção de compra _Reservar Instância_. Essa opção está na seção _Detalhes do Registro_, na guia _Registro_ no [Portal do EA do Azure](https://ea.azure.com/).

Depois que o registro de EA estiver habilitado para adicionar instâncias reservadas, qualquer proprietário de conta com uma assinatura ativa associada ao registro EA poderá comprar uma instância de máquina virtual reservada no [portal do Azure](https://aka.ms/reservations). Para obter mais informações, consulte [Pagar antecipadamente por máquinas virtuais e economizar dinheiro com Instâncias de Máquina Virtual Reservada](../../virtual-machines/prepay-reserved-vm-instances.md).

### <a name="how-to-view-reserved-instance-purchase-details"></a>Como exibir detalhes de compra da instância reservada

Você pode exibir seus detalhes de compra da instância reservada por meio do menu _Reservas_ no lado esquerdo do [portal do Azure](https://aka.ms/reservations) ou no [portal de EA do Azure](https://ea.azure.com/). Selecione **Relatórios** no menu do lado esquerdo e role para baixo até a seção _Encargos por serviços_ na guia _Resumo de uso_. Role até a parte inferior da seção e suas compras e uso da instância reservada serão listadas no final, como indicado pela designação de `1 year` ou `3 years` ao lado do nome do serviço, por exemplo: `Standard_DS1_v2 eastus 1 year` ou `Standard_D2s_v3 eastus2 3 years`.

### <a name="how-can-i-change-the-subscription-associated-with-reserved-instance-or-transfer-my-reserved-instance-benefits-to-a-subscription-under-the-same-account"></a>Como posso alterar a assinatura associada à instância reservada ou transferir meus benefícios de instância reservada para uma assinatura na mesma conta?

Você pode alterar a assinatura que recebe os benefícios da instância reservada:

- Entrar no [portal do Azure](https://aka.ms/reservations).
- Atualizando o escopo da assinatura aplicada associando uma assinatura diferente à mesma conta.

Para obter mais informações sobre como alterar o escopo de uma reserva, confira [Alterar o escopo da reserva](../reservations/manage-reserved-vm-instance.md#change-the-reservation-scope).

### <a name="how-to-view-reserved-instance-usage-details"></a>Como exibir detalhes de uso da instância reservada

Você pode exibir os detalhes de uso da instância reservada no [portal do Azure](https://aka.ms/reservations) ou no [portal do EA do Azure](https://ea.azure.com/) (para clientes do EA que têm acesso para exibir as informações de cobrança) em _Relatórios_ > _Resumo de uso_ > _Encargos por serviços_. Suas instâncias reservadas podem ser identificadas como nomes de serviço que contêm "Reserva", por exemplo: `Reservation-Base VM or Virtual Machines Reservation-Windows Svr (1 Core)`.

O seu arquivo CSV com detalhes de uso e download de relatório avançado contém informações adicionais de uso da instância reservada. O campo _Informações Adicionais_ ajuda a identificar o uso da instância reservada.

Se você não usou o benefício híbrido do Azure para comprar instâncias de VM reservadas do Azure, as instâncias reservadas emitirão dois medidores (hardware e software). Quando você usar o Benefício Híbrido do Azure para comprar a instância reservada, não verá o medidor de software nos detalhes de uso dessa instância.

### <a name="reserved-instance-billing"></a>Cobrança de instância reservada

Para clientes corporativos, o pagamento antecipado do Azure é usado para comprar instâncias de VM reservadas do Azure. Se o seu registro tiver saldo suficiente do pagamento antecipado do Azure para cobrir a compra da instância reservada, o valor será deduzido do seu saldo do pagamento antecipado do Azure e você não receberá uma fatura da compra.

Nos cenários em que os clientes do Azure EA usaram todo o pagamento antecipado do Azure, ainda é possível comprar instâncias reservadas, e essas compras serão faturadas na sua próxima fatura de excedente. O excedente da instância reservada, se houver, fará parte da fatura de excedente regular.

### <a name="reserved-instance-expiration"></a>Expiração da instância reservada

Você receberá notificações por email 30 dias antes da reserva e na expiração. Quando a reserva expirar, as VMs implantadas continuarão a ser executadas e faturadas de acordo com a taxa paga conforme o uso. Para saber mais, confira [Oferta de instâncias de Máquina Virtual Reservada.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre reservas do Azure, confira [O que são as Reservas do Azure?](../reservations/save-compute-costs-reservations.md).
- Para saber mais sobre os custos e o uso da reserva com Contrato Enterprise, confira [Obter custos e uso de reserva do Contrato Enterprise](../reservations/understand-reserved-instance-usage-ea.md).
- Para saber mais sobre os preços, confira [Preços de Máquinas Virtuais do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Preços das Máquinas Virtuais do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).