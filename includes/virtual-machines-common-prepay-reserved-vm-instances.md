---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/13/2019
ms.openlocfilehash: d9b9aae8bea323e5aac74a2e317b82d4cb43568f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60406855"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Pagar antecipadamente pelas Máquinas Virtuais com Instâncias de VM Reservadas do Microsoft Azure

Pagar antecipadamente por máquinas virtuais e economizar dinheiro com Instâncias de Máquina Virtual Reservada do Microsoft Azure. Para maiores informações, veja [Azure Reserved VM Instances offering](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Você pode comprar uma Instância de VM Reservada no [portal do Azure](https://portal.azure.com). Para comprara uma instância:

- Você deve estar em uma função de Proprietário em pelo menos uma assinatura Enterprise ou Pagamento Conforme o Uso.
- Para as assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal EA](https://ea.azure.com). Ou, se essa configuração estiver desabilitada, você deve ser um administrador de EA na assinatura.
- Para o programa do CSP (Provedor de Solução na Nuvem) somente os agentes administradores ou agentes de vendas podem comprar reservas.

O desconto da reserva é aplicado automaticamente ao número de máquinas virtuais em execução que correspondem ao escopo e atributos da reserva. Você pode atualizar o escopo da reserva por meio do [portal do Azure](https://portal.azure.com), PowerShell, CLI ou por meio da API.

## <a name="determine-the-right-vm-size-before-you-buy"></a>Determinar o tamanho correto da VM antes de comprar

Antes de comprar uma reserva, você deve determinar o tamanho da VM que você precisa. As seções a seguir ajudará você a determinar o tamanho correto de VM.

### <a name="use-reservation-recommendations"></a>Use as recomendações de reserva

Você pode usar as recomendações de reserva para ajudar a determinar as reservas, que você deve comprar.

- Recomendações de compra e a quantidade recomendada são mostram quando você adquire uma instância reservada de VM no portal do Azure.
- O Azure Advisor fornece recomendações de compra para assinaturas individuais.  
- Você pode usar as APIs para obter recomendações de compra para o escopo compartilhado e o escopo de assinatura única. Para obter mais informações, consulte [reservados recomendação de compra de instância APIs para clientes corporativos](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Para clientes do EA, adquirir as recomendações para compartilhado e escopos de assinatura única estão disponíveis com o [pacote de conteúdo de Insights de consumo do Azure Power BI](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="classic-vms-and-cloud-services"></a>Serviços de nuvem e VMs clássicas

Instâncias de máquina virtual reservada se aplicam a ambas as VMs clássicas e serviços de nuvem quando a flexibilidade de tamanho de instância é habilitada automaticamente. Não existem quaisquer SKUs especiais para VMs clássicas ou serviços de nuvem. Os mesmos SKUs de VM se aplicam a eles.

Por exemplo, você pode converter suas VMs clássicas ou serviços de nuvem para VMs baseadas no Azure Resource Manager. Neste exemplo, o desconto de reserva aplica automaticamente as VMs de correspondência. Não é necessário para *exchange* uma instância reservada existente - aplica automaticamente.

### <a name="analyze-your-usage-information"></a>Analise suas informações de uso
Você deve analisar suas informações de uso para ajudar a determinar quais reservas você deve comprar.

Dados de uso estão disponíveis no arquivo de uso e APIs. Usá-los juntos para determinar quais reserva para comprar. Você deve verificar para instâncias VM que têm alto uso diariamente para determinar a quantidade de compra de reservas.

Evite a `Meter` subcategoria e `Product` campos nos dados de uso. Eles não distinguir entre tamanhos de VM que usam armazenamento premium. Se você usar esses campos para determinar o tamanho da VM para compra de reserva, você pode comprar o tamanho incorreto. Em seguida, você não obterá o desconto de reserva que você espera. Em vez disso, consulte o `AdditionalInfo` campo em seu arquivo de uso ou a API de uso para determinar o tamanho correto de VM.

### <a name="purchase-restriction-considerations"></a>Considerações de restrição de compra

Instâncias de VM reservadas estão disponíveis para a maioria dos tamanhos de VM, com algumas exceções. Descontos de reserva não se aplicam para as seguintes VMs:

- **Série de VM** -A-series, série Av2 ou G-series.

- **As VMs no modo de visualização** -qualquer série de VM ou o tamanho que está em visualização.

- **Nuvens** -reservas não estão disponíveis para compra nas regiões da Alemanha ou China.

- **Cota insuficiente** -uma reserva que tem como escopo uma única assinatura deve ter uma cota de vCPU disponível na assinatura para a nova RI. Por exemplo, se a assinatura de destino tiver um limite de cota de 10 vCPUs para a Série D, você não poderá comprar uma reserva para 11 instâncias Standard_D1. A verificação de cota para reservas inclui as VMs já implantadas na assinatura. Por exemplo, se a assinatura tiver uma cota de 10 vCPUs para o D-Series e tiver duas instâncias standard_D1 implantadas, você poderá comprar uma reserva para 10 instâncias standard_D1 nessa assinatura. Você pode [criar solicitação de aumento de cotação](../articles/azure-supportability/resource-manager-core-quotas-request.md) para resolver esse problema.

- **Restrições de capacidade** – em circunstâncias raras, os limites do Azure dimensiona a compra de novas reservas para o subconjunto da VM, devido à baixa capacidade em uma região.

## <a name="buy-a-reserved-vm-instance"></a>Comprar uma Instância de VM Reservada

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione **Adicionar** para adquirir uma nova reserva.
4. Preencha os campos obrigatórios. As instâncias de VM em execução que correspondem com os atributos que você selecionar, ficam qualificados para obter o desconto de reserva. O número real de suas instâncias VM que obtêm o desconto depende do escopo e da quantidade selecionada.

    | Campo      | DESCRIÇÃO|
    |------------|--------------|
    |NOME        |O nome dessa reserva.|
    |Assinatura|A assinatura usada para pagar pela reserva. São cobrados os custos iniciais para a reserva à forma de pagamento na assinatura. O tipo de assinatura deve ser um contrato empresarial (números da oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Pagamento Conforme o Uso (números da oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como média. Para a assinatura de Pagamento Conforme o Uso, as cobranças são feitas ao cartão de crédito ou à forma de pagamento de faturas na assinatura.|    
    |Escopo       |O escopo de assinatura pode abranger uma ou várias assinaturas (escopo compartilhado). Se você selecionar: <ul><li>Assinatura única - o desconto da reserva é aplicado às VMs nesta assinatura. </li><li>Compartilhado - o desconto da reserva é aplicado às VMs em execução em qualquer assinatura, dentro de seu contexto de cobrança. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas no registro. Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.</li></ul>|
    |Região    |A região do Azure que é coberta pela reserva.|    
    |Tamanho da VM     |O tamanho das instâncias de VM.|
    |Otimizar para     |A flexibilidade de tamanho da instância de VM aplica o desconto de reserva a outras VMs no mesmo [grupo de tamanhos de VM](https://aka.ms/RIVMGroups). A prioridade da capacidade prioriza a capacidade de data center para suas implantações. Isso oferece confiança adicional na sua capacidade de iniciar as instâncias de VM quando precisar delas. A prioridade de capacidade está disponível apenas quando o escopo de reserva é uma assinatura única. |
    |Termo        |Um ano ou três anos.|
    |Quantidade    |O número de instâncias sendo compradas na reserva. A quantidade é o número de instâncias de VM que podem obter o desconto de cobrança. Por exemplo, se estiver executando 10 VMs Standard_D2 no Leste dos EUA, pode especificar a quantidade como 10 para maximizar o benefício para todas as máquinas em execução. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="change-a-reservation-after-purchase"></a>Alterar uma reserva após a compra

Você pode fazer os seguintes tipos de alterações de uma reserva depois da compra:

- Atualizar o escopo de reserva
- Flexibilidade de tamanho de instância (se aplicável)
- Propriedade

Você também pode dividir uma reserva em menor partes e mesclagem já dividida reservas. Nenhuma das alterações de fazer com que uma nova transação comercial ou altere a data de término da reserva.

Você não pode fazer os seguintes tipos de alterações após a compra, diretamente:

- Região de uma reserva existente
- SKU
- Quantidade
- Duration

No entanto, você pode *exchange* uma reserva, se você quiser fazer alterações.

## <a name="cancellations-and-exchanges"></a>Cancelamentos e trocas

Caso precise cancelar sua reserva, pode haver um valor de rescisão antecipada de 12%. Os reembolsos baseiam-se no preço de compra ou no preço atual da reserva, o que for mais baixo. Os reembolsos são limitados a US$ 50.000 por ano. O reembolso que você recebe é o saldo proporcional restante menos o valor de rescisão antecipada de 12%. Para solicitar um cancelamento, vá até a reserva no portal do Azure e selecione **Reembolso** para criar uma solicitação de suporte.

Se você precisar mudar sua reserva de Instâncias de VM Reservadas para outra região, outro grupo de tamanho de VM ou outro prazo, você poderá trocá-la por outra reserva de valor igual ou maior. A data de início do prazo da nova reserva não é a mesma da reserva trocada. O prazo de 1 ou 3 anos começa quando você cria a nova reserva. Para solicitar uma troca, vá até a reserva no portal do Azure e selecione **Trocar** para criar uma solicitação de suporte.

Para obter mais informações sobre como as reservas do exchange ou o reembolso, consulte [trocas de reserva e os reembolsos](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximos passos

- Para aprender a gerenciar uma reserva, confira [Gerenciar Reservas do Azure](../articles/billing/billing-manage-reserved-vm-instance.md).
- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
    - [O que são Reservas do Azure?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Gerenciar Reservas no Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Entender como o desconto de reserva é aplicado](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Entender o uso de reserva para a sua assinatura paga conforme o uso](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Entender o uso de reserva para seu registro de empresa](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Custos de software do Windows não estão incluídos nas reservas](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)
