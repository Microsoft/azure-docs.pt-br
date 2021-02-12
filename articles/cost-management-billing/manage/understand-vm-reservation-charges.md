---
title: Noções básicas sobre o desconto de Instâncias de VM Reservadas do Azure
description: Saiba como o desconto de Instância de VM Reservada do Azure é aplicado às máquinas virtuais em execução.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: bda93712dd9a7501fbfddf0e75b8ae3c0088ed55
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341233"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Como o desconto de reserva do Azure é aplicado a máquinas virtuais

Após uma Instância Reservada de Máquina Virtual do Azure, o desconto de reserva é aplicado automaticamente às máquinas virtuais correspondentes aos atributos e à quantidade da reserva. Uma reserva abrange os custos de infraestrutura das suas máquinas virtuais.

Um desconto de reserva é aplicado às VMs base que você compra do Azure Marketplace.

Para a capacidade reservada do Banco de Dados SQL do Microsoft Azure, consulte [Entenda o desconto de Instâncias Reservadas do Azure](../reservations/understand-reservation-charges.md).

A tabela a seguir ilustra os custos da máquina virtual após a compra de uma instância reservada de máquina virtual. Em todos os casos, você é cobrado pelo uso de armazenamento e rede com as taxas normais.

| Tipo de Máquina Virtual  | Encargos com a Instância Reservada de VM |
|-----------------------|--------------------------------------------|
|VMs Linux sem software adicional | A reserva abrange os custos de infraestrutura da sua VM.|
|VMs Linux com encargos de software (por exemplo, Red Hat) | A reserva abrange os custos de infraestrutura. Você é cobrado por software adicional.|
|VMs Windows sem software adicional |A reserva abrange os custos de infraestrutura. Você é cobrado por software do Windows.|
|VMs Windows com software adicional (por exemplo, SQL Server) | A reserva abrange os custos de infraestrutura. Você é cobrado para software Windows e por software adicional.|
|VMs Windows com [Benefício Híbrido do Azure](../../virtual-machines/windows/hybrid-use-benefit-licensing.md) | A reserva abrange os custos de infraestrutura. Os custos de software Windows são cobertos pelo Benefício Híbrido do Azure. Qualquer software adicional é cobrado separadamente.|

## <a name="how-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

Um desconto de reserva é do tipo "*usar ou perder*". Portanto, se você não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível postergar horas reservadas não utilizadas.

Quando você desliga um recurso, o desconto de reserva se aplica automaticamente a outro recurso correspondente no escopo especificado. Se nenhum recurso correspondente for encontrado no escopo especificado, as horas reservadas serão *perdidas*.

## <a name="reservation-discount-for-non-windows-vms"></a>Desconto de reserva a VMs não Windows

 O desconto de reserva do Azure é aplicado às instâncias de VM execução, por hora. Para a aplicação do desconto de reserva, as reservas compradas são comparadas ao uso emitido pelas VMs em execução. Para VMs que não podem ser executadas por uma hora completa, a reserva será preenchida com outras VMs que não estejam usando uma reserva, incluindo VMs em execução simultânea. Ao final da hora, o aplicativo de reserva para VMs na hora é bloqueado. No caso de uma máquina virtual não ser executada por uma hora ou VMs simultâneas em uma hora não preencherem a hora de reserva, a reserva é subutilizada nessa hora. O gráfico a seguir ilustra a aplicação de uma reserva ao uso faturável de VM. A ilustração baseia-se na compra de uma reserva e duas instâncias de VM correspondentes.

![Captura de tela de uma reserva aplicada e duas instâncias de VM correspondentes](./media/understand-vm-reservation-charges/billing-reserved-vm-instance-application.png)

1. Qualquer uso acima da linha de reserva é cobrado com base nas taxas regulares pagas conforme o uso. Não há cobrança por uso abaixo das reservas, pois ele já foi pago como parte da compra de reserva.
2. Na hora 1, a instância 1 é executada por 0,75 hora e a instância 2, por 0,5 hora. O uso total relativo à hora 1 é de 1,25 hora. São cobradas as taxas de pagamento pelo uso da 0,25 hora restante.
3. Nas horas 2 e 3, ambas as instâncias foram executadas por 1 hora. Uma instância está coberta pela reserva e a outro é cobrada com as taxas de pagamento pelo uso.
4. Na hora 4, a instância 1 é executada por 0,5 hora e a instância 2, por 1 hora. A instância 1 está totalmente coberta pela reserva, e também 0,5 hora da instância 2. São cobradas as taxas de pagamento pelo uso da 0,5 hora restante.

Para entender e exibir o aplicativo das Reservas do Azure nos relatórios de uso de cobrança, consulte [Entender o uso de reserva](../reservations/understand-reserved-instance-usage-ea.md).

## <a name="reservation-discount-for-windows-vms"></a>Desconto de reserva para VMs Windows

Quando você executa instâncias de VM Windows, a reserva é aplicada para cobrir os custos de infraestrutura. A aplicação da reserva aos custos de infraestrutura de VMs é igual para máquinas virtuais Windows e não - Windows. Você é cobrado separadamente pelo software Windows por vCPU. Consulte [Custos do software Windows com Reservas](../reservations/reserved-instance-windows-software-costs.md). Você pode cobrir os custos de licenciamento do Windows com [Benefício Híbrido do Azure para Windows Server](../../virtual-machines/windows/hybrid-use-benefit-licensing.md).

## <a name="discount-can-apply-to-different-sizes"></a>O desconto pode se aplicar a diferentes tamanhos

Ao comprar uma Instância de VM Reservada e selecionar **Otimizado para flexibilidade de tamanho da instância**, a cobertura do desconto se aplicará ao tamanho de VM que você selecionar. Ele também poderá se aplicar a outros tamanhos de VMs que estejam no mesmo grupo de flexibilidade de tamanho da instância da série. Para obter mais informações, confira [Flexibilidade de tamanho de máquina virtual com Instâncias de VM Reservadas](../../virtual-machines/reserved-vm-instance-size-flexibility.md).

## <a name="premium-storage-vms-dont-get-non-premium-discounts"></a>As VMs de armazenamento Premium não recebem descontos não Premium

Veja um exemplo. Suponha que você comprou uma reserva de cinco VMs Standard_D1; o desconto de reserva se aplicará somente a VMs Standard_D1 ou a outras VMs na mesma família de instâncias. O desconto não se aplicará à VM Standard_DS1 ou a outros tamanhos no grupo de flexibilidade de tamanho da instância DS1.

A aplicação do desconto de reserva ignora o medidor usado para as VMs e só avalia o ServiceType. Examine o valor `ServiceType` em `AdditionalInfo` para determinar as informações de série/grupo de flexibilidade de instância para suas VMs. Os valores estão no arquivo CSV do uso.

Não é possível alterar diretamente o grupo/a série de flexibilidade da instância da reserva após a compra. No entanto, você pode *trocar* uma reserva de VM de um grupo/uma série de flexibilidade de instância para outro.

## <a name="services-that-get-vm-reservation-discounts"></a>Serviços que têm descontos de reserva de VM

Suas reservas de VM podem se aplicar ao uso de VM emitido de vários serviços – não só para suas implantações de VM. Os recursos que têm descontos de reserva mudam dependendo da configuração de flexibilidade de tamanho da instância.

### <a name="instance-size-flexibility-setting"></a>Configuração de flexibilidade de tamanho da instância

A configuração de flexibilidade de tamanho da instância determina quais serviços têm os descontos de instância reservada.

Se a configuração estiver ativada ou desativada, os descontos de reserva se aplicarão automaticamente a qualquer uso da VM correspondente quando *ConsumedService* for `Microsoft.Compute`. Portanto, verifique os dados de uso para o valor *ConsumedService*. Alguns exemplos incluem:

- Máquinas virtuais
- conjuntos de escala de máquina virtual
- Serviço de contêiner
- Implantações do Lote do Azure (no modo de assinaturas do usuário)
- AKS (Serviço de Kubernetes do Azure)
- Service Fabric

Quando a configuração estiver ativada, os descontos de reserva se aplicarão automaticamente ao uso de VM correspondente quando *ConsumedService* for qualquer um dos seguintes itens:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Verifique o valor *ConsumedService* em seus dados de uso para determinar se o uso está qualificado para descontos de reserva.

Para obter mais informações sobre a flexibilidade de tamanho da instância, confira [Flexibilidade de tamanho de máquina virtual com Instâncias de VM Reservadas](../../virtual-machines/reserved-vm-instance-size-flexibility.md).


## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são reservas para o Azure?](../reservations/save-compute-costs-reservations.md)
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Pagar antecipadamente por recursos de computação de banco de dados SQL com capacidade reservada do Banco de Dados SQL do Azure](../../azure-sql/database/reserved-capacity-overview.md)
- [Gerenciar reservas do Azure](../reservations/manage-reserved-vm-instance.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](../reservations/understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](../reservations/understand-reserved-instance-usage-ea.md)
- [Entender o uso de reserva para assinaturas de CSP](/partner-center/azure-reservations)
- [Custos de software do Windows não estão incluídos nas reservas](../reservations/reserved-instance-windows-software-costs.md)