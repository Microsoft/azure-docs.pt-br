---
title: Pagar antecipadamente pelas máquinas virtuais do Azure para economizar dinheiro
description: Saiba como comprar Instâncias de Máquinas Virtuais Reservadas do Azure para economizar nos custos de sua computação.
author: vikramdesai01
manager: vikramdesai01
ms.service: virtual-machines
ms.subservice: reserved-instances
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/30/2017
ms.author: vikdesai
ms.openlocfilehash: 7eab410bb2344617e555a7a9d1d44cd9adcc676d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674732"
---
# <a name="save-costs-with-azure-reserved-vm-instances"></a>Economize custos com instâncias de VM reservadas do Azure


Ao se comprometer com uma instância de VM reservada do Azure, você pode economizar dinheiro. O desconto da reserva é aplicado automaticamente ao número de máquinas virtuais em execução que correspondem ao escopo e atributos da reserva. Você não precisa atribuir uma reserva a uma máquina virtual para obter os descontos. Uma compra de instância reservada abrange apenas a parte de computação do uso da VM. Para VMs do Windows, o medidor de uso é dividido em dois medidores separados. Há um medidor de computação, que é o mesmo que o medidor do Linux e um medidor de IP do Windows. Os encargos que você vê quando faz a compra são apenas para os custos de computação. Os encargos não incluem os custos de software do Windows. Para obter mais informações sobre os custos de software, consulte [custos de software não incluídos com instâncias de VM reservadas do Azure](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Determinar o tamanho correto da VM antes de comprar

Antes de comprar uma reserva, você deve determinar o tamanho da VM de que precisa. As seções a seguir ajudarão você a determinar o tamanho correto da VM.

### <a name="use-reservation-recommendations"></a>Usar as recomendações de reserva

Use as recomendações de reserva para ajudar a determinar as reservas que devem ser compradas.

- As recomendações de compra e as quantidades recomendadas são mostradas quando você adquire uma instância reservada de VM no portal do Azure.
- O Assistente do Azure fornece recomendações de compra para assinaturas individuais.  
- Use as APIs para obter recomendações de compra para o escopo compartilhado e o escopo de assinatura única. Para obter mais informações, confira [APIs de recomendação de compra de instância reservada para clientes empresariais](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Para os clientes do EA (Contrato Enterprise) e do MCA (Contrato de Cliente da Microsoft), as recomendações de compra para os escopos de assinatura compartilhada e única estão disponíveis no [pacote de conteúdo do Azure Consumption Insights para Power BI](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Serviços que têm descontos de reserva de VM

Suas reservas de VM podem se aplicar ao uso de VM emitido de vários serviços – não só para suas implantações de VM. Os recursos que têm descontos de reserva mudam dependendo da configuração de flexibilidade de tamanho da instância.

#### <a name="instance-size-flexibility-setting"></a>Configuração de flexibilidade de tamanho da instância

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

Para obter mais informações sobre a flexibilidade de tamanho da instância, confira [Flexibilidade de tamanho de máquina virtual com Instâncias de VM Reservadas](reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>Analisar suas informações de uso

Analise suas informações de uso para ajudar a determinar quais reservas devem ser compradas. Os dados de uso estão disponíveis no arquivo de uso e nas APIs. Use-os juntos para determinar qual reserva deve ser comprada. Verifique as instâncias de VM que têm alto uso diariamente para determinar a quantidade de reservas a serem compradas. Evite a `Meter` subcategoria e os `Product` campos nos dados de uso. Eles não fazem distinção entre os tamanhos de VM que usam o armazenamento Premium. Se você usar esses campos para determinar o tamanho da VM para a compra de reserva, você poderá comprar o tamanho errado. Em seguida, você não obterá o desconto de reserva esperado. Em vez disso, consulte o `AdditionalInfo` campo em seu arquivo de uso ou API de uso para determinar o tamanho correto da VM.

Seu arquivo de uso mostra os preços por período de cobrança e uso diário. Para obter informações sobre como baixar o arquivo de uso, confira [Ver e baixar o uso e os preços do Azure](../cost-management-billing/understand/download-azure-daily-usage.md). Em seguida, usando as informações do arquivo de uso, [determine a reserva a ser comprada](../cost-management-billing/reservations/determine-reservation-purchase.md).

### <a name="purchase-restriction-considerations"></a>Considerações sobre a restrição de compra

As instâncias de VM reservadas estão disponíveis para a maioria dos tamanhos de VM com algumas exceções. Os descontos de reserva não se aplicam às seguintes VMs:

- **Série de VMs** -séries a, Av2 ou G.

- **VMs de visualização ou promoção** -qualquer série de VM ou tamanho que esteja na visualização ou use o medidor promocional.

- **Nuvens**: as reservas não estão disponíveis para compra nas regiões da Alemanha ou da China.

- **Cota insuficiente** – uma reserva com escopo para uma única assinatura deve ter a cota vCPU disponível na assinatura para a nova ri. Por exemplo, se a assinatura de destino tiver um limite de cota de 10 vCPUs para a Série D, você não poderá comprar uma reserva para 11 instâncias Standard_D1. A verificação de cota para reservas inclui as VMs já implantadas na assinatura. Por exemplo, se a assinatura tiver uma cota de 10 vCPUs para o D-Series e tiver duas instâncias standard_D1 implantadas, você poderá comprar uma reserva para 10 instâncias standard_D1 nessa assinatura. Você pode [criar uma solicitação de aumento de cotação](../azure-portal/supportability/resource-manager-core-quotas-request.md) para resolver esse problema.

- **Restrições de capacidade** -em raras circunstâncias, o Azure limita a compra de novas reservas para o subconjunto de tamanhos de VM, devido à baixa capacidade em uma região.

## <a name="buy-a-reserved-vm-instance"></a>Comprar uma Instância de VM Reservada

Você pode comprar uma instância de VM reservada no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Pague pela reserva [antecipadamente ou com pagamentos mensais](../cost-management-billing/reservations/prepare-buy-reservation.md).
Esses requisitos se aplicam à compra de uma instância de VM reservada:

- Você precisa ter a função Proprietário em, no mínimo, uma assinatura do EA ou uma assinatura com uma taxa paga conforme o uso.
- Para as assinaturas do EA, a opção **Adicionar Instâncias Reservadas** precisa estar habilitada no [Portal do EA](https://ea.azure.com/). Ou, se essa configuração estiver desabilitada, você precisará ser um Administrador de EA da assinatura.
- Para o programa do CSP (Provedor de Solução na Nuvem) somente os agentes administradores ou agentes de vendas podem comprar reservas.

Para comprar uma instância:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os serviços** > **Reservas**.
1. Escolha **Adicionar** para comprar uma nova reserva e, em seguida, clique em **Máquina Virtual**.
1. Preencha os campos obrigatórios. As instâncias de VM em execução que correspondem com os atributos que você selecionar, ficam qualificados para obter o desconto de reserva. O número real de suas instâncias VM que obtêm o desconto depende do escopo e da quantidade selecionada.

Se você tiver um contrato EA, poderá usar a opção **Adicionar mais** para adicionar mais instâncias rapidamente. A opção não está disponível para outros tipos de assinaturas.


| Campo      | Descrição|
|------------|--------------|
|Subscription|A assinatura usada para pagar pela reserva. Os custos da reserva são cobrados segundo a forma de pagamento da assinatura. O tipo de assinatura deve ser um contrato empresarial (números da oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Contrato de Cliente da Microsoft ou ainda uma assinatura individual com tarifas pagas conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Os preços são deduzidos do saldo do Pagamento antecipado do Azure (anteriormente conhecido como compromisso monetário), se disponível, ou cobrados como excedente. Para uma assinatura com taxas pagas conforme o uso, as cobranças são feitas na forma de pagamento de cartão de crédito ou de fatura na assinatura.|    
|Escopo       |O escopo da reserva pode abranger uma ou várias assinaturas (escopo compartilhado). Se você selecionar: <ul><li>**Escopo de grupo de recursos único** — aplica o desconto de reserva apenas aos recursos correspondentes no grupo de recursos selecionado.</li><li>**Escopo de assinatura única** — aplica o desconto de reserva apenas aos recursos correspondentes na assinatura selecionada.</li><li>**Escopo compartilhado** — aplica o desconto de reserva aos recursos correspondentes em assinaturas qualificadas que estão no contexto de cobrança. Para clientes do EA, o contexto de cobrança é o registro. Para assinaturas individuais com tarifas pagas conforme o uso, o escopo do orçamento são todas as assinaturas qualificadas criadas pelo administrador da conta.</li></ul>|
|Região    |A região do Azure que é coberta pela reserva.|    
|Tamanho da VM     |O tamanho das instâncias de VM.|
|Otimizar para     |A flexibilidade do tamanho da instância VM é selecionada por padrão. Clique em **Configurações avançadas** para alterar o valor de flexibilidade do tamanho da instância para aplicar o desconto de reserva a outras VMs no mesmo [grupo de tamanhos de VM](reserved-vm-instance-size-flexibility.md). A prioridade da capacidade prioriza a capacidade de data center para suas implantações. Ele oferece confiança adicional em sua capacidade de iniciar as instâncias de VM quando você precisar delas. A prioridade de capacidade está disponível apenas quando o escopo de reserva é uma assinatura única. |
|Termo        |Um ano ou três anos. Há também um termo de 5 anos disponível apenas para VMs HBv2.|
|Quantidade    |O número de instâncias sendo compradas na reserva. A quantidade é o número de instâncias de VM que podem obter o desconto de cobrança. Por exemplo, se você estiver executando 10 VMs Standard_D2 no leste dos EUA, você deve especificar Quantity como 10 para maximizar o benefício de todas as VMs em execução. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Dados de uso e utilização de reserva

Seus dados de uso têm um preço efetivo de zero para o uso, que obtém um desconto de reserva. Você pode ver qual instância de VM recebeu o desconto de reserva para cada reserva.

Para obter mais informações sobre como os descontos de reserva aparecem nos dados de uso, consulte [entender o uso de reserva do Azure para o registro de sua empresa](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) se você for um cliente do ea. Se você tiver uma assinatura individual, consulte [entender o uso de reserva do Azure para sua assinatura paga conforme o uso](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Alterar uma reserva após a compra

É possível realizar os seguintes tipos de alterações em uma reserva após a compra:

- Atualizar o escopo de reserva
- Flexibilidade de tamanho de instância (se aplicável)
- Propriedade

Você também pode dividir uma reserva em partes menores e mesclar reservas já divididas. Nenhuma das alterações causa uma nova transação comercial ou alterar a data de término da reserva.

Você não pode fazer os seguintes tipos de alterações após a compra, diretamente:

- Uma região de reserva existente
- SKU
- Quantidade
- Duration

No entanto, você pode *trocar* uma reserva se desejar fazer alterações.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

- Para aprender a gerenciar uma reserva, confira [Gerenciar Reservas do Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md).
- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
    - [O que são Reservas do Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [Gerenciar Reservas no Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [Entender como o desconto de reserva é aplicado](../cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [Noções básicas sobre o uso de reserva para uma assinatura com taxas pagas conforme o uso](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [Entender o uso de reserva para seu registro de empresa](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [Custos de software do Windows não estão incluídos nas reservas](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](/partner-center/azure-reservations)