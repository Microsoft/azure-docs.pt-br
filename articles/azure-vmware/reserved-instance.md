---
title: Economize custos com a instância reservada da solução Azure VMware
description: Saiba como comprar uma instância reservada para a solução do Azure VMware.
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 227ea9274f70cc58468e7e0b3ddcf8239f5b7f5b
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550968"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Economize custos com a solução Azure VMware

Quando você se compromete com uma instância reservada da [solução do Azure VMware](introduction.md), economiza dinheiro.  O desconto de reserva é aplicado automaticamente aos hosts da solução do Azure VMware em execução que correspondem ao escopo e aos atributos de reserva. Uma compra de instância reservada abrange apenas a parte de computação do seu uso e inclui os custos de licenciamento de software. 

## <a name="purchase-restriction-considerations"></a>Considerações sobre a restrição de compra

As instâncias reservadas estão disponíveis com algumas exceções.

-   **Nuvens** – as reservas estão disponíveis apenas nas regiões listadas na página [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) .

-   **Cota insuficiente** -uma reserva com escopo para uma assinatura única/compartilhada deve ter a cota de hosts disponível na assinatura para a nova instância reservada. Você pode [criar uma solicitação de aumento de cota](enable-azure-vmware-solution.md) para resolver esse problema.

-   **Qualificação da oferta**-você precisará de um [ea (contrato Enterprise do Azure)](../cost-management-billing/manage/ea-portal-agreements.md) com a Microsoft.

-   **Restrições de capacidade** -em raras circunstâncias, o Azure limita a compra de novas reservas para SKUs do host da solução Azure VMware devido à baixa capacidade em uma região.

## <a name="buy-a-reservation"></a>Comprar uma reserva

Você pode comprar uma instância reservada de uma instância de host de solução do Azure VMware no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Você pode pagar pela reserva [antecipada ou por pagamentos mensais](../cost-management-billing/reservations/prepare-buy-reservation.md).

Esses requisitos se aplicam à compra de uma instância de host dedicada reservada:

-   Você deve estar em uma função de *proprietário* para pelo menos uma assinatura de ea ou uma assinatura com uma taxa pré-paga.

-   Para assinaturas EA, você deve habilitar a opção **adicionar instâncias reservadas** no portal de [ea](https://ea.azure.com/). Se desabilitado, você deverá ser um administrador de EA para a assinatura para habilitá-lo.

-   Para a assinatura em um plano do Azure CSP (provedor de soluções de nuvem), o parceiro deve comprar as instâncias reservadas no portal do Azure para o cliente. 

### <a name="buy-reserved-instances-for-an-ea-subscription"></a>Comprar instâncias reservadas para uma assinatura de EA

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Selecione **Todos os serviços** > **Reservas**.

3. Selecione **comprar agora** e, em seguida, selecione **solução do Azure VMware**.

4. Insira os campos obrigatórios. Os atributos selecionados que correspondem à execução dos hosts da solução Azure VMware são qualificados para o desconto de reserva.  Os atributos incluem o SKU, as regiões (quando aplicável) e o escopo. O escopo de uma reserva seleciona o local a que a economia da reserva se aplica.

   Se você tiver um contrato EA, poderá usar a **opção Adicionar mais** para adicionar instâncias rapidamente. A opção não está disponível para outros tipos de assinatura.

   | Campo        |  Descrição |
   | ------------ | ------------ |
   | Subscription | A assinatura usada para pagar pela reserva. Os custos da reserva são cobrados segundo a forma de pagamento da assinatura. O tipo de assinatura deve ser um Enterprise Agreement (números de oferta: MS-AZR-0017P ou MS-AZR-0148P), contrato de cliente da Microsoft ou uma assinatura individual com tarifas pagas conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Os preços são deduzidos do saldo do Pagamento antecipado do Azure (anteriormente conhecido como compromisso monetário), se disponível, ou cobrados como excedente. Para uma assinatura com tarifas pagas conforme o uso, os encargos são cobrados no cartão de crédito da assinatura ou em um método de pagamento de fatura. |
   | Escopo        | O escopo da reserva pode cobrir uma assinatura ou várias assinaturas (escopo compartilhado). Se você selecionar:<br><ul><li><b>Escopo do grupo de recursos único</b> – aplica o desconto de reserva aos recursos correspondentes somente no grupo de recursos selecionado.</li><li><b>Escopo de assinatura única</b> – aplica o desconto de reserva aos recursos de correspondência na assinatura selecionada.</li><li><b>Escopo compartilhado</b> – aplica o desconto de reserva aos recursos correspondentes em assinaturas qualificadas que estão no contexto de cobrança. Para clientes do EA, o contexto de cobrança é o registro. Para assinaturas individuais com tarifas pagas conforme o uso, o escopo do orçamento são todas as assinaturas qualificadas criadas pelo administrador da conta.</li></ul>       |
   | Região       | A região do Azure que é coberta pela reserva.   |
   | Tamanho do host    | AV36    |
   | Termo         | Um ano ou três anos.  |
   | Quantidade     | O número de instâncias a serem compradas dentro da reserva. A quantidade é o número de hosts da solução do Azure VMware em execução que podem obter o desconto de cobrança.    |

### <a name="buy-reserved-instances-for-a-csp-subscription"></a>Comprar instâncias reservadas para uma assinatura CSP

Os CSPs que desejam comprar instâncias reservadas para seus clientes devem usar o procedimento **admin em nome do** (Aobo) da [documentação do Partner Center](/partner-center/azure-plan-manage). Para obter mais informações, exiba o vídeo [admin em nome do (Aobo)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) .

1. Entre no [Partner Center](https://partner.microsoft.com).

2. Selecione **CSP** para acessar a área **clientes** .

3. Expanda detalhes do cliente e selecione **portal de gerenciamento do Microsoft Azure**. 

   :::image type="content" source="media/reserved-instances/csp-partner-center-aobo.png" alt-text="Área de clientes do Microsoft Partner Center" lightbox="media/reserved-instances/csp-partner-center-aobo.png":::

4. Na portal do Azure, selecione **todas as**  >  **reservas** de serviços.

5. Selecione **comprar agora** e, em seguida, selecione **solução do Azure VMware**.

   :::image type="content" source="media/reserved-instances/csp-buy-ri-azure-portal.png" alt-text="Reservas de portal do Microsoft Azure" lightbox="media/reserved-instances/csp-buy-ri-azure-portal.png":::

6. Insira os campos obrigatórios. Os atributos selecionados que correspondem à execução dos hosts da solução Azure VMware são qualificados para o desconto de reserva.  Os atributos incluem o SKU, as regiões (quando aplicável) e o escopo. O escopo de uma reserva seleciona o local a que a economia da reserva se aplica.

   | Campo        |  Descrição |
   | ------------ | ------------ |
   | Subscription | A assinatura usada para pagar pela reserva. Os custos da reserva são cobrados segundo a forma de pagamento da assinatura. O tipo de assinatura deve ser um qualificado, que neste caso é uma assinatura do CSP|
   | Escopo        | O escopo da reserva pode cobrir uma assinatura ou várias assinaturas (escopo compartilhado). Se você selecionar:<br><ul><li><b>Escopo do grupo de recursos único</b> – aplica o desconto de reserva aos recursos correspondentes somente no grupo de recursos selecionado.</li><li><b>Escopo de assinatura única</b> – aplica o desconto de reserva aos recursos de correspondência na assinatura selecionada.</li><li><b>Escopo compartilhado</b> – aplica o desconto de reserva aos recursos correspondentes em assinaturas qualificadas que estão no contexto de cobrança. Para clientes do EA, o contexto de cobrança é o registro. Para assinaturas individuais com tarifas pagas conforme o uso, o escopo do orçamento são todas as assinaturas qualificadas criadas pelo administrador da conta.</li></ul>       |
   | Região       | A região do Azure que é coberta pela reserva.   |
   | Tamanho do host    | AV36    |
   | Termo         | Um ano ou três anos.  |
   | Quantidade     | O número de instâncias a serem compradas dentro da reserva. A quantidade é o número de hosts da solução do Azure VMware em execução que podem obter o desconto de cobrança.     |

Para saber mais sobre como exibir as reservas adquiridas para seu cliente, consulte o artigo [Exibir reservas do Azure como provedor de soluções de nuvem (CSP)](../cost-management-billing/reservations/how-to-view-csp-reservations.md) .

## <a name="usage-data-and-reservation-usage"></a>Dados de uso e uso de reserva

Seu uso que obtém um desconto de reserva tem um preço efetivo de zero. Você pode ver qual instância de solução do Azure VMware recebeu o desconto de reserva para cada reserva.

Para obter mais informações sobre como os descontos de reserva aparecem nos dados de uso:

- Para clientes do EA, consulte [entender o uso de reserva do Azure para seu registro corporativo](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- Para assinaturas individuais, consulte [entender o uso de reserva do Azure para sua assinatura paga conforme o uso](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="change-a-reservation-after-purchase"></a>Alterar uma reserva após a compra

Você pode fazer essas alterações em uma reserva após a compra:

-   Atualizar o escopo de reserva

-   Flexibilidade de tamanho de instância (se aplicável)

-   Propriedade

Você também pode dividir uma reserva em partes menores ou reservas de mesclagem. Nenhuma das alterações causa uma nova transação comercial ou alterar a data de término da reserva.

Para obter detalhes sobre reservas gerenciadas pelo CSP, consulte [vender Microsoft Azure reservas para clientes usando o Partner Center, o portal do Azure ou as APIs](/partner-center/azure-reservations).



>[!NOTE]
>Depois de adquirir sua reserva, você não poderá fazer esses tipos de alterações diretamente:
>
> - Uma região de reserva existente
> - SKU
> - Quantidade
> - Duration
>
>No entanto, você pode *trocar* uma reserva se desejar fazer alterações.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

Os CSPs podem cancelar, trocar ou reembolsar reservas, com determinadas limitações, adquiridas para o cliente. Para obter mais informações, consulte [gerenciar, cancelar, trocar ou reembolsar reservas de Microsoft Azure para clientes](/partner-center/azure-reservations-manage).

## <a name="next-steps"></a>Próximas etapas

Agora que você já abordou a compra de uma instância reservada da solução Azure VMware, talvez queira saber mais sobre:

- [Criando uma avaliação de solução do Azure VMware](../migrate/how-to-create-azure-vmware-solution-assessment.md).
- [Gerenciando o DHCP para a solução do Azure VMware](manage-dhcp.md).
- [Gerenciamento do ciclo de vida de VMs de solução do Azure VMware](lifecycle-management-of-azure-vmware-solution-vms.md).
 