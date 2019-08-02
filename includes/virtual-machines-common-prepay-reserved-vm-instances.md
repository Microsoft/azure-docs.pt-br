---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 07/19/2019
ms.openlocfilehash: 763d424d9d462c4a9531df84f3e5e26bfc1b0a14
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68502259"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances-ri"></a>Pagar antecipadamente por máquinas virtuais com instâncias de VM reservadas do Azure (RI)

Pagar antecipadamente por máquinas virtuais e economizar dinheiro com Instâncias de Máquina Virtual Reservada do Microsoft Azure. O desconto da reserva é aplicado automaticamente ao número de máquinas virtuais em execução que correspondem ao escopo e atributos da reserva. Você não precisa atribuir uma reserva a uma máquina virtual para obter os descontos. Uma compra de instância reservada abrange apenas a parte de computação do uso da VM. Para VMs do Windows, o medidor de uso é dividido em dois medidores separados. Há um medidor de computação, que é o mesmo que o medidor do Linux e um medidor de IP do Windows. Os encargos que você vê quando faz a compra são apenas para os custos de computação. Os encargos não incluem os custos de software do Windows. Para obter mais informações sobre os custos de software, consulte [custos de software não incluídos com instâncias de VM reservadas do Azure](../articles/billing/billing-reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Determinar o tamanho correto da VM antes de comprar

Antes de comprar uma reserva, você deve determinar o tamanho da VM de que precisa. As seções a seguir ajudarão você a determinar o tamanho correto da VM.

### <a name="use-reservation-recommendations"></a>Usar recomendações de reserva

Você pode usar recomendações de reserva para ajudar a determinar as reservas que devem ser compradas.

- As recomendações de compra e a quantidade recomendada são mostradas quando você adquire uma instância reservada de VM no portal do Azure.
- O Azure Advisor fornece recomendações de compra para assinaturas individuais.  
- Você pode usar as APIs para obter recomendações de compra para o escopo compartilhado e o escopo de assinatura única. Para obter mais informações, consulte [APIs de recomendação de compra de instância reservada para clientes empresariais](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Para clientes Enterprise Agreement (EA), as recomendações de compra para escopos de assinatura compartilhada e única estão disponíveis com o [Azure consumption insights pacote de conteúdo Power bi](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Serviços que obtêm descontos de reserva de VM

Suas reservas de VM podem se aplicar ao uso de VM emitido de vários serviços – não apenas para implantações de VM. Os recursos que obtêm descontos de reserva mudam dependendo da configuração de flexibilidade do tamanho da instância.

#### <a name="instance-size-flexibility-setting"></a>Configuração de flexibilidade do tamanho da instância

A configuração flexibilidade do tamanho da instância determina quais serviços obtêm os descontos da instância reservada.

Se a configuração estiver ativada ou desativada, os descontos de reserva serão aplicados automaticamente a  qualquer uso `Microsoft.Compute`de VM correspondente quando o ConsumedService for. Portanto, verifique os dados de uso para o valor *ConsumedService* . Eis alguns exemplos:

- Máquinas virtuais
- Conjuntos de dimensionamento de máquinas virtuais
- Serviço de contêiner
- Implantações do lote do Azure (no modo de assinaturas do usuário)
- AKS (Serviço de Kubernetes do Azure)
- Service Fabric

Quando a configuração está ativada, os descontos de reserva se aplicam automaticamente ao uso correspondente da VM quando o *ConsumedService* é qualquer um dos seguintes itens:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Verifique o valor de *ConsumedService* em seus dados de uso para determinar se o uso está qualificado para descontos de reserva.

Para obter mais informações sobre flexibilidade de tamanho de instância, consulte [flexibilidade de tamanho de máquina virtual com instâncias de VM reservadas](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>Analisar suas informações de uso
Analise suas informações de uso para ajudar a determinar quais reservas devem ser compradas.

Os dados de uso estão disponíveis no arquivo de uso e nas APIs. Use-os juntos para determinar qual reserva deve ser comprada. Verifique as instâncias de VM que têm alto uso diariamente para determinar a quantidade de reservas a serem compradas.

Evite a `Meter` subcategoria e `Product` os campos nos dados de uso. Eles não fazem distinção entre os tamanhos de VM que usam o armazenamento Premium. Se você usar esses campos para determinar o tamanho da VM para a compra de reserva, você poderá comprar o tamanho errado. Em seguida, você não obterá o desconto de reserva esperado. Em vez disso, consulte `AdditionalInfo` o campo em seu arquivo de uso ou API de uso para determinar o tamanho correto da VM.

### <a name="purchase-restriction-considerations"></a>Considerações sobre a restrição de compra

As instâncias de VM reservadas estão disponíveis para a maioria dos tamanhos de VM com algumas exceções. Os descontos de reserva não se aplicam às seguintes VMs:

- **Série de VMs** -séries a, Av2 ou G.

- **VMs de visualização ou promoção** -qualquer série de VM ou tamanho que esteja na visualização ou use o medidor promocional.

- **Nuvens** – as reservas não estão disponíveis para compra nas regiões da Alemanha ou da China.

- **Cota insuficiente** – uma reserva com escopo para uma única assinatura deve ter a cota vCPU disponível na assinatura para a nova ri. Por exemplo, se a assinatura de destino tiver um limite de cota de 10 vCPUs para a Série D, você não poderá comprar uma reserva para 11 instâncias Standard_D1. A verificação de cota para reservas inclui as VMs já implantadas na assinatura. Por exemplo, se a assinatura tiver uma cota de 10 vCPUs para o D-Series e tiver duas instâncias standard_D1 implantadas, você poderá comprar uma reserva para 10 instâncias standard_D1 nessa assinatura. Você pode [criar uma solicitação de aumento](../articles/azure-supportability/resource-manager-core-quotas-request.md) de cotação para resolver esse problema.

- **Restrições de capacidade** -em raras circunstâncias, o Azure limita a compra de novas reservas para o subconjunto de tamanhos de VM, devido à baixa capacidade em uma região.

## <a name="buy-a-reserved-vm-instance"></a>Comprar uma Instância de VM Reservada

Você pode comprar uma instância de VM reservada no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Esses requisitos se aplicam à compra de uma instância de VM reservada:

- Você deve estar em uma função de proprietário para pelo menos uma assinatura de EA ou uma assinatura com uma taxa pré-paga.
- Para assinaturas EA, a opção **adicionar instâncias reservadas** deve ser habilitada no [portal de ea](https://ea.azure.com/). Ou, se essa configuração estiver desabilitada, você deverá ser um administrador de EA para a assinatura.
- Para o programa do CSP (Provedor de Solução na Nuvem) somente os agentes administradores ou agentes de vendas podem comprar reservas.

Para comprara uma instância:

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Selecione **Todos os serviços** > **Reservas**.
1. Selecione **Adicionar** para comprar uma nova reserva e, em seguida, clique em **máquina virtual**.
1. Insira os campos obrigatórios. As instâncias de VM em execução que correspondem com os atributos que você selecionar, ficam qualificados para obter o desconto de reserva. O número real de suas instâncias VM que obtêm o desconto depende do escopo e da quantidade selecionada.

| Campo      | Descrição|
|------------|--------------|
|Assinatura|A assinatura usada para pagar pela reserva. São cobrados os custos iniciais para a reserva à forma de pagamento na assinatura. O tipo de assinatura deve ser um contrato empresarial (números da oferta: MS-AZR-0017P ou MS-AZR-0148P) ou uma assinatura individual com tarifas pagas conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como média. Para uma assinatura com tarifas pagas conforme o uso, os encargos são cobrados no cartão de crédito ou no método de pagamento de fatura na assinatura.|    
|Escopo       |O escopo de assinatura pode abranger uma ou várias assinaturas (escopo compartilhado). Se você selecionar: <ul><li>**Escopo do grupo de recursos único** — aplica o desconto de reserva aos recursos correspondentes somente no grupo de recursos selecionado.</li><li>**Escopo de assinatura única** — aplica o desconto de reserva aos recursos de correspondência na assinatura selecionada.</li><li>**Escopo compartilhado** — aplica o desconto de reserva aos recursos correspondentes em assinaturas qualificadas que estão no contexto de cobrança. Para clientes do EA, o contexto de cobrança é o registro. Para assinaturas individuais com tarifas pagas conforme o uso, o escopo de cobrança é todas as assinaturas qualificadas criadas pelo administrador da conta.</li></ul>|
|Região    |A região do Azure que é coberta pela reserva.|    
|Tamanho da VM     |O tamanho das instâncias de VM.|
|Otimizar para     |A flexibilidade do tamanho da instância VM é selecionada por padrão. Clique em **Configurações avançadas** para alterar o valor de flexibilidade do tamanho da instância para aplicar o desconto de reserva a outras VMs no mesmo [grupo de tamanhos de VM](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). A prioridade da capacidade prioriza a capacidade de data center para suas implantações. Ele oferece confiança adicional em sua capacidade de iniciar as instâncias de VM quando você precisar delas. A prioridade de capacidade está disponível apenas quando o escopo de reserva é uma assinatura única. |
|Termo        |Um ano ou três anos.|
|Quantidade    |O número de instâncias sendo compradas na reserva. A quantidade é o número de instâncias de VM que podem obter o desconto de cobrança. Por exemplo, se você estiver executando 10 VMs Standard_D2 no leste dos EUA, você deverá especificar Quantity como 10 para maximizar o benefício de todas as VMs em execução. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Dados de uso e utilização de reserva

Os dados de uso têm um preço efetivo de zero para o uso que obtém um desconto de reserva. Você pode ver qual instância de VM recebeu o desconto de reserva para cada reserva.

Para obter mais informações sobre como os descontos de reserva aparecem nos dados de uso, consulte [entender o uso de reserva do Azure para o registro de sua empresa](../articles/billing/billing-understand-reserved-instance-usage-ea.md) se você for um cliente do ea. Se você tiver uma assinatura individual, consulte [entender o uso de reserva do Azure para sua assinatura paga conforme o uso](../articles/billing/billing-understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Alterar uma reserva após a compra

Você pode fazer os seguintes tipos de alterações em uma reserva após a compra:

- Atualizar escopo de reserva
- Flexibilidade de tamanho de instância (se aplicável)
- Propriedade

Você também pode dividir uma reserva em partes menores e mesclar reservas já divididas. Nenhuma das alterações causa uma nova transação comercial ou alterar a data de término da reserva.

Você não pode fazer os seguintes tipos de alterações após a compra, diretamente:

- Uma região de reserva existente
- SKU
- Quantidade
- Duração

No entanto, você pode *trocar* uma reserva se desejar fazer alterações.

## <a name="cancellations-and-exchanges"></a>Cancelamentos e trocas

Caso precise cancelar sua reserva, pode haver um valor de rescisão antecipada de 12%. Os reembolsos baseiam-se no preço de compra ou no preço atual da reserva, o que for mais baixo. Os reembolsos são limitados a US$ 50.000 por ano. O reembolso que você recebe é o saldo proporcional restante menos o valor da tarifa de rescisão antecipada de 12%. Para cancelar, vá para a reserva no portal do Azure e selecione **reembolso**.

Se você precisar alterar a reserva de instâncias de VM reservadas para outra região, grupo de tamanhos de VM ou termo, você poderá fazer o Exchange. O intercâmbio deve ser para outra reserva que seja de valor igual ou maior. A data de início do prazo da nova reserva não é a mesma da reserva trocada. O termo de um ou três anos começa com quando você cria a nova reserva. Para o Exchange, acesse a reserva no portal do Azure e selecione **Exchange**.

Para obter mais informações sobre como trocar ou reembolsar reservas, consulte trocas [e reembolsos de reserva](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

- Para aprender a gerenciar uma reserva, confira [Gerenciar Reservas do Azure](../articles/billing/billing-manage-reserved-vm-instance.md).
- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
    - [O que são Reservas do Azure?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Gerenciar Reservas no Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Entender como o desconto de reserva é aplicado](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Entender o uso de reserva para uma assinatura com tarifas pagas conforme o uso](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Entender o uso de reserva para seu registro de empresa](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Custos de software do Windows não estão incluídos nas reservas](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)
