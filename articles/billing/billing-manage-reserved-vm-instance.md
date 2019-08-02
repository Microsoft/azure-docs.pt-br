---
title: Gerenciar reservas do Azure
description: Saiba como é possível alterar o escopo de assinatura e gerenciar o acesso às Reservas do Azure.
ms.service: billing
author: bandersmsft
manager: yashesvi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 89279387b3630ea654070eef671f131ec757d55f
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491191"
---
# <a name="manage-reservations-for-azure-resources"></a>Gerenciar Reservas para recursos do Azure

Depois de comprar uma reserva para o Azure, você precisa aplicar a reserva para uma assinatura diferente, altere a quem pode gerenciar a reserva, ou alterar o escopo da reserva. Também é possível dividir uma reserva em duas reservas para aplicar algumas das instâncias que você comprou para outra assinatura.

Se você comprou Instâncias de Máquinas Virtuais Reservadas do Azure, poderá alterar a configuração de otimização da reserva. O desconto de reserva pode ser aplicado a VMs na mesma série ou você pode reservar a capacidade do data center para um tamanho de VM específico.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Reserva e o pedido de reserva

Quando você compra de uma reserva, dois objetos são criados: **Pedido de reserva** e **reserva**.

No momento da compra, um pedido de reserva tem uma reserva sob ele. Ações como a divisão, mesclagem, reembolso parcial ou exchange criam novas reservas sob o **pedido de reserva**.

Para exibir um pedido de reserva, vá para **reservas** > selecione a reserva e, em seguida, clique no **ID do pedido de reserva**.

![Exemplo de detalhes do pedido de reserva que mostra a ID do pedido de reserva ](./media/billing-manage-reserved-vm-instance/reservation-order-details.png)

Uma reserva herda permissões do seu pedido de reserva.

## <a name="change-the-reservation-scope"></a>Alterar o escopo de reserva

 O desconto de reserva se aplica a máquinas virtuais, bancos de dados SQL, BD Cosmos do Azure ou outros recursos que correspondem à reserva e executados no escopo de reserva. O contexto de cobrança depende da assinatura usada para comprar a reserva.

Para atualizar o escopo de uma reserva:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione a reserva.
4. Selecione **Configurações** > **Configuração**.
5. Altere o escopo.

Se você alterar de compartilhado para escopo único, poderá selecionar apenas as assinaturas em que é o proprietário. Somente as assinaturas no mesmo contexto de cobrança que a reserva podem ser selecionadas.

O escopo só se aplica a assinaturas individuais com tarifas pré-pagas (ofertas MS-AZR - 0003p ou MS-AZR - 0023P), a oferta Enterprise MS-AZR - 0017p ou MS-AZR - 0148p ou tipos de assinatura de CSP.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Adicionar ou alterar os usuários que podem gerenciar uma reserva

Você pode delegar o gerenciamento de reserva adicionando pessoas às funções na ordem de reserva ou a reserva. Por padrão, a pessoa que coloca o pedido de reserva e o administrador da conta tem a função de proprietário na ordem de reserva e a reserva.

Você pode gerenciar o acesso aos pedidos de reservas e reservas independentemente das assinaturas que obtêm o desconto de reserva. Quando você conceder a alguém permissões para gerenciar a reserva ou um pedido de reserva, ele não conceder permissão para gerenciar a assinatura. Da mesma forma, se você conceder a alguém permissões para gerenciar uma assinatura no escopo da reserva, ele não dá a eles direitos para gerenciar a ordem de reserva ou a reserva.

Para executar o exchange ou o reembolso, o usuário deve ter acesso ao pedido de reserva. Ao conceder a alguém permissões, é melhor conceder permissões para o pedido de reserva, não a reserva.


Para delegar o gerenciamento de acesso de uma reserva:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reserva** para listar as reservas às quais você tem acesso.
3. Selecione a reserva que deseja delegar acesso a outros usuários.
4. Selecione **IAM (Controle de acesso)** .
5. Selecione **Adicionar atribuição de função** > **Função** > **Proprietário**. Ou, se você quiser conceder acesso limitado, selecione uma função diferente.
6. Digite o endereço de email do usuário que você deseja adicionar como proprietário.
7. Selecione o usuário e, em seguida, selecione **Salvar**.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dividir uma única reserva em duas reservas

 Depois de comprar mais de uma instância de recurso em uma reserva, é recomendável atribuir instâncias nessa reserva a assinaturas diferentes. Por padrão, todas as instâncias têm um escopo - assinatura única ou compartilhada. Por exemplo, você comprou 10 instâncias de reserva e especificou o escopo para ser a assinatura A. Agora, você pode alterar o escopo de 7 reservas para a assinatura A e as 3 restantes para a assinatura B. A divisão de uma reserva permite distribuir instâncias para gerenciamento de escopo granular. Você pode simplificar a alocação para as assinaturas escolhendo o escopo compartilhado. Contudo, para fins de gerenciamento ou de orçamento, você pode alocar quantidades a assinaturas específicas.

 Você pode dividir uma reserva em duas reservas por meio do PowerShell, CLI ou da API.

### <a name="split-a-reservation-by-using-powershell"></a>Dividir uma reserva usando o PowerShell

1. Obtenha a ID do pedido de reserva executando o seguinte comando:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. Obtenha os detalhes de uma reserva:

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Divida a reserva em duas e distribua as instâncias:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Você pode atualizar o escopo executando o seguinte comando:

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancellations-and-exchanges"></a>Cancelamentos e trocas

Dependendo do tipo de reserva, você poderá cancelá-la ou trocá-la. Para obter mais informações, confira as seções de trocas e cancelamentos nos tópicos a seguir:

- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](..//virtual-machines/windows/prepay-reserved-vm-instances.md#cancellations-and-exchanges)
- [Pré-pagamento para planos de software SUSE das reservas do Azure](../virtual-machines/linux/prepay-suse-software-charges.md#cancellation-and-exchanges-not-allowed)
- [Pagar antecipadamente por recursos de computação de banco de dados SQL com capacidade reservada do Azure SQL Database](../sql-database/sql-database-reserved-capacity.md#cancellations-and-exchanges)

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Alterar a configuração de otimização para Instâncias de VM Reservadas

 Ao comprar uma Instância de VM Reservada, você escolhe a flexibilidade do tamanho da instância ou a prioridade da capacidade. A flexibilidade do tamanho da instância aplica o desconto de reserva a outras VMs no mesmo [grupo de tamanhos de VM](https://aka.ms/RIVMGroups). A prioridade da capacidade prioriza a capacidade de data center para suas implantações. Essa opção oferece mais confiança na capacidade de iniciar as instâncias de VM quando forem necessárias.

Por padrão, quando o escopo da reserva é compartilhado, a flexibilidade do tamanho da instância é ativada. A capacidade do data center não é priorizada para implantações de VM.

Para reservas em que o escopo é único, você pode otimizar a reserva para prioridade de capacidade em vez de flexibilidade de tamanho de instância de VM.

Para atualizar a configuração de otimização da reserva:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione a reserva.
4. Selecione **Configurações** > **Configuração**.
5. Altere a configuração **Otimizar para**.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [Quais são as reservas para o Azure?](billing-save-compute-costs-reservations.md)

Compre um plano de serviço:
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagar antecipadamente por recursos de computação de banco de dados SQL com capacidade reservada do Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Pagar antecipadamente por recursos do Azure Cosmos DB com capacidade reservada do Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)

Compre um plano de software:
- [Pagar antecipadamente por planos de software do Red Hat das reservas do Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Pré-pagamento para planos de software SUSE das reservas do Azure](../virtual-machines/linux/prepay-suse-software-charges.md)

Entenda o uso e desconto:
- [Entender como o desconto de reserva de VM é aplicado](billing-understand-vm-reservation-charges.md)
- [Entender como o desconto de plano de software do Red Hat Enterprise Linux é aplicado](../billing/billing-understand-rhel-reservation-charges.md)
- [Entender como o desconto do plano de software do SUSE Linux Enterprise é aplicado](../billing/billing-understand-suse-reservation-charges.md)
- [Entender como outros descontos de reserva são aplicados](billing-understand-reservation-charges.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](billing-understand-reserved-instance-usage-ea.md)
- [Custos de software do Windows não estão incluídos nas reservas](billing-reserved-instance-windows-software-costs.md)
